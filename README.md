# Multi-Agent Search Project - Pacman AI

Dự án phát triển các thuật toán tìm kiếm đa tác tử (Multi-Agent Search) nhằm tối ưu hóa hành vi di chuyển, né tránh ma và thu thập thức ăn của Pacman trong môi trường trò chơi.

## Mục lục
* [Q1: Reflex Agent](#q1-reflex-agent)
* [Q2: Minimax](#q2-minimax)
* [Q3: Alpha-Beta Pruning](#q3-alpha-beta-pruning)
* [Q4: Expectimax](#q4-expectimax)
* [Q5: Evaluation Function](#q5-evaluation-function)

---

## Q1: Reflex Agent

### 1. Mô tả bài toán
Xây dựng hàm đánh giá trạng thái cho ReflexAgent để giúp Pacman sống sót và thu thập thức ăn hiệu quả dựa trên việc đánh giá các hành động cục bộ.

### 2. Phương pháp triển khai
Hàm đánh giá kết hợp tổ hợp tuyến tính giữa điểm số hiện tại và các đặc trưng được trích xuất từ trạng thái kế tiếp:
* **Thức ăn:** Sử dụng công thức nghịch đảo khoảng cách để ưu tiên mạnh các viên thức ăn gần Pacman. Thưởng thêm điểm khi hành động ăn được thức ăn và khi tổng số thức ăn còn lại ít để thúc đẩy dọn sạch bản đồ.
* **Ma thường:** Áp dụng hình phạt rất nặng khi khoảng cách quá gần (nhỏ hơn hoặc bằng 1) để tránh chết ngay, và giảm dần hình phạt khi khoảng cách tăng lên.
* **Ma đang sợ:** Chuyển sang điểm thưởng lớn để khuyến khích Pacman lập tức truy đuổi.

### 3. Kết quả
* Tỉ lệ thắng cao, vượt qua toàn bộ các bài kiểm tra.
* Điểm trung bình đạt được: 1248.3.
* Đạt điểm tuyệt đối: 4/4 điểm.

---

## Q2: Minimax

### 1. Mô tả bài toán
Cài đặt thuật toán đối kháng Minimax mở rộng, làm việc với một Pacman (Max agent) và nhiều con ma (Min agents) qua một độ sâu (depth) cho trước.

### 2. Phương pháp triển khai
Triển khai hàm đệ quy với cơ chế chuyển đổi agent và giảm độ sâu hợp lý:
* Biến độ sâu chỉ giảm đi 1 khi Pacman đã hoàn thành một lượt đi đầy đủ và chuẩn bị chuyển sang lượt tiếp theo của vòng mới.
* Điều kiện dừng của hàm đệ quy kích hoạt khi trạng thái trò chơi thắng, thua, hoặc khi độ sâu bằng 0. Lúc này hàm sẽ trả về giá trị của hàm đánh giá.
* Sử dụng giá trị vô cùng âm để khởi tạo cho Node Max (Pacman) nhằm tối đa hóa điểm số và vô cùng dương cho các Node Min (Ghosts) nhằm tối thiểu hóa điểm số.

### 3. Kết quả
* Vượt qua tất cả các bài kiểm tra xác minh số lượng node và độ sâu duyệt của hệ thống.
* Đạt điểm tuyệt đối: 5/5 điểm.

---

## Q3: Alpha-Beta Pruning

### 1. Mô tả bài toán
Tối ưu hóa hiệu năng và tốc độ xử lý của cây Minimax bằng thuật toán Alpha-Beta Pruning, giúp loại bỏ các nhánh không cần thiết mà không làm thay đổi kết quả lựa chọn cuối cùng.

### 2. Phương pháp triển khai
Truyền tham số alpha (giá trị Max tốt nhất hiện tại) và beta (giá trị Min tốt nhất hiện tại) vào hàm đệ quy luân phiên:
* **Tại node Max (Pacman):** Nếu giá trị lớn hơn beta, lập tức ngắt nhánh và trả về giá trị đó. Ngược lại, cập nhật alpha bằng giá trị lớn nhất giữa alpha và giá trị hiện tại.
* **Tại node Min (Ghost):** Nếu giá trị nhỏ hơn alpha, thực hiện cắt tỉa và trả về giá trị đó. Ngược lại, cập nhật beta bằng giá trị nhỏ nhất giữa beta và giá trị hiện tại.
* **Tối ưu hóa nâng cao:** * Các trạng thái kế tiếp được khởi tạo tuần tự và kiểm tra điều kiện cắt tỉa ngay lập tức thay vì sinh toàn bộ mảng dữ liệu cùng lúc, giúp tránh duyệt lãng phí bộ nhớ.
  * Sử dụng toán tử bất đẳng thức nghiêm ngặt (`>` và `<`) thay vì dạng có dấu bằng để không cắt tỉa các trạng thái bằng nhau, bảo đảm số lượng node mở rộng khớp tuyệt đối với hệ thống kiểm tra tự động.

### 3. Kết quả
* Tốc độ duyệt cây tìm kiếm nhanh hơn rõ rệt so với Minimax nguyên bản, vượt qua hệ thống kiểm tra tự động thành công.
* Đạt điểm tuyệt đối: 5/5 điểm.

---

## Q4: Expectimax

### 1. Mô tả bài toán
Chuyển đổi mô hình dự đoán từ đối kháng lý tưởng sang Expectimax nhằm đối phó với tình huống ma di chuyển ngẫu nhiên thay vì di chuyển tối ưu hoàn toàn.

### 2. Phương pháp triển khai
Thay thế hoàn toàn logic chọn giá trị nhỏ nhất của Node Min (Ghost) bằng một Chance Node (Node cơ hội):
* Do đặc tính tính toán giá trị trung bình kỳ vọng, thuật toán Expectimax không thể áp dụng cơ chế cắt tỉa giống như Alpha-Beta Pruning.
* Tác tử sẽ tính tổng giá trị tích lũy của các trạng thái kế tiếp nhân với xác suất xuất hiện đồng đều của tất cả các hành động hợp lệ (xác suất bằng 1 chia cho số lượng hành động hợp lệ).
* Hàm trả về giá trị trung bình cộng kỳ vọng này lên các node phía trên để đưa ra quyết định di chuyển.

### 3. Kết quả
* Hành vi thông minh hơn: Pacman không còn bỏ cuộc sớm khi bị dồn vào góc; thay vào đó, tác tử cố gắng di chuyển ăn thêm điểm trước khi chết.
* Đạt điểm tuyệt đối: 5/5 điểm.

---

## Q5: Evaluation Function

### 1. Mô tả bài toán
Thiết kế hàm đánh giá trực tiếp một trạng thái trò chơi tĩnh (`gameState`) thay vì đánh giá hành động cục bộ, phục vụ trực tiếp cho các thuật toán duyệt cây độ sâu.

### 2. Phương pháp triển khai
Kế thừa cấu trúc logic thành công từ thuật toán Reflex Agent nhưng thực hiện tính toán và đánh giá trực tiếp trên trạng thái hiện tại:
* **Điểm gốc:** Tích hợp toàn bộ giá trị heuristic này trực tiếp vào điểm gốc thu được từ game engine.
* **Thức ăn:** Ưu tiên các viên thức ăn gần dựa trên khoảng cách, đồng thời phạt nhẹ dựa trên số lượng thức ăn còn lại trên bản đồ nhằm ép tác tử dọn dẹp liên tục. Thưởng lớn khi còn rất ít thức ăn để kết thúc trò chơi nhanh chóng.
* **Capsule:** Ưu tiên xử lý capsule mạnh hơn thông qua biến khoảng cách đến capsule gần nhất, đưa tác tử vào trạng thái dễ kích hoạt chuỗi ăn ma.
* **Phân loại Ma:** Tính toán điểm độc lập cho từng con ma trên bản đồ, đảm bảo Pacman bị ảnh hưởng bởi áp lực vị trí từ tất cả các phía. Xử lý riêng biệt ma sợ (cộng điểm thưởng) và ma thường (áp dụng mức phạt phù hợp khi ở gần).

### 3. Kết quả
* Vận hành ổn định trên mọi cấu trúc bản đồ, thắng toàn bộ các ván đấu thử nghiệm.
* Tỉ lệ thắng: 10/10 ván.
* Điểm trung bình đạt được: 1128.5.
* Đạt điểm tuyệt đối: 6/6 điểm.
