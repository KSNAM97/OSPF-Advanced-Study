# Chapter 02. OSPF Frame-Relay 구성 (Area 0)

> R1, R2, R3 Frame-Relay 구간에 OSPF Area 0 구성

---

## 🎯 요구사항

- OSPF Process = 1, Area = 0, Router-ID = `13.13.X.X` (X = Router 번호)
- OSPF 라우팅 업데이트가 필요한 인터페이스로만 OSPF Packet 송신
- R1, R2, R3의 `Serial1/0.123` 네트워크 → Area 0
- R1, R2, R3의 `Loopback0` 네트워크 → Area 0
- R2의 `FastEthernet0/1` 네트워크 → Area 0
- R1, R2, R3 Frame-Relay 구간에서 **DR/BDR 미선출**

---

## ⚙️ R1 설정

```bash
router ospf 1
 router-id 13.13.1.1
 passive-interface default
 no passive-interface s1/0.123
 network 13.13.1.0 0.0.0.255 area 0
 network 13.13.9.0 0.0.0.255 area 0
```

---

## ⚙️ R2 설정

```bash
router ospf 1
 router-id 13.13.2.2
 passive-interface default
 no passive-interface s1/0.123
 network 13.13.2.0 0.0.0.255 area 0
 network 13.13.9.0 0.0.0.255 area 0
 network 13.13.12.0 0.0.0.255 area 0
```

---

## ⚙️ R3 설정

```bash
router ospf 1
 router-id 13.13.3.3
 passive-interface default
 no passive-interface s1/0.123
 network 13.13.3.0 0.0.0.255 area 0
 network 13.13.9.0 0.0.0.255 area 0
```

---

## ⚙️ Network Type 변경 (DR/BDR 미선출)

`Point-to-Multipoint` 사용 → DR/BDR 선출하지 않음

```bash
! R1, R2, R3 공통
interface Serial1/0.123
 ip ospf network point-to-multipoint
```

---

## 🔍 확인 - R1

```bash
R1# show ip route ospf
     13.0.0.0/8 is variably subnetted, 9 subnets, 2 masks
O       13.13.3.3/32 [110/129] via 13.13.9.2, 00:00:16, Serial1/0.123
O       13.13.2.2/32 [110/65]  via 13.13.9.2, 00:00:16, Serial1/0.123
O       13.13.9.3/32 [110/128] via 13.13.9.2, 00:00:16, Serial1/0.123
O       13.13.9.2/32 [110/64]  via 13.13.9.2, 00:00:16, Serial1/0.123
O       13.13.12.0/24 [110/65] via 13.13.9.2, 00:00:16, Serial1/0.123
```

---

## 🔍 확인 - R2

```bash
R2# show ip route ospf
     13.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
O       13.13.3.3/32 [110/65] via 13.13.9.3, 00:00:23, Serial1/0.123
O       13.13.1.1/32 [110/65] via 13.13.9.1, 00:00:04, Serial1/0.123
O       13.13.9.1/32 [110/64] via 13.13.9.1, 00:00:04, Serial1/0.123
O       13.13.9.3/32 [110/64] via 13.13.9.3, 00:00:23, Serial1/0.123
```

---

## 🔍 확인 - R3

```bash
R3# show ip route ospf
     13.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
O       13.13.2.2/32 [110/65] via 13.13.9.2, 00:00:15, Serial1/0.123
O       13.13.9.2/32 [110/64] via 13.13.9.2, 00:00:15, Serial1/0.123
O       13.13.12.0/24 [110/65] via 13.13.9.2, 00:00:15, Serial1/0.123
```

---

## 🔍 인터페이스 상세 확인

```bash
R2# show ip ospf interface s1/0.123
Serial1/0.123 is up, line protocol is up
  Internet Address 13.13.9.2/24, Area 0
  Process ID 1, Router ID 13.13.2.2, Network Type POINT_TO_MULTIPOINT, Cost: 64
  Timer intervals configured, Hello 30, Dead 120, Wait 120, Retransmit 5
  Neighbor Count is 2, Adjacent neighbor count is 2
    Adjacent with neighbor 13.13.1.1
    Adjacent with neighbor 13.13.3.3
```

---

## 💡 핵심 포인트

- **Point-to-Multipoint** 사용 시 DR/BDR 선출 없음
- Hello 30초 / Dead 120초 (NBMA 계열 타이머)
- Loopback은 기본적으로 `/32`로 광고됨 → Chapter 07에서 변경

---

⬅️ **이전:** [01. Pre-Configuration](./01-Pre-Configuration.md)  
➡️ **다음:** [03. DR/BDR 선출 제어](./03-DR-BDR-Control.md)
