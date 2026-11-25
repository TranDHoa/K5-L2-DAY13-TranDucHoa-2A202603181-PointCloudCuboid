# Báo cáo Day 13 — Trần Đức Hòa - 2A202603181

> **Cách dùng mẫu này:** đổi tên file thành `<MSSV>_<HoTen>.md` (họ tên không dấu, viết liền), ví dụ `2A202600123_NguyenVanAn.md`. Đổi tên, đừng chép thành file thứ hai.
>
> Xoá mọi dấu TODO và dòng gợi ý (dòng bắt đầu bằng `>`). Mỗi con số lấy từ **bảng A** hoặc **phiếu tự kiểm** trong `submission/`, kèm đơn vị (m, °). Ảnh để trong `report/individual/images/` (nộp LMS trong file zip, không commit); trong báo cáo chỉ ghi **tên file ảnh**. Báo cáo này nằm trên repo public: không chép biển số, chữ trên biển hiệu hay biển chỉ đường, tên đường, địa danh; nhắc tới bạn khác thì ghi MSSV, không ghi họ tên. Cả báo cáo ~2 trang.

## 1. Kết luận trước
- MSSV: 2A202603181.
- Task: D13-2A202603181.
- Hệ điều hành: Windows.
- Trình duyệt: Microsoft Edge.
- Phiên bản CVAT: 2.81.0.
- 10 frame: s01f02, s01f18, s02f02, s02f33, s03f12, s03f49, s04f05, s04f53, s05f09, s05f45.

Tổng cộng 57 cuboid, gồm 51 Car, 2 Truck, 2 Bus và 2 Cyclist. Các frame s01f18, s02f02, s02f33, s03f49, s04f05 và s04f53 có ít cuboid nhất (5 cuboid/frame).

Selfcheck không gắn cờ cuboid nào (0/57). Tôi tin tưởng nhất vào vị trí và kích thước của các vật thể gần; phần còn yếu là xác định kích thước vật xa, thưa điểm và căn đáy trên mặt đường không bằng phẳng.

Cách sử dụng CVAT:
- Sử dụng Top View để quan sát từ trên xuống, kéo chuột để xoay hoặc dời góc nhìn, con lăn để phóng to và thu nhỏ.
- Chọn Shape, nháy đúp để đặt cuboid, chỉnh chiều dài, chiều rộng và yaw ở Top View; chỉnh chiều cao và đáy ở Side/Front View.
- Bật Cuboid orientation để kiểm tra hướng đầu vật thể; sử dụng preview để đối chiếu cuboid trên ảnh camera.

## 2. Đọc frame (Bước 2)
Ảnh: `t1-s01f02-3d.png`, `t1-s01f02-top.png`, `t1-s01f18-3d.png`, `t1-s01f18-top.png`.

| Frame | Phía trước và các trục trên BEV | Mặt đường | Vật xa nhất đã gán | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f02 | Trước ở cạnh trên (−Y), +X sang trái, +Y xuống dưới | Tương đối phẳng, cao độ tham chiếu −1,7 m | 23,68 m | Car, Cyclist, Pedestrian | Xe con phía sau và hai bên ngã tư | Một số cụm nhỏ ở xa và gần vỉa hè |
| s01f18 | Trước ở cạnh trên (−Y), +X sang trái, +Y xuống dưới | Tương đối phẳng, cao độ tham chiếu −1,7 m | 27,57 m | Car, Bus | Xe con phía sau và bên trái | Các cụm điểm xa dọc hai bên đường |

**Ba điều camera cho biết:**

- Màu sắc và kiểu dáng phương tiện, giúp phân biệt xe con với xe buýt.
- Đặc điểm đầu và đuôi xe, hỗ trợ xác định hướng yaw.
- Người đi bộ và người điều khiển xe hai bánh mà point cloud khó phân biệt.

**Ba điều LiDAR cho biết:**

- Vị trí và khoảng cách thực tế của vật thể.
- Hình dạng và kích thước trong không gian 3D.
- Mật độ điểm, vùng bóng che và sự phân bố vật thể xung quanh xe thu dữ liệu.

**Tám frame lướt:**

