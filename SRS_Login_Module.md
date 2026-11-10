# TÀI LIỆU ĐẶC TẢ YÊU CẦU PHẦN MỀM (SRS)
## Module: Đăng nhập (Login) – Perfex CRM | Anh Tester Demo

11111
2222

dđ

| Mục | Nội dung |
|---|---|
| Phiên bản tài liệu | 1.0 (Draft) |
| Ngày lập | 2026-11-10 |
| Hệ thống | Perfex CRM – Anh Tester Demo |
| URL module | https://crm.anhtester.com/admin/authentication |
| Phạm vi | Trang đăng nhập khu vực quản trị (Admin) và chức năng Quên mật khẩu |

> **Phương pháp & giới hạn khảo sát**
> - Tài liệu được xây dựng từ việc quan sát trực tiếp trang Login (giao diện, cấu trúc DOM, thuộc tính form, các liên kết) và trang Forgot Password.
> - Các mục đánh dấu **[Quan sát]** là thông tin đã xác nhận trên hệ thống. Các mục đánh dấu **[Giả định – cần xác minh]** là hành vi suy luận từ đặc thù của Perfex CRM hoặc thông lệ chung, **chưa** được kiểm chứng bằng cách gửi form.
> - Do quy tắc an toàn, quá trình khảo sát **không nhập mật khẩu / không gửi form đăng nhập**. Vì vậy các hành vi sau đăng nhập (redirect, thông báo lỗi, khóa tài khoản…) cần được QA xác nhận thủ công bằng tài khoản test.

---

## 1. Giới thiệu

### 1.1 Mục đích
Mô tả đầy đủ các yêu cầu chức năng và phi chức năng của module Login, làm cơ sở cho phát triển, kiểm thử (test case) và nghiệm thu.

### 1.2 Phạm vi
Module Login cho phép người dùng nội bộ (Admin/Staff) xác thực bằng Email và Mật khẩu để truy cập khu vực quản trị `/admin`. Bao gồm:
- Đăng nhập bằng email/mật khẩu
- Ghi nhớ đăng nhập (Remember me)
- Quên mật khẩu (yêu cầu đặt lại mật khẩu qua email)

Ngoài phạm vi: đăng ký tài khoản, đăng nhập khu vực Customer (client portal), SSO/2FA (chưa quan sát thấy trên giao diện).

### 1.3 Định nghĩa & viết tắt
| Thuật ngữ | Ý nghĩa |
|---|---|
| SRS | Software Requirements Specification |
| CSRF | Cross-Site Request Forgery |
| Staff/Admin | Người dùng nội bộ của CRM |
| FR / NFR | Functional / Non-Functional Requirement |

### 1.4 Đối tượng sử dụng tài liệu
BA, Developer, QA/Tester, Product Owner.

---

## 2. Mô tả tổng quan

### 2.1 Bối cảnh sản phẩm
Perfex CRM (nền tảng CodeIgniter/PHP). Trang đăng nhập là cổng vào duy nhất của khu vực `/admin`.

### 2.2 Tác nhân (Actors)
| Actor | Mô tả |
|---|---|
| Admin / Staff | Người dùng có tài khoản trong hệ thống, đăng nhập để sử dụng CRM |
| Khách (chưa xác thực) | Người truy cập trang Login; chỉ được xem form đăng nhập và quên mật khẩu |

### 2.3 Giả định & ràng buộc
- Người dùng truy cập bằng trình duyệt web hiện đại, bật JavaScript và cookie.
- Tài khoản do quản trị viên tạo sẵn; không có chức năng tự đăng ký trên trang Login **[Quan sát]**.

---

## 3. Mô tả giao diện (Quan sát thực tế)

### 3.1 Trang Login – `GET /admin/authentication`
- Tiêu đề trang (title): `Perfex CRM | Anh Tester Demo - Login` **[Quan sát]**
- Logo (liên kết về `https://crm.anhtester.com/`), tiêu đề `Login`, và một form nằm trong thẻ (card) căn giữa màn hình **[Quan sát]**.

