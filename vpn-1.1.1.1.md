Cloudflare cung cấp dịch vụ VPN miễn phí 1.1.1.1 với Warp

Cách cài đặt VNPT CloudFlare WARP trên Ubuntu

Bước 1: Tải và cài đặt gói Cloudflare WARP
```
sudo apt update
sudo apt install curl gnupg lsb-release
curl https://pkg.cloudflareclient.com/pubkey.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloudflare-warp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/cloudflare-warp-archive-keyring.gpg] https://pkg.cloudflareclient.com/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/cloudflare-client.list

sudo apt update
sudo apt install cloudflare-warp
```

Bước 2: Đăng ký thiết bị và kết nối Warp
```
sudo warp-cli registration new

NOTICE:

Cloudflare only collects limited DNS query and traffic data (excluding payload)
that is sent to our network when you have the app enabled on your device. We
will not sell, rent, share, or otherwise disclose your personal information to
anyone, except as otherwise described in this Policy, without first providing
you with notice and the opportunity to consent. All information is handled in
accordance with our Privacy Policy.

More information is available at:
- https://www.cloudflare.com/application/terms/
- https://www.cloudflare.com/application/privacypolicy/

Accept Terms of Service and Privacy Policy? [y/N] y
Success


sudo warp-cli connect

sudo warp-cli status
Status update: Connected
```

Bước 3: Một số lệnh hữu ích khác
```
Lệnh	Chức năng
warp-cli connect	Kết nối VPN
warp-cli disconnect	Ngắt kết nối VPN
warp-cli enable-always-on	Tự động bật VPN
warp-cli disable-always-on	Tắt tự động bật
warp-cli warp-stats	Xem thống kê
warp-cli set-mode warp+doh	Bật Warp (VPN đầy đủ)
warp-cli set-mode doh	Chỉ dùng DNS 1.1.1.1 (không VPN)
```
