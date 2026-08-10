# MILESTONE 5 — Mobile parity + dark/light toggle (TrumAcc)

Ảnh chụp xác nhận Milestone 5: đồng bộ giao diện mobile theo tham chiếu **nickfcmobile.com**,
đưa **nút chuyển sáng/tối** ra mobile, đưa toàn bộ **vùng chạm ≥ 44×44 px**, và kiểm tra
**không tràn ngang** trên mọi trang.

- **Mobile**: viewport **390 × 844**, `isMobile: true`, `hasTouch: true`, DPR 2, User-Agent iPhone.
- **Desktop**: viewport **1440 × 1000**.
- Đăng nhập bằng tài khoản demo **user01** (cookie `trumacc-vue`), có xác thực bắt buộc
  qua `GET /api/auth/profile` trước khi chụp.
- Ảnh sinh tự động bằng `tools/shotM5.mjs` (Playwright / Chromium).

Tổng cộng: **24 ảnh PNG**, **3.577.319 byte (≈ 3,41 MB)**.

---

## 1. Mobile 390 — giao diện sáng (light)

| # | Tệp | Kích thước | Nội dung |
|---|-----|-----------:|----------|
| 1 | `M5-m01-mobile-home-top.png` | 202.508 B | Trang chủ, phần đầu. Thấy rõ **nút chuyển sáng/tối 44×44** cạnh nút menu trên header, khung thông báo đỏ **hiển thị đúng chữ tiếng Việt** (không lộ thẻ HTML thô), thanh **bottom nav 5 tab / 64 px**. |
| 2 | `M5-m02-mobile-home-topnap.png` | 125.431 B | Trang chủ cuộn xuống bảng **TOP NẠP** — tab chuyển đã đạt chiều cao chạm 44 px. |
| 3 | `M5-m03-mobile-shop.png` | 52.661 B | Trang **CỬA HÀNG ACC** trên mobile — lưới sản phẩm, không tràn ngang. |
| 4 | `M5-m04-mobile-mua-acc.png` | 66.269 B | Trang **Mua acc** (danh mục Liên Quân) — bộ lọc và chip đã đủ vùng chạm. |
| 5 | `M5-m05-mobile-mua-acc-cards.png` | 49.349 B | Trang Mua acc cuộn xuống phần thẻ acc. |
| 6 | `M5-m06-mobile-acc-detail.png` | 160.776 B | Trang **chi tiết acc** trên mobile. |
| 7 | `M5-m07-mobile-wallet.png` | 63.882 B | Trang **NẠP TIỀN & VÍ** — các ô chọn mệnh giá hiển thị đúng, nhãn chạm 154×44. |
| 8 | `M5-m08-mobile-orders.png` | 95.256 B | Trang **Lịch Sử Mua Acc** — 3 thẻ thống kê + lưới đơn hàng theo layout M4. |
| 9 | `M5-m09-mobile-profile.png` | 49.717 B | Trang **Thông Tin Tài Khoản**. |
| 10 | `M5-m10-mobile-contact.png` | 45.324 B | Trang **LIÊN HỆ HỖ TRỢ** — các liên kết liên hệ đã đạt 44 px. |
| 11 | `M5-m11-mobile-footer.png` | 42.709 B | **Chân trang** trên mobile — liên kết danh sách, chip danh mục và nút mạng xã hội đều 44 px. |
| 12 | `M5-m12-mobile-orders-phantrang.png` | 311.147 B | **Bằng chứng nút phân trang**: cuộn tới bộ phân trang trang Lịch Sử Mua Acc. Đo được `.pg-btn` = **44×44** (cả 4 nút: `‹`, `1`, `2`, `›`), nút trang hiện tại nền đỏ chữ trắng, **không tràn ngang**. |

## 2. Mobile 390 — chuỗi chuyển sáng/tối (dark/light toggle)

Chuỗi thao tác được thực hiện liên tục trong cùng một phiên trình duyệt, chứng minh
nút chuyển hoạt động **và trạng thái được lưu lại**:

| # | Tệp | Kích thước | Nội dung |
|---|-----|-----------:|----------|
| 13 | `M5-t01-mobile-drawer-LIGHT.png` | 95.161 B | Mở **drawer menu** ở chế độ **sáng**. Hàng chuyển giao diện đo được **250×44**, biểu tượng **mặt trăng** + chữ *"Chế độ tối"*, **công tắc nằm bên TRÁI (tắt)**. Nền `rgb(243,245,249)`. |
| 14 | `M5-t02-mobile-drawer-DARK.png` | 96.077 B | Sau khi bấm hàng chuyển: **drawer chuyển sang tối ngay lập tức**. Biểu tượng **mặt trời** + chữ *"Chế độ sáng"*, **công tắc trượt sang PHẢI, màu cam (bật)**. Nền `rgb(15,20,32)`. |
| 15 | `M5-t03-mobile-home-DARK.png` | 150.883 B | Đóng drawer → **trang chủ ở chế độ tối**. `localStorage['trumacc-theme'] = dark`. Chấm phân trang carousel hiển thị đúng dạng viên thuốc (1 chấm dài màu san hô + các chấm tròn nhỏ). |
| 16 | `M5-t04-mobile-wallet-DARK.png` | 65.742 B | Trang **Ví** ở chế độ tối — nền tối, chữ sáng, không có mảng trắng vỡ giao diện. |
| 17 | `M5-t05-mobile-orders-DARK.png` | 95.666 B | Trang **Lịch Sử Mua Acc** ở chế độ tối. |
| 18 | `M5-t06-mobile-mua-acc-DARK.png` | 66.816 B | Trang **Mua acc** ở chế độ tối. |
| 19 | `M5-t07-mobile-profile-DARK.png` | 49.240 B | Trang **Tài khoản** ở chế độ tối. |
| 20 | `M5-t08-mobile-home-back-to-LIGHT.png` | 202.490 B | Bấm **nút chuyển trên header** → quay lại **chế độ sáng**. `localStorage['trumacc-theme'] = light`. Chứng minh **cả hai điểm chuyển** (header + drawer) đều hoạt động và **trạng thái được ghi nhớ**. |

