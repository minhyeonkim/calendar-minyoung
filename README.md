# 말랑 캘린더 (개인용)

브라우저 저장(localStorage) 대신 Firebase(Firestore)에 일정을 저장해서, 어느 기기에서 링크를 열어도 같은 일정이 보이는 개인용 캘린더입니다.

## 구조

- `index.html` 파일 하나로 동작하는 정적 웹페이지입니다. 별도 서버/빌드 과정이 없습니다.
- Firebase 프로젝트: `calendar-minyoung`
- 데이터 저장 위치: Firestore `events` 컬렉션
  - 문서 필드: `calendarId`("malang" 고정), `dateKey`("YYYY-MM-DD"), `text`, `color`, `createdAt`
  - `calendarId` 필드를 넣어둔 이유: 나중에 이 프로젝트가 여러 캘린더(예: 다른 용도)로 늘어나도, 같은 Firebase 프로젝트 안에서 `calendarId` 값만 다르게 주면 데이터가 서로 섞이지 않고 분리됩니다.
- 로그인 화면은 없지만, 내부적으로 Firebase 익명 로그인을 한 번 하고 나서만 Firestore 읽기/쓰기가 허용됩니다(아무나 막 쓰지 못하게 하는 최소한의 보안 장치).

## Firestore 보안 규칙

```
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    match /events/{eventId} {
      allow read, write: if request.auth != null;
    }
  }
}
```

## Firebase 설정값 위치

`index.html` 안의 `firebaseConfig` 객체에 있습니다. 다른 Firebase 프로젝트로 옮기고 싶으면 이 값만 교체하면 됩니다.

## 배포 방법 (GitHub Pages)

1. 이 폴더를 개인 GitHub 저장소에 push 합니다.
2. 저장소 설정(Settings) → Pages → Source에서 `main` 브랜치, 루트(`/`) 선택 후 저장합니다.
3. 몇 분 후 `https://<깃허브아이디>.github.io/<저장소이름>/` 주소로 접속 가능합니다.

## 나중에 할 수 있는 것

- 다른 사람과 공유하고 싶지 않다면, Firestore 규칙에 특정 이메일만 허용하도록 인증 방식을 이메일 로그인으로 바꿀 수 있습니다.
- 여러 개의 캘린더(부서별, 용도별)로 늘리고 싶다면 `CALENDAR_ID` 값을 화면마다 다르게 주면 됩니다(예: URL 파라미터로 받기).
