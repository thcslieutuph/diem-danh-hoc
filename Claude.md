# CLAUDE.md — App điểm danh học sinh (bản GitHub Pages + Supabase)

Tệp này là bàn giao từ cuộc trò chuyện trên claude.ai (12–17/11/2026). Đọc hết trước khi làm bất cứ việc gì.

## 1. Bối cảnh

- Người dùng: **thầy Kim Hà**, Trường THCS Liêu Tú (xã Liêu Tú, TP. Cần Thơ). Thầy là Quản trị duy nhất của app điểm danh hiện tại (Google Apps Script, bản 2.1, đang chạy thật cho năm học 2026–2027, **giữ nguyên, không đụng tới**).
- Kho này là **bản dựng lại trên GitHub Pages + Supabase**, hiện chỉ dùng **dữ liệu giả**. Mục tiêu dài hạn (thầy chốt 13/11): bản này phải **thay được app cũ mà không cần nâng cấp thêm**, nên mọi quy trình làm **giống app cũ**, chỉ khác ở chỗ bắt buộc vì bảo mật (và phải nói rõ từng chỗ).
- Trường có 3 điểm trường: Trường chính, Phân hiệu, điểm lẻ Giống Chát; 35 lớp, 1.346 học sinh (số thật, chưa nhập).
- Trang chạy: https://thcslieutuph.github.io/diem-danh-hoc/ (GitHub Pages từ nhánh `main`, thư mục gốc). Tên tệp phải viết thường (`Index.html` viết hoa từng gây 404).
- Supabase: dự án `diem-danh-hoc`, URL `https://lgvqdlqkoekhppppnpga.supabase.co`, vùng Tokyo, gói Free (**tự tạm dừng sau 7 ngày không dùng**). Khóa publishable nằm trong `index.html` là đúng, được phép công khai. **Không bao giờ** đưa khóa secret/service_role vào kho hay vào khung chat.

## 2. Cách làm việc với thầy (bắt buộc)

- Trả lời bằng **tiếng Việt**, xưng "tôi", gọi "thầy". Ngắn gọn, từng bước nhỏ.
- Thầy **không học viết mã**. Claude viết mã; thầy chỉ thử và báo kết quả.
- **Bảo mật thì thầy phải hiểu và duyệt**: mọi thay đổi quyền trình bày trước bằng **bảng quyền lời thường** (ai – được làm gì – dữ liệu nào), thầy duyệt rồi mới viết policy. Câu hỏi kiểm tra (nếu có) chỉ hỏi về bảo mật.
- **Giao diện: luôn cho thầy xem trước** (bản xem trước tương tác) và chốt xong mới viết mã thật. Làm **giống app cũ**; đừng tự thiết kế quy trình mới — hỏi trước.
- **Phản biện** khi yêu cầu chưa phù hợp; đưa phương án tốt nhất, không làm theo máy móc.
- Không dùng cụm "Ban giám hiệu" — gọi đúng **Hiệu trưởng**, **Phó Hiệu trưởng**. Dùng: Tổng phụ trách Đội, GVCN, giáo viên trực, Vắng P / Vắng KP, CMHS.
- **Không bao giờ hỏi, ghi, hay in mật khẩu** (kể cả tài khoản thử). Thầy tự gõ.
- Đẩy mã: hỏi thầy lần đầu có cho đẩy thẳng `main` không (bản học thì nên cho). GitHub Pages chỉ cập nhật sau khi vào `main`, đợi 1–2 phút, thử bằng Ctrl + F5.

## 3. Quy trình thay đổi (bắt buộc)

