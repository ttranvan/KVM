# 1. Kiến trúc KVM
## KVM 
(Kernel-based virtual machine) là giải pháp ảo hóa cho hệ thống Linux trên nền tảng phần cứng x86 có các module mở rộng hỗ trợ ảo hóa (Intel VT-x hoặc AMD AMD-V)
- Trong kiến trúc của KVM, Virtual machine được thực hiện như là quy trình xử lý thông thường của Linux, được lập lịch hoạt động như các scheduler tiểu chuẩn của Linux.Mỗi CPU ảo hoạt động như một tiến trình xử lý của Linux.
- VM không thực sự là một hypervisor có chức năng giải lập phần cứng để chạy các máy ảo.KVM  là một module của kernel linux hỗ trợ cơ chế mapping các chỉ dẫn trên VCPU  sang chỉ dẫn trên CPU Physical. KVM giống như một driver cho hypervisor để sử dụng được tính năng ảo hóa của các vi xử lý như Intel VT-x hay AMD-V, mục tiêu là tăng hiệu suất cho guest VM.
- KVMlà một mudule nằm trong nhân Linux để có thể tạo ra không gian cho các ứng dụng để các ứng dụng đó có thể chạy các tính với quyền lớn nhất. Qemu: là một hypervisor dạng Paravirtualization
- KVM-QEMU là ảo hóa kết hợp QEMU với KVM theo kiểu QEMU sẽ móc nối với mudule KVM trở thành dạng ảo hóa Full virtualization

<img src=https://s8.gifyu.com/images/image1a35043b63e99e9e.png>

## KVM-QEMU