| # | Thành phần | Loại | name / id | Ghi chú **[Quan sát]** |
|---|---|---|---|---|
| 1 | Logo | Hình ảnh + liên kết | – | Liên kết về trang chủ `/` |
| 2 | Tiêu đề | Heading | – | Văn bản "Login" |
| 3 | CSRF token | input hidden | `csrf_token_name` | Token chống CSRF, tự sinh theo phiên |
| 4 | Email Address | input `type=email` | `email` / `email` | Có `autofocus`; không có `required` HTML5, không `placeholder`, không `maxlength`, không `autocomplete` |
| 5 | Password | input `type=password` | `password` / `password` | Không `required` HTML5, không `maxlength`, không `autocomplete` |
| 6 | Remember me | checkbox | `remember` / `remember` | Mặc định bỏ chọn |
| 7 | Nút Login | button `type=submit` | – | Màu xanh, chiếm toàn bộ chiều rộng form |
| 8 | Forgot Password? | liên kết | – | → `/admin/authentication/forgot_password` |

- Form: `method=POST`, `action=https://crm.anhtester.com/admin/authentication` **[Quan sát]**; không `novalidate` (trình duyệt vẫn kiểm tra định dạng email của `type=email`).

### 3.2 Trang Forgot Password – `GET /admin/authentication/forgot_password`
| # | Thành phần | Loại | name / id | Ghi chú **[Quan sát]** |
|---|---|---|---|---|
| 1 | Tiêu đề | Heading | – | "Forgot Password" |
| 2 | CSRF token | input hidden | `csrf_token_name` | |
| 3 | Email Address | input `type=email` | `email` / `email` | Không `required` HTML5 |
| 4 | Nút Confirm | button `type=submit` | – | Gửi yêu cầu đặt lại mật khẩu |
| 5 | Logo | liên kết | – | Về trang chủ |

- Form: `method=POST`, `action=/admin/authentication/forgot_password` **[Quan sát]**.
- Trang này **không** có liên kết quay lại trang Login (ngoài logo) **[Quan sát]**.

---

## 4. Yêu cầu chức năng (FR)

### FR-LOGIN-01: Hiển thị trang đăng nhập
- Khi truy cập `/admin/authentication`, hệ thống hiển thị form gồm Email, Password, Remember me, nút Login, liên kết Forgot Password. **[Quan sát]**
- Con trỏ tự động focus vào ô Email. **[Quan sát]**
- Nếu người dùng **đã đăng nhập** mà truy cập lại trang này, hệ thống chuyển hướng đến Dashboard `/admin`. **[Giả định – cần xác minh]**

### FR-LOGIN-02: Đăng nhập bằng Email/Mật khẩu
| Mục | Mô tả |
|---|---|
| Tiền điều kiện | Người dùng có tài khoản Staff ở trạng thái hoạt động |
| Đầu vào | `email` (định dạng email), `password`, `remember` (tùy chọn), `csrf_token_name` |
| Xử lý | Gửi POST tới `/admin/authentication`; hệ thống kiểm tra CSRF, so khớp email/mật khẩu (mật khẩu được lưu dạng băm), kiểm tra tài khoản đang active |
| Thành công | Tạo phiên đăng nhập và chuyển hướng tới Dashboard khu vực `/admin` **[Giả định – cần xác minh]** |
| Thất bại | Ở lại trang Login và hiển thị thông báo lỗi chung (không tiết lộ email hay mật khẩu sai) **[Giả định – cần xác minh]** |

**Dữ liệu test đã được cung cấp (môi trường demo):**
| Email | Mật khẩu | Vai trò |
|---|---|---|
| `admin@example.com` | `123456` | Admin (tài khoản demo công khai) |

> Lưu ý: thông tin này chỉ dùng cho môi trường demo; không dùng mật khẩu yếu như vậy trong môi trường production.

### FR-LOGIN-03: Kiểm tra dữ liệu đầu vào (Validation)
| Trường | Quy tắc | Nguồn |
|---|---|---|
| Email | Bắt buộc; đúng định dạng email | Định dạng: trình duyệt (`type=email`) **[Quan sát]**; "bắt buộc" do server kiểm tra **[Giả định – cần xác minh]** (form không có `required`) |
| Password | Bắt buộc | **[Giả định – cần xác minh]** (phía server) |
| Remember me | Tùy chọn (boolean) | **[Quan sát]** |

