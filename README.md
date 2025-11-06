## 2. NỘI DUNG BÀI TẬP:
### 2.1. Cài đặt Apache web server:
- Vô hiệu hoá IIS: nếu iis đang chạy thì mở cmd quyền admin để chạy lệnh: iisreset /stop
- Download apache server, giải nén ra ổ D, cấu hình các file:
  + D:\Apache24\conf\httpd.conf
  + D:Apache24\conf\extra\httpd-vhosts.conf
  để tạo website với domain: fullname.com
  code web sẽ đặt tại thư mục: `D:\Apache24\fullname` (fullname ko dấu, liền nhau)
- sử dụng file `c:\WINDOWS\SYSTEM32\Drivers\etc\hosts` để fake ip 127.0.0.1 cho domain này
  ví dụ sv tên là: `Đỗ Duy Cốp` thì tạo website với domain là fullname ko dấu, liền nhau: `doduycop.com`
- thao tác dòng lệnh trên file `D:\Apache24\bin\httpd.exe` với các tham số `-k install` và `-k start` để cài đặt và khởi động web server apache.
### 2.2. Cài đặt nodejs và nodered => Dùng làm backend:
- Cài đặt nodejs:
  + download file `https://nodejs.org/dist/v20.19.5/node-v20.19.5-x64.msi`  (đây ko phải bản mới nhất, nhưng ổn định)
  + cài đặt vào thư mục `D:\nodejs`
- Cài đặt nodered:
  + chạy cmd, vào thư mục `D:\nodejs`, chạy lệnh `npm install -g --unsafe-perm node-red --prefix "D:\nodejs\nodered"`
  + download file: https://nssm.cc/release/nssm-2.24.zip
    giải nén được file nssm.exe
    copy nssm.exe vào thư mục `D:\nodejs\nodered\`
  + tạo file "D:\nodejs\nodered\run-nodered.cmd" với nội dung (5 dòng sau):
@echo off
REM fix path
set PATH=D:\nodejs;%PATH%
REM Run Node-RED
node "D:\nodejs\nodered\node_modules\node-red\red.js" -u "D:\nodejs\nodered\work" %*
  + mở cmd, chuyển đến thư mục: `D:\nodejs\nodered`
  + cài đặt service `a1-nodered` bằng lệnh: nssm.exe install a1-nodered "D:\nodejs\nodered\run-nodered.cmd"
  + chạy service `a1-nodered` bằng lệnh: `nssm start a1-nodered`
### 2.3. Tạo csdl tuỳ ý trên mssql (sql server 2022), nhớ các thông số kết nối: ip, port, username, password, db_name, table_name
### 2.4. Cài đặt thư viện trên nodered:
- truy cập giao diện nodered bằng url: http://localhost:1880
- cài đặt các thư viện: node-red-contrib-mssql-plus, node-red-node-mysql, node-red-contrib-telegrambot, node-red-contrib-moment, node-red-contrib-influxdb, node-red-contrib-duckdns, node-red-contrib-cron-plus
- Sửa file `D:\nodejs\nodered\work\settings.js` : 
  tìm đến chỗ adminAuth, bỏ comment # ở đầu dòng (8 dòng), thay chuỗi mã hoá mật khẩu bằng chuỗi mới
    adminAuth: {
        type: "credentials",
        users: [{
            username: "admin",
            password: "chuỗi_mã_hoá_mật_khẩu",
            permissions: "*"
        }]
    },   
   với mã hoá mật khẩu có thể thiết lập bằng tool: https://tms.tnut.edu.vn/pw.php
- chạy lại nodered bằng cách: mở cmd, vào thư mục `D:\nodejs\nodered` và chạy lệnh `nssm restart a1-nodered`
  khi đó nodered sẽ yêu cầu nhập mật khẩu mới vào được giao diện cho admin tại: http://localhost:1880
### 2.5. tạo api back-end bằng nodered:
- tại flow1 trên nodered, sử dụng node `http in` và `http response` để tạo api
- thêm node `MSSQL` để truy vấn tới cơ sở dữ liệu
- logic flow sẽ gồm 4 node theo thứ tự sau (thứ tự nối dây): 
  1. http in  : dùng GET cho đơn giản, URL đặt tuỳ ý, ví dụ: /timkiem
  2. function : để tiền xử lý dữ liệu gửi đến
  3. MSSQL: để truy vấn dữ liệu tới CSDL, nhận tham số từ node tiền xử lý
  4. http response: để phản hồi dữ liệu về client: Status Code=200, Header add : Content-Type = application/json
  có thể thêm node `debug` để quan sát giá trị trung gian.
- test api thông qua trình duyệt, ví dụ: http://localhost:1880/timkiem?q=thị
### 2.6. Tạo giao diện front-end:
- html form gồm các file : index.html, fullname.js, fullname.css
  cả 3 file này đặt trong thư mục: `D:\Apache24\fullname`
  nhớ thay fullname là tên của bạn, viết liền, ko dấu, chữ thường, vd tên là Đỗ Duy Cốp thì fullname là `doduycop`
  khi đó 3 file sẽ là: index.html, doduycop.js và doduycop.css
- index.html và fullname.css: trang trí tuỳ ý, có dấu ấn cá nhân, có form nhập được thông tin.
- fullname.js: lấy dữ liệu trên form, gửi đến api nodered đã làm ở bước 2.5, nhận về json, dùng json trả về để tạo giao diện phù hợp với kết quả truy vấn của bạn.
### 2.7. Nhận xét bài làm của mình:
- đã hiểu quá trình cài đặt các phần mềm và các thư viện như nào?
- đã hiểu cách sử dụng nodered để tạo api back-end như nào?
- đã hiểu cách frond-end tương tác với back-end ra sao?
-------------------------------------------------------------------
## BÀI LÀM
### CÀI ĐẶT APACHE WEB SERVER
#### B1. Vô hiệu hóa IIS
- Mở Command Prompt (Run as Administrator)
- Gõ lệnh:
`iisreset /stop`
- Nếu xuất hiện dòng Internet services successfully stopped → OK.

<img width="1920" height="1080" alt="Screenshot (2)" src="https://github.com/user-attachments/assets/faab871a-4fd1-44ea-9773-eb7f2634759d" />

#### B2. Tải và giải nén Apache
- Truy cập: `https://www.apachelounge.com/download/`
- Tải bản mới nhất, ví dụ: `httpd-2.4.58-win64-VS17.zip`