**Chuỗi chuyển giao diện đã kiểm chứng:**
`sáng → (bấm trong drawer) → tối → điều hướng 4 trang vẫn giữ tối → (bấm trên header) → sáng`,
với `localStorage['trumacc-theme']` được đọc và in ra ở mỗi bước (`dark`, rồi `light`).
Chế độ tối dùng lớp `html.dark` cùng bộ biến CSS `--c-*`, nên áp dụng cho toàn bộ trang.

## 3. Desktop 1440 — đối chiếu (không đổi)

| # | Tệp | Kích thước | Nội dung |
|---|-----|-----------:|----------|
| 21 | `M5-d01-desktop-home.png` | 826.183 B | Trang chủ desktop — xác nhận thay đổi mobile **không ảnh hưởng** bố cục desktop. |
| 22 | `M5-d02-desktop-mua-acc.png` | 140.303 B | Trang Mua acc desktop. |
| 23 | `M5-d03-desktop-orders.png` | 398.241 B | Trang Lịch Sử Mua Acc desktop (layout M4 đã duyệt). |
| 24 | `M5-d04-desktop-wallet.png` | 125.488 B | Trang Ví desktop. |

---

## 4. Kết quả đo đạc (DOM probe)

Đo bằng `tools/auditMobile.mjs` trên 8 trang ở 390 px:

| Trang | Tràn ngang | Vùng chạm < 44px (trước) | Vùng chạm < 44px (sau) | Bottom nav | Nút chuyển giao diện |
|-------|-----------|------------------------:|----------------------:|-----------|---------------------|
| Trang chủ | không | 53 | **0** | 5 tab / 64 px | có |
| Cửa hàng | không | 39 | **0** | 5 tab / 64 px | có |
| Mua acc | không | 30 | **0** | 5 tab / 64 px | có |
| Ví | không | 37 | **0**\* | 5 tab / 64 px | có |
| Lịch sử | không | 28 | **0** | 5 tab / 64 px | có |
| Tài khoản | không | 45 | **0** | 5 tab / 64 px | có |
| Vòng quay | không | 31 | **0** | 5 tab / 64 px | có |
| Liên hệ | không | 32 | **0** | 5 tab / 64 px | có |

\* Trang Ví còn một số ô `input[type=radio]` kích thước 1×1 nhưng đó là **phần tử ẩn phục vụ
trợ năng** (`peer sr-only`); phần người dùng thực sự chạm là nhãn `.amt-opt` đo được **154×44**.
Tương tự với các thẻ `<select>` gốc bị ẩn sau nút chọn tuỳ biến.

**Nút phân trang** (`.pg-btn`, xuất hiện khi có nhiều hơn 12 đơn hàng):
- Mobile 390 px: **44×44**, không tràn ngang (`scrollWidth 390 = clientWidth 390`).
- Desktop 1440 px: giữ nguyên **36×36** như thiết kế cũ.

Trên desktop, số vùng chạm nhỏ hơn 44 px là **bình thường và có chủ ý** — lớp 44 px chỉ áp
dụng trong `@media (max-width: 767px)` vì desktop dùng chuột.

## 5. Hai lỗi hiển thị đã sửa trong Milestone này

1. **Khung thông báo in ra thẻ HTML thô.** `NotificationSection.vue` trước đây in nội dung
   quản trị bằng `{{ }}` nên các thẻ `<p>`, `<strong>` hiện nguyên văn. Đã chuyển sang
   `v-html` có điều kiện (nội dung vẫn được backend làm sạch bằng `App\Utils\Html::sanitize()`),
   giữ nhánh văn bản thuần cho nội dung không phải HTML. Xem `M5-m01`.
2. **Chấm phân trang carousel bị phình thành khối chữ nhật.** Do quy tắc `min-height: 44px`
   áp trực tiếp lên `.splide__pagination__page`. Đã thay bằng vùng chạm trong suốt
   `::after` 44×44, trả chấm về đúng kích thước 24×8 / 8×8. Xem `M5-t03`.

## 6. Ghi chú

Ảnh nền, ảnh sản phẩm và biểu tượng trong các ảnh chụp lấy từ bộ tài nguyên tham chiếu,
chỉ dùng cho **bản dựng xem thử**. Cần thay bằng tài nguyên có bản quyền hợp lệ trước
khi phát hành thương mại.
