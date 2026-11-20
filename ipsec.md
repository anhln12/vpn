1. Kiến trúc hai giai đoạn: IKE (Phase 1) và IPsec (Phase 2)

Phase 1 (IKS SA - Internet Key Exchange): 2 GW xác thực danh tính (Pre-Shared Key, Certificate ...) và thương lượng thuật toán mã hóa/băm để tạo một kênh điều khiển bảo mật (IKE Security Association). Kênh này dùng trao đổi khóa an toàn cho Phase 2.

Phase 2 (Child SA/IPsec SA): Dựa trên kênh của Phase 1 để thương lượng thông số mã hóa dữ liệu thực tế (ESP - Encapsulating Security Payload) cho từng cặp Local Subnet <-> Remote Subnet. Dữ liệu ứng dụng sẽ đi qua các Child SA này.

Lưu ý: Phase 1 có thể UP nhưng Phase 2 không lên nếu lệch subnet, lệch cipher/DH group, hoặc thiết traffic khớp selector.

2. Các khái niệm bắt buộc phải hiểu
* ESP & AH: Thực tế hầu hết các VPN hiện nay dùng ESP vì vừa mã hóa vừa xác thực toàn vẹn gói tin, đồng thời hỗ trợ xuyên NAT tốt hơn AH (AH ký cả IP header nên thường gãy khi đi qua NAT)
* NAT Traversal (NAT-T - UDP 4500): Khi gateway nằm sau NAT, gói ESP thuần dễ bị router NAT xử lý lỗi. IPsec sẽ bọc gói ESP bên trong UDP port 4500 để đi qua NAT an toàn; giai đoạn khởi tạo IKE dùng UDP port 500.
* Policy-based vs Route-based (VTI): 
  - Policy-based: khai báo trực tiếp các cặp subnet (Traffic Selectors) trong Phase 2; IPsec tự bắt gói tin khớ subnet để đẩy vào tunnel.
  - Route-based (VTI/ipsec interface): Tạo giao diện ảo (inpseX), định tuyến bằng routing table/static route hoặc giao thức định tuyến động. Linh hoạt khi mở rộng nhiều subnet hoặc chạy routing động.
* PFS (Perfect Forward Secrecy): Bắt buộc trao đổi Diffie-Hellman (DH) mới mỗi khi tạo/rekey Phase 2 SA. Nếu khóa dài hạn bị lộ về sau, các phên vũ vẫn an toàn hơn.

3. Checklist thông số cần khớp với nhau giữa 2 đầu

 Khi cấu hình 2 con OPNSense, Phase 1 và Phase 2 hai đầu phải tương thích.

 |Thành Phần| Điểm cần kiểm tra|
 |---|---|
 |IKE Version|Thông nhất IKEv2|
 |Authentication|Cùng kiểu xác thực (ví dụ dùng PSK) và đúng ID/FQN/IP nếu có cấu hình Local/Remote ID.|
 |Encryption & Hash|Cùng thuật toán mã hóa (ví dụ AES-256-GCM hoặc AES-256-CBC + SHA256) và cùng DH Group (ví dụ Group 14/19/20).|
 |Lifetime|Thời gian sống của Phase 1/ Phase 2|
 |Subnets(Phase2)|Subnet A và Subnet B không được trùng/đè nhau (Tránh overlab IP)|


 4. Kiểm tra khi tunnel không lên hoặc ping không thông
* Check Phase 1/ Phase 2 xem kẹt ở Phase 1 hay Phase 2, đọc log charon (IPsec deamon) để bắt đúng lỗi (no matching proposal, authentication failed, invalid ID_INT ..)
* Check Firewall & NAT: Có mở UDP 500, 4500 trên WAN chưa; có rule cho phép traffic trên interface IPsec (hoặc VTI) chưa, có bị NAT đè lên dải LAN nội bô trước khi vào tunnel không
* Check Routing & Packet Caputure: Dùng tcpdump trên inteface WAN (xem cú UDP 500/4500 qua lại không) và trên LAN/IPsec interface để biết gói tin có bị drop ở chặn nào không 