<img width="1920" height="1080" alt="Screenshot (3)" src="https://github.com/user-attachments/assets/38f84015-91d8-479b-8b2b-b217b3534040" />

- Giải nén vào: G:\Apache24

<img width="1920" height="1080" alt="Screenshot (5)" src="https://github.com/user-attachments/assets/d81d7415-c414-4360-99be-1a8c4ecb8633" />

Kiểm tra thư mục tồn tại:
`G:\Apache24\bin
 G:\Apache24\conf
 G:\Apache24\htdocs`

#### B3. Chỉnh file cấu hình chính
- Mở file:
  `G:\Apache24\conf\httpd.conf`
- Tìm và sửa các dòng:
  - `Define SRVROOT` thành `Define SRVROOT "G:/Apache24"` để thay đường dẫn gốc 
  - Bỏ dấu *#* ở trước dòng `Include conf/extra/httpd-vhosts.conf` để bật VirtualHost

<img width="1920" height="1080" alt="Screenshot (7)" src="https://github.com/user-attachments/assets/8ece0395-f707-42fc-8b1d-50ebd92aeae8" />

<img width="1920" height="1080" alt="Screenshot (8)" src="https://github.com/user-attachments/assets/d1513e32-4b2e-47d1-b4ea-f7bdde6ffddf" />

#### B4. Cấu hình VirtualHost
- Mở: `G:\Apache24\conf\extra\httpd-vhosts.conf`
- Xóa nội dung cũ, thêm đoạn sau:

<img width="1920" height="1080" alt="Screenshot (9)" src="https://github.com/user-attachments/assets/124f3544-8cf7-4ca5-bc60-bebadf37b5cd" />

#### B5. Tạo thư mục web
- Tạo thư mục:
`G:\Apache24\haunhatninh`

<img width="1920" height="1080" alt="Screenshot (10)" src="https://github.com/user-attachments/assets/6743e5e0-807b-49b4-9cbe-e6ec8b7b8ee8" />
 
#### B6. Thêm domain ảo
- Mở file bằng Notepad (Run as Administrator):
`C:\Windows\System32\drivers\etc\hosts`
- Thêm dòng cuối:
`127.0.0.1    haunhatninh.com`
- Lưu lại. 

<img width="1920" height="1080" alt="Screenshot (13)" src="https://github.com/user-attachments/assets/5ccb16b6-3ce9-48e2-89aa-399f538e5ad1" />

