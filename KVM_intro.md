# 1. Kiến trúc KVM
## KVM 
(Kernel-based virtual machine) là giải pháp ảo hóa cho hệ thống Linux trên nền tảng phần cứng x86 có các module mở rộng hỗ trợ ảo hóa (Intel VT-x hoặc AMD AMD-V)
- Trong kiến trúc của KVM, Virtual machine được thực hiện như là quy trình xử lý thông thường của Linux, được lập lịch hoạt động như các scheduler tiểu chuẩn của Linux.Mỗi CPU ảo hoạt động như một tiến trình xử lý của Linux.
- VM không thực sự là một hypervisor có chức năng giải lập phần cứng để chạy các máy ảo.KVM  là một module của kernel linux hỗ trợ cơ chế mapping các chỉ dẫn trên VCPU  sang chỉ dẫn trên CPU Physical. KVM giống như một driver cho hypervisor để sử dụng được tính năng ảo hóa của các vi xử lý như Intel VT-x hay AMD-V, mục tiêu là tăng hiệu suất cho guest VM.
- KVM là một mudule nằm trong nhân Linux để có thể tạo ra không gian cho các ứng dụng để các ứng dụng đó có thể chạy các tính với quyền lớn nhất. Qemu: là một hypervisor dạng Paravirtualization
- KVM-QEMU là ảo hóa kết hợp QEMU với KVM theo kiểu QEMU sẽ móc nối với mudule KVM trở thành dạng ảo hóa Full virtualization

*Có thể hình dung KVM giống như driver cho hypervisor để sử dụng được virtualization extension của physical CPU nhằm boost performance cho guest VM. KVM như định nghĩa trên trang chủ thì là core virtualization infrastructure mà thôi, nó được các hypervisor khác lợi dụng làm back-end để tiếp cận được các công nghệ hardware acceleration (Dịch code để mô phỏng phần cứng)*

<img src=https://s8.gifyu.com/images/image1a35043b63e99e9e.png>

## KVM-QEMU
QEMU ban đầu không phải là thành phần của KVM, nó là hệ thống ảo hóa được thực hiền bằng phần mềm với hiệu suất thấp
- QEMU là một Emulator có bộ dịch của là TCG (Tiny Code Generate) để xử lý các yêu cầu trên CPU ảo và giả lập kiến trúc của máy ảo.QEMU chứa bộ ảo hóa hoàn chỉnh bao gồm CPU, MEM virtualization và các Device emulation (network card, hardisk ..) mà KVM cần sử dụng
- Trong khi chạy VM QEMU sẽ nhập kernel thông qua lệnh call từ KVM module.KVM sẽ đưa VM vào chế độ đặc biệt để xử lý, khi VM thực hiện I/O thì QEMU phân tích cú pháp và mô phỏng các thiết bị này,Các tính năng như tạo , cấu hình, tương tác với VM hoặc migrate đều do QEMU thực hiện

 *Nên có thể coi, QEMU như là một hypervisor type 2 (hypervisor chỉ chung cho chức năng ảo hóa). Nhằm nâng cao hiệu suất của VM. Cụ thể, lúc tạo VM bằng QEMU có VirtType là KVM thì khi đó các instruction có nghĩa đối với virtual CPU sẽ được QEMU sử dụng KVM để mapping thành các instruction có nghĩa đối với physical CPU. Làm như vậy sẽ nhanh hơn là chỉ chạy độc lập QEMU, vì nếu không có KVM thì QEMU sẽ phải quay về (fall-back) sử dụng translator của riêng nó là TCG để chuyển dịch các instruction của virtual CPU rồi đem thực thi trên physical CPU.*
<img src=https://s8.gifyu.com/images/image25271741fc94e209.png>

# 2.Các tool điều khiển KVM-QEMU

- virsh : libvirt-based  CLI
- virt-manager:  libvirt-based GUI
- svirt: Security tools
- virt-v2v: công cụ format migrate VM
- virt-* tools: bao gồm virt-install (Tạo VM), Virt-viewer ...
- libvirt: API ảo hóa để vận hành và quản lý các máy ảo KVM. Tương thích nhiều loại hypervisor:  KVM, vmware, XEN, Hyper-v, LXC, etc ..

*Đối với từng dạng ảo hóa như Kvm, Xen, .. sẽ có một tiến trình Libvirt chạy để điều khiển các dang ảo hóa và cung cấp những API để các tool như virsh, virt-manager, Openstack, ovirt có thể giao tiếp với KVM-Qemu thông qua livbirt*

<img src=https://s8.gifyu.com/images/image97ce7da0c4b58017.png>

# 3 Các tính năng của ảo hóa KVM

 - Hỗ trợ CPU và MEM overcommit: Overcommit được hiểu là việc mapping CPU, RAM, DISK lên vài lần hoặc vài chục lần. [overcommit](https://github.com/ttranvan/KVM/blob/main/overcommit.md)
 - Hỗ trợ Paravirtualized I/O (virtio):là một chuẩn ảo hóa cho các thiết bị mạng và đĩa cứng. Nói một cách dễ hiểu thì virtio làm cho máy ảo biết nó là máy ảo, và vì thế sẽ hợp tác với trình ảo hóa  để tăng tốc các thiết bị mạng và ổ cứng (còn bình thường nó sẽ chạy như một máy thật, sử dụng các thiết bị ảo, các thiết bị này rất phức tạp, khiến cho tốc độ chậm...) tham khảo: https://www.linux-kvm.org/page/Virtio
- Hỗ trợ hot swap (CPU, Block device, network ..)
- Hỗ trợ Live Migration : Cho phép di chuyển máy ảo không downtime 
- Hỗ trợ PCI Device Direct Assignment and Single Root I/O Virtualization (SR-IOV)

