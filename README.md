# BT2-QTCSDL
## Phần mở đầu
Thông tin cá nhân
- Tên : Hoàng Đình Điệp
- Lớp : K59KMT.K01
- MSSV : K235480106016
- 
**Yêu Cầu Đầu Bài**

- Đề tài: Quản lý khách sạn

Thực hiện xây dựng một hệ thống quản lý khách sạn hoàn chỉnh trên SQL Server, đáp ứng các tiêu chí cụ thể sau đây:

- Toàn bộ quá trình thực hiện phải được ghi lại thông qua các screenshot minh họa, mỗi bức ảnh đều đi kèm với câu lệnh SQL tương ứng và chú thích rõ ràng về chức năng, mục đích xử lý, cũng như kết quả đạt được.

- Bài tập được nộp dưới dạng repository GitHub (public), bao gồm hai thành phần chính: tệp README.md chứa toàn bộ nội dung, hình ảnh minh họa và giải thích chi tiết, tệp baikiemtra2.sql chứa toàn bộ mã SQL.

Đánh giá dựa trên ba yếu tố quan trọng:

- Logic xử lý dữ liệu — các câu lệnh có giải quyết đúng bài toán không

- Quy tắc đặt tên — các bảng, cột, hàm có tuân theo chuẩn bướu Lạc Đà không

- Commit history — quá trình phát triển có rõ ràng, có thể theo dõi được không. Deadline nộp bài là cuối kỳ, sinh viên cần hoàn thành và push lên GitHub trước ngày hết hạn.

**Giới Thiệu Về Hệ Thống Quản Lý Khách Sạn**

Xây dựng một hệ thống quản lý khách sạn (QuanLyKhachSan) từ nền tảng SQL Server, bao gồm các chức năng chủ yếu như quản lý thông tin khách hàng, quản lý dữ liệu phòng, và quản lý các lượt đặt phòng. Mỗi khách hàng đều có hồ sơ lưu trữ với các thông tin cá nhân, số điện thoại liên hệ, ngày sinh và điểm đánh giá; mỗi phòng trong khách sạn được phân loại theo loại phòng (Standard, Superior, Deluxe, Suite), có diện tích riêng biệt và giá thuê theo ngày; các lượt đặt phòng lưu giữ mối quan hệ giữa khách hàng và phòng, cùng với thời gian nhận-trả phòng và tiền cọc.

Toàn bộ bài làm được chia thành 5 phần chính theo thứ tự tăng độ phức tạp.

- Thiết Kế Cơ Sở Dữ Liệu — khởi tạo các bảng KhachHang, Phong, DatPhong với các ràng buộc toàn vẹn (Primary Key, Foreign Key, Check Constraint) và chèn dữ liệu mẫu.
- Xây Dựng Function — tạo các hàm tính toán như tính số ngày ở, tính doanh thu, tìm phòng trống; những hàm này giúp tái sử dụng logic và làm sạch mã.
- Xây Dựng Stored Procedure — tạo các thủ tục lưu trữ để xử lý các nghiệp vụ phức tạp như đặt phòng mới, tính hóa đơn, báo cáo doanh thu theo tháng.
- Trigger Xử Lý Nghiệp Vụ — định nghĩa các trigger tự động kích hoạt khi dữ liệu thay đổi (chẳng hạn, tự động cập nhật trạng thái phòng khi có đặt phòng mới, hoặc ghi log thay đổi).
- Dùng Cursor và Xử Lý Dữ Liệu — sử dụng cursor để duyệt từng bản ghi và thực hiện các xử lý tuần tự, đồng thời so sánh hiệu năng giữa phương pháp cursor và phương pháp set-based, từ đó rút ra kết luận về tối ưu hóa.

Trong quá trình thực hiện, sẽ thiết kế cơ sở dữ liệu với tên chuẩn: QuanLyKhachSan_K235480106022. Mỗi phần lệnh SQL viết ra sẽ đi kèm một ảnh screenshot chứa mã lệnh và kết quả thực thi, với các chú thích chi tiết: ảnh dùng để minh họa bước nào, câu lệnh giải quyết vấn đề gì, kết quả thể hiện thông tin gì. Thông qua bài tập này sẽ nắm vững các khái niệm cơ bản và nâng cao của SQL Server, từ DDL (Data Definition Language) đến DML (Data Manipulation Language), từ những truy vấn đơn giản đến những xử lý phức tạp bằng Function, Procedure, Trigger, và Cursor, từ đó tích lũy kỹ năng thiết kế và quản trị cơ sở dữ liệu chuyên nghiệp.

## Phần 1 : Thiết kế và Khởi tạo Cấu trúc Dữ liệu
- Tên db : QuanlyKhachSan_K235480106016
<img width="1920" height="1080" alt="Screenshot (74)" src="https://github.com/user-attachments/assets/8c3e67b1-0c1a-4afe-bb4c-28d7125a4a10" />
*Ảnh này cho thấy tạo tạo cơ sở dữ liệu QuanlyKhachSan_K235480106016

