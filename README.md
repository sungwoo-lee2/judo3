# Leader Finder v10

Render 실데이터 연결 진단 강화 버전입니다.

- `/api/diagnostics`: 가격·거래대금 / 종목목록 / 외국인 수급 / 기관 수급을 독립 진단
- 메인 화면에 연결 성공/실패와 오류 원인 표시
- `/api/refresh` 실패 시 기존 fallback 유지하되 진단 API에서 실패를 숨기지 않음
- Render Start Command: `uvicorn app:app --host 0.0.0.0 --port $PORT`
- Build Command: `pip install -r requirements.txt`

기존 v9의 주도주/초기주도/눌림목/생애주기/성과검증/Supabase 기능을 유지합니다.

# Leader Finder v9

국내 주식 주도주·초기 주도주·눌림목·섹터 대장 교체·성과검증을 한 화면에서 추적하는 FastAPI 앱입니다.

## v8 변경점

- Render Blueprint 배포용 `render.yaml` 정리
- Supabase 초기화용 `supabase.sql` 분리
- 환경변수 예시 `.env.example` 추가
- Render Health Check용 `GET /healthz` 추가
- Supabase 미연결/장애 시 로컬 JSON fallback 유지
- v7 기능 전체 유지: TOP20, 주도섹터, EARLY LEADER, 외국인·기관 수급, 연속 순매수, 눌림목, 생애주기, 대장교체, 성과검증

## 가장 쉬운 배포 순서

1. 이 폴더 전체를 GitHub 새 Repository에 업로드합니다.
2. Supabase 프로젝트를 만들고 SQL Editor에서 `supabase.sql`을 1회 실행합니다.
3. Render에서 **New > Blueprint**를 선택하고 GitHub Repository를 연결합니다.
4. Render가 `render.yaml`을 읽으면 `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY` 두 Secret 값만 입력합니다.
5. 배포 후 `/healthz`가 `ok: true`를 반환하는지 확인합니다.
6. 앱에 접속해 **데이터 새로고침**을 눌러 실제 시장 데이터 조회를 시도합니다.

## Render 환경변수

```text
AUTO_REFRESH=0
UNIVERSE_SIZE=80
SUPABASE_URL=https://YOUR_PROJECT.supabase.co
SUPABASE_SERVICE_ROLE_KEY=YOUR_SERVICE_ROLE_KEY
SUPABASE_STATE_TABLE=leader_finder_state
SUPABASE_STATE_ID=leader_finder_v9
```

`SUPABASE_SERVICE_ROLE_KEY`는 서버 비밀키입니다. GitHub 파일이나 브라우저 자바스크립트에 넣으면 안 됩니다.

## 로컬 실행

```bash
pip install -r requirements.txt
uvicorn app:app --host 0.0.0.0 --port 8000
```

- 메인: `http://localhost:8000`
- 상태체크: `http://localhost:8000/healthz`

## 주요 API

- `GET /healthz`
- `GET /api/storage-status`
- `POST /api/refresh`
- `GET /api/snapshot`
- `GET /api/leaders`
- `GET /api/sectors`
- `GET /api/early-leaders`
- `GET /api/lifecycle`
- `GET /api/pullbacks`
- `GET /api/rotations`
- `GET /api/performance`

## 데이터 주의사항

pykrx 및 pykrx는 외부 데이터 소스나 네트워크 상태 변화에 따라 조회가 실패할 수 있습니다. 이 앱은 실패 시 샘플/로컬 fallback으로 계속 동작하도록 설계되어 있습니다. 실제 투자에 사용하기 전에는 종가·수급 데이터가 정상 갱신되는지 반드시 확인하세요.


## v9 Render 빌드 수정

- Render에서 설치되지 않던 `FinanceDataReader==0.9.96` 의존성을 완전히 제거했습니다.
- 가격/거래대금/종목명/투자자 수급을 `pykrx` 중심으로 통일했습니다.
- Render Python 버전은 `3.11.11`로 고정합니다.
- Start Command: `uvicorn app:app --host 0.0.0.0 --port $PORT`
- 앱 부팅 시 실시간 KRX 조회를 하지 않도록 `AUTO_REFRESH=0`을 유지합니다. 서버가 먼저 뜨고 사용자가 데이터 새로고침을 눌렀을 때 조회합니다.