1. **SQL**: Claude viết vào `sql/NN_ten_viet_thuong.sql` (đánh số tiếp theo). Claude **không có quyền** chạy SQL trên Supabase; thầy dán vào **SQL Editor → New query → Run**. Mọi tệp thay đổi quyền phải **kết thúc bằng** `select * from rieng.chay_thu();` (phải toàn ĐẠT); thêm quyền mới thì **thêm bài thử mới** vào `rieng.chay_thu()`.
2. **Thứ tự triển khai: SQL trước, trang sau** (trang mới gọi hàm mới; ngược lại sẽ lỗi).
3. **Thử trang trước khi đẩy**: `python3 tests/thu_giao_dien.py` (Playwright + Supabase giả `tests/mock_supabase.js`). Khi thêm lệnh máy chủ mới thì thêm vào mock. Ngoài trường hợp suôn sẻ, phải thử: **phiên đăng nhập cũ còn lưu**, **mất mạng / máy chủ không trả lời**, **giờ máy sai**, **sang ngày khác**. (Lỗi "trang trắng" 14/11 xảy ra vì bỏ sót trường hợp phiên cũ.)
4. **Không bao giờ để trang trắng**: mọi lượt gọi có giới hạn thời gian (15 giây), lỗi hiện chữ trên trang.
5. Kiểm tra cấu trúc thật trên máy chủ: `sql/00_xem_cau_truc.sql` (chỉ đọc). Các SQL 01–05 không được lưu thành tệp; **nguồn đúng là máy chủ** — cần chi tiết hàm/policy thì nhờ thầy chạy tệp 00 và gửi kết quả.

## 4. Trạng thái hiện tại (17/11/2026)

- Đã chạy trên Supabase: 14 bảng, RLS, lệnh nghiệp vụ, nhật ký, bài thử `rieng.chay_thu()` (16 bài; bản 07 không phụ thuộc ngày).
- Đã chạy `sql/06_toi_uu_toc_do.sql` (17/11). Cần chạy `sql/07_bai_thu_khong_phu_thuoc_ngay.sql` (16 ĐẠT).
- **CHƯA đưa lên**: `index.html` bản tối ưu trong kho bàn giao này (bản đang chạy trên github.io là bản trước, gọi bảng trực tiếp, 4 lượt gọi khi mở app).
- Việc đầu tiên: xác nhận thầy đã chạy `sql/07` (16 ĐẠT) và đẩy `index.html` → thầy thử trên điện thoại qua Zalo, gửi ảnh dòng đo thời gian cuối trang. Thầy chê bản trước **chậm hơn app Apps Script**; nếu bản tối ưu vẫn không nhanh hơn rõ thì báo thật cho thầy, không cố đi tiếp.
- Cài đặt Supabase đã đặt: tắt tự đăng ký, tắt đăng nhập ẩn danh, mật khẩu ≥ 8 ký tự có chữ và số.

## 5. Tài khoản thử (dữ liệu giả)

Đăng nhập trên app: gõ phần trước `@`; app tự ghép `@diemdanh.test`. Quản trị dùng email đầy đủ.

| Tài khoản | Vai |
|---|---|
| `thcsvienbinh.lieutu@gmail.com` | Quản trị (thầy Hà) |
| `ht` | Hiệu trưởng (giả), chỉ xem |
| `gv03` | Giáo viên 03: GVCN 8A; trực **sáng thứ Ba, sáng thứ Sáu** Phân hiệu |
| `gv05` | Giáo viên 05: trực **sáng thứ Tư**; **Tổng phụ trách Đội** |

Dữ liệu: Phân hiệu có 6A, 7A, 8A, 9A (mỗi lớp 10 em "Học sinh 8A-01"…); sáng T2–T6 tất cả lớp, chiều mỗi lớp 3 buổi so le; phân công trực đợt 09/11–29/11/2026; `ngay_bat_dau` = 2026-11-09. Giáo viên 01–06 (01–04 là GVCN 6A–9A).

## 6. Cấu trúc dữ liệu (schema `public`)

- `diem_truong`, `lop` (khoi, diem_truong_id), `hoc_sinh` (lop_id, dang_hoc), `lich_hoc` (lop_id, thu 2–7, buoi sang|chieu)
- `giao_vien` (user_id → auth.users, vai_tro quan_tri|hieu_truong|pho_hieu_truong|giao_vien, diem_truong_id, lop_chu_nhiem_id, **la_tpt**, **du_phong**, dang_lam)
- `phan_cong_truc` (giao_vien_id, diem_truong_id, thu, buoi, tu_ngay, den_ngay)
- `buoi_chot` (lop_id, ngay, buoi, si_so, so_p, so_kp, nguoi_chot, lan_chot, luc) — một dòng mỗi lớp/buổi đã chốt
- `vang` (hoc_sinh_id, lop_id, ngay, buoi, loai P|KP, ly_do, nguon truc|duyet|chot_lai|nham|quan_tri) — chỉ lưu em vắng
- `de_nghi` (kieu bao_phep|chuyen_p|ghi_nham, hoc_sinh_id, lop_id, ngay, buoi, ly_do, hoc_sinh_dung_id, loai_dung, nguoi_gui, trang_thai ghi_nhan|cho|dong_y|khong_dong_y|da_rut, nguoi_xu_ly)
- `moi_cmhs`, `chot_thi_dua` (diem_truong_id, tuan = thứ Hai), `cai_dat` (khoa, gia_tri), `nhat_ky` (tự ghi bằng trigger)
- `cham_cong` — **còn quy tắc cũ "chỉ trong ngày", chưa làm lại theo app cũ** (phần 3)

