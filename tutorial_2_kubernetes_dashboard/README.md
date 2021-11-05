Tải file này về lưu thành tên dashboard-v2.yaml
curl https://raw.githubusercontent.com/kubernetes/dashboard/v2.4.0/aio/deploy/recommended.yaml > dashboard-v2.yaml

Triển khai dashboard-v2.yaml
Bây giờ ta sẽ triển khai các thành phần định nghĩa, cấu hình trong dashboard-v2.yaml, bạn thực hiện lệnh:
kubectl apply -f dashboard-v2.yaml

Tạo kubernetes-dashboard-certs, xác thực SSL
Ta sẽ dùng OpenSSL để sinh certificates tự xác thực SSL (ngoài ra khi triển khai thực tế có thể lấy các certificate do mua hoặc miễn phí từ https://letsencrypt.org/ theo Domain), chạy các lệnh sau:
sudo mkdir certs
sudo chmod 777 -R certs
openssl req -nodes -newkey rsa:2048 -keyout certs/dashboard.key -out certs/dashboard.csr -subj "/C=/ST=/L=/O=/OU=/CN=kubernetes-dashboard"
hoặc
openssl req -nodes -new -newkey rsa:2048 -keyout certs/dashboard.key -out certs/dashboard.csr -subj "/C=US/ST=YourState/L=YourCity/O=Example-Certificates/CN=kubernetes-dashboard"

openssl x509 -req -sha256 -days 365 -in certs/dashboard.csr -signkey certs/dashboard.key -out certs/dashboard.crt
sudo chmod -R 777 certs

Sau khi hoàn thành thì các thông tin certificate lưu ở thư mục certs, chạy lệnh sau để tạo Secret
kubectl create secret generic kubernetes-dashboard-certs --from-file=certs -n kubernetes-dashboard

Triển khai metrics server
metrics server trong kubernetes (metrics server) giám sát về tài nguyên sử dụng trên cluster, cung cấp các API để các thành phần khác truy vấn đến biết được và mức độ sử dụng tài nguyên (CPU, Memory) của Pod, Node ... Cần có Metric Server để HPA hoạt động chính xác
Để triển khai, lấy về mã nguồn từ GitHub

git clone git@github.com:kubernetes-sigs/metrics-server.git

Mở file metrics-server/deploy/1.8+/metrics-server-deployment.yaml tìm đến dòng args: sửa lại thành

        args:
          - --cert-dir=/tmp
          - --secure-port=4443
          - --kubelet-insecure-tls
          - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname

Sau đó triển khai
kubectl apply -f deploy/1.8+/

Các cập nhật mới của mã nguồn Metric trên GitHub đã xóa thư mục triển khai metrics-server/deploy/1.8+/, để triển khai bạn tải về file components.yaml mới tại https://github.com/kubernetes-sigs/metrics-server/releases
Sửa file components.yml như trên rồi thực hiện lệnh:

kubectl apply -f components.yml

Khi có Metric server thì trong Dashboard có thêm thống kê về CPU, Memory của các POD, và có thêm lệnh xem tài nguyên về Node, Pod

kubectl top no
kubectl top pod
