# mltx_meter_ble_gatt_specifications

* Xây dựng dựa trên BLE GATT của chuẩn Bluetooth 4.0

## 1. Tài liệu:

* [Cơ bản về GATT](https://learn.adafruit.com/introduction-to-bluetooth-low-energy/gatt)

## 2. Định nghĩa

2.1. GATT định nghĩa **Profile**, **Service**, **Characteristics**
* Mỗi Profile định nghĩa 1 nhóm các Services
* Mỗi Service định nghĩa 1 nhóm các Characteristics:
  * Các service được định danh bằng 16-bit hoặc 128-bit UUID
  * MLTX là custom service định danh bằng 128-bit UUID
* Mỗi Characteristic định nghĩa 1 thuộc tính định danh bằng 16-bit hoặc 128-bit UUID
  * Các thuộc tính định nghĩa kiểu, kích thước dữ liệu
  * Các thuộc tính định nghĩa phương thức đọc, ghi, notification

2.2. Đồng hồ: gọi tên **mltx-meter**

2.3. Thiết bị chuyển tiếp đóng vai trò ngoại vi(peripheral) hay GATT server: gọi tên **mltx-bg-dev**

2.3. Mobile app đóng vai trò central device hay GATT client: gọi tên **mltx-bg-app**

## 3. Tương tác giữ mobile app và thiết bị:

3.1. Scan

* **mltx-bg-app** thục hiện scan các thiết bị BLE

* Thiết bị **mltx-bg-dev** trả lời bằng bản tin scan-response

* **mltx-bg-app** kiểm tra thiết bị hợp lệ: kiểm tra nội dung bản tin san-response để xác nhận.

3.2. Pairing:

* Thiết bị **mltx-bg-dev** cho phép 1 hoặc nhiều **mltx-bg-app** pair (giới hạn == 3)

* Sau pairing **mltx-bg-app** sẽ có quyền tương tác với các characteristic( đọc ghi, nhận notification) từ **mltx-bg-dev**

* Thiết bị vào chế độ pairing:
    * User kích hoạt bằng 1 nút vật lý: nhấn giữ trong vòng 5s
    * **mltx-bg-app** thực hiện kết nối và gửi pairing request
    * **mltx-bg-dev** accept và lưu thông tin kết nối
    * **mltx-bg-dev** reject nếu bảng lưu thông tin pairing đã đầy
    * Kết thúc quá trình, các lần kết nối tiếp theo không cần thực hiện lại **pairing**
    
3.3. Connect

* Nếu **mltx-bg-app** đã thực hiện pairing trước đó có thể thực hiện luôn quá trình kết nối:
    * Nếu thiết bị **mltx-bg-dev** trong tầm của **mltx-bg-app**: kết nối thành công
    * Nếu thiết bị **mltx-bg-dev** ngoài tầm của **mltx-bg-app** hoặc bị hỏng: kết nối thất bại

## 4. MLTX Service

4.1 MLTX Characteristics table

> data type: uint16, uint32, time, float có format little endian

Characteristic | Type | Length | Properties | Description | UUID
--- | --- | --- | --- | --- | ---
version | uint8 | 1 | r--
update_count | uint8 | 1 | r-n | số lần **mltx-meter** update data
tx_status | uint16 | 2 | r-- | xem 4.1.1
time | time | 4 | rw- | Thời gian hiện tại **mltx-meter**
geo_lon | float | 4 | r-- | Kinh độ
geo_lat | float | 4 | r-- | Kinh độ
geo_speed | uint16 | 2 | r-- | Vận tốc GPS
ecu_speed | uint16 | 2 | r-- | Vận tốc ECU
tripid | uint16 | 2 | r-- | ID cuốc
shiftid | uint16 | 2 | r-- | ID ca
meter_fare | uint32 | 4 | r-- | Tiền **mltx-meter**
trip_fare | uint32 | 4 | r-- | Tiền cuốc
trip_time | uint32 | 4 | r-- | Thời gian cuốc
trip_lon | float | 4 | r-- | Kinh độ bắt đầu cuốc
trip_lat | float | 4 | r-- | Vĩ độ bắt đầu cuốc
trip_runtime_100m | uint32_t | 4 | r-- | Khoảng cách cuốc đơn vị 100m
trip_wait_time | uint16 | 2 | r-- | Thời gian chờ
trip_wait_fare_100 | uint16 | 2 | r-- |  Tiền chờ đơn vị 100đ
trip_runtime_100m_last | uint32 | 4 | r-- | reserved
trip_wait_time_last | uint16 | 2 | r-- | reserved
trip_wait_fare_100_last | uint16 | 2 | r-- | reserved

4.1.1 tx_status Characteristic

  * trạng thái **mltx-meter** dạng bit

  | Name | bit | Description
  --- | --- | ---
  trip_status | 0 | trạng thái có khách/rỗng
  engine_status | 1 | engine status
  ac_status | 2 | trạng thái điều hòa đóng/mở
  ir_status | 3 | trạng thái mắt thần có/không