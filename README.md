# Truyền file an toàn – Đề 6: Gửi bài tập chia làm nhiều phần

## Mô tả đề tài

Một giảng viên cần gửi file `assignment.txt` chứa bài tập lớn đến hệ thống chấm điểm. Để tối ưu truyền tải qua mạng băng thông hạn chế và đảm bảo an toàn, hệ thống yêu cầu:

- File được **chia thành 3 phần**
- Mỗi phần **mã hóa bằng DES**
- Metadata và nội dung được **ký số bằng RSA-1024 + SHA-512**
- Kiểm tra tính toàn vẹn bằng **SHA-512**
- Phản hồi về trạng thái nhận: **ACK hoặc NACK**

---

## Các kỹ thuật sử dụng

| Thành phần | Kỹ thuật |
|------------|----------|
| Mã hóa      | DES (CBC Mode) |
| Ký số       | RSA 1024-bit (PKCS#1 v1.5 + SHA-512) |
| Toàn vẹn    | SHA-512 |
| Giao diện   | Flask + Bootstrap |
| Trao đổi khóa | RSA Public/Private Key |

---

## Cấu trúc thư mục
![image](https://github.com/user-attachments/assets/4deeca0e-334e-44d5-8ee3-aa54bbde04ee)

---

## Luồng xử lý

### 1. Handshake
- Người gửi gửi `"Hello!"`
- Người nhận phản hồi `"Ready!"`

### 2. Ký số & trao khóa
- Người gửi ký metadata `{filename | timestamp | số phần}` bằng **RSA + SHA-512**
- Session Key dùng để mã hóa DES được mã hóa bằng **khóa công khai của người nhận**

### 3. Mã hóa và kiểm tra toàn vẹn
- File `assignment.txt` được chia thành **3 phần**
- Mỗi phần bao gồm:
```json
{
  "iv": "<Base64>",            
  "cipher": "<Base64>",        
  "hash": "<SHA-512 hex>",     
  "sig": "<Base64 chữ ký số>"  
}
```
### 4. Nhận và xác thực
Người nhận kiểm tra: Chữ ký metadata, Chữ ký từng phần và Hash toàn vẹn

Nếu hợp lệ: Giải mã từng phần, Ghép và lưu lại thành assignment.txt và Gửi ACK

Nếu không hợp lệ: Gửi NACK, chỉ rõ lỗi (hash sai, chữ ký sai,...)

---

Giao diện ứng dụng

Ứng dụng sử dụng Flask với giao diện hiện đại từ Bootstrap.

Trang chính

Người gửi

Người nhận