### Mô tả logic

Bài toán quản lý thư viện với 3 bảng chính:
- **LoaiPhong**: Định nghĩa các hạng phòng (Standard, VIP, Deluxe) và giá niêm yết.
- **Phong**: Danh sách các phòng vật lý cụ thể, liên kết với loại phòng.
- **PhieuDatPhong**: Ghi nhận thông tin khách hàng đến thuê, thời gian ở và tổng hợp đánh giá.

Cấu trúc chi tiết từng bảng

#### Bảng 1: Loaiphong (Loại phòng)
| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---------|--------------|-----------|-------------|
| MaLoaiPhong | INT | PRIMARY KEY | Mã loại phòng (Tự tăng 1, 2, 3...) |
| TenLoaiPhong | NVARCHAR(50) | NOT NULL | Tên hạng phòng (VD: Phòng VIP Đơn) |
| GiaThueTheoNgay | MONEY | CHECK >= 0 | Giá thuê mặc định mỗi ngày |
| MoTa | NVARCHAR(200) | NULL | Mô tả tiện nghi phòng |

#### Bảng 2: Phong (Thông tin các phòng)
| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---------|--------------|-----------|-------------|
| MaPhong | INT | PRIMARY KEY | Mã định danh phòng |
| SoPhong | NVARCHAR(10) | NOT NULL, UNIQUE | Số phòng hiển thị (VD: P101, P202) |
| MaLoaiPhong | INT | PRIMARY KEY | Liên kết tới bảng (LoaiPhong) |
| Tang | INT | CHECK > 0 | Vị trí tầng của phòng |
| TrangThai | NVARCHAR(20) | DEFAULT N'Trống' | Trạng thái: Trống, Có khách, Đang sửa |
| SoDienThoai | VARCHAR(15) | NULL | Số điện thoại liên lạc |

#### Bảng 3: PhieuDatPhong (Thông tin thuê phòng)

| Tên cột | Kiểu dữ liệu | Ràng buộc | Giải thích |
|---------|--------------|-----------|-------------|
| MaPhieu | INT | PRIMARY KEY | Mã phiếu đặt tự tăng |
| HoTenKhach | NVARCHAR(100) | NOT NULL | Họ tên người đại diện thuê |
| MaPhong | INT | PRIMARY KEY | Phòng được thuê |
| NgayCheckIn | DATETIME | DEFAULT GETDATE() | Ngày giờ khách nhận phòng |
| SoNgayO | INT | CHECK > 0 | Số ngày khách đăng ký ở |
| DiemDanhGia | FLOAT | CHECK (0-10) | Điểm hài lòng của khách sau khi trả phòng |

####  Các kiểu dữ liệu được sử dụng

| Kiểu dữ liệu | Mục đích | Ví dụ sử dụng trong bài |
|--------------|----------|------------------------|
| VARCHAR(10) | Mã số ngắn (cố định độ dài) | MaLoaiPhong, MaPhong, MaPhieu |
| NVARCHAR(200) | Chuỗi Unicode có độ dài thay đổi | TenLoaiPhong, HoTenKhach, MoTa |
| INT | Số nguyên | Tang, SoNgayO |
| DATETIME | Lưu cả ngày và giờ | NgayCheckIn |
| MONEY | Dữ liệu tiền tệ | GiaThue |
| FLOAT | Số thực (số thập phân) | DiemDanhGia |

#### Tổng hợp các ràng buộc

**PRIMARY KEY (3 khóa):**

- `LoaiPhong.MaLoaiPhong` - Mã loại phòng duy nhất.
- `Phong.MaPhong` - Mã phòng duy nhất.
- `PhieuDatPhong.MaPhieu` - Mã phiếu đặt duy nhất.

**FOREIGN KEY (2 khóa):**

- `Phong.MaLoaiPhong` → `LoaiPhong.MaLoaiPhong` (Phòng phải thuộc loại phòng hợp lệ).
- `PhieuDatPhong.MaPhong` → `Phong.MaPhong` (Không đặt được phòng không tồn tại).

**CHECK (5 ràng buộc):**

- `GiaThue >= 0` - Đơn giá phòng không được âm.
- `Tang BETWEEN 1 AND 50` - Số tầng thực tế của khách sạn.
- `SoNgayO > 0` - Số ngày lưu trú tối thiểu là 1.
- `DiemDanhGia BETWEEN 0 AND 10` - Điểm đánh giá trong thang điểm 10.
- `TrangThai IN (N'Trống', N'Có khách', N'Đang sửa')` - Chỉ chấp nhận 3 giá trị cụ thể.

**DEFAULT (2 ràng buộc):**

- `TrangThai DEFAULT N'Trống'` - Phòng mới mặc định là phòng trống.
- `NgayCheckIn DEFAULT GETDATE()` - Tự động lấy giờ hiện tại khi lập phiếu.

