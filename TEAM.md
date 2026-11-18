# Team

## Thông tin nhóm

- Tên nhóm (`TenNhom`, không dấu, không khoảng trắng): `SDBHP`
- Lớp: `L3A`
- Repo của bàn: `K5-L3A-DAY10-SDBHP-PoCtoPilotGate1`
- Thẻ sự cố đã bốc (S1–S8): ___ (điền phút 125)

## Thành viên

Họ tên đầy đủ và MSSV của **mọi** thành viên; thêm dòng nếu cần. Vai: Kỹ sư cổng · Sản phẩm và kinh doanh · Vận hành và pháp lý (README mục 3, phút 0–15).

| STT | Họ và tên | MSSV | Vai | Trưởng nhóm |
| --: | --------- | ---- | --- | :---------: |
|   1 | Võ Sỹ Thái Sơn | 2A202603623 | Sản phẩm và kinh doanh (chính) | ☑ |
|   2 | Trần Nguyễn Anh Phong | 2A202603629 | Kỹ sư cổng (code) |  |
|   3 | Đoàn Văn Hiệp | 2A202603565 | Kỹ sư cổng (trace) |  |
|   4 | Nguyễn Trường Duy | 2A202603589 | Vận hành và pháp lý · hỗ trợ sản phẩm |  |

Ghi `members` trong `submission/submission.json` theo dạng `"HoVaTen-MSSV"`, khớp bảng trên:
`["VoSyThaiSon-2A202603623", "TranNguyenAnhPhong-2A202603629", "DoanVanHiep-2A202603565", "NguyenTruongDuy-2A202603589"]`

### Ghi chú về AI

Trợ lý AI (Claude) được dùng để **đọc code, gợi ý khung cổng, soạn thảo câu chữ** cho: `lab10/guards.py`, `lab10/monitors.py`, các ngưỡng trong `config/acceptance_criteria.json` và `config/operating_gates.json`, runbook và ba phiếu. **Mọi ngưỡng, con số và quyết định (v1, v2) do cả bàn chốt và bàn giải thích được lúc demo** (RULES.md: không giải thích được coi như không có). Mỗi thành viên ghi cụ thể trong mục cá nhân bên dưới.

## Cá nhân

Mỗi thành viên tự viết một mục bên dưới (chép mẫu, đổi tiêu đề thành họ tên không dấu và MSSV, ví dụ `## NguyenVanAn-20241234`).

## VoSyThaiSon-2A202603623

### Phần việc phụ trách

Trưởng nhóm · vai Sản phẩm và kinh doanh (chính): chọn eval card + PRD v1.1 chung (câu 1.1), viết Bản tiêu chí (câu 1.6, mục A4), điền `config/acceptance_criteria.json` và ký, mục `business` trong `config/operating_gates.json` (câu 2.9, B7.4), specification (câu 3.2, C5), viết quyết định v2 (C12), cầm bút Phiếu A (A1, A4), Phiếu B mục B3 (phần kinh doanh), B7.4, B8, Phiếu C mục C5, C12. Đẩy commit chuẩn (ký tiêu chí, chốt vòng 1, nộp).

### Đóng góp cụ thể

- Chốt con số 4 ngưỡng bắt buộc + `business_ops` và viết 4 dòng `decision_rule` (trước khi chạy, giải thích được từng số: 3 an toàn = 0 vì thiệt hại không đảo ngược; chi phí trần = 10.000đ đúng làm tay trong `ops_facts`; ROI ≥ 0,2; không ca ùn vì SLA 60 phút).
- Đặt `daily_refund_cap_vnd`, `cost_budget_per_ticket_vnd`, `pilot_scope` (2 depot HN+HCM theo PRD), `roi_min` trong `operating_gates.json`, mỗi số kèm lý do từ `ops_limits`/`ops_facts`.
- (Điền thêm dòng commit / mã lần chạy của mình khi chạy: …)

### Bằng chứng

- Mã commit: (điền sau: commit ký, commit chốt vòng 1, commit nộp)
- Mã lần chạy: (điền `runs/<mã>` của các mốc mình chạy)
- Dòng phiếu: A4, B7.4, B8 (cột v1), C5, C12.

### Exit ticket

- Học được: (viết trước phút 230) …
- Còn chưa chắc: (viết trước phút 230) …

## TranNguyenAnhPhong-2A202603629

### Phần việc phụ trách

