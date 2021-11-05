Cài đặt trên Ubuntu với snap

# cài snap nếu chưa có (bản Ubutu mới có sẵn)

sudo apt update
sudo apt install snapd

# cài k9s

sudo snap install k9s

Hoặc
curl -Lo k9s.tgz https://github.com/derailed/k9s/releases/download/v0.24.1/k9s_Linux_x86_64.tar.gz
tar -xf k9s.tgz
sudo install k9s /usr/local/bin/
