# Báo cáo Day 5 — điền trực tiếp trong fork của bạn

- Mã học viên theo lớp: Phùng Thảo An 
- Ngày / CVAT local: 18/09/2026 / CVAT local (localhost:8080)
- Công cụ đã dùng: CVAT Polygon, CVAT Brush (không dùng SAM)

## 1. Bài đã nộp

| Task | File ZIP đúng tên | Hoàn thành mấy ảnh | Điểm tối đa (coach chấm sau) |
| --- | --- | ---: | ---: |
| easy_semantic | easy_semantic.zip | 3 / 3 | 20 |
| medium_instance | medium_instance.zip | 3 / 3 | 32 |
| hard_panoptic | hard_panoptic.zip | 2 / 2 | 30 |
| cp1_holes | cp1_holes.zip | 1 / 1 | 3 |
| cp2_slice | cp2_slice.zip | 1 / 1 | 3 |
| cp5_occlusion | chưa có | 0 / 1 | 3 |
| cp3_thin | cp3_thin.zip | 1 / 1 | 3 |
| cp4_curb | cp4_curb.zip | 1 / 1 | 3 |
| cp6_coverage | cp6_coverage.zip | 1 / 1 | 3 |
| **Tổng tối đa** | | | **100** |

Ghi chú: Quyết định bỏ qua trạm cp5_occlusion do giới hạn thời gian thực hành. Các file ZIP còn lại đều xuất thành công không báo lỗi.

## 2. Một quyết định trước khi dùng gợi ý

- Ảnh, vị trí và object Medium đầu tiên tự vẽ: Ảnh đầu tiên chặng medium_instance, đối tượng là chiếc xe máy bị một phương tiện khác che ngang thân làm đứt đoạn.
- Class và quy tắc tôi dùng để chọn biên: Class `motorcycle`. Dùng quy tắc "occlusion" (vật bị che khuất). Tôi vẽ các đa giác bao quanh phần lộ ra của đầu xe và đuôi xe, sau đó nối mảng (bấm Finish) để gộp chung thành một Object ID duy nhất thay vì tách thành hai vật riêng biệt.
- Nếu dùng gợi ý sau đó: vùng gợi ý sai/đúng, hành động sửa/giữ và lý do: Không dùng gợi ý tự động, hoàn toàn tự vẽ bằng công cụ Mask và Polygon.
- Nếu không dùng gợi ý: Đã áp dụng đúng luật đếm cá thể (Things), tách rời từng xe dù chúng đậu sát nhau.

## 3. Một lỗi tôi tìm thấy và sửa

- Task/ảnh/vùng: cp1_holes / Ảnh có chiếc ô tô nhìn xuyên qua kính cửa sổ thấy phông nền phía sau.
- Lỗi thuộc loại: thiếu-thừa vật / biên.
- Bằng chứng tôi nhìn thấy: Ban đầu tôi đã khoét chừa lại phần kính xe do nhìn thấy bối cảnh phía sau, khiến mask của chiếc xe bị rỗng ở giữa.
- Quy tắc và hành động sửa: Quy tắc cp1 cấm khoét lỗ/khe trái quy tắc. Đã sửa bằng cách dùng Polygon tô đặc toàn bộ diện tích xe bao gồm cả các ô kính, gộp thành 1 khối đặc duy nhất.
- Sau sửa đã Save và export lại chưa? Đã Save và xuất lại đè lên file cp1_holes.zip cũ.
- Kết quả Summary: Chưa có điểm do chưa đến giờ phát đáp án ground truth, Action Summary báo trạng thái cấu trúc ZIP OK.

## 4. Ba ca chưa chắc hoặc đã cân nhắc

| Ảnh/vị trí | Hai cách hiểu có thể | Quy tắc/chứng cứ | Quyết định hoặc câu hỏi cho coach |
| --- | --- | --- | --- |
| 1. hard_panoptic / Xe tải lớn chở nhiều ô tô con trên đường | 1: Gộp chung xe tải và xe con thành 1 khối `truck` to. <br> 2: Phải tách rời đầu kéo và từng xe con. | Nhóm "Things" bắt buộc đếm từng cá thể độc lập (luật Instance). | Vẽ khung xe tải là 1 object `truck`. Mỗi ô tô con nằm trên rơ-moóc được vẽ thành các object `car` riêng biệt đè lên lớp khung xe tải. |
| 2. cp4_curb / Ranh giới vỉa hè và mặt đường nhựa | 1: Vẽ ranh giới bám theo vạch sơn kẻ đường màu trắng. <br> 2: Vẽ ranh giới bám theo gờ bó vỉa vật lý. | Luật cp4_curb yêu cầu chia ranh giới theo chức năng/bó vỉa, không phụ thuộc vào màu sắc của ảnh. | Cắt ranh giới chính xác dọc theo mép bó vỉa nhô lên, gán phần trong là `sidewalk`, phần ngoài là `road`. |
| 3. hard_panoptic / Cần cẩu tháp rất mỏng xa xa trên nóc tòa nhà | 1: Bỏ qua vì cấu trúc quá mỏng và nhỏ, dễ gây nhiễu mask. <br> 2: Bắt buộc vẽ để lấy điểm luật cấu trúc mỏng. | Luật cp3_thin cấm bỏ sót hoặc tô nét quá dày đối với các cột/biển mảnh. | Đã phóng to và dùng Brush size 2px tô cẩn thận dọc theo thân cần cẩu, gộp chúng vào lớp `building` của tòa nhà bên dưới. |