```sql

USE [master];
GO
IF EXISTS (SELECT * FROM sys.databases WHERE name = 'QuanLyKhachSan_K235480106016')
    DROP DATABASE [QuanLyKhachSan_K235480106016];
GO
CREATE DATABASE [QuanLyKhachSan_K235480106016];
GO
USE [QuanLyKhachSan_K235480106016];
GO

CREATE TABLE [LoaiPhong] (
    [MaLoaiPhong] VARCHAR(10) PRIMARY KEY, 
    [TenLoaiPhong] NVARCHAR(50) NOT NULL, 
    [GiaThue] MONEY CONSTRAINT [CK_GiaThue] CHECK ([GiaThue] >= 0), 
    [MoTa] NVARCHAR(200) NULL
);


CREATE TABLE [Phong] (
    [MaPhong] VARCHAR(10) PRIMARY KEY,
    [SoPhong] VARCHAR(10) NOT NULL UNIQUE,
    [MaLoaiPhong] VARCHAR(10) NOT NULL, 
    [Tang] INT CONSTRAINT [CK_Tang] CHECK ([Tang] >= 1 AND [Tang] <= 50), 
    [TrangThai] NVARCHAR(20) DEFAULT N'Trống',
    CONSTRAINT [FK_Phong_Loai] FOREIGN KEY ([MaLoaiPhong]) REFERENCES [LoaiPhong]([MaLoaiPhong]),
    CONSTRAINT [CK_TrangThai] CHECK ([TrangThai] IN (N'Trống', N'Có khách', N'Đang sửa')) 
);


CREATE TABLE [PhieuDatPhong] (
    [MaPhieu] VARCHAR(10) PRIMARY KEY, 
    [HoTenKhach] NVARCHAR(100) NOT NULL, 
    [MaPhong] VARCHAR(10) NOT NULL, 
    [NgayCheckIn] DATETIME DEFAULT GETDATE(), 
    [SoNgayO] INT CONSTRAINT [CK_SoNgayO] CHECK ([SoNgayO] > 0), 
    [DiemDanhGia] FLOAT CONSTRAINT [CK_Diem] CHECK ([DiemDanhGia] >= 0 AND [DiemDanhGia] <= 10), 
    CONSTRAINT [FK_Phieu_Phong] FOREIGN KEY ([MaPhong]) REFERENCES [Phong]([MaPhong])
);
GO

```

Kết quả
<img width="1920" height="1080" alt="Screenshot (82)" src="https://github.com/user-attachments/assets/0a23fc7f-5bcd-43f4-94ad-2b72600759e4" />
*Ảnh này cho thấy tôi đã tạo thành công 3 bảng với các kiểu dữ liệu đúng yêu cầu

Thực thi các câu lệnh CREATE TABLE để tạo đồng thời 3 bảng: [LoaiPhong], [Phong], [PhieuDatPhong] trong database QuanLyKhachSan_K235480106016, bao gồm các ràng buộc PRIMARY KEY, FOREIGN KEY, CHECK, DEFAULT.

Bài toán quản lý khách sạn với 3 bảng chính có quan hệ chặt chẽ với nhau:
- [LoaiPhong]: Lưu thông tin các hạng phòng (mã loại là khóa chính, đơn giá thuê kiểu tiền tệ, đơn giá không được âm).
- [Phong]: Lưu thông tin chi tiết từng phòng vật lý (mã phòng là khóa chính, số tầng phải hợp lệ, trạng thái mặc định là Trống).
- [PhieuDatPhong]: Làm bảng trung gian kết nối giữa phòng và khách hàng thông qua khóa ngoại, quản lý thời gian lưu trú và điểm đánh giá dịch vụ (điểm từ 0..10).

## Phần 2 : Xây dựng Function 
**1. Tổng quan về Built-in Function**

Trong SQL Server, Built-in functions được chia thành nhiều nhóm dựa trên kiểu dữ liệu mà chúng xử lý:

- Hàm toán học:` ABS, ROUND, CEILING, FLOOR` .
- Hàm chuỗi:` LEN, UPPER, LOWER, SUBSTRING, REPLACE` .
- Hàm ngày tháng:` GETDATE, DATEDIFF, DATEADD, YEAR, MONTH `.
- Hàm tổng hợp (Aggregate):` SUM, AVG, COUNT, MAX, MIN `.
- Hàm hệ thống (System Functions): Các hàm kiểm tra thông tin vận hành của Database.

Một vài System Function đặc sắc:
- ` ISNULL (check_expression, replacement_value)` : Cực kỳ hữu dụng để xử lý dữ liệu rác/trống. Nếu trường dữ liệu bị NULL, nó sẽ ngay lập tức thay thế bằng một giá trị mặc định để tránh lỗi tính toán.
- ` DATEDIFF(interval, start, end)` : "Xương sống" của các bài toán quản lý thời gian, giúp tính chính xác khoảng cách giữa hai thời điểm (ngày, giờ, phút).
- ` FORMAT()`: Hàm này rất "nghệ thuật" vì nó cho phép định dạng tiền tệ hoặc ngày tháng theo chuẩn từng quốc gia (ví dụ: định dạng tiền VNĐ).

**SQL khai thác:**

