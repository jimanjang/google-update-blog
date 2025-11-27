 📨 Google Workspace Updates Chat Bot
<img width="1630" height="798" alt="image" src="https://github.com/user-attachments/assets/40e84240-3453-4b0d-a045-d38d227c112f" />
 Google Apps Script와 Webhook을 이용해 RSS/Atom 피드의 새 글을 Google Chat으로 자동 전송하는 봇입니다.

---

## 📖 개요

이 스크립트는 공식 Google 블로그 등의 RSS/Atom 피드를 주기적으로 확인하고,
새로운 게시물이 있으면 지정된 **Google Chat 채팅방**에 자동으로 발행합니다.
Google Apps Script 하나로 동작하며, 별도의 서버나 백엔드는 필요 없습니다.

---

## ⚙️ 작동 방식

1. **초기화**

   * `initializeScript()` 함수를 한 번 실행합니다.
   * 권한 승인, 로컬 저장소 초기화, 트리거 설정, 첫 실행을 자동으로 처리합니다.

2. **주기적 실행**

   * 설정된 시간 간격(`TRIGGER_INTERVAL_HOURS`)마다 `executeUpdateWorkflow()`가 자동 실행됩니다.
   * 각 피드를 가져와 파싱한 뒤, 이전에 본 적 없는 새 게시물을 식별합니다.
   * 새 게시물만 지정된 Google Chat Webhook으로 발송합니다.

3. **로컬 저장소 관리**

   * 게시물 URL 경로를 `ScriptProperties`에 저장하여 중복 발행을 방지합니다.

---

## 🧩 설정 방법

아래 변수들을 필요에 맞게 수정하면 됩니다.

| 변수                         | 설명                                                                       |
| -------------------------- | ------------------------------------------------------------------------ |
| `WEBHOOKS`                 | 메시지를 보낼 Chat 방의 Webhook URL. `YOUR_WEBHOOK_URL_GOES_HERE` 부분을 실제 URL로 변경 |
| `MAX_CONTENT.CHARS`        | 게시물 요약문 길이 제한                                                            |
| `MAX_CONTENT.UPDATES`      | 한 번에 전송할 최대 게시물 수 (RSS의 경우 최대 25개)                                       |
| `MAX_INIT_UPDATES`         | 초기 실행 시 발행할 게시물 개수                                                       |
| `TRIGGER_INTERVAL_HOURS`   | 피드 업데이트 확인 주기 (시간 단위)                                                    |
| `NOTIFY_HOURS.START / END` | 알림을 보낼 수 있는 시간대 (로컬 기준)                                                  |
| `NOTIFY_WEEKEND`           | 주말에도 실행할지 여부                                                             |

---

## 🌐 피드 예시

```js
const FEEDS = {
  WORKSPACE_UPDATES: {
    format: FEED_FORMAT.FB_XML,
    title: 'Google Workspace Updates',
    subtitle: 'workspaceupdates.googleblog.com',
    source: 'https://feeds.feedburner.com/GoogleAppsUpdates',
    logo: 'https://fonts.gstatic.com/s/i/productlogos/googleg/v6/web-512dp/logo_googleg_color_1x_web_512dp.png',
    cta: 'READ MORE',
    filters: ['What’s changing', 'Quick launch summary'],
    webhooks: [WEBHOOKS.ADMIN_ROOM]
  },
};
```

---

## 💬 지원 피드 형식

| 형식                | 설명                                  |
| ----------------- | ----------------------------------- |
| `FB_XML`          | FeedBurner XML 형식 (Google 공식 블로그 등) |
| `GOOGLE_BLOG_RSS` | 표준 Google Blog RSS 형식               |

---

## 🪄 지원 Webhook 플랫폼

| 플랫폼           | 설명                             |
| ------------- | ------------------------------ |
| `GOOGLE_CHAT` | Google Chat Webhook을 통해 메시지 발송 |

---

## 🌍 번역 기능 (옵션)

영문 게시물을 자동으로 한글로 번역하는 기능이 포함되어 있습니다.
`LanguageApp.translate()`를 사용하며, 기본 번역 방향은 `영어(en) → 한국어(ko)`입니다.

```js
translateText_(text, targetLang = 'ko')
```

---

## 🚀 배포 방법

1. [Google Apps Script](https://script.google.com/) 에서 새 프로젝트를 만듭니다.
2. 전체 코드를 복사해 붙여넣습니다.
3. Webhook URL을 실제 Google Chat Webhook으로 교체합니다.
4. `initializeScript()` 함수를 한 번 실행하여 권한을 승인합니다.
5. 이후 자동으로 주기적 실행 트리거가 설정됩니다 ✅

---

## 📦 확장 방법

이 스크립트는 확장이 쉽습니다.
새 피드 형식이나 다른 플랫폼을 지원하려면 다음과 같이 추가하면 됩니다.

### ▶ 새로운 피드 형식 추가

```js
FEED_FORMAT.MY_CUSTOM_FEED = {
  name: 'My Feed Type',
  parseFunction: parseMyFeed_,
};
```

### ▶ 새로운 플랫폼 추가

```js
WEBHOOK_PLATFORMS.SLACK = {
  name: 'Slack',
  viewFunction: buildSlackView_,
};
```

---

## 🧰 주요 함수 요약

| 함수명                        | 기능                         |
| -------------------------- | -------------------------- |
| `initializeScript()`       | 스크립트 초기화 및 트리거 생성          |
| `executeUpdateWorkflow()`  | 피드 확인 및 새로운 게시물 전송         |
| `fetchLatestUpdates_()`    | 피드 데이터를 가져옴                |
| `checkForNewUpdates_()`    | 새 게시물 감지 및 중복 방지           |
| `sendUpdatesToWebhooks_()` | Webhook으로 게시물 발송           |
| `buildGoogleChatViewV2_()` | Google Chat 카드 형식으로 메시지 생성 |

