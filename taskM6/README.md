# MILESTONE 6 — Loading / Skeleton states (TrumAcc)

Ảnh xác nhận cho MILESTONE 6: thêm **skeleton / loading states** cho toàn bộ các trang chính,
theo đúng "từ vựng loading" của site tham chiếu **shopaccgame** (khối xám `animate-pulse`,
spinner, overlay, hover-shine), nhưng dịch sang hệ CSS custom properties `--c-skel-a` /
`--c-skel-b` của TrumAcc để **an toàn cho cả light và dark theme**.

- Repo code: https://github.com/Polyphia2008/trumacc (branch `main`)
- Code HEAD khi chụp: **`250a9f0`**
- Bundle đang chạy khi chụp: `assets/index-IsFgqNUX.js`
- Harness chụp ảnh: `tools/shotM6.mjs` (Playwright / chromium)
- Tổng: **33 PNG**, **4,964,898 bytes**

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