```sql
SELECT 
    [HoTenKhach], 
    ISNULL([DiemDanhGia], 0) AS [DiemSo], 
    FORMAT([GiaThueTheoNgay], 'C', 'vi-VN') AS [GiaTienVND] 
FROM [PhieuDatPhong] p JOIN [Phong] r ON p.[MaPhong] = r.[MaPhong] JOIN [LoaiPhong] l ON r.[MaLoaiPhong] = l.[MaLoaiPhong];
```

kết quả
<img width="1920" height="1080" alt="Screenshot (76)" src="https://github.com/user-attachments/assets/e8288ab0-f422-4390-ba99-479da0d95db2" />

**2. User-Defined Functions (Hàm do người dùng tự viết)**
**Tại sao cần tự viết hàm khi đã có System Function?**
Dù SQL Server rất mạnh, nhưng nó không thể biết "logic nghiệp vụ" riêng biệt của từng doanh nghiệp. Ví dụ: System function không có hàm` fn_TinhGiamGiaChoKhachVip` hay `fn_KiemTraPhongTrong` . Việc tự viết giúp:

- **Tái sử dụng:** Viết một lần, dùng ở mọi nơi (Select, Where, Join).

- **Gói gọn logic:** Giúp câu lệnh SQL chính trông sạch sẽ, dễ đọc hơn.

**Các loại hàm tự viết:**
**1. Scalar Function (Hàm đơn trị):** Trả về một giá trị duy nhất (số, chuỗi, ngày). Dùng khi cần tính toán một con số cụ thể tại mỗi dòng dữ liệu.

**2. Inline Table-Valued Function (Hàm bảng đơn giản):** Trả về một tập bản ghi (bảng) nhưng chỉ gồm một câu lệnh SELECT. Hiệu năng rất cao, dùng như một View có tham số.

**3. Multi-statement Table-Valued Function (Hàm bảng phức tạp):** Trả về một bảng nhưng bên trong có logic phức tạp (IF...ELSE, vòng lặp, biến tạm). Dùng khi cần xử lý nhiều bước trước khi ra kết quả cuối.

**Chèn dữ liệu**

```sql

INSERT INTO [LoaiPhong] VALUES 
('LP001', N'Standard', 500000, N'Phòng tiêu chuẩn'),
('LP002', N'Superior', 800000, N'Phòng cao cấp'),
('LP003', N'Deluxe', 1500000, N'Phòng sang trọng'),
('LP004', N'Suite', 3000000, N'Hạng VIP cực sang');


DECLARE @iP INT = 1;
WHILE @iP <= 20
BEGIN
    DECLARE @T INT = (@iP - 1) / 5 + 1;
    DECLARE @MP VARCHAR(10) = 'P' + CAST(@T AS VARCHAR) + RIGHT('0' + CAST(((@iP - 1) % 5) + 1 AS VARCHAR), 2);
    DECLARE @ML VARCHAR(10) = 'LP00' + CAST((ABS(CHECKSUM(NEWID())) % 4 + 1) AS VARCHAR);
    INSERT INTO [Phong] VALUES (@MP, RIGHT(@MP, 3), @ML, @T, N'Trống');
    SET @iP = @iP + 1;
END;


DECLARE @iDP INT = 1;
WHILE @iDP <= 100
BEGIN
    DECLARE @H NVARCHAR(50) = CHOOSE(ABS(CHECKSUM(NEWID())) % 5 + 1, N'Nguyễn', N'Trần', N'Lê', N'Phạm', N'Lâm');
    DECLARE @T_Khach NVARCHAR(50) = CHOOSE(ABS(CHECKSUM(NEWID())) % 5 + 1, N'An', N'Bình', N'Dũng', N'Linh', N'Hòa');
    DECLARE @MaP_R VARCHAR(10); SELECT TOP 1 @MaP_R = MaPhong FROM [Phong] ORDER BY NEWID();

    INSERT INTO [PhieuDatPhong] ([MaPhieu], [HoTenKhach], [MaPhong], [NgayCheckIn], [SoNgayO], [DiemDanhGia])
    VALUES ('MP' + RIGHT('000' + CAST(@iDP AS VARCHAR), 4), @H + ' ' + @T_Khach, @MaP_R, 
            DATEADD(DAY, -ABS(CHECKSUM(NEWID())) % 365, GETDATE()), 
            (ABS(CHECKSUM(NEWID())) % 7) + 1, ROUND(RAND() * 5 + 5, 1));
    SET @iDP = @iDP + 1;
END;
GO
```
Kết quả chèn dữ liệu thành công
<img width="1920" height="1080" alt="Screenshot (83)" src="https://github.com/user-attachments/assets/3c09bf33-7972-4301-abae-b27d7dfa5160" />

**3. Thực hành viết Function cho QuanLyKhachSan_K235480106016**

**3.1. Scalar Function: Tính tổng tiền một phiếu đặt phòng**

