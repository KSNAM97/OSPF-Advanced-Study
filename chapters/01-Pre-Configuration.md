# Chapter 01. Pre-Configuration

> Dynamips 실행 후 SecureCRT로 R1 ~ R5에 접속하여 기본 인터페이스 및 Frame-Relay 매핑 상태 확인

---

## 🎯 목표

- R1 ~ R5 라우터 접속 및 인터페이스 상태 확인
- Frame-Relay 매핑 및 직접 연결 통신 확인 (ping)

---

## 🔍 R1 확인

```bash
R1# show ip route
     13.0.0.0/24 is subnetted, 4 subnets
C       13.13.1.0 is directly connected, Loopback0
C       13.13.9.0 is directly connected, Serial1/0
C       13.13.10.0 is directly connected, Serial1/1
C       13.13.11.0 is directly connected, FastEthernet0/1
     150.1.0.0/24 is subnetted, 1 subnets
C       150.1.13.0 is directly connected, FastEthernet0/0

R1# ping 13.13.9.2
R1# ping 13.13.9.3
R1# ping 13.13.10.3
R1# ping 150.1.13.4
```

---

## 🔍 R2 확인

```bash
R2# show ip route
     13.0.0.0/24 is subnetted, 3 subnets
C       13.13.2.0 is directly connected, Loopback0
C       13.13.9.0 is directly connected, Serial1/0.123
C       13.13.12.0 is directly connected, FastEthernet0/1

R2# ping 13.13.9.1
R2# ping 13.13.9.3
```

---

## 🔍 R3 확인

```bash
R3# show ip route
     13.0.0.0/24 is subnetted, 4 subnets
C       13.13.3.0 is directly connected, Loopback0
C       13.13.9.0 is directly connected, Serial1/0
C       13.13.10.0 is directly connected, Serial1/1
C       13.13.13.0 is directly connected, FastEthernet0/1
     150.3.0.0/24 is subnetted, 1 subnets
C       150.3.13.0 is directly connected, FastEthernet0/0

R3# ping 13.13.9.1
R3# ping 13.13.9.2
R3# ping 13.13.10.1
R3# ping 150.3.13.5
```

---

## 🔍 R4 확인

```bash
R4# show ip route
     13.0.0.0/24 is subnetted, 2 subnets
C       13.13.4.0 is directly connected, Loopback0
C       13.13.14.0 is directly connected, FastEthernet0/1
     150.1.0.0/24 is subnetted, 1 subnets
C       150.1.13.0 is directly connected, FastEthernet0/0

R4# ping 150.1.13.1
```

---

## 🔍 R5 확인

```bash
R5# show ip route
     13.0.0.0/24 is subnetted, 2 subnets
C       13.13.5.0 is directly connected, Loopback0
C       13.13.15.0 is directly connected, FastEthernet0/1
     150.3.0.0/24 is subnetted, 1 subnets
C       150.3.13.0 is directly connected, FastEthernet0/0

R5# ping 150.3.13.3
```

---

➡️ **다음 챕터:** [02. OSPF Frame-Relay 구성 (Area 0)](./02-OSPF-FrameRelay-Area0.md)