#### B7. Cài đặt và khởi động Apache
- Mở CMD (Run as Administrator):
`cd /d G:\Apache24\bin
httpd.exe -k install
httpd.exe -k start`

<img width="1920" height="1080" alt="Screenshot (14)" src="https://github.com/user-attachments/assets/56621820-73e0-4716-9c5a-ff2a2d5cd4ab" />

- Kiểm tra:
  - Mở trình duyệt → truy cập: http://haunhatninh.com
  - Nếu thấy chữ “It works!” hoặc lỗi 403 → Apache chạy thành công.

### CÀI NODE.JS VÀ NODE-RED
#### B1. Cài Node.js
- Tải file: `node-v20.19.5-x64.msi`

<img width="1920" height="1080" alt="Screenshot (16)" src="https://github.com/user-attachments/assets/9e083e25-a7fe-4743-b4e9-5e292e1c0b54" />

- Cài đặt → Chọn đường dẫn: `G:\nodejs`
- Sau khi cài, mở CMD:
`node -v
npm -v`

<img width="1920" height="1080" alt="Screenshot (17)" src="https://github.com/user-attachments/assets/5f6d4017-d4f7-4dea-930f-c8ca0bf0d24b" />

- Nếu hiện version → OK.

#### B2. Cài Node-RED
- Mở CMD:
`cd /d G:\nodejs
npm install -g --unsafe-perm node-red --prefix "G:\nodejs\nodered"`

<img width="1920" height="1080" alt="Screenshot (18)" src="https://github.com/user-attachments/assets/5241e9e6-8f33-4b43-a73a-42908e8a5d76" />

<img width="1920" height="1080" alt="Screenshot (19)" src="https://github.com/user-attachments/assets/bbcaf0c1-375d-4ea4-ac4b-ee102f6ca637" />

#### B3. Cài nssm (service manager)
- Tải: `https://nssm.cc/release/nssm-2.24.zip`
- Giải nén → copy file nssm.exe vào:
`G:\nodejs\nodered\`

<img width="1920" height="1080" alt="Screenshot (20)" src="https://github.com/user-attachments/assets/7b66fe06-e881-4bac-b1d4-e1941e6e0b26" />

<img width="1920" height="1080" alt="Screenshot (21)" src="https://github.com/user-attachments/assets/f703fce1-2a5f-46e7-b4d4-f2d9310b2a16" />

#### B4. Tạo file chạy Node-RED
- Tạo file mới:
`G:\nodejs\nodered\run-nodered.cmd`
- Dán 5 dòng sau:
  `@echo off
   REM fix path
   set PATH=D:\nodejs;%PATH%
   REM Run Node-RED
   node "G:\nodejs\nodered\node_modules\node-red\red.js" -u "G:\nodejs\nodered\work" %*`

#### B5. Cài Node-RED làm service
- Mở CMD:
`cd /d G:\nodejs\nodered
nssm.exe install a1-nodered "G:\nodejs\nodered\run-nodered.cmd"`

<img width="1920" height="1080" alt="Screenshot (21)" src="https://github.com/user-attachments/assets/b211e03e-4b67-47db-b04e-bed57f451f27" />

- Sau đó chạy:
  `nssm start a1-nodered`
- Mở trình duyệt → truy cập:
  `http://localhost:1880`
- Nếu thấy giao diện Node-RED → thành công!

<img width="1920" height="1080" alt="Screenshot (23)" src="https://github.com/user-attachments/assets/04e73295-b60d-44f1-bcd0-667f623900a6" />

### 2.3. TẠO CƠ SỞ DỮ LIỆU SQL SERVER
- Mở SQL Server Management Studio (SSMS)
- Tạo Database mới, ví dụ:

<img width="1918" height="993" alt="image" src="https://github.com/user-attachments/assets/d1bb0764-3a55-4897-94db-ebf977b3cad0" />

- Ghi nhớ thông tin:
  `server: localhost
   port: 1433
   username: sa
   password: ......
   database: QL_CAFE_TNUT_FUN`

####2.4. CÀI THƯ VIỆN TRÊN NODE-RED
- Truy cập: `http://localhost:1880`
- Vào Menu → Manage palette → Install
- Tìm và cài các package sau:  
`node-red-contrib-mssql-plus
node-red-node-mysql
node-red-contrib-telegrambot
node-red-contrib-moment
node-red-contrib-influxdb
node-red-contrib-duckdns
node-red-contrib-cron-plus`

