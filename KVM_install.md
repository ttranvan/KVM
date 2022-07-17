# 1. Cài đặt
## 1.1 Kiểm tra CPU có hỗ trợ ảo hóa:
> $ egrep -c '(vmx|svm)' /proc/cpuinfo
>  sudo kvm-ok

Kết quả lớn hơn 0 hì CPU được hỗ trợ.
## 1.2 Cài đặt các gói và phụ thuộc

> sudo apt -y install bridge-utils cpu-checker libvirt-clients libvirt-daemon qemu qemu-kvm

Trong đó:
- qemu (quick emulator): Cho phép ảo hóa phần cứng
- qemu-kvm: Package chính của KVM
-  libvritd-daemon: virtualization daemon
-  bridge-utils : mạng
-  virt-manager  : quản lí VM qua UI

Kiểm tra trạng thái của libvirt:
> sudo systemctl status libvirtd

Cấu hình libvirtd chạy cùng hệ thống:
>  sudo systemctl enable libvirtd

Kiểm tra KVM đã được load:
> lsmod | grep -i kvm
