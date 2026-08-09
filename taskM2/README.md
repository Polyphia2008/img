# MILESTONE 2 — Bỏ ô mua số lượng + bỏ header lớn, thêm KHỐI THÔNG BÁO gấp/mở

15 ảnh, chụp bằng `node tools/shotM2.mjs` (Playwright, Chromium).
Bundle đang chạy: `assets/index-DIZP4WX1.js` → `assets/MuaAcc-Dd6kDwH3.js`.

## PART A — đã XOÁ HẲN (đo bằng DOM, không chỉ nhìn mắt)
Trên **cả hai** loại trang mua acc, 7 khối sau đều `false` trong DOM:
`#ma-random-box`, `#ma-qty`, `#ma-total`, `#ma-buy-random`, `#ma-bulk-pill`,
`#ma-stock`, `#ma-item-head`.
Thay bằng **`#ma-title-line`** — 1 dòng gọn: tên sản phẩm + giá (+ nhãn
"Ngẫu nhiên" nếu là túi mù). Chỉ còn **đúng 1** `<h1>` trên trang.
Mỗi nút "Mua Ngay" trên card mua **1 acc ngẫu nhiên** (`quantity=1`, KHÔNG gửi
`account_id`) — đã chứng minh bằng `tools/probeBuy.mjs` (33/33).

## PART B — khối `.notification-section`
Đúng markup site tham chiếu: `.notification-section` >
`.notification-content.expanded#notificationContent` + `.notification-footer` >
`button.toggle-notification` > `span.toggle-text`.
- **MẶC ĐỊNH MỞ** (`.expanded`, `max-height: 1000px`, nút ghi **"Thu lại"**).
- Bấm ⇒ **thu lại** (`max-height: 100px`, nút ghi **"Xem thêm"**).
- Nội dung do admin cấu hình, lưu trong DB, soạn bằng CKEditor 5, render đã
  qua `Html::sanitize()` (72/72 test). Ảnh trang tự chọn chứng minh **fallback
  item → nhóm**: item đó `notice_html = NULL` nên lấy nội dung của **nhóm**.
- Đặt đúng chỗ header lớn cũ, trên cả 2 trang, dark-safe bằng token `--c-*`.

## Danh sách ảnh
| Ảnh | Nội dung |
|---|---|
| `M2-r1-random-desktop-toan-canh.png` | RANDOM desktop 1440×900 — không ô số lượng, không header lớn: title-line + thông báo (MỞ) + lưới card |
| `M2-r2-random-desktop-notice-MO.png` | Cận cảnh khối thông báo **MỞ** (nút "Thu lại") |
| `M2-r3-random-desktop-notice-THU.png` | Cận cảnh khối thông báo **THU** (cắt 100px, nút "Xem thêm") |
| `M2-r4-random-desktop-thu-toan-canh.png` | RANDOM desktop khi đã thu — lưới card lên cao hơn |
| `M2-r5-random-mobile-notice-MO.png` | RANDOM mobile 390×844 — thông báo MỞ |
| `M2-r6-random-mobile-notice-THU.png` | RANDOM mobile 390×844 — thông báo THU |
| `M2-r7-random-mobile-luoi-card.png` | RANDOM mobile — cuộn xuống lưới card 2 cột |
| `M2-s1-tuchon-desktop-toan-canh.png` | TỰ CHỌN desktop — không header lớn: title-line + thông báo (MỞ) + bộ lọc + lưới card |
| `M2-s2-tuchon-desktop-notice-MO.png` | Cận cảnh thông báo MỞ (nội dung **lấy từ NHÓM** vì item = NULL) |
| `M2-s3-tuchon-desktop-notice-THU.png` | Cận cảnh thông báo THU |
| `M2-s4-tuchon-desktop-thu-toan-canh.png` | TỰ CHỌN desktop khi đã thu — bộ lọc lên cao hơn |
| `M2-s5-tuchon-mobile-notice-MO.png` | TỰ CHỌN mobile 390×844 — thông báo MỞ |
| `M2-s6-tuchon-mobile-notice-THU.png` | TỰ CHỌN mobile 390×844 — thông báo THU |
| `M2-a1-admin-form-item-ckeditor.png` | ADMIN form item #4 — ô CKEditor "Khối thông báo của item (HTML)" + nút "Chèn mẫu mặc định" / "Xoá nội dung" |
| `M2-a2-admin-ckeditor-can-canh.png` | Cận cảnh ô CKEditor: thanh công cụ (10 nút) + nội dung `notice_html` thật |

## Bộ test tại thời điểm chụp — 573/573 PASS
`test_html_sanitize.php` 72/72 · `e2e_test.php` 222/222 · `imgaudit.mjs` 91/91 ·
`smoke9.py` 46/46 · `probeB.mjs` 89/89 · `probeBuy.mjs` 33/33.

> Lưu ý bản quyền: ảnh/CSS tham chiếu chỉ dùng cho bản nháp/preview.
