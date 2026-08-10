# MILESTONE 4 — Nạp tiền (bank + thẻ cào), Sổ cái ví, Vòng quay may mắn, Admin duyệt thẻ/nạp

Ảnh xác nhận cho MILESTONE 4 của TrumAcc.
Code tương ứng: repo `Polyphia2008/trumacc`, branch `main`, HEAD **`122f13b`**.

Chụp bằng `tools/shotM4.mjs` (Playwright + Chromium). Dữ liệu demo được tạo
**100% qua API thật** (`tools/seedM4demo.sh`) — không có INSERT tay vào DB, nên
mọi con số trong ảnh đều đi qua đúng luồng nghiệp vụ (validate, rate-limit,
transaction, sổ cái).

- Desktop: **1440 × 1000**
- Mobile: **390 × 844** (iPhone 12/13)
- Dark mode: bật bằng `html.dark` + biến CSS `--c-*` (project **không** dùng biến thể `dark:` của Tailwind)
- Tài khoản: `user01 / User@2026` (role 901), `admin1 / Admin@2026` (role 998)

## Trạng thái dữ liệu khi chụp (đã kiểm chứng bằng SQL + đọc ảnh)

- **Sổ cái ví trang 1 có đủ 5 loại**: `deposit-bank`, `deposit-card`, `purchase`,
  `game-spin`, `game-prize` — kèm tiền vào (xanh, dấu `+`) và tiền ra (đỏ, dấu `-`)
- **Thẻ cào có đủ 4 trạng thái**: `Hoàn tất` (3), `Chờ xử lý` (2), `Đã huỷ` (1), `Lỗi` (1)
- **Yêu cầu nạp bank**: 2 `Chờ duyệt` + 14 `Đã duyệt`
- **Sổ cái khớp tuyệt đối**: `users.account_balance` == `balance_after` của giao dịch cuối cùng

## 1) Ví người dùng — desktop 1440px (11 ảnh)

| File | Bytes | Nội dung |
|---|---:|---|
| `M4-w1-desktop-tab-nap-tien.png` | 119.185 | Tab **Nạp tiền** (chuyển khoản): số dư, mã nạp `NAP<UID>` |
| `M4-w2-desktop-bank-QR-ma-nap.png` | 178.005 | Cuộn xuống: QR VietQR + danh sách tài khoản bank |
| `M4-w3-desktop-tab-nap-the-cao.png` | 204.762 | Tab **Nạp thẻ cào**: nhà mạng, mệnh giá, serial + mã thẻ |
| `M4-w4-desktop-the-cao-phi-truc-tiep.png` | 207.803 | Sau khi điền thẻ: phí + số tiền thực nhận tính **live** |
| `M4-w5-desktop-the-cao-bang-phi-luu-y.png` | 167.757 | Bảng phí theo nhà mạng + khối lưu ý |
| `M4-w6-desktop-tab-the-cua-toi.png` | 127.050 | Tab **Thẻ của tôi**: đủ 4 badge, serial được che (`SERZ••••722`) |
| `M4-w7-desktop-tab-so-cai.png` | 161.316 | Tab **Sổ cái**: đủ 5 loại giao dịch, xanh `+` / đỏ `-` |
| `M4-w8-desktop-so-cai-cuon-xuong.png` | 175.552 | Sổ cái cuộn xuống (thấy thêm các dòng phía dưới) |
| `M4-w9-desktop-so-cai-DARK.png` | 169.185 | Sổ cái **DARK MODE** — nền tối thật, chữ/badge/màu tiền vẫn rõ |
| `M4-w10-desktop-the-cao-DARK.png` | 207.789 | Form nạp thẻ cào **DARK MODE** |
| `M4-w11-desktop-lich-su-mua-acc.png` | 214.844 | Lịch sử mua acc (đơn hàng) |

## 2) Ví người dùng — mobile 390px (6 ảnh)

| File | Bytes | Nội dung |
|---|---:|---|
| `M4-m1-mobile-tab-nap-tien.png` | 63.592 | Tab nạp tiền mobile, tab xếp 2×2, tap target ≥ 44px |
| `M4-m2-mobile-bank-QR.png` | 64.590 | QR + thông tin bank trên mobile |
| `M4-m3-mobile-the-cao-form.png` | 49.777 | Form nạp thẻ cào mobile |
| `M4-m4-mobile-the-cao-phi.png` | 59.504 | Bảng phí nạp thẻ mobile |
| `M4-m5-mobile-the-cua-toi.png` | 50.638 | Bảng thẻ của tôi + gợi ý `↔ Kéo ngang để xem đủ cột` |
| `M4-m6-mobile-so-cai.png` | 61.268 | Sổ cái mobile, không tràn ngang |

