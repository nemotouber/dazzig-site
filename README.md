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
2. 시행일 — 현재 2026년 9월 1일로 적혀 있음. 실제 출시일에 맞춰 조정
3. 출시 후 `index.html`의 App Store 버튼 — `aria-disabled` 지우고 `href`를 실제 링크로 교체

## 배포

도메인은 **예스닉(yesnic)** 등록, 네임서버는 `DNS1~4.YESNIC.COM`입니다.
현재 A 레코드는 파킹 페이지를 가리키고 MX 레코드는 없어, 사이트도 메일도 아직 동작하지 않습니다.

### 1단계 — 사이트 (GitHub Pages)

**실제 서빙 리포는 별도의 공개 리포 `nemotouber/dazzig-site`(main 루트)입니다.** 이 폴더(`site/`)가 원본이고,
`npm run deploy:site`(`scripts/deploy-site.sh`)가 그 리포에 복사·커밋·푸시합니다. `--dry-run`을 붙이면 바뀔 파일만 봅니다.
이 리포의 `main`에 커밋하는 것만으로는 dazzig.com이 바뀌지 않습니다 (2026-09-09 확인).
`CNAME` 파일이 있으므로 커스텀 도메인은 자동으로 잡힙니다.

예스닉 DNS 관리에서 파킹 A 레코드를 지우고 아래를 등록:

```
A     @    185.199.108.153
A     @    185.199.109.153
A     @    185.199.110.153
A     @    185.199.111.153
CNAME www  <계정명>.github.io
```

### 2단계 — 메일

문의처는 `ceo@nem0.kr`입니다. nem0.kr 도메인은 네이버웍스(worksmobile) MX가 이미 연결되어 있어 별도 설정이 필요 없습니다.
`dazzig.com`에는 MX 레코드가 없으므로 `@dazzig.com` 주소를 문의처로 적지 마세요.

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
