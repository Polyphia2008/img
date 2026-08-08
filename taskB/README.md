# TASK B — hai loai item (TU CHON vs NGAU NHIEN)

Anh chup sau khi BO header item kieu cu tren trang /mua-acc/:slug
(repo trumacc, HEAD 3ea93b6).

## Kiem chung tu dong (tools/shotB.mjs, chay trong trinh duyet)
Ca hai loai item deu cho ket qua:
- `div.card.mb-5.sm:flex-row` (card header ngang cu) = **false**
- `img.h-24.w-24.ring-1.ring-cardline` (thumbnail 96px cu) = **false**
- so div co text dung bang `"Gia tu"` = **0**
- `#ma-item-head` (khoi tieu de moi) = **true**
- so `<h1>` tren trang = **1** (khong con trung lap tieu de)
- Rieng item NGAU NHIEN: `#ma-filter` = 0, `#ma-filter-chips` = 0, `.acc-card` = 0

## Danh sach anh
| File | Noi dung |
|---|---|
| B1a-tuchon-trang-ban-co-bo-loc.png | TU CHON: tieu de moi + panel "Loc tai khoan theo yeu cau" + luoi card |
| B1b-tuchon-bo-loc-can-canh.png | Can canh panel bo loc (Muc gia / Sap xep / Tim kiem / Tim+Xoa) |
| B1c-tuchon-chip-dang-loc.png | Chip "Dang loc" + so ket qua |
| B2a-tuchon-luoi-card-acc.png | Luoi card tung acc |
| B2b-tuchon-modal-xac-nhan-mua.png | Modal xac nhan mua 1 acc (khong co dong so luong) |
| B3a-ngaunhien-o-mua-so-luong.png | NGAU NHIEN: tieu de + pill "Toi da N acc/lan" + o mua so luong |
| B3b-ngaunhien-can-canh-o-mua.png | Can canh o mua (Con N acc / Toi da M / stepper / Tong tien) |
| B3c-ngaunhien-tang-so-luong-tong-tien.png | Tang so luong -> tong tien doi theo |
| B3d-ngaunhien-modal-mua-nhieu.png | Modal co dong "So luong: N x gia" |
| B4a-admin-bang-cot-loai.png | Admin: bang item co cot "Loai" |
| B4b-admin-bang-can-canh.png | Can canh cot "Loai" (Tu chon / Toi da N) |
| B4c-admin-form-is-bulk-tuchon.png | Form: is_bulk = 1 -> goi y "Tu chon" |
| B4c2-admin-form-toan-canh-tuchon.png | Form toan canh, is_bulk = 1 |
| B4d-admin-form-is-bulk-random.png | Form: is_bulk = 5 -> goi y "Random" |
| B4d2-admin-form-toan-canh-random.png | Form toan canh, is_bulk = 5 |

> Luu y ban quyen: ban preview con dung CSS/asset tham chieu tu nickfcmobile.com.
> Can thay bang asset tu so huu / co giay phep truoc khi phat hanh cong khai.
