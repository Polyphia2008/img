# MILESTONE 6 — Loading / Skeleton states (TrumAcc)

Ảnh xác nhận cho MILESTONE 6: thêm **skeleton / loading states** cho toàn bộ các trang chính,
theo đúng "từ vựng loading" của site tham chiếu **shopaccgame** (khối xám `animate-pulse`,
spinner, overlay, hover-shine), nhưng dịch sang hệ CSS custom properties `--c-skel-a` /
`--c-skel-b` của TrumAcc để **an toàn cho cả light và dark theme**.

- Repo code: https://github.com/Polyphia2008/trumacc (branch `main`)
- Code HEAD khi chụp bộ skeleton: **`250a9f0`** — bundle `assets/index-IsFgqNUX.js`
- Code HEAD khi chụp bộ overlay spinner: **`3faf9b6`** (sau `4cbaf76`) — bundle `assets/index-CTiqxvGY.js`
- Harness chụp ảnh: `tools/shotM6.mjs` (skeleton) + `tools/shotM6x.mjs` (overlay spinner) — Playwright / chromium
- Tổng: **41 PNG** — 33 skeleton (mục 4) + 8 overlay spinner (mục 4b), **6,520,641 bytes**

---

## 1. Cách chụp được trạng thái skeleton (differential per-endpoint delay)

Skeleton chỉ tồn tại vài chục đến vài trăm ms trên máy thật, nên không thể chụp bằng cách
chụp nhanh. Cách làm:

1. **Không** dùng cách stall đều toàn bộ `**/api/**`. Lý do đã đo được: `router/index.js:60`
   có `if (!auth.loaded) await auth.fetchProfile()`, nên nếu delay tất cả API thì **router guard
   cũng bị delay** — view chỉ mount ở khoảng **+9645 ms**, lúc đó mọi API khác đã resolve xong
   và các skeleton phụ thuộc `settings.loaded` **không bao giờ xuất hiện**.
2. Vì vậy harness dùng **differential delay**: cho các endpoint "cổng" (`/api/auth/profile`,
   `/api/graphql`) và một allowlist `fast` đi qua ngay, rồi **chỉ delay đúng endpoint mục tiêu**
   9000 ms. Nhờ đó view mount bình thường nhưng phần dữ liệu cần quan sát vẫn đang "loading".
3. Điều hướng bằng `waitUntil: 'commit'` rồi `waitForSelector('#<id>-skeleton')` — chụp đúng
   khoảnh khắc skeleton đang hiển thị, không đoán bằng `setTimeout`.
4. Với các shot Profile dùng thêm cờ `gateOff: true` để chủ động delay luôn `/api/graphql`
   (vì `pageLoading` của Profile do chính call graphql điều khiển).
5. Các tab dùng `v-show` (Wallet "Thẻ của tôi", "Sổ cái") có kích thước 0 khi ẩn nên không thể
   probe được — phải vào bằng deep link `?tab=mycards` / `?tab=ledger`.
6. Đăng nhập bằng cookie `trumacc-vue` mint sẵn (bypass captcha), có guard bắt buộc:
   `GET /api/auth/profile` phải trả **200**, nếu không thì abort — đảm bảo không có ảnh nào bị
   chụp ở trạng thái khách.
7. Dark theme set qua `addInitScript` → `localStorage['trumacc-theme'] = 'dark'`.

## 2. Kết quả probe DOM (không phải phỏng đoán)

Mỗi shot đều chạy `page.evaluate()` đo trực tiếp trên DOM:

```
skeleton ids observed : 18/18
not observed          : (none)
shots with overflowX  : 0
unstyled .skeleton    : 0
files written         : 33
```

- **18/18** ID skeleton được quan sát thực tế: `home-topnap-skeleton`, `home-banner-skeleton`,
  `home-products-skeleton`, `profile-skeleton`, `winners-panel-skeleton`, `winners-strip-skeleton`,
  `notification-skeleton`, `category-carousel-skeleton`, `wallet-topups-skeleton`,
  `wallet-cards-skeleton`, `wallet-ledger-skeleton`, `acc-detail-skeleton`,
  `product-detail-skeleton`, `spin-skeleton`, `spin-history-skeleton`, `shop-grid-skeleton`,
  `muaacc-grid-skeleton`, `orders-grid-skeleton`.