```sql
CREATE OR ALTER FUNCTION [fn_TinhTienMotPhieu] (@MaPhieu VARCHAR(10))
RETURNS MONEY AS
BEGIN
    DECLARE @T MONEY;
    SELECT @T = p.[SoNgayO] * l.[GiaThue] FROM [PhieuDatPhong] p 
    JOIN [Phong] r ON p.[MaPhong] = r.[MaPhong] JOIN [LoaiPhong] l ON r.[MaLoaiPhong] = l.[MaLoaiPhong]
    WHERE p.[MaPhieu] = @MaPhieu;
    RETURN ISNULL(@T, 0);
END;
GO
-- Khai thác:
SELECT *, [dbo].[fn_TinhTienMotPhieu]([MaPhieu]) AS [TongTien] FROM [PhieuDatPhong];
```
Kết quả
<img width="1920" height="1080" alt="Screenshot (84)" src="https://github.com/user-attachments/assets/a670f667-cc33-46b9-adf8-bbdfde5fa21b" />
*Ảnh này tạo hàm fn_TinhTongTienMotPhieu và Một phiếu tiền phòng

**3.2. Inline Table-Valued Function: Tìm phiếu theo khách**

```sql
CREATE OR ALTER FUNCTION [fn_TimPhieuTheoKhach] (@TenKhach NVARCHAR(100))
RETURNS TABLE AS RETURN (
    SELECT [MaPhieu], [MaPhong], [NgayCheckIn], [SoNgayO] FROM [PhieuDatPhong]
    WHERE [HoTenKhach] LIKE '%' + @TenKhach + '%'
);
GO
-- Khai thác:
SELECT * FROM [dbo].[fn_TimPhieuTheoKhach](N'Nguyễn');
```
Kết quả
<img width="1920" height="1080" alt="Screenshot (85)" src="https://github.com/user-attachments/assets/04b7560a-59a2-4acf-a091-80362e5558c2" />

*Ảnh này tạo hàm fn_TimPhieuTheoKhach 

**3.3. Multi-statement Table-Valued Function: Phân loại đánh giá**

```sql
CREATE OR ALTER FUNCTION [fn_PhanLoaiDanhGia]()
RETURNS @Result TABLE (MaPhieu VARCHAR(10), KhachHang NVARCHAR(100), XepLoai NVARCHAR(30)) AS
BEGIN
    INSERT INTO @Result
    SELECT [MaPhieu], [HoTenKhach],
           CASE WHEN [DiemDanhGia] >= 9 THEN N'Xuất sắc'
                WHEN [DiemDanhGia] >= 7 THEN N'Tốt'
                ELSE N'Trung bình' END
    FROM [PhieuDatPhong];
    RETURN;
END;
GO
-- Khai thác:
SELECT * FROM [dbo].[fn_PhanLoaiDanhGia]();
```
Kết quả
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/12662688-9c45-4fbc-9834-5a89b5fb1447" />
*Ảnh này tạo hàm fn_PhanLoaiDanhGia và đánh giá khách hàng 

## Phần 3 :  Xây dựng Store Procedure

**1. Tổng quan về System Store Procedure (SP có sẵn)**

SQL Server cung cấp rất nhiều SP hệ thống (thường bắt đầu bằng tiền tố sp_) để quản lý và tra cứu thông tin hệ thống.

**Một số System SP đặc sắc:**

- `sp_help`: Được coi là "cuốn bách khoa toàn thư" nhanh. Khi bạn truyền tên một bảng vào (VD: sp_help 'Phong'), nó sẽ liệt kê tất cả cột, kiểu dữ liệu, khóa chính, khóa ngoại của bảng đó.

- `sp_helptext`: Dùng để xem mã nguồn của các Function, View hoặc SP khác mà bạn đã viết. Rất hữu ích khi bạn quên mất mình đã viết logic gì bên trong.

- `sp_rename` Cho phép đổi tên các đối tượng như bảng hoặc cột mà không cần phải xóa đi tạo lại.

**2. Thực hành viết Stored Procedure cho dự án**

**2.1. Store Procedure thực hiện lệnh INSERT (Có kiểm tra điều kiện logic)**

**- Yêu cầu:** Tạo SP để thêm một loại phòng mới vào bảng `LoaiPhong`. Trước khi thêm phải kiểm tra xem mã loại phòng đã tồn tại chưa, nếu tồn tại rồi thì không cho thêm.

```sql
CREATE OR ALTER PROCEDURE [sp_ThemLoaiPhong]
    @MaLoai VARCHAR(10),
    @TenLoai NVARCHAR(50),
    @Gia MONEY,
    @MoTa NVARCHAR(200)
AS
BEGIN
  
    IF EXISTS (SELECT 1 FROM [LoaiPhong] WHERE [MaLoaiPhong] = @MaLoai)
    BEGIN
        PRINT N'Lỗi: Mã loại phòng này đã tồn tại trong hệ thống!';
        RETURN;
    END

    INSERT INTO [LoaiPhong] ([MaLoaiPhong], [TenLoaiPhong], [GiaThue], [MoTa])
    VALUES (@MaLoai, @TenLoai, @Gia, @MoTa);

    PRINT N'Thêm loại phòng mới thành công!';
END;
GO

-- Khai thác SP:
EXEC [sp_ThemLoaiPhong] 'LP005', N'Penthouse', 10000000, N'Căn hộ tầng thượng cực sang';
```
Kết quả
<img width="1920" height="1080" alt="Screenshot (87)" src="https://github.com/user-attachments/assets/4f359bca-ca15-4d00-a26b-fe4a0f74daea" />
*Thêm một phòng mới 