- Khi để trống Email và/hoặc Password, hệ thống không đăng nhập và hiển thị thông báo lỗi yêu cầu nhập. **[Giả định – cần xác minh]**
- Vì không có `required`/`maxlength` ở client, việc xác thực dữ liệu **phải** được thực hiện ở server. **[Khuyến nghị]**

### FR-LOGIN-04: Ghi nhớ đăng nhập (Remember me)
- Khi chọn, hệ thống lưu cookie/phiên dài hạn để người dùng không cần đăng nhập lại khi mở lại trình duyệt. **[Giả định – cần xác minh]**
- Khi không chọn, phiên kết thúc theo thời hạn phiên mặc định/đóng trình duyệt. **[Giả định – cần xác minh]**

### FR-LOGIN-05: Quên mật khẩu
| Mục | Mô tả |
|---|---|
| Điểm vào | Liên kết "Forgot Password?" tại trang Login **[Quan sát]** |
| Đầu vào | `email` + CSRF token |
| Xử lý | POST tới `/admin/authentication/forgot_password`; nếu email tồn tại, gửi email chứa liên kết đặt lại mật khẩu **[Giả định – cần xác minh]** |
| Phản hồi | Hiển thị thông báo kết quả (thành công/lỗi email không hợp lệ hoặc không tồn tại) **[Giả định – cần xác minh]** |

### FR-LOGIN-06: Bảo vệ chống CSRF
- Mọi form POST (Login, Forgot Password) chứa `csrf_token_name` hợp lệ; yêu cầu thiếu/sai token bị từ chối. **[Quan sát: token hiện diện; hành vi từ chối là giả định]**

### FR-LOGIN-07: Điều hướng
| Từ | Hành động | Đến |
|---|---|---|
| Login | Nhấn logo | `/` (trang chủ) **[Quan sát]** |
| Login | Nhấn "Forgot Password?" | `/admin/authentication/forgot_password` **[Quan sát]** |
| Login | Đăng nhập thành công | `/admin` **[Giả định – cần xác minh]** |

---

## 5. Luồng nghiệp vụ

### 5.1 Luồng chính – Đăng nhập thành công
1. Người dùng mở `/admin/authentication`.
2. Nhập Email hợp lệ và Mật khẩu đúng.
3. (Tùy chọn) Tích "Remember me".
4. Nhấn **Login**.
5. Hệ thống xác thực thành công → chuyển tới Dashboard.

### 5.2 Luồng thay thế / ngoại lệ
| Mã | Tình huống | Kết quả mong đợi |
|---|---|---|
| E1 | Email/Password trống | Không đăng nhập; báo lỗi yêu cầu nhập |
| E2 | Email sai định dạng | Trình duyệt chặn gửi (HTML5 `type=email`) |
| E3 | Email không tồn tại hoặc mật khẩu sai | Ở lại trang Login; thông báo lỗi chung |
| E4 | Tài khoản bị vô hiệu hóa | Không đăng nhập; thông báo tài khoản không hoạt động |
| E5 | CSRF token hết hạn/sai | Yêu cầu bị từ chối / tải lại trang |
| E6 | Đã đăng nhập, truy cập lại trang Login | Chuyển hướng tới Dashboard |

(Toàn bộ mục E1–E6 là **[Giả định – cần xác minh]**, trừ E2 là hành vi chuẩn của trình duyệt.)

### 5.3 Luồng Quên mật khẩu
1. Từ trang Login nhấn **Forgot Password?**.
2. Nhập email tài khoản → nhấn **Confirm**.
3. Hệ thống gửi email hướng dẫn đặt lại mật khẩu và hiển thị thông báo.

---

## 6. Yêu cầu phi chức năng (NFR)

