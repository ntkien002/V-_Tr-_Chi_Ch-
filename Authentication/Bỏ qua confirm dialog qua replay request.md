Kịch bản: Trang web hiển thị popup JS confirm “Bạn có chắc muốn xóa?”. Nếu nhấn OK, client gửi POST /api/user/delete với body { user_id }.

Logic tấn công: Kẻ tấn công cURL trực tiếp tới endpoint, bypass UI confirm hoàn toàn. Backend không kiểm tra thêm flag confirm, nên xóa luôn.

Vì sao WAF bỏ qua: Mọi request “POST delete” đều bình thường, hệ thống không logics verify.