**2.2. Store Procedure sử dụng tham số OUTPUT**

**- Yêu cầu:** Tạo SP để đếm số lượng phiếu đặt phòng của một phòng cụ thể. Kết quả trả về qua tham số `@TongSoLuot`.

```sql
CREATE OR ALTER PROCEDURE [sp_DemSoLuotDatCuaPhong]
    @MaP VARCHAR(10),
    @TongSoLuot INT OUTPUT 
AS
BEGIN
    SELECT @TongSoLuot = COUNT(*) 
    FROM [PhieuDatPhong] 
    WHERE [MaPhong] = @MaP;
END;
GO

-- Khai thác SP:
DECLARE @Result INT;
EXEC [sp_DemSoLuotDatCuaPhong] 'P101', @Result OUTPUT;
PRINT N'Số lượt đặt của phòng P101 là: ' + CAST(@Result AS VARCHAR);
```

Kết quả
<img width="1920" height="1080" alt="Screenshot (88)" src="https://github.com/user-attachments/assets/9ce10fe8-124a-4f59-bd71-db417c1538d4" />
*ảnh cho thấy số lượng đặt phòng 

**2.3. Store Procedure trả về tập kết quả (Result Set) sau khi JOIN nhiều bảng**

**- Yêu cầu:** Viết SP lấy thông tin chi tiết của tất cả các phiếu đặt phòng, bao gồm: Mã phiếu, Tên khách, Số phòng, Tầng, Tên loại phòng và Thành tiền (sử dụng lại Function `fn_TinhTienMotPhieu` đã viết ở phần 2).

```sql
CREATE OR ALTER PROCEDURE [sp_BaoCaoChiTietDatPhong]
AS
BEGIN
    SELECT 
        PDP.[MaPhieu],
        PDP.[HoTenKhach],
        P.[SoPhong],
        P.[Tang],
        LP.[TenLoaiPhong],
        PDP.[NgayCheckIn],
        [dbo].[fn_TinhTienMotPhieu](PDP.[MaPhieu]) AS [ThanhTien]
    FROM [PhieuDatPhong] PDP
    INNER JOIN [Phong] P ON PDP.[MaPhong] = P.[MaPhong]
    INNER JOIN [LoaiPhong] LP ON P.[MaLoaiPhong] = LP.[MaLoaiPhong]
    ORDER BY PDP.[NgayCheckIn] DESC;
END;
GO

-- Khai thác SP:
EXEC [sp_BaoCaoChiTietDatPhong];
```
Kết quả
<img width="1920" height="1080" alt="Screenshot (89)" src="https://github.com/user-attachments/assets/e9a3d9a4-777d-446a-adae-97de160adb43" />
*Ảnh cho thấy báo cáo chi tiết đặt phòng

 ## Phần 4: Trigger và Xử lý logic nghiệp vụ

**1. Viết 01 Trigger xử lý logic nghiệp vụ thực tế**

**- Yêu cầu:** Khi có một khách hàng đặt phòng mới (Insert vào bảng `PhieuDatPhong`), hệ thống phải tự động cập nhật trạng thái của phòng đó trong bảng `Phong` thành N'Có khách'.

**- Giải thích:** Đây là logic cực kỳ thực tế trong quản lý khách sạn. Khi lễ tân tạo phiếu đặt phòng, trạng thái phòng phải ngay lập tức chuyển sang "Có khách" để tránh việc đặt trùng phòng.

```sql
CREATE OR ALTER TRIGGER [trg_CapNhatTrangThaiPhongKhiDat]
ON [PhieuDatPhong]
AFTER INSERT
AS
BEGIN
    
    UPDATE [Phong]
    SET [TrangThai] = N'Có khách'
    FROM [Phong] P
    INNER JOIN INSERTED I ON P.[MaPhong] = I.[MaPhong];
    
    PRINT N'Trigger: Đã tự động cập nhật trạng thái phòng thành "Có khách".';
END;
GO

INSERT INTO [PhieuDatPhong] ([MaPhieu], [HoTenKhach], [MaPhong], [SoNgayO], [DiemDanhGia])
VALUES ('TEST01', N'Khách Hàng Test', 'P101', 2, 10);

-- Kiểm tra kết quả:
SELECT [SoPhong], [TrangThai] FROM [Phong] WHERE [MaPhong] = 'P101';
```
Kết quả
<img width="1920" height="1080" alt="Screenshot (90)" src="https://github.com/user-attachments/assets/be4948f3-ad2c-4fc8-82ae-b8edfd7425e9" />
*Ảnh cho thấy tự cập nhật trạng thái phòng 