Schema `rieng` (không gọi được từ Internet): hàm kiểm tra quyền `gv_hien_tai, vai_tro, lop_chu_nhiem, la_nhan_vien, la_quan_tri, la_lanh_dao, la_tpt, la_du_phong, xem_lop, co_lich_truc, dang_truc, tuan_da_khoa, chua_den, hom_nay (giờ VN), gio_vn, thu_hai, tinh_lai_buoi`; trigger `ghi_nhat_ky, dem_lai_vang`; bài thử `chay_thu, thu_lenh`. (sql/06 thêm `xem_het, ds_lop_xem`.)

Lệnh nghiệp vụ (`public`, SECURITY DEFINER, tự kiểm tra đủ điều kiện): `chot_lop(p_lop, p_ngay, p_buoi, p_vang jsonb)`, `gui_bao_phep`, `gui_de_nghi_nham`, `xu_ly_de_nghi(p_id, p_dong_y)`, `rut_de_nghi`. Giáo viên **không có quyền ghi thẳng** vào `buoi_chot`, `vang`, `de_nghi`; chỉ ghi qua các lệnh này. sql/06 thêm lệnh gộp chỉ đọc (SECURITY INVOKER, RLS vẫn áp dụng): `khoi_dong()`, `tong_buoi(p_dt, p_ngay, p_buoi)`, `mo_lop(p_lop, p_ngay, p_buoi)`.

Nguyên tắc bảo mật: Khách (anon) bị thu hồi mọi quyền trên bảng; mọi bảng bật RLS; mọi hàm `public` phải `revoke ... from public, anon`; vai trò chỉ Quản trị sửa; nghỉ việc → `dang_lam = false` (không xóa, giữ lịch sử); nhật ký không ai sửa/xóa qua app. Giới hạn đã nói với thầy: chủ dự án vẫn sửa được mọi thứ trong SQL Editor → giữ kỹ tài khoản GitHub, bật xác thực hai bước.

## 7. Bảng quyền đã duyệt (13/11/2026)

| Việc | Ai | Điều kiện máy chủ kiểm tra |
|---|---|---|
| Chốt / chốt lại | GV trực đúng buổi, đúng điểm trường; hoặc trực dự phòng | Buổi đã đến (chiều từ 11 giờ); lớp có lịch học; không trước ngày bắt đầu; tuần chưa chốt thi đua |
| Chốt bù buổi đã qua | Người được phân công buổi đó | **Không giới hạn ngày** (thầy chọn như app cũ); tuần chưa chốt thi đua |
| GVCN báo phép trước khi chốt | GVCN lớp đó | GV trực thấy ngay, app điền sẵn Vắng P |
| Đề nghị chuyển KP → P (đã chốt) | GVCN gửi | Chỉ trong ngày đó và ngày hôm sau |
| Duyệt đề nghị | Tổng phụ trách Đội; nếu GVCN chính là TPT thì GV trực buổi đó chốt lại | Không ai tự duyệt đề nghị của mình; tuần chưa chốt thi đua |
| Đề nghị sửa ghi nhầm tên | GVCN gửi, duyệt như trên | Buổi đã chốt, em đang bị ghi vắng |
| Mời CMHS | GVCN lớp mình | — |
| Xem | GVCN: lớp mình; GV trực: lớp ở điểm trường mình trực; HT, PHT, TPT, Quản trị: toàn trường | — |
| Nhật ký | Máy chủ tự ghi | Không ai sửa/xóa qua app; QT, HT, PHT xem |
| Vai trò, phân công, trực dự phòng | Chỉ Quản trị | — |

