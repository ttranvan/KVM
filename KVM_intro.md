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