<img width="1920" height="1080" alt="Screenshot (27)" src="https://github.com/user-attachments/assets/1aa510be-4918-4708-80bb-7f6a75de972d" />

- Bổ sung: chỉnh mật khẩu admin
- Mở file: `G:\nodejs\nodered\work\settings.js`
- Bỏ dấu // và sửa thành:
`adminAuth: {
    type: "credentials",
    users: [{
        username: "admin",
        password: "sha256:b9c54f...chuỗi_mã_hóa...", 
        permissions: "*"
    }]
},`

<img width="1920" height="1080" alt="Screenshot (28)" src="https://github.com/user-attachments/assets/0285d3c9-07f5-494b-ab3a-8986e583d644" />

- Để tạo mật khẩu mã hóa: truy cập `https://tms.tnut.edu.vn/pw.php`
- Sau đó restart:
  `cd /d G:\nodejs\nodered
   nssm restart a1-nodered`

<img width="1920" height="1080" alt="Screenshot (28)" src="https://github.com/user-attachments/assets/6075731a-46c4-4023-9a37-f343063014d4" />

### 2.5. TẠO API BACKEND TRÊN NODE-RED
- Truy cập Node-RED:
`http://localhost:1880`
- Tạo Flow 1 → Kéo các node:
  1. http in  : dùng GET cho đơn giản, URL đặt tuỳ ý, ví dụ: /timkiem
  2. function : để tiền xử lý dữ liệu gửi đến
  3. MSSQL: để truy vấn dữ liệu tới CSDL, nhận tham số từ node tiền xử lý
  4. http response: để phản hồi dữ liệu về client: Status Code=200, Header add : Content-Type = application/json
  có thể thêm node `debug` để quan sát giá trị trung gian.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/70fe6312-9c4f-4a0c-95e7-e5c96fdffea6" />

- Test api thông qua trình duyệt, ví dụ: `http://localhost:1880/nhanvien`

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/592508c3-572f-4675-a715-fb9432be7924" />

### 2.6. TẠO GIAO DIỆN FRONT-END
- Trong thư mục:
`G:\Apache24\haunhatninh\`
- Tạo 3 file:

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/2fdd1c86-d8b9-4965-882e-5b471b821aa2" />

### KIỂM TRA TOÀN BỘ HỆ THỐNG
- Mở CMD:
`httpd.exe -k start
 nssm start a1-nodered`
- Mở trình duyệt:
`http://haunhatninh.com`
- Nhập thử tên đồ uống → Nhấn “Tìm kiếm”
- Hiện danh sách các loại cà phê → Hoàn thành!

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/45c9ed8e-9a4c-4da3-a3f2-4054d4f6c493" />

### Nhận xét bài làm của mình:
- Về quá trình cài đặt các phần mềm và thư viện
  - Những gì đã hiểu:
    - Cài đặt Node-RED: Đã nắm được cách cài đặt Node-RED thông qua npm, khởi động service và truy cập giao diện web tại http://localhost:1880
    - Cài đặt thư viện MSSQL: Hiểu được cần cài đặt package node-red-contrib-mssql-plus trong thư mục ~/.node-red để Node-RED có thể kết nối với SQL Server
  - Cấu hình SQL Server: Đã nắm được các bước:
    - Enable TCP/IP Protocol trong SQL Server Configuration Manager
    - Mở port 1433 cho kết nối từ xa
    - Cấu hình SQL Server Authentication mode
    - Tạo user và phân quyền truy cập database
    - Dependency giữa các thành phần: Hiểu được mối quan hệ:
    - SQL Server ← Node-RED (MSSQL node) ← Frontend (fetch API)

#### Khó khăn gặp phải:
- Ban đầu gặp lỗi kết nối SQL Server do chưa enable TCP/IP
- Lỗi "Trust Server Certificate" cần phải cấu hình đúng trong node MSSQL
- Phải khởi động lại Node-RED sau khi cài thư viện mới
#### Bài học kinh nghiệm:
- Luôn kiểm tra service SQL Server Browser đã chạy chưa
- Đọc kỹ error log trong Node-RED Debug tab để xác định lỗi
- Nên test connection trực tiếp trước khi build flow phức tạp
#### Về cách sử dụng Node-RED để tạo API back-end**
Những gì đã hiểu:
#### Cấu trúc cơ bản của một API endpoint:
`HTTP IN → Function (xử lý logic) → MSSQL → Function (format) → HTTP Response`