## 3) Vòng quay may mắn + feed người trúng (7 ảnh)

| File | Bytes | Nội dung |
|---|---:|---|
| `M4-s1-desktop-vong-quay.png` | 183.232 | Vòng quay 8 ô, nút `QUAY NGAY — 10.000đ`, thống kê, feed bên phải |
| `M4-s2-desktop-vong-quay-co-cau-giai.png` | 123.130 | Bảng cơ cấu giải (tỉ lệ % từng ô) |
| `M4-s3-desktop-feed-nguoi-trung.png` | 22.584 | Feed **Trúng thưởng gần đây** (chụp riêng element, username che `use***1`) |
| `M4-s4-mobile-vong-quay.png` | 89.465 | Vòng quay trên mobile |
| `M4-s5-mobile-vong-quay-giai.png` | 52.588 | Cơ cấu giải trên mobile |
| `M4-s6-mobile-feed-nguoi-trung.png` | 55.593 | Feed người trúng trên mobile |
| `M4-s7-desktop-home-dai-nguoi-trung.png` | 825.979 | Dải chip người trúng trên trang chủ |

## 4) Admin — duyệt thẻ cào & yêu cầu nạp (6 ảnh)

| File | Bytes | Nội dung |
|---|---:|---|
| `M4-a1-desktop-admin-the-thong-ke.png` | 131.077 | `/admin/cards`: 4 thẻ thống kê (tổng / tháng / tuần / hôm nay) |
| `M4-a2-desktop-admin-chart-30-ngay.png` | 145.896 | **Chart 30 ngày** `Nạp thẻ 30 ngày gần nhất` + khối dò nội dung CK |
| `M4-a3-desktop-admin-bang-the-nut-duyet.png` | 151.745 | Bảng thẻ + nút Duyệt / Kiểm tra lại / Huỷ, đủ 4 badge |
| `M4-a4-desktop-admin-yeu-cau-nap.png` | 165.429 | `/admin/topups`: danh sách yêu cầu nạp + nút duyệt/từ chối |
| `M4-a5-desktop-admin-modal-duyet.png` | 198.447 | Modal **Duyệt lệnh nạp** (cộng tiền vào ví người dùng) |
| `M4-a6-mobile-admin-the.png` | 53.592 | Admin thẻ cào trên mobile |

**Tổng: 30 ảnh, ~4,4 MB.**

## Ghi chú kỹ thuật — vì sao a2/a3 từng bị trùng nhau

Ở lần chụp trước, `M4-a2` và `M4-a3` ra **hai file giống nhau y hệt** (cùng
157.013 bytes). Nguyên nhân: `window.scrollTo(0, y)` bị browser **clamp** về
`scrollHeight - innerHeight`. Trang `/admin/cards` chỉ cao ~1500px với viewport
1000px (max scroll ~450–500), nên cả `y=620` lẫn `y=1350` đều cuộn về **cùng một
chỗ**.

Đã sửa trong `122f13b`:
- Viết `tools/probeYs.mjs` để **đo Y thật** bằng `getBoundingClientRect()` thay vì đoán.
- Đặt lại offset theo số đo: **a2 = 200** (chart ở y≈265) và **a3 = 560** (bảng ở y≈853).
- `snapAt()` giờ trả về `window.scrollY` thật và **in cảnh báo `SCROLL CLAMP`** khi
  bị clamp ⇒ lỗi này không thể lặng lẽ đi qua được nữa.
- Feed người trúng nằm ở **sidebar phải** (y≈326), cuộn xuống sẽ vượt qua hẳn ⇒
  thêm `snapEl()` để chụp riêng element bằng `locator.screenshot()`.

Đã kiểm chứng lại: 3 ảnh `a1`/`a2`/`a3` có **3 mã md5 khác nhau**, `a2` hiện rõ
chart 30 ngày, `a3` hiện bảng dữ liệu + nút xử lý với đủ 4 badge trạng thái.

> **Lưu ý bản quyền hình ảnh**: một phần CSS/asset tham khảo từ site mẫu chỉ dùng
> cho bản draft/preview, **không** dùng cho bản thương mại.
