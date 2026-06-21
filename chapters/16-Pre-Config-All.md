# Chapter 16. 전체 Pre-Config

> R1 ~ R5 라우터의 초기 설정 전체 모음

---

## 📄 R1

```bash
en
conf t
!
no ip domain-lookup
!
enable secret cisco
!
hostname R1
!
line con 0
 logging syn
 exec-timeout 0 0
 password cisco
 login
!
line vty 0 4
 password cisco
 login
!
interface loopback 0
 ip add 13.13.1.1 255.255.255.0
!
interface fastethernet0/0
 ip add 150.1.13.1 255.255.255.0
 no shutdown
 bandwidth 100000
!
interface fastethernet0/1
 ip add 13.13.11.1 255.255.255.0
 no shutdown
 bandwidth 100000
!
interface serial 1/0
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
!
interface serial 1/0.123 multipoint
 frame-relay map ip 13.13.9.2 102 broadcast
 frame-relay map ip 13.13.9.3 102 broadcast
 ip add 13.13.9.1 255.255.255.0
!
interface serial 1/1
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
 ip add 13.13.10.1 255.255.255.0
 frame-relay map ip 13.13.10.3 113 broadcast
 end
```

---

## 📄 R2

```bash
en
conf t
!
no ip domain-lookup
!
enable secret cisco
!
hostname R2
!
line con 0
 logging syn
 exec-timeout 0 0
 password cisco
 login
!
line vty 0 4
 password cisco
 login
!
interface loopback 0
 ip add 13.13.2.2 255.255.255.0
!
interface fastethernet0/1
 ip add 13.13.12.2 255.255.255.0
 no shutdown
 bandwidth 100000
!
interface serial1/0
 encapsulation frame-relay
 no frame-relay inverse-arp
 no shutdown
!
interface serial1/0.123 multipoint
 description ### OSPF Area 0 ###
 ip add 13.13.9.2 255.255.255.0
 frame-relay map ip 13.13.9.1 201 broadcast
 frame-relay map ip 13.13.9.3 203 broadcast
 end
```

---

## 📄 R3

```bash
en
conf t
!
no ip domain-lookup
!
enable secret cisco
!
hostname R3
!
line con 0
 logging syn
 exec-timeout 0 0
 password cisco
 login
!
line vty 0 4
 password cisco
 login
!
interface loopback 0
 ip add 13.13.3.3 255.255.255.0
!
interface fastethernet0/0
 ip add 150.3.13.3 255.255.255.0
 no shutdown
 bandwidth 100000
!
interface fastethernet0/1
 ip add 13.13.13.3 255.255.255.0
 no shutdown
 bandwidth 100000
!
interface serial 1/0
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
!
interface serial 1/0.123 multipoint
 frame-relay map ip 13.13.9.2 302 broadcast
 frame-relay map ip 13.13.9.1 302 broadcast
 ip add 13.13.9.3 255.255.255.0
!
interface serial 1/1
 no shutdown
 encapsulation frame-relay
 no frame-relay inverse-arp
 ip add 13.13.10.3 255.255.255.0
 frame-relay map ip 13.13.10.1 311 broadcast
 end
```

---

## 📄 R4

```bash
en
conf t
!
no ip domain-lookup
!
hostname R4
!
line con 0
 logging syn
 exec-timeout 0 0
 password cisco
 login
!
line vty 0 4
 password cisco
 login
!
interface loopback 0
 ip add 13.13.4.4 255.255.255.0
!
interface fastethernet0/0
 ip add 150.1.13.4 255.255.255.0
 no shutdown
 bandwidth 100000
!
interface fastethernet0/1
 ip address 13.13.14.4 255.255.255.0
 no shutdown
 bandwidth 100000
 end
```

---

## 📄 R5

```bash
en
conf t
!
no ip domain-lookup
!
hostname R5
!
line con 0
 logging syn
 exec-timeout 0 0
 password cisco
 login
!
line vty 0 4
 password cisco
 login
!
interface loopback 0
 ip add 13.13.5.5 255.255.255.0
!
interface fastethernet0/0
 ip address 150.3.13.5 255.255.255.0
 no shutdown
 bandwidth 100000
!
interface fastethernet0/1
 ip address 13.13.15.5 255.255.255.0
 no shutdown
 bandwidth 100000
 end
```

---

## 🎓 종합 정리

### 🌟 Stub Area 비교표

| 기능 | 차단 LSA | Default | ASBR 허용 |
|------|----------|---------|-----------|
| **Stub** | Type 4, 5 | 자동 | ❌ |
| **Totally Stub** | Type 3, 4, 5 | 자동 | ❌ |
| **NSSA** | Type 4, 5 | 수동 | ✅ (Type-7) |
| **Totally NSSA** | Type 3, 4, 5 | 자동 | ✅ (Type-7) |

### 🌟 Summary 명령어 비교

| 요약 위치 | 명령어 | 대상 LSA |
|-----------|--------|----------|
| **ABR** | `area X range A.B.C.D mask` | Type-3 (Internal) |
| **ASBR** | `summary-address A.B.C.D mask` | Type-5 (External) |

### 🌟 OSPF Network Type 비교

| Network Type | DR/BDR | Hello/Dead | 적용 환경 |
|--------------|--------|------------|-----------|
| Broadcast | O | 10/40 | Ethernet |
| Non-Broadcast | O | 30/120 | Frame-Relay (기본) |
| Point-to-Point | X | 10/40 | HDLC, PPP |
| Point-to-Multipoint | X | 30/120 | Frame-Relay Hub-Spoke |

---



⬅️ **이전:** [15. OSPF 경로 요약](./15-OSPF-Summary.md)  
🏠 **메인:** [README](../README.md)