**2. Trigger vòng lặp (Recursive Trigger) và Nhận xét**

Yêu cầu này yêu cầu chúng ta tạo ra một tình huống "đá chéo" nhau giữa hai bảng để quan sát hiện tượng.

**Bước 1: Viết Trigger cho Bảng A (PhieuDatPhong) cập nhật sang Bảng B (Phong)**

```sql
CREATE OR ALTER TRIGGER [trg_A_To_B]
ON [PhieuDatPhong]
AFTER INSERT, UPDATE
AS
BEGIN
    UPDATE [Phong]
    SET [TrangThai] = N'Có khách'
    WHERE [MaPhong] IN (SELECT [MaPhong] FROM INSERTED);
END;
GO
```
Kết quả
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/1b93a29e-109d-49b9-b99e-acd47cf70dd8" />

**Bước 2: Viết Trigger cho Bảng B (Phong) cập nhật ngược lại Bảng A (PhieuDatPhong)**

(Giả sử cập nhật số ngày ở của khách khi phòng bị sửa trạng thái - Một logic giả định để test lỗi)
```sql
CREATE OR ALTER TRIGGER [trg_B_To_A]
ON [Phong]
AFTER UPDATE
AS
BEGIN
    UPDATE [PhieuDatPhong]
    SET [SoNgayO] = [SoNgayO] + 0 -- Cập nhật giả định để kích hoạt trigger bảng A
    WHERE [MaPhong] IN (SELECT [MaPhong] FROM INSERTED);
END;
GO
```
Kết quả
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/8c280cf4-a684-4686-900f-356ae866c572" />

**Bước 3: Quan sát thông báo hệ thống khi thực hiện lệnh UPDATE:**
Khi bạn thực hiện lệnh `UPDATE [Phong] SET [TrangThai] = N'Đang sửa' WHERE [MaPhong] = 'P101`';, hệ thống có thể đưa ra thông báo lỗi:

Maximum stored procedure, function, trigger, or view nesting level exceeded (limit 32).

**Giải thích thông báo:**

- Nguyên nhân: Đây là lỗi Vòng lặp vô tận (Infinite Loop) hay còn gọi là Đệ quy (Recursion).

- Cơ chế: Trigger A gọi hành động làm kích hoạt Trigger B -> Trigger B lại thực hiện hành động làm kích hoạt ngược lại Trigger A. Quá trình này lặp đi lặp lại không có điểm dừng.

- Cơ chế bảo vệ: SQL Server có một giới hạn mặc định là 32 cấp. Khi các trigger gọi nhau vượt quá 32 lần, hệ thống sẽ tự động ngắt (Kill process) và báo lỗi để tránh treo máy chủ hoặc tràn bộ nhớ.

**3. Nhận xét cuối cùng về tình trạng này**

**1. Về mặt kỹ thuật:** Việc thiết kế trigger chéo nhau gây ra hiện tượng đệ quy là một lỗi thiết kế nghiêm trọng trong Database. Nó gây tốn tài nguyên và dẫn đến treo hệ thống nếu không có cơ chế chặn của SQL Server.

**2. Về mặt quản lý:** Trigger là con dao hai lưỡi. Chúng ta chỉ nên dùng Trigger cho các tác vụ mang tính chất đồng bộ dữ liệu một chiều (như cập nhật trạng thái, tính tổng tồn kho, ghi nhật ký log).

