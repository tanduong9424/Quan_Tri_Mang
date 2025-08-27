# Cài đặt, Build, Run Container Docker
**Cập nhật hệ thống.**

    sudo apt update 
    sudo apt upgrade -y

**Cài đặt Docker Engine.**

Chúng ta sẽ cài đặt từ kho lưu trữ chính thức của Docker để đảm bảo có phiên bản mới nhất.
Cài đặt các gói cần thiết để apt có thể sử dụng kho lưu trữ qua HTTPS:

    sudo apt install apt-transport-https ca-certificates curl software-properties-common -y
**Thêm GPG key chính thức của Docker:**

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
**Thiết lập kho lưu trữ (repository) của Docker:**

    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

**Cài đặt Docker Engine:**

    sudo apt update

    sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin -y


**Các command line để build chạy docker**

    docker build -t test-docker .
    (Build image/Dockerfile trong thư mục test-docker)

	docker run -p 5000:8080 test-docker
    (Run docker ánh xạ port 5000 của docker sang 8080 của localhost)

	http://localhost:8080
    (Kết quả sau khi build thành công)

	docker compose up --build
    (có thể thay thế docker run và docker build)
    
	docker compose down 
    (xóa dữ liệu)

	docker compose down -v 
    (xóa cả volume dữ liệu. Ví dụ, xóa sạch database)

	docker system prune -f
		Dọn dẹp toàn bộ hệ thống (Lệnh mạnh nhất):
		Lệnh này sẽ xóa:
		Tất cả các container đã dừng.
		Tất cả các "image mồ côi".
		Tất cả các network không được sử dụng.
		Tất cả build cache.

	docker system prune -a -f 
    (Dọn dẹp cả các image không được dùng đến không chỉ mồ côi)

**Giải thích theo một ví dụ để dễ hình dung hơn:**

- **docker build** : Là hành động bạn vào bếp và làm theo công thức để nướng một cái bánh kem.

- **docker run**: Là hành động bạn lấy cái bánh kem đã nướng đó ra, đặt lên một cái đĩa và bày ra bàn.

- **docker compose up**: Là hành động bạn cầm toàn bộ thực đơn (file docker-compose.yaml), ra lệnh cho cả đội ngũ đầu bếp:
  - "Nướng bánh kem đi!" (build service app)
  - "Pha nước cam đi!" (kéo image mysql)
  - "Mang bánh kem ra bàn số 5!" (run service app)
  - "Mang nước cam ra bàn số 5!" (run service db)
  - "Nhớ nối một cái ống hút từ ly nước cam sang đĩa bánh kem nhé!" (tạo network và kết nối chúng lại).


# Dangling Image (Image mồ côi)
- Ngắt kết nối với tag
	``docker images -f dangling=true``
- **Image**: Là một tập hợp các lớp (layers) file chỉ đọc. Nó có một mã định danh duy nhất, rất dài gọi là Image ID (ví dụ: sha256:a1b2c3d4...). Đây là "thể xác" của image.

- **Tag**: Là một cái nhãn tên thân thiện mà con người có thể đọc được (ví dụ: test-docker:latest). Cái nhãn này được dán vào một Image ID cụ thể. Đây là "danh tính" của image.

**Giải thích**

- BUILD lần 1 (**test-docker:latest ---> Image ID: abcde**)
	- Docker tạo ra một **Image** mới với **ID** là **abcde**.
	Nó lấy cái nhãn tên **test-docker:latest** và dán vào Image abcde.

- BUILD lần 2 
Docker phải dán cái nhãn tên **test-docker:latest**. Nhưng một cái nhãn tên chỉ có thể được dán trên **một Image** tại một thời điểm.
Docker sẽ **gỡ** cái nhãn tên test-docker:latest ra khỏi Image cũ (abcde) và **dán nó vào Image mới (12345)**.

- Trạng thái sau lần build thứ hai:

- **test-docker:latest** ---> **Image ID: 12345** (Image mới)
- ???????????????????---x **Image ID: abcde** (Image cũ)=> Image mồ côi



# Tmux
Dùng để chia nhiều terminal thuận tiện cho việc quản lí, chẳng hạn khi chạy Docker thì không còn màn hình terminal để tương tác trên container, việc dùng Tmux có thể giải quyết được vấn đề nêu trên.
- ctrl B " để chia chiều ngang

- ctrl B % để chia chiều dọc
- ctrl B hướng để di chuyển con trỏ
- exit để thoát tmux
# Ngrok
Ngrok dùng để deploy tạm tời sản phẩm, đến khách hàng người dùng thuận tiện cho việc góp ý thay đổi sản phẩm. Hay nói một cách đơn giản là deploy sản phẩm thông qua Ngrok, một Website local có thể được người dùng internet truy cập được.
## Cài đặt Ngrok
    curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null && echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list && sudo apt update && sudo apt install ngrok

Truy cập ``https://dashboard.ngrok.com/get-started/your-authtoken`` và tạo tài khoản để lấy mã xác thực và thêm vào cấu hình Ngrok trong máy cần cài.

    ngrok config add-authtoken 31eR31eRxxxxxxxxxxxxxxxxxxxxxxxxxHfR7QXyKUN8j1ry
Chạy Container

    docker run -p  5000:8000 test-docker
    Hoặc
    docker compose up --build
Dùng Ngrok

    ngrok http 8000

Kết quả cho ra sẽ là một chuỗi ngẫu nhiên Alias của tên miền ngrok ví dụ ``https://40644d56164b.ngrok-free.app/`` mỗi lần dùng ngrok chuỗi ngẫu nhiên sẽ được thay đổi, người dùng có thể truy cập vào product trong container bằng đường dẫn này.
# Copy ảnh từ máy thật windows sang container proxmox bằng powershell
192.168.29.200 là IP của Container

    scp "C:\Users\dhuyn\Desktop\luongcamdao.jpg" root@192.168.29.200:/home/test-docker/static/images/

