# dazzig.com 정적 사이트

App Store Connect 필수 항목(개인정보처리방침 URL·지원 URL)과 앱 소개 페이지.
빌드 도구 없이 파일 그대로 올리면 되는 정적 사이트입니다.

| 파일 | 용도 | App Store Connect 입력란 |
|---|---|---|
| `index.html` | 앱 소개 | 마케팅 URL (선택) |
| `privacy.html` | 개인정보처리방침 | **개인정보 처리방침 URL (필수)** |
| `support.html` | 문의·FAQ | **지원 URL (필수)** |
| `CNAME` | GitHub Pages 커스텀 도메인 | — |
| `robots.txt` · `sitemap.xml` | 검색엔진 크롤링 안내 (SEO) | — |
| `og.jpg` | 링크 공유 미리보기 이미지 1200×630 | — |
| `site.webmanifest` · `404.html` | 브라우저 메타 · 404 페이지 | — |

## SEO

- 세 페이지 모두 `canonical`·Open Graph·Twitter 카드·JSON-LD(`SoftwareApplication`/`Organization`/`WebSite`, 지원 페이지는 `FAQPage`, 문서 페이지는 `BreadcrumbList`)가 들어 있습니다.
- 사이트 배포 후 **Google Search Console**과 **네이버 서치어드바이저**에 `dazzig.com`을 등록하고 `sitemap.xml`을 제출하세요. 발급받는 인증 메타태그는 `index.html` 상단 주석을 풀어 채웁니다.
- `og.jpg`는 `site/og.svg` 같은 소스 없이 macOS `qlmanage`로 렌더한 결과물입니다. 문구를 바꾸려면 새로 만들어 1200×630 JPEG로 교체하면 됩니다.
- `sitemap.xml`의 `lastmod`는 페이지를 고칠 때 함께 갱신하세요.

## 올리기 전에 채워야 할 것

1. `privacy.html` 10항의 `[이름을 입력하세요]` — 개인정보 보호책임자 이름
2. `help@dazzig.com` 메일 수신 설정 (Cloudflare Email Routing 무료 포워딩 권장)
3. 시행일 — 현재 2026년 9월 1일로 적혀 있음. 실제 출시일에 맞춰 조정
4. 출시 후 `index.html`의 App Store 버튼 — `aria-disabled` 지우고 `href`를 실제 링크로 교체

## 배포

도메인은 **예스닉(yesnic)** 등록, 네임서버는 `DNS1~4.YESNIC.COM`입니다.
현재 A 레코드는 파킹 페이지를 가리키고 MX 레코드는 없어, 사이트도 메일도 아직 동작하지 않습니다.

### 1단계 — 사이트 (GitHub Pages)

GitHub 레포 Settings → Pages → Source를 `main` 브랜치 `/site` 폴더로 지정.
`CNAME` 파일이 있으므로 커스텀 도메인은 자동으로 잡힙니다.

예스닉 DNS 관리에서 파킹 A 레코드를 지우고 아래를 등록:

```
A     @    185.199.108.153
A     @    185.199.109.153
A     @    185.199.110.153
A     @    185.199.111.153
CNAME www  <계정명>.github.io
```

### 2단계 — 메일 (help@dazzig.com)

**네이버웍스 Standard (권장, 1인/월 7,000원 연간 계약)**
도메인 메일은 Standard 플랜부터입니다. Free·Lite에는 메일이 없습니다.
네임서버를 옮길 필요 없이, 가입 후 안내받는 MX 레코드를 예스닉 DNS에 추가하면 됩니다.
수신과 발신이 모두 `help@dazzig.com`으로 동작합니다.

**Cloudflare Email Routing (무료)**
네임서버를 Cloudflare로 이전해야 하며, **수신 전용**입니다.
답장은 포워딩 받는 주소로 발신되므로 개인 메일 주소가 노출됩니다.
쓰려면 앱 전용 메일 계정을 따로 만들어 연결하세요.

### Vercel을 쓰는 경우

```
npx vercel --prod site
```

배포 후 Settings → Domains에서 `dazzig.com` 연결. DNS는 Vercel 안내에 따릅니다.

### 로컬 확인

```
npx serve site
```

## 내용을 고칠 때

`privacy.html`은 앱의 실제 동작에 맞춰 작성했습니다. 아래가 바뀌면 방침도 함께 고쳐야 합니다.

- AI 처리 업체 변경 (현재 OpenAI — `backend/supabase/functions/generate-note/index.ts`가 `ANTHROPIC_API_KEY`로도 동작하므로, 전환 시 5항의 수탁업체와 국외 이전 항목 수정)
- 촬영본 서버 보관 정책 변경 (현재는 노트 생성 후 즉시 삭제 — `app/src/lib/pipeline.ts`의 `keepCloud`)
- 광고·분석 SDK 도입 (현재 없음을 명시하고 있음)
- 결제 도입 (1.0은 무료 전용)

## 주의

페이지 사이 링크가 `/privacy.html`처럼 루트 기준입니다. `dazzig.com` 최상위에 올릴 때는 문제없지만,
`계정명.github.io/레포명/` 같은 하위 경로에 배포하면 링크가 깨집니다. 그 경우 상대 경로로 바꿔주세요.