- **0 shot bị overflow ngang** (`scrollWidth == clientWidth` ở cả 1440 và 390).
- **0 phần tử `.skeleton` bị thiếu style** — tất cả đều có `animation-name: shimmer`.
- Các shot `-LOADED` đều đo được `skel=0`: skeleton biến mất hoàn toàn khi dữ liệu về.
- Dark: `dark=true`, `bodyBg=rgb(15, 20, 32)`, khối skeleton dùng token tối
  (`--c-skel-a:#1c2434` / `--c-skel-b:#263047`) — **không bị lóa trắng trên nền tối**.

## 3. Ghi chú: console error khi throttle (đã điều tra, KHÔNG phải bug thật)

Khi ép differential delay như trên, console có thể xuất hiện:

```
TypeError: Cannot destructure property 'slots' of 'undefined' as it is undefined.
TypeError: Cannot read properties of null (reading 'emitsOptions')
```

Đã điều tra dứt điểm bằng A/B và bisect từng endpoint:

- **Tải bình thường (không throttle): 0 error.** Stall đều toàn bộ API: **0 error.**
  Chỉ khi dùng differential delay nhân tạo mới xuất hiện.
- Bisect từng endpoint: **chỉ `/api/v2/settings` về muộn** mới gây ra; các endpoint khác
  (`recent-purchases`, `top-depositors`, `spin/winners`) đều 0 error.
- Bản chất: Vue patch một component instance **đã unmount** trong nhánh `v-if`/`v-else` phụ
  thuộc `settings`, đi qua factory icon của lucide.
- **A/B quyết định**: bundle **trước M6** (`bd7b99a`), chạy cùng điều kiện, throw **8 error**;
  bundle M6 hiện tại throw **3**. ⇒ Lỗi **đã có từ trước M6** và M6 làm **giảm** nó.
- Render cuối cùng luôn **đúng và đầy đủ** trong mọi trường hợp đo được:
  `footer:true, header:true, cards:25, icons:46, catItems:20, skelLeft:0`.

⇒ Kết luận: đây là **artifact của việc throttle khi test**, không ảnh hưởng người dùng thật,
không phải regression của M6.

---

## 4. Danh sách file

### Desktop 1440 — LOADING / skeleton (9)

| File | Size (B) | Nội dung |
|---|---:|---|
| `M6-d01-desktop-home-SKELETON.png` | 181,293 | Trang chủ: skeleton TOP nạp, banner, lưới sản phẩm, strip trúng thưởng, thông báo, carousel danh mục |
| `M6-d02-desktop-shop-SKELETON.png` | 154,480 | `/shop` — lưới sản phẩm đang load (`shop-grid-skeleton`) |
| `M6-d03-desktop-mua-acc-SKELETON.png` | 95,207 | `/mua-acc/:slug` — lưới acc đang load (`muaacc-grid-skeleton`) |
| `M6-d04-desktop-acc-detail-SKELETON.png` | 145,763 | `/acc/:id` — chi tiết acc đang load (`acc-detail-skeleton`) |
| `M6-d05-desktop-product-detail-SKELETON.png` | 198,136 | `/product/:id` — chi tiết sản phẩm đang load (`product-detail-skeleton`) |
| `M6-d06-desktop-wallet-SKELETON.png` | 206,232 | `/wallet` — bảng lịch sử nạp đang load (`wallet-topups-skeleton`) |
| `M6-d07-desktop-orders-SKELETON.png` | 170,163 | `/orders` — lịch sử mua acc đang load (`orders-grid-skeleton`) |
| `M6-d08-desktop-profile-SKELETON.png` | 156,351 | `/profile` — thông tin tài khoản đang load (`profile-skeleton`), menu trái vẫn là nội dung thật |
| `M6-d09-desktop-spin-SKELETON.png` | 138,144 | `/vong-quay` — vòng quay đang load (`spin-skeleton`) |

