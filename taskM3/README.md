# MILESTONE 3 — /wallet (Nạp tiền) trên mobile 390×844

Tất cả ảnh chụp bằng Playwright, viewport **390×844** (iPhone 12/13/14),
`isMobile:true, hasTouch:true`, đăng nhập bằng tài khoản demo `user01`.
Bộ **before** chụp trên bundle CŨ, bộ **after** chụp trên bundle MỚI
(entry `assets/index-CqNFWzr5.js`, chunk `assets/Wallet-jgP1egF6.js`).

## Kết quả đo (harness `tools/shotM3.mjs`, tự kiểm tra rồi exit code)

| Chỉ số đo | BEFORE | AFTER |
|---|---|---|
| Tràn ngang tab "Thẻ cào" (`overflowX`) | **188 px** | **0 px** |
| Số phần tử tràn khỏi viewport | **89** | **0** |
| Tap target < 44px (tab Ngân hàng) | 8 | **0** |
| Tap target < 44px (tab Thẻ cào) | 18 | **0** |
| Hàng dữ liệu ngân hàng bị RỖNG | 2 / mỗi card | **0** |
| Ảnh QR bị hỏng | **2 / 2 card** | **0 / 2 card** |
| **Tổng số lỗi harness báo** | **8** | **0** (exit 0) |

## Ba lỗi đã sửa

### 1. Tràn ngang 188 px (tab "Thẻ cào / Ví")
Wrapper là `grid` 1 cột trên mobile, track được size theo *min-content*.
Bên trong có `<table class="tbl min-w-[560px]">`. Grid item mặc định
`min-width: auto` nên không chịu co nhỏ hơn min-content ⇒ track phình ra
**562 px** trong viewport 390 px.
**Sửa:** thêm `min-w-0` cho grid + mọi wrapper trung gian, bọc bảng trong
`overflow-x-auto` (kèm gợi ý "Kéo ngang để xem đủ cột"), root thêm
`overflow-x-hidden`, section thêm `pb-safe-mobile` để không bị thanh nav
dưới che, và tab bar chuyển thành grid 2 cột trên mobile.

### 2. Card ngân hàng rỗng + QR hỏng
Cột JSON `settings.bank_accounts` lưu shape `{bank, number, holder}` nhưng
component đọc `b.account` / `b.content` / `b.bin` ⇒ hai hàng "Số TK" và
"Nội dung" **rỗng**, URL QR suy biến thành
`img.vietqr.io/image/--compact.png?...` ⇒ **ảnh hỏng trên mọi card**.
**Sửa:** chuẩn hoá một shape duy nhất, nhận CẢ `number` (seed/DB) và
`account` (form admin); map tên bank → slug VietQR (bỏ khoảng trắng, vì
`mb%20bank` trả về ảnh lỗi 13 byte); dùng đúng định dạng của site tham chiếu:
`api.vietqr.io/{slug}/{số TK}/{số tiền}/{nội dung}/qronly2.jpg?accountName=`
(MoMo → `api.qrserver.com`), luôn có QR fallback.
Layout tách "Số tài khoản" / "Nội dung chuyển khoản" thành khối riêng chữ to
`break-all` + nút copy 44×44, thêm ô nhập số tiền để QR tự điền sẵn mệnh giá.

### 3. Mã nạp riêng từng user + tap target 44 px (WCAG 2.5.5)
Nội dung chuyển khoản giờ là `NAP{UID}` (ví dụ `NAPX5DM2GT47UW8`) — in ra ô
"Nội dung chuyển khoản" và nhúng luôn vào mã QR.
Các target đã nâng lên ≥ 44 px: `.input`, `.btn-md`, `.btn-lg`, `.pay-tab`,
`.amt-opt`, `.icon-btn-44`, `.copy-btn`, `.download-btn`.
Dark mode: thêm biến `--c-amber/red/blue/green` để các khối cảnh báo không
còn bị "trắng trên trắng" (dự án không dùng được biến thể `dark:`).

## Danh sách ảnh (before ↔ after theo cặp)

| # | Nội dung | before | after |
|---|---|---|---|
| w1 | Tab Ngân hàng — đầu trang (tabs + tiêu đề + card đầu) | `M3-before-w1-bank-dau-trang.png` | `M3-after-w1-bank-dau-trang.png` |
| w2 | Tab Ngân hàng — card: số TK / nội dung / QR | `M3-before-w2-bank-card-QR.png` | `M3-after-w2-bank-card-QR.png` |
| w3 | Tab Ngân hàng — card thứ 2 | `M3-before-w3-bank-card-2.png` | `M3-after-w3-bank-card-2.png` |
| w4 | Tab Thẻ cào — form nạp + lưới mệnh giá | `M3-before-w4-the-form-tren.png` | `M3-after-w4-the-form-tren.png` |
| w5 | Tab Thẻ cào — số tiền khác + mã GD + nút xác nhận | `M3-before-w5-the-form-duoi.png` | `M3-after-w5-the-form-duoi.png` |
| w6 | Tab Thẻ cào — bảng Lịch Sử Nạp Tiền | `M3-before-w6-the-lich-su.png` | `M3-after-w6-the-lich-su.png` |
| w7 | Tab Thẻ cào — thẻ số dư + khối Lưu ý | `M3-before-w7-the-so-du-luu-y.png` | `M3-after-w7-the-so-du-luu-y.png` |
| w8 | **DARK** — tab Thẻ cào: form nạp tiền | `M3-before-w8-dark-the-form.png` | `M3-after-w8-dark-the-form.png` |
| w9 | **DARK** — tab Ngân hàng: card + QR | `M3-before-w9-dark-bank-card.png` | `M3-after-w9-dark-bank-card.png` |

## Miễn trừ a11y (có điều kiện, không làm yếu test)
- `input.sr-only` (radio ẩn cho screen-reader): vùng bấm thật là `<label for>` đi kèm — **label đó vẫn bị đo**.
- `[aria-hidden="true"]` (`<select>` ẩn mà radix-vue render kèm): người dùng bấm vào `SelectTrigger` — **trigger vẫn bị đo**.
- `<label for>` dạng chữ đặt phía trên input: **chỉ miễn trừ khi control liên kết đã đạt ≥ 44 px**; nếu control còn nhỏ thì lỗi vẫn bị báo qua chính control.
- Phần tử bị ancestor `overflow: hidden/auto/scroll` cắt (bảng trong vùng cuộn ngang có chủ đích): không phải lỗi tràn trang — thước đo thật là `overflowX` cấp document, đã = 0.

> Lưu ý bản quyền: CSS/asset tham chiếu chỉ dùng cho bản nháp/preview nội bộ.
