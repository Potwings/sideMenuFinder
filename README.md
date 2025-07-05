# MenuFinder - 사이드 메뉴 검색 서비스

Vue.js로 제작된 음식점 메뉴 검색 웹 애플리케이션입니다.

## 기능

- 음식점 이름(카테고리)과 메뉴 이름으로 검색
- 실시간 검색 결과 표시
- 인기 음식점 및 근처 추천 음식점 정보
- 반응형 웹 디자인
- 로딩 및 에러 상태 처리

## 기술 스택

- **Frontend**: Vue.js 3
- **Build Tool**: Vite
- **HTTP Client**: Axios
- **Styling**: CSS3 (Plus Jakarta Sans 폰트)

## 설치 및 실행

### 1. 의존성 설치

```bash
npm install
```

### 2. 개발 서버 실행

```bash
npm run dev
```

개발 서버가 `http://localhost:3000`에서 실행됩니다.

### 3. 빌드

```bash
npm run build
```

## API 연동

백엔드 서버와의 연동을 위해 다음 설정이 필요합니다:

- **서버 URL**: `http://localhost:8080`
- **API 엔드포인트**: `/store`
- **HTTP 메서드**: GET
- **파라미터**:
  - `store`: 음식점 이름(카테고리)
  - `menu`: 음식 메뉴 이름

### API 응답 예시

```json
[
  {
    "id": 1,
    "name": "김씨네 부엌",
    "category": "한식",
    "menu": "김치찌개",
    "rating": "4.5"
  }
]
```

## 프로젝트 구조

```
src/
├── App.vue          # 메인 애플리케이션 컴포넌트
├── main.js          # 애플리케이션 진입점
└── style.css        # 글로벌 스타일
```

## 사용법

1. 첫 번째 입력 필드에 음식점 이름이나 카테고리를 입력
2. 두 번째 입력 필드에 찾고자 하는 메뉴 이름을 입력
3. "검색" 버튼을 클릭하여 검색 실행
4. 검색 결과가 화면에 표시됩니다

## 브라우저 지원

- Chrome (최신 버전)
- Firefox (최신 버전)
- Safari (최신 버전)
- Edge (최신 버전) 