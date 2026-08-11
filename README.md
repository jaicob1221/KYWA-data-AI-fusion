# KYWA 데이터 융복합 서비스

한국청소년활동진흥원 데이터 융복합 조회 전용 Streamlit 앱입니다.

## 메뉴

| 메뉴 | 설명 |
|------|------|
| 🏕️ 수련시설 안전 및 인증프로그램 현황 | 공공데이터 svc001 + svc004 매칭 조회 |
| 🌟 미래로(진로 안내 도우미) | 대화형 진로·학과·체험·활동 안내 |
| 🔐 관리자 · 가입승인 / 비밀번호 | 가입 승인, 비번 재설정 (yjchoi) |

## 회원가입·승인

1. 사용자가 **회원가입** 신청 → `approved: false` 로 저장  
2. 관리자(`yjchoi`)가 **가입승인** 메뉴에서 승인  
3. 승인된 계정만 **로그인** 가능 (관리자는 자동 승인)

---

## 1. 로컬에서 옮길 것

프로젝트 폴더 구조:

```
kywa_data_fusion/
├── app.py                 # 메인 앱 (필수)
├── requirements.txt       # 필수
├── README.md
├── .streamlit/
│   ├── config.toml        # 테마 (선택)
│   └── secrets.toml       # 로컬만, Git에 올리지 말 것
├── assets/
│   ├── logo.png           # CI (권장)
│   ├── kiumee.png         # 캐릭터 (권장)
│   └── irumee.png
└── cache/
    └── ggomgil_programs.csv   # 꿈길 체험 CSV (미래로 권장)
```

**GitHub에 올리면 안 되는 것**

- `.streamlit/secrets.toml` (API 키)
- `cache/users.json` (개인정보, 선택)
- 실제 API 키가 들어간 파일

---

## 2. GitHub 등록

### 2-1. 저장소 만들기

1. [https://github.com/new](https://github.com/new) 접속  
2. Repository name 예: `kywa-data-fusion`  
3. Public 또는 Private 선택 → Create repository  

### 2-2. 로컬에서 푸시

Windows PowerShell / CMD 예시:

```bash
cd kywa_data_fusion

git init
git add app.py requirements.txt README.md .streamlit/config.toml assets cache/README.md assets/README.md
git add cache/ggomgil_programs.csv
# secrets.toml 은 추가하지 않음

echo .streamlit/secrets.toml>> .gitignore
echo cache/users.json>> .gitignore
echo __pycache__/>> .gitignore

git add .gitignore
git commit -m "Initial commit: KYWA data fusion service"
git branch -M main
git remote add origin https://github.com/본인계정/kywa-data-fusion.git
git push -u origin main
```

GitHub 로그인·토큰이 필요하면 Personal Access Token을 비밀번호 자리에 사용합니다.

---

## 3. Streamlit Community Cloud 배포

1. [https://share.streamlit.io](https://share.streamlit.io) 접속 후 GitHub 계정으로 로그인  
2. **New app**  
3. 설정  
   - Repository: `본인계정/kywa-data-fusion`  
   - Branch: `main`  
   - Main file path: `app.py`  
4. **Advanced settings → Secrets** 에 아래 형식으로 입력 (로컬 `secrets.toml`과 동일):

```toml
OPENAI_API_KEY = "sk-..."
CAREER_NET_API_KEY = "..."
DATA_GO_KR_KEY = "..."
YOUTH_ACTIV_KEY = "..."
```

5. Deploy  

배포 후 URL 예: `https://kywa-data-fusion.streamlit.app`

### 관리자 첫 로그인

1. 앱에서 `yjchoi` 계정으로 **회원가입** (또는 로컬에서 users.json에 미리 생성)  
2. Cloud는 파일 시스템이 휘발성일 수 있으므로, 첫 배포 후 회원가입 →  
   Secrets만으로는 유저 DB가 없으므로 **직접 가입 후**  
   코드상 `ADMIN_USER = "yjchoi"` 이므로 해당 아이디는 승인 없이 로그인되도록 이미 처리됨  
3. 다른 사용자는 가입 신청 → `yjchoi`가 **가입승인** 메뉴에서 승인  

---

## 4. API 키 (기존과 동일)

| 키 | 용도 |
|----|------|
| OPENAI_API_KEY | 미래로 AI 대화·분석 (또는 Claude/Groq) |
| CAREER_NET_API_KEY | 직업·학과·진로교육자료 |
| DATA_GO_KR_KEY | 수련시설 안전평가(svc001), 인증프로그램(svc004) |
| YOUTH_ACTIV_KEY | 청소년활동 프로그램 검색 |

로컬 테스트:

```bash
cd kywa_data_fusion
pip install -r requirements.txt
# .streamlit/secrets.toml 작성
streamlit run app.py
```

---

## 5. 참고

- 디자인·로고·캐릭터 배치는 기존 KYWA AI 업무지원도구와 동일 코드 경로를 사용합니다.  
- `assets/` 에 이미지 없으면 텍스트 로고로 대체됩니다.  
- Streamlit Cloud 무료 티어는 앱이 잠들 수 있고, 재시작 시 `cache/users.json`이 초기화될 수 있습니다.  
  상시 운영·회원 영구 보관이 필요하면 DB(Supabase 등) 연동을 권장합니다.