| Frame | Phía trước | Điều đáng chú ý |
|---|---|---|
| s02f02 | Cạnh trên | Xe buýt lớn sát bên trái, che khuất một vùng point cloud. |
| s02f33 | Cạnh trên | Hàng rào phân cách và phương tiện di chuyển ngược chiều. |
| s03f12 | Cạnh trên | Nhiều phương tiện nằm gần nhau, khó phân tách cụm điểm. |
| s03f49 | Cạnh trên | Cyclist phía bên phải, một phần xe bị hàng rào che trên camera. |
| s04f05 | Cạnh trên | Xe minivan rất gần bên phải, mật độ điểm cao. |
| s04f53 | Cạnh trên | Xe con ở xa 34,65 m chỉ có 103 điểm. |
| s05f09 | Cạnh trên | Ngã tư rộng, phương tiện có nhiều hướng yaw. |
| s05f45 | Cạnh trên | Hai xe tải ở hai phía ngã tư, một số cuboid có đáy lệch mốc tham chiếu. |
## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.png`, `t2-b-bus-truck.png`, `t2-c-ped-cyclist.png`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) Xe con gần | s01f02, 1 | Car | 264,6°, gần hướng 270° | Có | Đã đối chiếu BEV và preview | Có |
| (b) Xe buýt | s02f02, 1 | Bus | 269,7°, gần hướng 270° | Có | Đã đối chiếu BEV và preview | Chiều rộng và chiều cao ngoài khoảng tham chiếu |
| (c) Xe hai bánh | s03f49, 5 | Cyclist | 269,0°, gần hướng 270° | Có | Đã đối chiếu BEV và preview | Cần đối chiếu loại xe cụ thể |

**Vòng hiệu chuẩn:** Chưa bổ sung kết quả so sánh với các học viên khác. Nguyên tắc áp dụng là căn cuboid theo cụm điểm, xác định hướng đầu bằng camera, kiểm tra đáy và kích thước ở các góc nhìn khác nhau.

**Vật chưa gán cần xem lại:**

| Frame | Vị trí | Mô tả | Loại nghi vấn |
|---|---|---|---|
| s01f02 | Khu vực vạch qua đường bên phải | Một số người đi bộ xuất hiện trên camera nhưng chưa có cuboid | LOW_VISIBILITY (cần xác nhận) |
| s01f02 | Khu vực ngoài các cuboid | Các cụm điểm nhỏ, rời rạc, chưa xác định được vật thể | UNCERTAIN_CLASS |

## 4. Bốn hiện tượng chất lượng (Bước 4)
| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán |
|---|---|---|---|---|
| Bóng che | t4-1-bong-che.png / task cá nhân | Xe buýt lớn nằm gần cảm biến và chắn tia LiDAR | Vùng phía ngoài xe buýt có ít điểm phản hồi | Đối chiếu camera, không tự suy đoán vật bị che hoàn toàn. |
| Thưa ở xa | t4-2-thua-o-xa.png / task cá nhân | Mật độ điểm giảm theo khoảng cách | Car idx 5 ở khoảng cách 34,65 m chỉ có 103 điểm | Khó xác định biên và kích thước, cần kết hợp camera. |
| Phản xạ thấp | t4-3-phan-xa-thap.png / task cá nhân | Có thể do vật liệu hoặc góc tới của tia LiDAR | Cần tìm cụm điểm ít bất thường so với vật cùng loại và khoảng cách tương đương | Không suy đoán kích thước hoặc class nếu thiếu cơ sở. |
| Điểm lạ | t4-4-diem-la.png / task cá nhân (ứng viên) | Có thể do nhiễu hoặc phản xạ bất thường | Một số điểm rời rạc ngoài các cụm vật thể, cần kiểm tra thêm trên CVAT | Không tạo cuboid từ các điểm rời rạc chưa xác định được vật thể. |
## 5. Số từ cuboid của tôi (Bước 5)

Nguồn: `submission/D13-2A202603181-bangA.csv` và `submission/D13-2A202603181-tukiem.csv`.

Mặt đường tham chiếu khoảng −1,7 m; ngưỡng chênh lệch đáy là 0,3 m.

| Cuboid (frame, idx, class) | z (m) | h (m) | z đáy (m) | dist_m (m) | r (m) | yaw_deg (°) | Nhận xét |
|---|---:|---:|---:|---:|---:|---:|---|
| s01f02, 1, Car | −1,010 | 1,514 | −1,767 | 6,11 | 6,19 | 264,6 | Chạm đường |
| s05f45, 3, Truck | −0,540 | 3,204 | −2,142 | 22,53 | 22,54 | 89,0 | Chìm 0,442 m so với mốc |
| s05f45, 6, Truck | 0,430 | 3,204 | −1,172 | 34,59 | 34,60 | 12,5 | Lơ lửng 0,528 m so với mốc |

