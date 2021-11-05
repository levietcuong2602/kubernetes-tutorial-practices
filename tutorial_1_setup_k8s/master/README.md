SSH Node Master

vagrant ssh
sudo su

Khởi tạo Cluster
Trong lệnh khởi tạo cluster có tham số --pod-network-cidr để chọn cấu hình mạng của POD, do dự định dùng Addon calico nên chọn --pod-network-cidr=192.168.0.0/16
Gõ lệnh sau để khởi tạo là nút master của Cluster

kubeadm init --apiserver-advertise-address=172.16.10.100 --pod-network-cidr=192.168.0.0/16

Sau khi lệnh chạy xong, chạy tiếp cụm lệnh nó yêu cầu chạy sau khi khởi tạo- để chép file cấu hình đảm bảo trình kubectl trên máy này kết nối Cluster

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

Tiếp đó, nó yêu cầu cài đặt một Plugin mạng trong các Plugin tại addon, ở đây đã chọn calico, nên chạy lệnh sau để cài nó

kubectl apply -f https://docs.projectcalico.org/v3.10/manifests/calico.yaml

Gõ vài lệnh sau để kiểm tra

# Thông tin cluster

kubectl cluster-info

# Các node trong cluster

kubectl get nodes

# Các pod đang chạy trong tất cả các namespace

kubectl get pods -A

# Xem nội dung cấu hình kubectl gõ lệnh

kubectl config view