### Desktop 1440 — LOADING targeted, các skeleton khó chạm tới (4)

| File | Size (B) | Nội dung |
|---|---:|---|
| `M6-d14-desktop-home-banner-notif-SKELETON.png` | 185,894 | Chỉ delay `/api/v2/settings` ⇒ bắt được `home-banner-skeleton` + `notification-skeleton` (2 skeleton gate bởi `settings.loaded`) |
| `M6-d15-desktop-wallet-mycards-SKELETON.png` | 166,679 | `/wallet?tab=mycards` — `wallet-cards-skeleton` (tab `v-show`, phải vào bằng deep link) |
| `M6-d16-desktop-wallet-ledger-SKELETON.png` | 141,050 | `/wallet?tab=ledger` — `wallet-ledger-skeleton` (sổ cái biến động số dư) |
| `M6-d17-desktop-spin-panel-history-SKELETON.png` | 183,858 | `/vong-quay` — `winners-panel-skeleton` + `spin-history-skeleton` (chỉ render ở nhánh đã load) |

### Desktop 1440 — LOADED, để so sánh (4)

| File | Size (B) | Nội dung |
|---|---:|---|
| `M6-d10-desktop-home-LOADED.png` | 825,552 | Trang chủ sau khi load xong — `skel=0` |
| `M6-d11-desktop-wallet-LOADED.png` | 125,949 | Ví sau khi load xong — `skel=0` |
| `M6-d12-desktop-profile-LOADED.png` | 159,831 | Profile sau khi load xong — `skel=0` |
| `M6-d13-desktop-orders-LOADED.png` | 253,150 | Lịch sử mua acc sau khi load xong — `skel=0`, hiện dữ liệu thật (mã GD, giá, badge "Thành công") |

### Desktop 1440 — DARK skeleton (bằng chứng dark-safe) (4)

| File | Size (B) | Nội dung |
|---|---:|---|
| `M6-k01-desktop-home-SKELETON-DARK.png` | 190,775 | Trang chủ, dark, 102 khối skeleton — tất cả dùng token tối |
| `M6-k02-desktop-wallet-SKELETON-DARK.png` | 207,024 | Ví, dark |
| `M6-k03-desktop-profile-SKELETON-DARK.png` | 154,783 | Profile, dark |
| `M6-k04-desktop-orders-SKELETON-DARK.png` | 178,250 | Lịch sử mua acc, dark |

### Mobile 390 — LOADING / skeleton (9)

| File | Size (B) | Nội dung |
|---|---:|---|
| `M6-m01-mobile-home-SKELETON.png` | 46,050 | Trang chủ mobile, skeleton, không overflow ngang |
| `M6-m02-mobile-home-topnap-SKELETON.png` | 53,941 | Khu TOP nạp / TOP tiêu mobile đang load |
| `M6-m03-mobile-shop-SKELETON.png` | 46,706 | `/shop` mobile đang load |
| `M6-m04-mobile-mua-acc-SKELETON.png` | 49,502 | `/mua-acc/:slug` mobile đang load |
| `M6-m05-mobile-acc-detail-SKELETON.png` | 33,481 | Chi tiết acc mobile đang load |
| `M6-m06-mobile-wallet-SKELETON.png` | 63,653 | Ví mobile đang load |
| `M6-m07-mobile-orders-SKELETON.png` | 51,686 | Lịch sử mua acc mobile đang load |
| `M6-m08-mobile-profile-SKELETON.png` | 49,594 | Profile mobile đang load |
| `M6-m09-mobile-spin-SKELETON.png` | 36,364 | Vòng quay mobile đang load |

### Mobile 390 — LOADED (3)

| File | Size (B) | Nội dung |
|---|---:|---|
| `M6-m10-mobile-home-LOADED.png` | 201,448 | Trang chủ mobile load xong — `skel=0` |
| `M6-m11-mobile-wallet-LOADED.png` | 64,345 | Ví mobile load xong — `skel=0` |
| `M6-m12-mobile-profile-LOADED.png` | 49,564 | Profile mobile load xong — `skel=0` |

