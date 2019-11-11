# IPSec Tunnel

https://www.youtube.com/watch?v=kqrWjR2Nn7Q

```bash
[root@ip-172-31-41-119 ipsec.d]# cat aws-vpn.conf 
conn Tunnel1
  authby=secret
  auto=start
  left=%defaultroute
  leftid=3.81.30.228
  right=34.255.22.202
  type=tunnel
  ikelifetime=8h
  keylife=1h
  phase2alg=aes128-sha1;modp1024
  ike=aes128-sha1;modp1024
  keyingtries=%forever
  keyexchange=ike
  leftsubnet=172.31.0.0/16
  rightsubnet=10.0.0.0/24
  dpddelay=10
  dpdtimeout=30
  dpdaction=restart_by_peer
```

```bash
[root@ip-172-31-41-119 ipsec.d]# cat aws-vpn.secrets 
3.81.30.228 34.255.22.202: PSK "bUtdYYmmRjLDLKtpuEkYSQ_p_sBqvzmq"
```