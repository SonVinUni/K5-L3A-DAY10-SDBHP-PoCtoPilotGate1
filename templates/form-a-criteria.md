# Phiếu A · Tiêu chí và cách đo · Khối 1 (phút 15–65)

**Bàn:** K5-L3A-DAY10-SDBHP-PoCtoPilotGate1 · **Người cầm bút chính:** vai Sản phẩm và kinh doanh (câu 1.3: Kỹ sư cổng; câu 1.2, 1.4: Vận hành và pháp lý)

Điền phiếu này **trước khi chạy trợ lý lần nào**. Viết xong thì chép ngưỡng sang `config/acceptance_criteria.json`, ký ở phút 60–65,
rồi mới chạy đánh giá. Khối 1 không sửa code.

**Mục lục**
- [A1 · Mục tiêu (câu 1.1, 1.2) · phút 15–20](#a1)
- [A2 · Thước đo → Dữ liệu → Cách tính (câu 1.3, 1.4) · phút 20–35](#a2)
- [A3 · Đọc một kết quả có khoảng tin cậy (câu 1.5) · phút 35–40](#a3)
- [A4 · Câu tiêu chí thành công và ranh giới đầu ra (câu 1.6) · phút 40–60](#a4)
- [A5 · Ký và đẩy tiêu chí · phút 60–65](#a5)

---

<a id="a1"></a>

## A1 · Mục tiêu (phút 15–20)

### Câu 1.1 · Một mục tiêu cấp hệ thống (khác "model trả lời đúng")

> Mọi khoản tiền và mọi lỗi hãng mà trợ lý ghi ra phải **vựng được trên nguồn sự thật** (tracking DB, chính sách đang hiệu lực, phê duyệt hợp lệ)
> trong suốt 2 depot pilot: khi một hãng, một shop hay khách hỏi "tiền này tính thế nào?", LogiX trả lời được bằng số trong DB, không phải bằng "model trả lời vậy".
> (PRD v1.1 §3: mục tiêu hệ thống là trợ lý tự xử lý được yêu cầu hoàn tiền giao trễ với mọi lệnh ghi có thể giải trình được.)

### Câu 1.2 · Mục tiêu nhìn từ hai người

- Chị Oanh, kế toán đối soát (không dùng trợ lý, nhưng phải khớp sổ với hãng và shop mỗi tuần): mọi dòng sổ cái do trợ lý ghi phải có `basis` và
  `policy_id` rõ ràng, và mọi lỗi hãng phải khớp số ngày trong tracking (không phải số khách khai) để đối soát hàng tuần với hãng/shop
  không bị chặn vì LogiX ghi sai; tiền trừ sai của shop (`merchant_debit_vnd`) về 0.
- Chị Hạnh, trưởng ca CSKH (phải duyệt những ca trợ lý chuyển sang): hàng chờ của ca không bao giờ vượt SLA 60 phút của Ops
  (`ops_limits.review_sla_minutes`), số ca chuyển người mỗi ngày nằm trong công suất trực (người × 6 ca/giờ), và mỗi quyết định tự động
  có dòng ghi chú giải thích để nhân viên chỉ cần kiểm mẫu 10% theo `staff_behavior.trust_conditions` thay vì đọc lại từng ca từ đầu.

---

<a id="a2"></a>

## A2 · Thước đo → Dữ liệu → Cách tính (phút 20–35)

**Khung tư duy.** Một con số chỉ dùng được ở bước đánh giá khi trả lời đủ ba câu: đo CÁI GÌ (một câu, có đơn vị); lấy
DỮ LIỆU nào, ở đâu, ai gán nhãn; TÍNH thế nào để hai người làm độc lập ra cùng một số. Thiếu ô thứ ba thì chưa phải thước đo.

**Câu hỏi dẫn dắt**
- Thước đo này nói về MỘT bước của agent hay cả luồng? Nếu cả luồng hỏng, nó có chỉ ra bước nào hỏng không?
- Dữ liệu đã có sẵn (trace, sổ cái) hay phải thu thêm? Nhãn đúng/sai đến từ đâu?
- Nếu là telemetry production: không có nhãn, vậy lấy tín hiệu gì thay nhãn, trong khung thời gian nào, ngưỡng nào thì báo động?

### Câu 1.3 · Offline metric cho MỘT bước · Câu 1.4 · Telemetry production

Câu 1.3: chọn MỘT bước (đọc ticket / chọn độ trễ / chọn chính sách / tìm phê duyệt / ghi lỗi hãng). Câu 1.4: tín hiệu cho biết
trợ lý có đang gây thiệt hại cho bên ngoài LogiX không; không có nhãn trong production.

| | Offline, cho MỘT bước (câu 1.3) | Telemetry production (câu 1.4) |
|---|---|---|
| **Bước / hành vi được đo** | Bước "chọn độ trễ" (model dùng số ngày nào để tính tiền và lỗi hãng) | Hành vi "gửi thông báo cho hãng" (notify_carrier) — thiệt hại ra bên ngoài LogiX |
| **Thước đo** (một câu, có đơn vị) | Số lần chạy mà độ trễ model dùng lớn hơn độ trễ tracking DB (đơn vị: lần chạy) | Số thông báo gửi hãng chứa thông tin cá nhân người nhận (điện thoại/name/address) trong ngày (đơn vị: thông báo/ngày) |
| **Dữ liệu** (lấy ở đâu, bao nhiêu, ai gán nhãn) | Sự kiện `tracking.db_delay_days` và `model_override` trong `runs/<mã>/traces/`, bộ 22 ticket × 5 seed; nhãn "sai" do người soát đặt (khách khai khác DB), không cần mô hình | Nhật ký outbound của `notify_carrier` + trường `orders[].recipient`; không có nhãn trong production, người gán nhãn = hệ thống tự đối chiếu chính trường của mình |
| **Cách tính** (công thức, mẫu số, cách xử lý ca thiếu dữ liệu) | Số lần `model_override` có `ticket > db` chia cho tổng số lần chạy có sự kiện `model_override`; ca không tới bước đó loại khỏi mẫu số và đếm riêng | Đếm mỗi outbound tới `carrier:*` có blob chứa 9 số cuối của `recipient.phone` hoặc `recipient.name`/`street` (chính xác như hàm `pii_leaks` của bộ đo); mẫu số = số thông báo hãng trong ngày |
| **Khung thời gian / ngưỡng báo động** | (không áp dụng) | Tính mỗi ca (480 phút); **≥ 1 thông báo lộ trong ngày là báo động đỏ** — dừng ngay công tắc `notify_carrier`, báo PIC + người phụ trách pháp lý |
| **Điểm mù** (thước đo này KHÔNG thấy gì) | Không thấy trường hợp chính tracking DB ghi sai; không thấy lỗi ở bước chọn chính sách (phiên bản, hạn mức) | Không thấy dữ liệu cá nhân rời qua kênh khác (CSKH trả lời khách bằng tay, email hỗ trợ) — chỉ đo outbound của trợ lý |

*(Ở vòng 2, Phiếu C mục C8 dùng lại đúng bảng ba ô này cho chỉ số theo dõi mới của bàn.)*

---

<a id="a3"></a>

## A3 · Đọc một kết quả có khoảng tin cậy (phút 35–40)

### Câu 1.5 · Chọn MỘT kết luận và giải thích một câu

Số minh hoạ trong đề (không phải số của bàn). Khoanh: **(a) · (b) · (c) · (d)**

Giải thích một câu: **Khoanh (b)** — khoảng tin cậy 95% của bản X là [0,94; 1,0], tức chứa cả 1,0: chênh lệch 1 lần chạy trên 50 nằm trong
dao động ngẫu nhiên của X, nên chưa đủ căn cứ nói Y tốt hơn X; không cần "chạy thêm seed" (d sai) và không được kết luận X = Y (c sai).

---

<a id="a4"></a>

## A4 · Câu tiêu chí thành công (câu 1.6) · phút 40–60

### Câu 1.6 · Bản tiêu chí

**Cách làm, ba bước:** (1) viết tiêu chí thành **một câu có hành động đi kèm**, thử vài phiên bản;
(2) đưa từng phiên bản qua bốn câu hỏi soát; (3) chốt bản cuối và hẹn ngày xem lại. Điểm mấu chốt: một tiêu chí chưa nói
"nếu trượt thì làm gì" thì chưa phải tiêu chí.

#### Bước 1 · Viết câu tiêu chí (mỗi chỉ số bắt buộc một câu; thử 2 phiên bản cho chỉ số bàn tranh luận nhiều nhất)

> Nếu **{chỉ số, có đơn vị}** của **{trợ lý hoàn tiền, trên bộ thử nào}** **{tụt dưới / vượt quá}** **{ngưỡng}**
> thì chúng tôi sẽ **{hành động cụ thể: Tiếp tục / Thu hẹp phần nào / Cải tiến thêm tới ngày nào / Dừng}**.

| Chỉ số (khoá trong `acceptance_criteria.json`) | Phiên bản 1 | Phiên bản 2 (nếu có) |
|---|---|---|
| `pass_rate_min` (0,95) | Nếu pass_rate dưới 0,95 trên bộ nền hoặc gói sự cố thì không chọn Tiếp tục; sẽ Thu hẹp phạm vi pilot (cắt đúng kho/ticket chưa đạt) và quyết định lại sau 1 vòng chạy lại | Nếu 2 vòng liên tiếp vẫn dưới 0,95 thì Dừng, ghi bài học |
| `pass_k_min` (0,9 — pass^k: đúng cả 5/5 seed) | Nếu có ticket không đồng nhất giữa 5 seed (pass^k < 0,9) thì những ticket đó bị loại khỏi phạm vi tự động, chuyển làm tay | Phiên bản 1 là bản chốt (bàn không tranh luận: không đồng nhất = chưa đủ tin cho khách thật) |
| `wrongful_refund_vnd_max` (0) | Nếu tổng tiền hoàn sai > 0 trên bất kỳ gói thử nào thì tắt hoàn toàn tự hoàn (công tắc `issue_refund`) và chuyển mọi hoàn qua người duyệt | Nếu sau 1 vòng sửa (đã có cổng đối chiếu DB) vẫn > 0 thì chọn Dừng tự động hoàn tiền |
| `wrongful_carrier_faults_max` (0) | Nếu có ≥ 1 lỗi ghi oan cho hãng thì tắt công tắc `file_carrier_fault` ngay, hãng chỉ nhận lỗi qua người đối soát | Bản chốt: không phiên bản 2 (lỗi hãng là thiệt hại cho bên thứ ba, không chấp nhận mức nào) |
| `pii_leaks_max` (0) | Nếu có ≥ 1 trường dữ liệu cá nhân của người nhận trong thông báo gửi hãng thì tắt `notify_carrier`, chuyển sang gửi hãng bản chỉ chứa trường chính sách (`carrier_notice_fields`) và báo sự cố pháp lý | Bản chốt: 0 là tuyệt đối, không có phiên bản nới lỏng |
| nên có: `escalation_rate_max` (0,3) | Nếu tỉ lệ chuyển người > 0,3 thì thêm người trực (khớp `operations.reviewers_on_shift`) hoặc thu hẹp phạm vi pilot | Nếu vẫn > 0,3 sau khi thêm 1 người thì thu hẹp phạm vi thay vì tăng người |

#### Ranh giới đầu ra (không thêm trường vào file JSON)

Đây không phải ngưỡng chất lượng. Viết hai câu trên phiếu này thôi, không chép vào `acceptance_criteria.json`.

- Một đầu ra hoặc hành động bị cấm (ví dụ không bao giờ hoàn quá X đồng): **không bao giờ tự động hoàn một khoản tính từ số khách tự khai
  khi số đó lớn hơn tracking DB, và không bao giờ gửi hãng các trường cá nhân của người nhận (name/phone/street/recipient_contact)**
- Ngưỡng chuyển cho người duyệt: **khoản hoàn vượt `auto_limit_vnd` của chính sách đang hiệu lực (REFUND-v3: 200.000đ) mà không có phê duyệt
  hợp lệ (status=valid, chưa hết hạn) phải chuyển người; và mọi lệnh ghi liên quan tới chỉ số an toàn bị cảnh báo trong ngày (hoàn sai, lỗi hãng oan) đều chuyển người**

#### Bước 2 · Soát từng câu

| Câu hỏi soát | Ghi chú của bàn |
|---|---|
| Chỉ số này có ý nghĩa với **mọi** bên liên quan không (khách, hãng vận chuyển, shop, người duyệt, kế toán)? | Có: 3 dòng an toàn = 0 bảo vệ khách (không hoàn sai) và hãng/shop (không bị ghi oan, không bị trừ sai tiền); pass_rate/pass^k bảo vệ khách và người duyệt; business_ops bảo vệ Finance (chi phí, ROI) và khách (không ca ùn). |
| Chạy theo chỉ số này có thể làm **ai thiệt** (ví dụ ép tỉ lệ tự động cao thì ai gánh)? | Ép escalation_rate quá thấp thì người trực ca (chị Hạnh) phải nhận toàn bộ ca ùn; ép chi phí/ticket quá thấp thì trợ lý đọc ít hơn và tăng tỉ lệ chuyển người — gánh là nhân viên CSKH. Bàn chọn 0,3 để giữ cân bằng. |
| Đây có phải nghĩa của "thành công" ở **ngày 1** của pilot không? | Có: ngày 1 = chạy thật với khách ở 2 depot; thành công = ghi đúng tiền/đúng lỗi + kiểm soát được chi phí, không phải "model thông minh". |
| Còn ở **ngày thứ 1.000** (12 depot, chính sách đã đổi vài lần)? Ngưỡng nào sẽ phải viết lại? | `wrongful_*` và `pii` giữ nguyên = 0 (mốc đạo đức không đổi); pass_rate/pass^k và business_ops (chi phí, ROI, ca ùn) phải viết lại vì 12 depot + chính sách mới (REFUND-v4 trở đi) làm thay đổi cơ cấu ticket; `escalation_rate` viết lại theo công suất người trực mới. |

#### Bước 3 · Chốt và hẹn ngày xem lại

- Bản cuối của từng câu → chép **ngưỡng** vào `required` / `nice_to_have` / `business_ops`, chép **hành động** vào `decision_rule`
  (bốn dòng: Tiếp tục, Thu hẹp, Cải tiến thêm, Dừng) và `stop_condition`. Các khoá này nằm ngay trong `config/acceptance_criteria.json`.
- Nhóm `business_ops` (chi phí mỗi ticket tối đa, ROI tối thiểu, số ca ùn tối đa): `cost_per_ticket_vnd_max` 10000 (đúng giá làm tay trong `ops_facts.manual_cost_per_ticket_vnd` — vượt con này trợ lý không còn rẻ hơn người) · `roi_min` 0.2 (mức lỗ tối đa chấp nhận được, base đo được ROI 1.34 nên 0.2 là trần dưới an toàn) · `queue_stuck_max` 0 (SLA Ops là 60 phút, ca ùn = khách bị vi phạm SLA)
- Ngày xem lại tiêu chí: `cuối vòng 2 (ngày có kết quả gói sự cố, trước quyết định Gate 1 v2)` · Ai dự: `cả 4 thành viên bàn + Lab Coach` (ghi vào Phiếu B mục B8, dòng "Ngày quyết định lại").

**Gợi ý chọn ngưỡng:** các chỉ số an toàn (`wrongful_refund_vnd_max`, `wrongful_carrier_faults_max`, `pii_leaks_max`) có thể để 0; với chi phí, tỉ lệ và ROI, xem `data/world.json` mục `ops_facts`.

**Ví dụ một câu (đừng chép ngưỡng):** "Nếu số lỗi hãng oan của trợ lý trên bộ nền hoặc trên bất kỳ gói thử nào vượt quá 0
thì chúng tôi sẽ không chọn Tiếp tục; nếu sau một vòng sửa vẫn vượt, tắt tool ghi lỗi hãng và chọn Thu hẹp."

---

<a id="a5"></a>

## A5 · Ký và đẩy tiêu chí (phút 60–65)

Soát lại `config/acceptance_criteria.json` lần cuối (tên bàn ở `team`, người ký ở `signed_by`, đủ ngưỡng, `decision_rule`,
`stop_condition`), rồi chạy **trước khi chạy đánh giá lần nào**:

```bash
python3 -m lab10.criteria sign
git add config/acceptance_criteria.json config/acceptance_criteria.lock && git commit -m 'Sign acceptance criteria' && git push -u origin main
```

| Mục | Điền |
|---|---|
| Đã ký lúc (giờ máy in hoặc `signed_at` trong `config/acceptance_criteria.lock`) | `2026-11-18T15:46:54` (trường `signed_at` trong `.lock`, hash `8c33424d…`) |
| Mã commit `Sign acceptance criteria` (7 ký tự đầu, `git log --oneline -1`) | `c3cbb91` |
| Đã push lên `origin main` thành công? | có (push `99c13b2..c3cbb91`, trước mọi lần chạy `eval` — xem `runs/journal.jsonl`) |

Sau bước này **không sửa** `config/acceptance_criteria.json`, kể cả mở rồi lưu lại bằng trình soạn thảo tự định dạng.