Hai Truck có đáy lệch mốc tham chiếu nhưng độ lệch so với mặt đất cục bộ trong phiếu tự kiểm lần lượt là 0,14 m và 0,13 m. Vì vậy, chưa thể kết luận chúng bị chìm hoặc lơ lửng trên mặt đường thực tế.

**(a) Cuboid gần nhất và xa nhất:**

- Gần nhất: s02f02, idx 1, Bus; dist_m = 3,40 m; r = 3,41 m.
- Xa nhất: s04f53, idx 5, Car; dist_m = 34,65 m; r = 34,66 m.

**(b) So sánh kích thước với bảng 4.3:**

| Cuboid | l (m) | w (m) | h (m) | Nhận xét |
|---|---:|---:|---:|---|
| s01f02, 7, Cyclist | 1,903 | 0,711 | 1,629 | Nằm trong khoảng tham chiếu xe máy có người |
| s01f02, 1, Car | 4,357 | 1,772 | 1,514 | Nằm trong khoảng tham chiếu sedan |

Cả hai cuboid đều có kích thước phù hợp với loại vật thể tham chiếu.

**(c) So sánh yaw:**

- Xe cùng chiều phía trước: s01f02, idx 1, Car; yaw = 264,6°, lệch 5,4° so với 270°.
- Xe ngược chiều: s01f02, idx 5, Car; yaw = 89,3°, lệch 0,7° so với 90°.

Cả hai đều gần hướng tham chiếu, phù hợp với hai hướng xe đối diện nhau.

**Mật độ theo khoảng cách:**

| Cuboid (frame, idx, class) | dist_m (m) | Số điểm (points_in) |
|---|---:|---:|
| s01f02, 1, Car | 6,11 | 2239 |
| s01f02, 3, Car | 15,05 | 566 |
| s01f18, 5, Car | 25,55 | 211 |
| s04f53, 5, Car | 34,65 | 103 |

Khi khoảng cách tăng khoảng 5,67 lần, số điểm chỉ còn khoảng 4,6%. Kết quả phù hợp với xu hướng giảm mật độ điểm trong slide bài giảng (1968, 718, 214 và 72 điểm tương ứng với các khoảng 0–10 m, 10–20 m, 20–30 m và 30–40 m). Sự chênh lệch còn phụ thuộc vào kích thước, góc nhìn và khả năng phản xạ của vật thể.

**Tự kiểm:**

Selfcheck không gắn cờ cuboid nào (0/57). Các trường hợp cần kiểm tra lại từ preview:

- s02f02, idx 1, Bus: chiều rộng 2,736 m và chiều cao 2,945 m nằm ngoài khoảng tham chiếu xe buýt thường.
- s04f05, xe minivan bên phải: cuboid chiếu lên camera có dấu hiệu dư ở phần nóc và đuôi, cần kiểm tra độ khít.



## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc

**(a) Ý nghĩa của nhãn:**

Nhãn 3D sử dụng đơn vị mét để model học vị trí, kích thước và hướng thực tế của vật thể trong không gian thay vì kích thước theo pixel. Vật thể thưa điểm cần quy tắc riêng để hạn chế nhãn sai về kích thước, vị trí và class khi dữ liệu không đủ rõ.

**(b) Sử dụng AI:**

- Công cụ: ChatGPT.
- Câu hỏi chính: Cách gán cuboid 3D, phân tích bốn hiện tượng LiDAR, kiểm tra thông số cuboid và hoàn thiện báo cáo.
- Cách sử dụng: Tham khảo hướng dẫn, đối chiếu ảnh preview và ảnh CVAT, tính toán số liệu từ Bảng A và phiếu tự kiểm.

**(c) Điều còn chưa chắc:**

Việc xác định biên vật thể ở xa và phân biệt phản xạ thấp với bóng che vẫn còn khó khăn. Một số cuboid có kích thước ngoài khoảng tham chiếu hoặc đáy lệch mốc mặt đường, cần kết hợp nhiều góc nhìn và camera để đánh giá trước khi quyết định sửa nhãn.