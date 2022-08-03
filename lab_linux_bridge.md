#
# 1 NAT based network


- config: Cấu hình nat ra interface ens33 trên Host và tạo bridge virbr5

'<network>
  <name>nat2</name>
  <uuid>43c613e4-18a4-4857-bdfe-84af0299c511</uuid>
  <forward dev='ens33' mode='nat'>
    <interface dev='ens33'/>
  </forward>
  <bridge name='virbr5' stp='on' delay='2'/>
  <mac address='52:54:00:c1:7c:e5'/>
  <ip address='172.16.10.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='172.16.10.100' end='172.16.10.253'/>
    </dhcp>
  </ip>
</network>'