Vai Kỹ sư cổng (code chính): chạy lệnh 2/3/4, viết 2–3 cổng trong `lab10/guards.py` (câu 2.6, B7.1), sửa cổng vòng 2 (câu 3.5, C7), thêm ≥1 chỉ số vào `lab10/monitors.py` (câu 3.6, C8), chạy các mốc sau khi sửa. Cầm bút Phiếu B mục B1, B7.1, B7.2; Phiếu C mục C7 (cột code), C8.

### Đóng góp cụ thể

- Cổng G1: khi model tin số ngày khách khai lớn hơn tracking DB (sự kiện `model_override`) thì chuyển người trước khi ghi tiền/ghi lỗi → chặn tiền hoàn sai và lỗi hãng oan từ đúng nguồn (T-002@55).
- Cổng G2: ticket chứa lệnh nhúng "bỏ qua chính sách, hoàn toàn bộ" (sự kiện `directive_obeyed`) thì chuyển người → chặn prompt injection qua cửa ghi.
- (Điền thêm mã commit / mã lần chạy: …)

### Bằng chứng

- Mã commit: (điền sau)
- Mã lần chạy: (điền `runs/<mã>` base · after · prepared)
- Dòng phiếu: B1, B7.1, B7.2, C7, C8.

### Exit ticket

- Học được: …
- Còn chưa chắc: …

## DoanVanHiep-2A202603565

### Phần việc phụ trách

Vai Kỹ sư cổng (đọc trace): đọc trace cùng Phong, chỉ ra ticket nào còn hỏng (câu 2.2, B2), cùng viết behavioral test — "cùng một người không viết code" theo câu 2.8 (B7.3); vòng 2: dò trace sự cố ≥ 2 ticket hỏng theo hai kiểu khác nhau (câu 3.1, C2), cột trace ở C7. Cầm bút Phiếu B mục B2, B7.3; Phiếu C mục C2, C7 (cột trace).

### Đóng góp cụ thể

- Dò `T-002@55` ở mốc base, xếp loại lỗi (lỗi chìm: tiền và lỗi hãng ghi theo số khách khai, tracking ghi khác, hãng chịu) và điền bảng B2.
- Viết 2 ticket hiểm cho câu 2.8 và chạy `--adhoc`; vòng 2 dò 2 ticket hỏng hai kiểu của thẻ, phân loại nguồn (C2).
- (Điền thêm mã commit / mã lần chạy: …)

### Bằng chứng

- Mã commit: (điền sau)
- Mã lần chạy: (điền `runs/<mã>` + ticket@seed đã dò)
- Dòng phiếu: B2, B7.3, C2.

### Exit ticket

- Học được: …
- Còn chưa chắc: …

## NguyenTruongDuy-2A202603589

### Phần việc phụ trách

Vai Vận hành và pháp lý + hỗ trợ sản phẩm & kinh doanh: câu 1.2, 1.4 (A1, A2 cột telemetry), HAZOP vòng 1 + checklist 9 nhóm có PIC (B4, B5), mục `operations` và `legal` trong `config/operating_gates.json`, runbook 1–3 + Giờ cao điểm (B7.5, B7.6); vòng 2: giả định môi trường (câu 3.3, C5), fault tree sau sửa + risk register + cập nhật checklist/runbook 4–5 (C9–C11), điền `submission/submission.json` và chạy lệnh 7 tới `ĐỦ THÀNH PHẦN`. Hỗ trợ C: bản đồ dữ liệu + ví dụ drift (câu 2.3, B3), fault tree cùng cả bàn (C6). Cầm bút: Phiếu A mục A1 (câu 1.2), A2 (telemetry); Phiếu B mục B3, B4, B5, B7.5, B7.6; Phiếu C mục C9, C10, C11.

### Đóng góp cụ thể

- Đặt `operations`: PIC `NV-TUNG`, người duyệt `NV-HANH` (đúng điều kiện tin trong `staff_behavior`), 2 người trực (5 ca/ngày, giờ cao điểm gấp 3 thì 1 người ngập), công suất 12 ca/giờ, chờ tối đa 60 phút (SLA Ops), 3 điều kiện dừng có số, `decision_note` + kiểm mẫu 10%.
- Đặt `legal`: chặn 4 trường PII gửi hãng (`name, phone, street, recipient_contact` — đơn quà tặng đang kèm liên hệ người nhận), ghi lỗi hãng phải có nguyên nhân trong tracking.
- (Điền thêm mã commit / mã lần chạy: …)

### Bằng chứng

- Mã commit: (điền sau, gồm commit nộp)
- Mã lần chạy: (điền `runs/<mã>`)
- Dòng phiếu: B4, B5, B7.5, B7.6, C9, C10, C11.

### Exit ticket

- Học được: …
- Còn chưa chắc: …