Khác app cũ (đã giải thích, thầy đồng ý): HT/PHT **phải đăng nhập** (một lần); đăng nhập bằng **tên + mật khẩu** (không chọn tên từ danh sách + PIN, vì lộ danh sách giáo viên và PIN yếu); **một link** cho mọi vai trò (quyền gắn với tài khoản, không cần 2 link như app cũ).

## 8. Giao diện điểm danh (theo app cũ)

Đầu trang xanh đậm: vai trò, tên, nút Mật khẩu, Thoát; chọn ngày (‹ ›) và Sáng/Chiều; chip điểm trường; "Lịch trực của thầy/cô"; "Đề nghị chờ xử lý" (TPT/GV trực); lưới lớp ("✓ có mặt/sĩ số" hoặc "Chưa chấm", "Đã chấm x/y lớp"). Màn hình lớp: chạm trạng thái **Có mặt → Vắng P → Vắng KP**, ô lý do (không bắt buộc), nút **Cả lớp có mặt**, thanh dưới sĩ số + nút **Chốt / Chốt lại**. Nút Chốt luôn hiện; không đủ điều kiện thì nhấn mới báo. Sau khi chốt, đối chiếu số máy chủ trả về với màn hình (không tin `success` suông). Lần đầu đăng nhập bắt đổi mật khẩu.

## 9. Hiệu năng (thầy rất quan tâm)

Mỗi màn hình **một lượt gọi máy chủ**; danh mục lưu `localStorage` (khóa `dd_kd_<uid>`, xóa khi Thoát) để mở app hiện ngay; giờ "hôm nay" lấy theo **giờ máy chủ**; policy gọi hàm phải bọc `(select rieng.fn())` để tính một lần; thư viện supabase-js ghim phiên bản `2.45.4`; cuối trang có dòng đo ms (Khởi động · Tải buổi · Mở lớp · Chốt). Ngày VN tính bằng `Intl.DateTimeFormat(...).formatToParts` (cách `toLocaleDateString("en-CA")` từng ra "undefined").

## 10. Lộ trình (thứ tự thầy đã đồng ý)

1. **Điểm danh + báo phép** (đang làm): còn thử trang tối ưu trên Zalo; **màn hình GVCN** (báo phép trước, đề nghị chuyển P, ghi nhầm tên, mời CMHS, học sinh cần chú ý) — xem trước rồi mới viết; lưu tạm khi mất mạng.
2. Tổng quan + cảnh báo (vắng ≥ 2 ngày/tuần, nguy cơ bỏ học), ảnh báo cáo gửi Zalo.
3. Chấm công giáo viên (theo TKB, hiện chỉ Phân hiệu) — làm lại policy `cham_cong` theo app cũ.
4. Thống kê theo kỳ/tháng, xuất Excel/CSV.
5. Thi đua + đọc sổ đầu bài vnEdu. Quy tắc trường: thi đua tách riêng từng điểm trường; tiêu chí chuyên cần, tiết tốt, điểm tốt (8, 9, 10), vệ sinh, tác phong, nề nếp đầu giờ (bỏ cột phát biểu); xếp hạng tuần theo hệ số = tổng điểm ÷ số tiết (tiết Tốt + Khá + Trung bình nếu có); học kỳ: điểm hạng tuần (số lớp − hạng + 1) × 10, cộng lại ÷ số tuần đã chốt, + điểm phong trào (TPT nhập); cả năm = (HK I + HK II) ÷ 2; tuần kiểm tra giữa/cuối kỳ không tính thi đua; tuần Tết không đánh số. Tổng phụ trách Đội chốt thi đua tuần (chốt rồi thì khóa điểm danh tuần đó).

Trước khi nhập dữ liệu thật: sao lưu tự động (gói Free không có bản khôi phục), rà lại toàn bộ bảng quyền cùng thầy, thử đăng nhập trong Zalo nhiều máy.

## 11. Tài liệu app cũ

Thầy có gói bàn giao app cũ (`ban_giao_diem_danh.zip`: Code.gs, Index.html, hướng dẫn GV trực/GVCN, ảnh màn hình). **Không đưa vào kho này** (kho công khai, có thể có tên thật, mã bảng tính). Cần tham khảo thì nhờ thầy đính kèm vào phiên làm việc.