---

## 4b. BỔ SUNG — Overlay spinner giữa màn hình (8 PNG, bộ `M6-x*`)

Ngoài skeleton, MILESTONE 6 bổ sung **loader overlay có spinner ở giữa màn hình**, chép lại
đúng loader của site tham chiếu **shopaccgame**
(`resources/views/admin/layouts/master.blade.php`, khối `#page-overlay` + `.lds-double-ring`).

### 4b.1 Kiến trúc 2 overlay

Có **2 overlay** vì 2 tình huống load khác nhau:

| Overlay | Nơi khai báo | Khi nào hiện |
|---|---|---|
| `#boot-overlay` | tĩnh trong `frontend/index.html` | **Tải lại / mở trang lần đầu** — render trước khi Vue mount nên phủ được cả khoảng trắng đầu tiên. Bị xoá sau `router.isReady()`. |
| `#page-overlay` | `frontend/src/components/PageOverlay.vue` | **Đổi route trong SPA** — hook `router.beforeEach` / `afterEach` / `onError`. |

Chi tiết:

- `#boot-overlay` kèm một `<script>` inline chạy **trước Vue**, đọc
  `localStorage['trumacc-theme']` và gắn `html.dark` ngay lập tức ⇒ **không bị nháy sáng
  (theme flash)** khi user dùng dark mode; script cũng ghi `window.__bootAt` để tính thời gian
  hiển thị tối thiểu.
- `PageOverlay.vue`: `MIN_VISIBLE_MS = 420` (chống nhấp nháy khi route đổi quá nhanh),
  `MAX_VISIBLE_MS = 10000` (fail-safe, tự ẩn nếu route treo). Khi mount xong thì gỡ
  `#boot-overlay`.

### 4b.2 Spec vòng xoay (theo đúng shopaccgame)

`.lds-double-ring` gồm **4 phần tử con**: vòng 44×44 (`border: 4px`, 2 cạnh tô màu, quay
0→360°), vòng 32×32 quay **ngược** 0→−360°, và 2 vòng chỉ chứa **dot 4×4** dựng bằng
`box-shadow 0 40px` / `0 28px` (rotate 45°). Tốc độ `1s linear infinite`.

### 4b.3 Token màu — an toàn dark theme

Không dùng class `dark:` (Tailwind của dự án không bật `darkMode`), mà thêm 2 CSS custom
property vào `frontend/src/style.css`:

| Token | Light (`:root`) | Dark (`html.dark`) |
|---|---|---|
| `--c-overlay-bg` | `rgba(249, 249, 249, 0.8)` | `rgba(10, 14, 22, 0.82)` |
| `--c-ring` | `#ce0d0d` | `#fe696a` |

Có thêm khối `@media (prefers-reduced-motion: reduce)`: kéo animation về `3s` và bỏ
`transition` cho người dùng bật giảm chuyển động.

### 4b.4 Cách chụp (overlay chỉ tồn tại ~400 ms trên máy thật)

Harness `tools/shotM6x.mjs`:

- **Bộ reload (`M6-x1`…`M6-x4`)**: `page.route('**/api/**')` delay **6000 ms**, rồi
  `page.goto(..., { waitUntil: 'commit' })` → `waitForSelector('#boot-overlay .lds-double-ring')`
  → chờ 500 ms → probe → chụp. Vì API bị giữ, `#boot-overlay` còn nguyên trên màn hình.
- **Bộ đổi route (`M6-x5`…`M6-x8`)**: load `/` xong, chờ `#boot-overlay` bị xoá, **rồi mới**
  delay 6000 ms cho **cả `**/api/**` và `**/assets/*.js`** (chunk route lazy) → click
  `a[href="/shop"]` → `waitForSelector('#page-overlay .lds-double-ring')` → chỉ chờ **150 ms**
  → probe → chụp. Nếu **không** delay `/assets/*.js` thì trên localhost chunk về quá nhanh,
  overlay tắt sau `MIN_VISIBLE_MS` và probe trả `which=NONE`.
