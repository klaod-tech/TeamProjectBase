# TeamProjectBase

팀 프로젝트용 칸반 보드. 메모 카드, 우선순위(1=가장 중요, 위로 정렬)와 우선순위 필터, 카드별 댓글(이미지 첨부·붙여넣기 가능), 좋아요, 접속 중인 팀원 표시를 지원한다.
페이지는 HTML 파일 하나이고, 데이터와 로그인은 Firebase(Firestore + Google 로그인)가 맡는다.

칸: To Do → Analysis → Development → Test → Blocked → Done

## 파일

| 파일 | 역할 |
|---|---|
| `team-board.html` | 보드 페이지 전체 (HTML·CSS·JS) |
| `firestore.rules` | Firestore 보안 규칙 |
| `firebase.json` | 호스팅·규칙 배포 설정 |
| `.firebaserc` | 배포할 Firebase 프로젝트 ID |

## 새 팀플에 다시 쓰기

`team-board.html`의 `firebaseConfig`와 `.firebaserc`가 지금 Firebase 프로젝트를 가리키고 있다.
그대로 배포하면 **이전 팀의 보드와 데이터를 같이 쓰게 되므로**, 새 팀에서는 Firebase 프로젝트를 새로 만든다.

1. [Firebase 콘솔](https://console.firebase.google.com)에서 프로젝트 추가 (Spark 무료 요금제)
2. Firestore Database 만들기: ID `(default)`, 위치 `asia-northeast3 (Seoul)`, 프로덕션 모드
3. Authentication → 로그인 방법 → Google 사용 설정
4. 프로젝트 개요 → 앱 추가 → 웹 앱 등록(호스팅 체크 안 함) → 나온 `firebaseConfig`로 `team-board.html`의 값을 교체
5. `.firebaserc`의 `default`를 새 프로젝트 ID로 교체
6. 배포 (Node.js 필요)

   ```
   npx firebase-tools login
   npx firebase-tools deploy --only firestore:rules,hosting
   ```

7. 나온 `https://<프로젝트ID>.web.app` 주소를 팀원에게 공유

## 접근 제한

기본값은 Google 계정으로 로그인한 누구나 읽고 쓸 수 있다.
특정 팀원만 허용하려면 `firestore.rules`의 `member()`를 주석에 적힌 이메일 목록 방식으로 바꾸고 규칙을 다시 배포한다.

## 참고

- `firebaseConfig`의 apiKey 등은 원래 웹페이지에 공개되는 값이다. 실제 접근 통제는 `firestore.rules`가 한다.
- 댓글 이미지는 브라우저에서 최대 1280px JPEG로 줄여 Firestore 문서 안에 저장한다(유료 Storage 불필요). GIF는 첫 프레임만 남는다.
- 링크를 카카오톡 안에서 열면 Google 로그인이 막힌다. "다른 브라우저로 열기"로 연다.
