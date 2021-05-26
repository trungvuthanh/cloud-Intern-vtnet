# VXLAN

### Môi trường

- 2 máy ảo OS: Centos 7.9
- Network: 192.168.1.0/24
- Software: Openvswitch 2.13.3

### Topo

````
VM 1                                                           VM 2

+--------------+                                   +--------------+
|     br0      |                                   |     br0      |
|  10.0.0.1/24 |                                   |  10.0.0.2/24 |
+--------------+                                   +--------------+
|    vxlan0    |                                   |    vxlan0    |
+--------------+                                   +--------------+
        |                                                  |
        |                                                  |
        |                                                  |
+--------------+                                   +--------------+
|     eth0     |                                   |     eth0     |
|192.168.1.1/24|                                   |192.168.1.2/24|
+--------------+                                   +--------------+
        |                                                  |
        ----------------------------------------------------
````

### Cấu hình

- VM1:

```bash
ovs-vsctl add-br br0
ovs-vsctl add-port br0 vxlan0 -- set int vxlan0 type=vxlan options:remote_ip=192.168.1.2
ip addr add 10.0.0.1/24 dev br0
ifconfig br0 up
```

- VM2 tương tự

### Ưu điểm của Vxlan

- Mở rộng số lượng mạng ảo có thể cấp phát: 2^24^ so với 2^12^ của vlan
- Vxlan hoạt động trên L3 nên tận dụng được lợi thế của mạng L3: tránh loop, sử dụng ECMP tăng hiệu suất sử dụng so với STP ở L2
- Tính transparent: cung cấp mạng ảo trong suốt với hạ tầng mạng underlay
- Tính isolate: tách biệt các mạng ảo với nhau, giảm BUM traffic

### Nhược điểm của Vxlan

- Gói tin được đóng gói thêm nhiều layer, kích thước tăng lên gây giảm hiệu năng mạng:  tiêu tốn thêm tài nguyên xử lý, giảm băng thông truyền tải
- Cấu hình phức tạp hơn mạng vlan
- Troubleshoot khó hơn do tính transparent
- Khó tích hợp với các thiết bị mạng truyền thống không hỗ trợ vxlan