- Dark theme: `ctx.addInitScript(() => localStorage.setItem('trumacc-theme','dark'))`.
- Mỗi shot **bắt buộc** kiểm tra đăng nhập: `ctx.request.get('/api/auth/profile')` phải trả
  `200` (in dòng `AUTH[...] ok -> user01`), nếu không thì abort.
- Chụp bằng `clip` theo viewport (không dùng `fullPage`).

### 4b.5 Kết quả probe DOM (8/8, không phỏng đoán)

```
shots                    : 8
overlay present          : 8/8
ring 4 segments          : 8/8
ring centered (<=2px)    : 8/8
animating                : 8/8
shots with overflowX     : 0
dark shots dark=true     : 4
FAILURES                 : 0
```

Giá trị đo được ở từng shot: `size=96x96`, `z-index=9998`, `position=fixed`, `duration=1s`;
`animation=bootRing` (bộ reload) / `ldsDoubleRing` (bộ đổi route);
light `bg=rgba(249, 249, 249, 0.8)` + `border=rgb(206, 13, 13)`;
dark `bg=rgba(10, 14, 22, 0.82)` + `border=rgb(254, 105, 106)`;
tâm vòng xoay lệch tâm viewport **≤ 2 px** ở cả 8 shot.

### 4b.6 Danh sách file (8)

| File | Size (B) | Nội dung |
|---|---:|---|
| `M6-x1-desktop-reload-SPINNER-LIGHT.png` | 9,448 | Desktop 1440 — tải lại trang, `#boot-overlay`, light |
| `M6-x2-desktop-reload-SPINNER-DARK.png` | 9,846 | Desktop 1440 — tải lại trang, `#boot-overlay`, dark |
| `M6-x3-mobile-reload-SPINNER-LIGHT.png` | 5,554 | Mobile 390 — tải lại trang, `#boot-overlay`, light |
| `M6-x4-mobile-reload-SPINNER-DARK.png` | 5,992 | Mobile 390 — tải lại trang, `#boot-overlay`, dark |
| `M6-x5-desktop-route-SPINNER-LIGHT.png` | 637,995 | Desktop 1440 — đổi route `/` → `/shop`, `#page-overlay` mờ đè lên nội dung, light |
| `M6-x6-desktop-route-SPINNER-DARK.png` | 693,545 | Desktop 1440 — đổi route `/` → `/shop`, `#page-overlay`, dark |
| `M6-x7-mobile-route-SPINNER-LIGHT.png` | 99,664 | Mobile 390 — đổi route `/` → `/shop`, `#page-overlay`, light |
| `M6-x8-mobile-route-SPINNER-DARK.png` | 93,699 | Mobile 390 — đổi route `/` → `/shop`, `#page-overlay`, dark |

Tổng bộ `M6-x*`: **1,555,743 bytes**.

Commit code liên quan: **`4cbaf76`** (loader) + **`3faf9b6`** (harness chụp).

Ở 4 ảnh `reload` phía sau overlay là trang trắng/tối vì API còn bị giữ (chưa render nội dung).
Ở 4 ảnh `route` thấy rõ nội dung trang bị **làm mờ** phía sau lớp overlay bán trong suốt —
đúng hành vi mong muốn.

---

## 5. Lưu ý bản quyền hình ảnh (image rights)

Bản dựng preview/nháp này có dùng **CSS và asset hình ảnh lấy từ site tham chiếu**
(nickfcmobile / shopaccgame) để đối chiếu giao diện. Các hình ảnh sản phẩm, logo, banner
xuất hiện trong ảnh chụp **có thể thuộc bản quyền thương mại của bên thứ ba** và chỉ dùng
cho mục đích **so sánh giao diện nội bộ / nháp**. **Trước khi vận hành thương mại phải thay
thế toàn bộ bằng asset có giấy phép hợp lệ hoặc asset tự sản xuất.**

## 6. Tài khoản test dùng khi chụp

- `user01` (role 901) — dùng cho toàn bộ ảnh user-facing ở trên.
- `admin1` (role 998) — không dùng trong bộ M6 này.

Mật khẩu là mật khẩu demo dùng chung của môi trường preview, không phải credential thật.
