# MILESTONE 1 — Trang item NGẪU NHIÊN (túi mù) đã có LƯỚI CARD ACC

Trước đây trang item ngẫu nhiên chỉ hiện **một ô số lượng trơ trọi**. Sau MILESTONE 1,
trang này hiển thị **nhiều card acc** giống trang tham chiếu `nickfcmobile.com` /
`shopnickv6.sieuthicode.net` — khách xem được kho hàng thật, nhưng **không chọn được acc
cụ thể**: mọi nút trên card đều mua **theo số lượng** (không gửi `account_id`).

- Repo code: https://github.com/Polyphia2008/trumacc — branch `main`
- Commit source: `7a9924d` · bundle: `04080ba` · bộ chụp ảnh: `a254c5a`
- Bundle đang chạy: `assets/index-BfdI8UVV.js` → `assets/MuaAcc-Fi5BVEXp.js`

## Bằng chứng DOM (in ra ngay lúc chụp, cùng một lần chạy)

| Trang | `.acc-card` | `.acc-card--random` | `.acc-rnd-tag` | `.acc-buy-btn--random` | nút tự chọn | link `/acc/:id` |
|---|---|---|---|---|---|---|
| NGẪU NHIÊN — desktop 1440×900 | **24** | 24 | 24 | 24 | **0** | **0** |
| NGẪU NHIÊN — mobile 390×844 | **24** | 24 | 24 | 24 | **0** | **0** |
| TỰ CHỌN (đối chiếu) | 24 | **0** | **0** | **0** | 24 | **24** |

Tiêu đề lưới: `Kho hàng đang có` + pill `30 acc` · ghi chú:
`Acc giao ra là ngẫu nhiên — không chọn được acc cụ thể.` · giới hạn `Tối đa 5 acc/lần`.

## Danh sách ảnh

### Desktop 1440×900
| Ảnh | Nội dung | Kích cỡ |
|---|---|---|
| `M1-d1-ngaunhien-toan-canh.png` | Toàn cảnh: ô mua số lượng + tiêu đề "Kho hàng đang có 30 acc" + lưới card acc | 177.701 B |
| `M1-d2-ngaunhien-luoi-card.png` | Cận cảnh lưới card acc + nhãn đỏ "Ngẫu nhiên" trên từng card | 187.715 B |
| `M1-d3-card-can-canh.png` | Cận cảnh 1 card: nhãn "Ngẫu nhiên", "Mã số", giá, nút mua | 41.909 B |
| `M1-d4-o-mua-so-luong-SL3.png` | Ô mua ngẫu nhiên: stepper số lượng = 3 → tổng tiền 27.000đ | 54.683 B |
| `M1-d5-card-nut-theo-so-luong.png` | Nút trên card đổi thành "Mua 3 acc Ngẫu Nhiên" theo ô số lượng | 75.849 B |

### Mobile 390×844
| Ảnh | Nội dung | Kích cỡ |
|---|---|---|
| `M1-m1-ngaunhien-o-mua-mobile.png` | Đầu trang: ô mua số lượng trên mobile | 66.429 B |
| `M1-m2-ngaunhien-luoi-card-mobile.png` | Lưới card acc 2 cột + nhãn "Ngẫu nhiên" | 121.198 B |
| `M1-m3-ngaunhien-luoi-card-mobile-2.png` | Cuộn tiếp: thêm nhiều card acc | 111.223 B |
| `M1-m4-card-can-canh-mobile.png` | Cận cảnh 1 card ngẫu nhiên trên mobile | 36.046 B |

### Đối chiếu
| Ảnh | Nội dung | Kích cỡ |
|---|---|---|
| `M1-s1-tuchon-khong-doi.png` | Item TỰ CHỌN **không đổi**: vẫn có bộ lọc, không có nhãn "Ngẫu nhiên", card vẫn link `/acc/:id` | 194.525 B |

**Tổng: 10 ảnh ≈ 1,1 MB.**

## Kiểm thử sau MILESTONE 1
`e2e 222/222` · `imgaudit 91/91` · `smoke9 46/46` · `probeB 46/46` (38→46, thêm 9 assert
lưới ngẫu nhiên) · `probeBuy 29/29` (27→29; cú click mua giờ đi qua nút **trên card**
`.acc-buy-btn--random` thứ 2 và vẫn gửi `{product_id, quantity}` **không** có `account_id`).

> Lưu ý bản quyền: bản dựng xem trước có dùng CSS/asset tham chiếu để đối chiếu giao diện;
> cần thay bằng asset riêng trước khi dùng thương mại.