| Mã | Loại | Yêu cầu |
|---|---|---|
| NFR-01 | Bảo mật | Truyền dữ liệu qua HTTPS **[Quan sát: site chạy HTTPS]** |
| NFR-02 | Bảo mật | Mật khẩu được ẩn khi nhập (`type=password`) **[Quan sát]**; lưu dạng băm ở server |
| NFR-03 | Bảo mật | Thông báo lỗi không tiết lộ email có tồn tại hay không (chống dò tài khoản) |
| NFR-04 | Bảo mật | Giới hạn số lần đăng nhập sai / chống brute-force (khóa tạm thời hoặc CAPTCHA) – **chưa thấy trên giao diện, cần xác minh** |
| NFR-05 | Bảo mật | Cookie phiên có cờ `Secure`, `HttpOnly`, `SameSite` |
| NFR-06 | Khả dụng | Giao diện responsive, hiển thị tốt trên desktop, tablet, mobile |
| NFR-07 | Khả dụng | Hỗ trợ phím Enter để gửi form; focus tự động ở ô Email **[Quan sát]** |
| NFR-08 | Hiệu năng | Trang Login tải < 3 giây trong điều kiện mạng bình thường |
| NFR-09 | Tương thích | Chrome, Firefox, Edge, Safari bản mới nhất |
| NFR-10 | Trợ năng | Mỗi input có `label` gắn với trường tương ứng **[Quan sát]** |
| NFR-11 | Ngôn ngữ | Giao diện tiếng Anh (`lang="en"`) **[Quan sát]** |

---

## 7. Quy tắc nghiệp vụ

| Mã | Quy tắc |
|---|---|
| BR-01 | Chỉ tài khoản Staff đang hoạt động mới được đăng nhập vào `/admin`. |
| BR-02 | Email là định danh duy nhất để đăng nhập. |
| BR-03 | Thông báo lỗi đăng nhập là thông báo chung, không chỉ rõ sai email hay mật khẩu. |
| BR-04 | Mật khẩu không bao giờ hiển thị dạng rõ hoặc được gửi qua URL/query string. |

---

## 8. Ma trận truy vết & gợi ý kiểm thử

| Yêu cầu | Gợi ý test case |
|---|---|
| FR-LOGIN-01 | TC: Hiển thị đủ thành phần; auto-focus Email; redirect khi đã đăng nhập |
| FR-LOGIN-02 | TC: Đăng nhập đúng; sai mật khẩu; sai email; email viết hoa/thường; khoảng trắng đầu/cuối |
| FR-LOGIN-03 | TC: Trống cả hai; trống một trường; email sai định dạng; chuỗi rất dài; ký tự đặc biệt; SQL injection / XSS |
| FR-LOGIN-04 | TC: Đăng nhập có/không tích Remember me → đóng/mở lại trình duyệt |
| FR-LOGIN-05 | TC: Email tồn tại; không tồn tại; sai định dạng; trống |
| FR-LOGIN-06 | TC: Gửi form khi thiếu/sai CSRF token |
| FR-LOGIN-07 | TC: Kiểm tra từng liên kết điều hướng |
| NFR-04 | TC: Nhập sai nhiều lần liên tiếp, quan sát cơ chế khóa/CAPTCHA |

---

## 9. Điểm cần xác minh / Câu hỏi mở

1. Nội dung chính xác các thông báo lỗi (đăng nhập sai, trống trường, tài khoản bị khóa).
2. Trang đích sau khi đăng nhập thành công và hành vi `redirect` khi vào URL đã bảo vệ trước khi đăng nhập.
3. Có cơ chế giới hạn đăng nhập sai / CAPTCHA / 2FA không?
4. Thời hạn của phiên và của cookie "Remember me".
5. Nội dung/thời hạn email đặt lại mật khẩu và thông báo khi email không tồn tại.
6. Form chưa có `required`, `autocomplete`, `maxlength` ở phía client – có cần bổ sung không?

---

## 10. Lịch sử thay đổi

| Phiên bản | Ngày | Nội dung | Tác giả |
|---|---|---|---|
| 1.0 | 2026-11-10 | Khởi tạo SRS dựa trên khảo sát giao diện trang Login và Forgot Password | Claude Code |
