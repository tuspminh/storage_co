### pre:

>  **Kích hoạt Systemd cho Debian trên WSL**
>
> Docker yêu cầu `systemd` để quản lý dịch vụ. Mở Terminal Debian của bạn và chạy lệnh:
>
> bash
>
> ```text-x-trilium-auto
> echo -e "[boot]\nsystemd=true" | sudo tee /etc/wsl.conf
> ```
>
> Sau đó, hãy tắt và khởi động lại WSL bằng cách mở **PowerShell trên Windows** và gõ:
>
> powershell
>
> ```text-x-trilium-auto
> wsl --shutdown
> ```
>
> Mở lại Terminal Debian để tiếp tục.

Để cài đặt hoàn chỉnh Docker trên **Debian 13 (Trixie)** thông qua **WSL**, bạn chỉ cần thực hiện dứt điểm theo quy trình chuẩn hóa định dạng nguồn mới (`DEB822`). [[1](https://www.reddit.com/r/docker/comments/1plsy7t/how_do_i_install_docker_et_all_in_debian_13_trixie/), [2](https://linuxcapable.com/how-to-install-docker-on-debian-linux/)]

Hãy mở Terminal Debian lên và chạy lần lượt các bước sau:

**Bước 1: Cập nhật hệ thống và cài đặt công cụ cần thiết**

Chạy lệnh để làm mới danh sách gói và cài đặt các thành phần bảo mật hỗ trợ tải kho lưu trữ: [[1](https://linuxize.com/post/how-to-install-docker-on-debian-13/)]

bash

```text-x-trilium-auto
sudo apt-get update
sudo apt-get install -y ca-certificates curl
```

**Bước 2: Tải khóa GPG chính thức của Docker**

Tạo thư mục lưu trữ và tải khóa bảo mật để Debian xác thực gói cài đặt từ Docker: [[1](https://linuxize.com/post/how-to-install-docker-on-debian-13/)]

bash

```text-x-trilium-auto
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

**Bước 3: Cấu hình kho lưu trữ chuẩn cho Debian 13**

Sử dụng cấu hình định dạng `.sources` mới theo khuyến nghị của Docker để tránh các lỗi xung đột gói trên Debian Trixie: [[1](https://www.reddit.com/r/docker/comments/1plsy7t/how_do_i_install_docker_et_all_in_debian_13_trixie/)]

bash

```text-x-trilium-auto
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

**Bước 4: Tiến hành cài đặt Docker Engine**

Cập nhật lại danh sách gói để nhận diện kho lưu trữ mới vừa thêm, sau đó cài đặt toàn bộ công cụ Docker & Docker Compose: [[1](https://linuxize.com/post/how-to-install-docker-on-debian-13/), [2](https://www.reddit.com/r/docker/comments/1plsy7t/how_do_i_install_docker_et_all_in_debian_13_trixie/)]

bash

```text-x-trilium-auto
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

**Bước 5: Cấu hình WSL và phân quyền User (Rất quan trọng)**

1. **Bật quyền chạy không cần sudo**: Thêm user hiện tại của bạn vào nhóm `docker` để tiện thao tác về sau:

   bash

   ```text-x-trilium-auto
   sudo usermod -aG docker $USER
   ```
`newgrp docker` để hệ thống cập nhật ngay nhóm docker vào phiên làm việc hiện tại của bạn


2. **Khởi động dịch vụ**: Vì chạy trên WSL, bạn cần đảm bảo Docker Daemon được kích hoạt thông qua systemd:

   bash

   ```text-x-trilium-auto
   sudo systemctl enable --now docker
   ```

   *(Nếu lệnh systemctl báo lỗi, hãy chắc chắn bạn đã bật* `*systemd=true*` *trong file* `*/etc/wsl.conf*` *như hướng dẫn ở các bước đầu tiên và khởi động lại WSL bằng lệnh* `*wsl --shutdown*` *trong PowerShell)*. [[1](https://daniel.es/blog/how-to-install-docker-in-wsl-without-docker-desktop/), [3](https://medium.com/@hasanmcse/docker-issue-definitive-fix-100-working-in-wsl-0a6368aeab21)]

**Bước 6: Kiểm tra kết quả**

Tắt Terminal Debian đi và **mở lại một Terminal mới** để user được cập nhật quyền. Sau đó chạy lệnh: [[1](https://daniel.es/blog/how-to-install-docker-in-wsl-without-docker-desktop/)]

bash

```text-x-trilium-auto
docker run hello-world
```

Nếu màn hình hiển thị lời chào **"Hello from Docker!"**, hệ thống của bạn đã cài đặt thành công và hoạt động hoàn hảo. [[1](https://daniel.es/blog/how-to-install-docker-in-wsl-without-docker-desktop/)]

Bạn có cần hỗ trợ cấu hình thêm về **Docker Compose** hoặc thiết lập **môi trường dev** nào cụ thể trên container không?
