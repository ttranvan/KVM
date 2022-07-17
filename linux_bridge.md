# LINUX BRIDGE

## ***Mục lục***

[1. Tổng quan về Linux Bridge](#1)

- [1.1. Giới thiệu về Linux Bridge](#1.1)

- [1.2. Cấu trúc hệ thống sử dụng Linux bridge](#1.2)

[2. Tìm hiểu Linux Bridge](#2)

- [2.1. Cấu trúc và các thành phần ](#2.1)

- [2.2. Các tính năng của Linux bridge](#2.2)

[3. Các thao tác quản lý Linux bridge](#3)

- [3.1. Cài đặt công cụ quản lý Linux Bridge](#3.1)

- [3.2. Một số câu lệnh với Linux bridge](#3.2)

[4. Ghi chép về một số khái niệm liên quan](#4)

- [4.1. Port](#4.1)

- [4.2. Uplink port](#4.2)

- [4.3. Tap interface](#4.3)

- [4.4. Cấu hình Linux bridge khởi động cùng hệ thống](#4.4)

[5. Lab cơ bản ](#5)

- [5.1. Chuẩn bị và mô hình](#5.1)

- [5.2. Cấu hình](#3.2)

[6. Tham khảo](#6)

---

<a name = "1"></a>
# 1. Tổng quan vê Linux Bridge

<a name = "1.1"></a>
## 1.1. Giới thiệu

-	**Linux bridge** là một soft switch –  1 trong 3 công nghệ cung cấp switch ảo trong hệ thống Linux (bên cạnh macvlan và OpenvSwitch), giải quyết vấn đề ảo hóa network bên trong các máy vật lý. 

-	Bản chất, linux bridge sẽ tạo ra các switch layer 2 kết nối các máy ảo (VM) để các VM đó giao tiếp được với nhau và có thể kết nối được ra mạng ngoài. Linux bridge thường sử dụng kết hợp với hệ thống ảo hóa KVM-QEMU.

-	Linux Bridge thật ra chính là một switch ảo và được sử dụng với ảo hóa KVM/QEMU. ***Nó là 1 module trong nhân kernel***. Sử dụng câu lệnh `brctl` để quản lý .

-	Mô tả linux bridge (trường hợp cơ bản nhất):

<img src = "http://imgur.com/LpMlNof.jpg">

***Lưu ý***: Linux bridge được dùng kết hợp với các card ethernet của máy host. Không sử dụng với card wireless. (phần này tìm hiểu sau). 

<a name = "1.2"></a>
## 1.2. Cấu trúc hệ thống sử dụng  Linux bridge

<img src = "http://imgur.com/7d8bY6u.jpg">

Khái niệm về physical port và virtual port:

\- **Virtual Computing Device**: Thường được biết đến như là máy ảo VM chạy trong host server 

\- **Virtual NIC (vNIC)**: máy ảo VM có virtual network adapters(vNIC) mà đóng vai trò là NIC cho máy ảo.

\- **Physical swtich port**: Là port sử dụng cho Ethernet switch, cổng vật lý xác định bởi các port RJ45. Một port RJ45 kết nối tới port trên NIC của máy host.

\- **Virtual swtich port**: là port ảo tồn tại trên virtual switch. Cả virtual NIC (vNIC) và virtual port đều là phần mềm, nó liên kết với virtual cable kết nối vNIC

<a name = '2'></a>
# 2. Tìm hiểu Linux Bridge

<a name = '2.1'></a>
## 2.1. Cấu trúc và các thành phần

Cấu trúc Linux Bridge:

<img src = "http://imgur.com/J0ZvKPk.jpg">

Một số khái niệm liên quan tới linux bridge:

\-	**Port**: tương đương với port của switch thật

\-	**Bridge**: tương đương với switch layer 2

\-	**Tap**: hay tap interface có thể hiểu là giao diện mạng để các VM kết nối với bridge cho linux bridge tạo ra (nó nằm trong nhân kernel, hoạt động ở lớp 2 của mô hình OSI)

\-	**fd**: forward data - chuyển tiếp dữ liệu từ máy ảo tới bridge.

<a name = "2.2"></a>
## 2.2. Các tính năng

\- 	**STP**: Spanning Tree Protocol - giao thức chống loop gói tin trong mạng.

\-	**VLAN**: chia switch (do linux bridge tạo ra) thành các mạng LAN ảo, cô lập traffic giữa các VM trên các VLAN khác nhau của cùng một switch.

\-	**FDB**: chuyển tiếp các gói tin theo database để nâng cao hiệu năng switch.


<a name = "3"></a>
# 3. Các thao tác quản lý Linux Bridge

<a name = '3.1'></a>
## 3.1. Cài đặt công cụ phần mềm quản lý Linux Bridge

Linux bridge được hỗ trợ từ version nhân kernel từ 2.4 trở lên. Để sử dụng và quản lý các tính năng của linux birdge, cần cài đặt gói bridge-utilities (dùng các câu lệnh `brctl` để sử dụng linux bridge). Cài đặt dùng lệnh như sau: 

`sudo  apt-get install bridge-ultils -y `

<a name = '3.2'></a>
## 3.2. Một số câu lệnh quản lý

### BRIDGE MANAGEMENT

|ACTION	|BRCTL	|BRIDGE|
|-|-|-|
|creating bridge|	`brctl addbr <bridge>`| |	
|deleting bridge|	`brctl delbr <bridge>`| |
|add interface (port) to bridge	| `brctl addif <bridge> <ifname>`	| |
|delete interface (port) on bridge |	`brctl delif <bridge> <ifname>`|  |	


### FDB MANAGEMENT

|ACTION	|BRCTL	|BRIDGE|
|-|-|-|
|Shows a list of MACs in FDB|	`brctl showmacs <bridge>`	|`bridge fdb show`|
|Sets FDB entries ageing time|	`brctl setageingtime  <bridge> <time>`|	|
|Sets FDB garbage collector interval|	`brctl setgcint <brname> <time>`| |	
|Adds FDB entry	|	|`bridge fdb add dev <interface> [dst, vni, port, via]`|
|Appends FDB entry|		|`bridge fdb append` (parameters same as for fdb add)|
|Deletes FDB entry|		|`bridge fdb delete ` (parameters same as for fdb add)|

### STP MANAGEMENT

|ACTION	|BRCTL	|BRIDGE|
|-|-|-|
|Turning STP on/off	|`brctl stp <bridge> <state>`| |	
|Setting bridge priority|	`brctl setbridgeprio <bridge> <priority>`	| |
|Setting bridge forward delay|	`brctl setfd <bridge> <time>`	| |
|Setting bridge 'hello' time|	`brctl sethello <bridge> <time>`| |	
|Setting bridge maximum message age|	`brctl setmaxage <bridge> <time>`	| |
|Setting cost of the port on bridge|	`brctl setpathcost <bridge> <port> <cost>`|	`bridge link set dev <port> cost <cost>`|
|Setting bridge port priority	|`brctl setportprio <bridge> <port> <priority>`|	`bridge link set dev <port> priority <priority>`|
|Should port proccess STP BDPUs	|	|`bridge link set dev <port > guard [on, off]`|
|Should bridge might send traffic on the port it was received|		|`bridge link set dev <port> hairpin [on,off]`|
|Enabling/disabling fastleave options on port|		|`bridge link set dev <port> fastleave [on,off]`|
|Setting STP port state	|	|`bridge link set dev <port> state <state>`|

### VLAN MANAGEMENT

|ACTION|	BRCTL|	BRIDGE|
|-|-|-|
|Creating new VLAN filter entry|		|`bridge vlan add dev <dev> [vid, pvid, untagged, self, master]`|
|Delete VLAN filter entry	|	|`bridge vlan delete dev <dev>` (parameters same as for vlan add)|
|List VLAN configuration|		|`bridge vlan show`|


***Lưu ý*** : Các ảnh hưởng của câu lệnh chỉ là tạm thời cho đến khi máy host khởi động lại, sau khi khởi động lại, toàn bộ thông tin sẽ bị mất. 

<a name = '4'></a>
# 4.	Ghi chép về một số khái niệm liên quan

<a name = '4.1'></a>
## 4.1.	Port

- Trong networking, khái niệm port đại diện cho điểm vào ra của dữ liệu trên máy tính hoặc các thiết bị mạng. Port có thể là khái niệm phần mềm hoặc phần cứng. 
Software port là khái niệm tồn tại trong hệ điều hành. Chúng thường là các điểm vào ra cho các lưu lượng của ứng dụng. Tức là khái niệm port mức logic. Ví dụ: port 80 trên server liên kết với Web server và truyền các lưu lượng HTTP. 

- Hardware port (port khái niệm phần cứng): là các điểm kết nối lưu lượng ở mức khái niệm vật lý trên các thiết bị mạng như switch, router, máy tính, … ví dụ: router với cổng kết nối RJ45 (L2/Ethernet) kết nối tới máy tính của bạn.

- Physical switch port: Thông thường chúng ta hay sử dụng các switch L2/ethernet với các cổng RJ45. Một đầu connector RJ45 kết nối port trên switch tới các port trên NIC của máy tính. 

- Virtual switch port: giống như các physical switch port mà tổn tại như một phần mềm trên switch ảo. cả virtual NIC và virtual port đều duy trì bởi phần mềm, được kết nối với nhau thông qua virtual cable.

<a name = '4.2'></a>
## 4.2.	Uplink port

-	Uplink port là khái niệm chỉ điểm vào ra của lưu lượng trong một switch ra các mạng bên ngoài. Nó sẽ là nơi tập trung tất cả các lưu lượng trên switch nếu muốn ra mạng ngoài. 

    ![img](../../images/1.1.png)

-	Khái niệm virtual uplink switch port được hiểu có chức năng tương đương, là điểm để các lưu lượng trên các máy guest ảo đi ra ngoài máy host thật, hoặc ra mạng ngoài. Khi thêm một interface trên máy thật vào bridge (tạo mạng bridging với interface máy thật và đi ra ngoài), thì interface trên máy thật chính là virtual uplink port.

<a name = '4.3'></a>
## 4.3.	Tap interface

- Ethernet port trên máy ảo VM (mô phỏng pNIC) thường gọi là vNIC (Virtual NIC). Virtual port được mô phỏng với sự hỗ trợ  của KVM/QEMU.

- Port trên máy ảo VM chỉ có thể xử lý các frame Ethernet. Trong môi trường thực tế (không ảo hóa) interface NIC vật lý sẽ nhận và xử lý các khung Ethernet. Nó sẽ bóc lớp header và chuyển tiếp payload (thường là gói tin IP) tới lên cho hệ điều hành. Tuy nhiên, với môi trường ảo hóa, nó sẽ không làm việc vì các virtual NIC sẽ mong đợi các khung Ethernet. 

- Tap interface là một khái niệm về phần mềm được sử dụng để nói với Linux bridge là chuyến tiếp frame Ethernet vào nó. Hay nói cách khác, máy ảo kết nối tới tap interface sẽ có thể nhận được các khung frame Ethernet thô. Và do đó, máy ảo VM có thể tiếp tục được mô phỏng như là một máy vật lý ở trong mạng.

- Nói chung, tap interface là một port trên switch dùng để kết nối với các máy ảo VM.

<a name = '4.4'></a>