**3. Giải pháp:** * Sử dụng lệnh IF (`TRIGGER_NESTLEVEL() > 1) RETURN`; ở đầu trigger để ngăn chặn đệ quy.

- Thay thế bằng Stored Procedure để kiểm soát luồng dữ liệu một cách chủ động và rõ ràng hơn thay vì dựa hoàn toàn vào Trigger tự động.

## Phần 5 : Cursor và Duyệt dữ liệu

**1. Sử dụng CURSOR để giải quyết bài toán nghiệp vụ**

**Bài toán:** Khách sạn muốn gửi thông báo cá nhân hóa cho các khách hàng đã trả phòng. Với mỗi khách hàng, dựa vào điểm đánh giá `(DiemDanhGia)`, hệ thống sẽ in ra một thông điệp chăm sóc khách hàng khác nhau.

```sql
DECLARE @TenKhach NVARCHAR(100);
DECLARE @Diem FLOAT;
DECLARE @NoiDungTB NVARCHAR(MAX);

DECLARE cur_ThongBaoKhachHang CURSOR FOR
SELECT [HoTenKhach], [DiemDanhGia] FROM [PhieuDatPhong];

OPEN cur_ThongBaoKhachHang;

FETCH NEXT FROM cur_ThongBaoKhachHang INTO @TenKhach, @Diem;

WHILE @@FETCH_STATUS = 0
BEGIN
    
    IF @Diem >= 9
        SET @NoiDungTB = N'Trân trọng cảm ơn Quý khách ' + @TenKhach + N'. Hy vọng sớm gặp lại VVIP!';
    ELSE IF @Diem >= 7
        SET @NoiDungTB = N'Cảm ơn Quý khách ' + @TenKhach + N' đã hài lòng với dịch vụ.';
    ELSE
        SET @NoiDungTB = N'Xin lỗi Quý khách ' + @TenKhach + N' vì trải nghiệm chưa tốt. Chúng tôi sẽ cải thiện.';

    PRINT @NoiDungTB;

    FETCH NEXT FROM cur_ThongBaoKhachHang INTO @TenKhach, @Diem;
END;

CLOSE cur_ThongBaoKhachHang;
DEALLOCATE cur_ThongBaoKhachHang;
GO
```
Kết quả
<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/eab0070d-cab4-4e72-85f1-04574b9bafa4" />
*Ảnh cho thấy thông báo đến khách hàng

**2. Giải quyết bài toán không dùng CURSOR (Sử dụng Set-based SQL)** 

- Hầu hết các bài toán hiển thị dữ liệu đều có thể giải quyết bằng lệnh `SELECT` kết hợp `CASE WHEN`.

```sql
SELECT 
    [HoTenKhach],
    CASE 
        WHEN [DiemDanhGia] >= 9 THEN N'Trân trọng cảm ơn Quý khách ' + [HoTenKhach] + N'. Hy vọng sớm gặp lại VVIP!'
        WHEN [DiemDanhGia] >= 7 THEN N'Cảm ơn Quý khách ' + [HoTenKhach] + N' đã hài lòng với dịch vụ.'
        ELSE N'Xin lỗi Quý khách ' + [HoTenKhach] + N' vì trải nghiệm chưa tốt. Chúng tôi sẽ cải thiện.'
    END AS [ThongDiep]
FROM [PhieuDatPhong];
```
Kết quả
<img width="1920" height="1080" alt="Screenshot (96)" src="https://github.com/user-attachments/assets/a8449fc0-2ae2-49ab-bffe-d7944e513366" />

**So sánh tốc độ xử lý:**

**- Có dùng Cursor:** Xử lý theo kiểu RBAR (Row-By-Agonizing-Row - Duyệt từng dòng gây đau đớn). SQL Server phải mở tài nguyên, khóa dòng, xử lý, rồi mới sang dòng tiếp theo. Với 100 dòng, sự khác biệt chưa rõ, nhưng với 1 triệu dòng, Cursor có thể mất vài phút.

**- Không dùng Cursor (Set-based):** Xử lý tập hợp toàn bộ các dòng cùng lúc trong bộ nhớ. Tốc độ nhanh hơn gấp nhiều lần (thường là tính bằng mili giây).

**Minh chứng:** (Sinh viên nên thực hiện SET STATISTICS TIME ON để chụp ảnh màn hình CPU Time của 2 cách). Thông thường cách dùng lệnh SELECT sẽ có thời gian gần bằng 0ms.

**3. Bài toán chỉ CURSOR mới giải quyết được (hoặc SQL tập hợp cực khó giải quyết)**

Theo logic suy nghĩ của em, các bài toán về Dòng chảy dữ liệu tích lũy (Running Totals/Accumulation) hoặc Logic phụ thuộc trạng thái dòng trước là nơi Cursor chiếm ưu thế:

**Bài toán: "Sắp xếp phòng tự động dựa trên quy tắc dồn hàng"**

- Giả sử có 10 đoàn khách đến cùng lúc, mỗi đoàn có số lượng người khác nhau. Hệ thống phải duyệt từng đoàn, kiểm tra xem phòng còn trống bao nhiêu chỗ, nếu đủ thì nhét vào, nếu thiếu thì phải tách đoàn sang phòng tiếp theo và cập nhật ngay lập tức số chỗ còn trống của phòng đó để đoàn sau vào kiểm tra.

- Tại sao SQL tập hợp khó giải quyết? Vì tại thời điểm tính toán cho đoàn thứ 2, dữ liệu "Số chỗ còn trống" của phòng đã bị thay đổi bởi đoàn thứ 1 (nhưng chưa được Commit vào bảng nếu dùng lệnh tập hợp).

- Tại sao Cursor giải quyết được? Vì Cursor cho phép chúng ta thực hiện: Duyệt đoàn 1 -> Trừ chỗ phòng A -> Lưu (Update) -> Duyệt đoàn 2 -> Lấy số chỗ mới của phòng A để kiểm tra. Đây là tính tuần tự mà SQL tập hợp rất khó mô phỏng chính xác.

**Dự án Quản lý khách sạn đã hoàn thành 5 phần:**

**1. Cấu trúc dữ liệu:** Chuẩn PascalCase, đầy đủ ràng buộc PK/FK/CK.

**2. Hàm (Function):** Đóng gói logic tính toán.

**3. Thủ tục (Stored Procedure):** Thực thi nghiệp vụ và báo cáo.

**4. Trigger:** Tự động hóa trạng thái và kiểm soát đệ quy.

**5. Cursor:** Duyệt dữ liệu tuần tự và xử lý logic cá nhân hóa.