#### Các node quan trọng:
- HTTP IN node: Nhận request từ client với method GET/POST và URL endpoint
- Function node: Viết JavaScript để xây dựng SQL query động dựa trên query parameters
- MSSQL node: Thực thi câu lệnh SQL và trả về kết quả
- HTTP Response node: Gửi dữ liệu JSON về client

#### Ưu điểm của Node-RED:
- Visual programming - dễ hiểu luồng xử lý
- Không cần setup server phức tạp như Express.js
- Debug trực quan qua Debug tab
- Hot reload khi deploy changes

#### Hạn chế nhận thấy:
- Code JavaScript phân tán trong nhiều function nodes
- Khó quản lý khi flow phức tạp
- Không có TypeScript support
- Bảo mật còn hạn chế (chưa có authentication/authorization)

#### Về cách front-end tương tác với back-end
Những gì đã hiểu:
1. Sử dụng Fetch API:
  ` async function loadMenu() {
    const res = await fetch(`${API_BASE}/menu?search=${search}`);
    const data = await res.json();
    // Xử lý dữ liệu
}`

2. Luồng tương tác:
`
User Action → JavaScript Event → Fetch API Call → Node-RED API
    ↓
Node-RED SQL Query → SQL Server → Trả dữ liệu JSON
    ↓
Frontend nhận JSON → Parse data → Render HTML động`

3. Xử lý bất đồng bộ:
- Sử dụng async/await cho code dễ đọc hơn callbacks
- Xử lý loading state khi đang fetch data
- Try-catch để bắt lỗi kết nối

4. Dynamic HTML rendering:
  `data.forEach(item => {
    html += `<tr><td>${item.TenMon}</td>...</tr>`;
});
element.innerHTML = html;`

5. Query parameters:
- Sử dụng encodeURIComponent() để encode tham số có ký tự đặc biệt
- Build URL động: ?search=${value}&loai=${type}

6. Error handling:
   `try {
    const res = await fetch(url);
    if (!res.ok) throw new Error('API Error');
} catch (err) {
    showError('Không thể kết nối server');
}  `

#### Quy trình làm việc với data:
- User interaction: Click button, nhập search, chọn filter
- Client-side validation: Kiểm tra input (trim, validate)
- API call: Gửi request với parameters
- Server processing: Node-RED nhận, query SQL, trả về JSON
- Client rendering: Parse JSON, tạo HTML, cập nhật DOM
- Error feedback: Hiển thị loading, success, error states

Best practices đã áp dụng:
- Tách biệt API_BASE để dễ thay đổi endpoint
- Tạo các functions riêng cho từng chức năng (loadMenu, loadOrders...)
- Hiển thị loading indicator khi fetch data
- Xử lý trường hợp không có data (empty state)
- Format số tiền với toLocaleString()
- Format ngày tháng theo chuẩn Việt Nam

Hiệu suất:
-Mỗi tab load data riêng biệt, không load tất cả cùng lúc
- Cache dropdown options (loại món) để không query lại
- Debounce có thể thêm cho search input để giảm số lần gọi API

### Tổng kết
- Mức độ hoàn thành:
  - Cài đặt thành công môi trường phát triển: 100%
  - Tạo database với dữ liệu mẫu: 100%
  - Xây dựng API backend với Node-RED: 100%
  - Xây dựng giao diện frontend: 100%
  - Xử lý lỗi và bảo mật: 70% (còn cần cải thiện)

- Kỹ năng đã học được:

  - Thiết kế và triển khai REST API
  - Làm việc với SQL Server từ Node-RED
  - Xử lý dữ liệu bất đồng bộ với async/await
  - DOM manipulation và dynamic rendering
  - Debugging và troubleshooting

- Hướng phát triển tiếp theo:

  - Bảo mật: Thêm JWT authentication, validate input chống SQL injection
  - Tối ưu: Thêm caching, pagination cho dữ liệu lớn
  - UX: Loading skeleton, toast notification, confirm dialogs
  - Chức năng: CRUD operations (Create, Update, Delete)
  - Deployment: Cấu hình production environment, HTTPS, reverse proxy

## Kết luận:
Qua bài lab này, em đã hiểu rõ quy trình phát triển một ứng dụng web full-stack đơn giản từ A-Z, từ thiết kế database, xây dựng API backend với Node-RED, đến phát triển giao diện frontend tương tác với API. Quan trọng nhất là đã nắm được cách các thành phần trong hệ thống giao tiếp với nhau và cách debug khi gặp lỗi.
