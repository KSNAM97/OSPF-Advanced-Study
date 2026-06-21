# Chapter 05. Area 14 구성 (R1-R4)

> R1, R4 FastEthernet 구간에 OSPF Area 14 구성

---

## 🎯 요구사항

- OSPF Process = 1, Area = 14, Router-ID = `13.13.X.X`
- R1의 `Fa0/1` 포함 네트워크 모두 Area 14
- OSPF 라우팅 업데이트가 필요한 인터페이스로만 송출
- **DR/BDR 미선출** → Point-to-Point

---

## ⚙️ R1 (ABR) 설정

```bash
router ospf 1
 no passive-interface fa0/0
 network 150.1.13.0 0.0.0.255 area 14
 network 13.13.11.0 0.0.0.255 area 14
!
interface FastEthernet0/0
 ip ospf network point-to-point
```

---

## ⚙️ R4 설정

```bash
router ospf 1
 router-id 13.13.4.4
 passive-interface default
 no passive-interface fa0/0
 network 150.1.13.0 0.0.0.255 area 14
 network 13.13.4.0 0.0.0.255 area 14
 network 13.13.14.0 0.0.0.255 area 14
!
interface FastEthernet0/0
 ip ospf network point-to-point
```

---

## 🔍 확인 - R1

```bash
R1# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address       Interface
13.13.3.3         0   FULL/  -        00:00:37    13.13.10.3    Serial1/1
13.13.2.2       255   FULL/DR         00:01:50    13.13.9.2     Serial1/0.123
13.13.4.4         0   FULL/  -        00:00:33    150.1.13.4    FastEthernet0/0
```

---

## 🔍 확인 - R4

```bash
R4# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address       Interface
13.13.1.1         0   FULL/  -        00:00:39    150.1.13.1    FastEthernet0/0
```

---

## 💡 핵심 포인트

- **R1은 ABR**이 됨 (Area 0 + Area 14)
- FastEthernet 구간에 Point-to-Point 적용 → DR/BDR 선출 안 함
- 1:1 연결만 있는 Ethernet 구간에서 효율적

---

⬅️ **이전:** [04. R1-R3 Frame-Relay 구간](./04-R1-R3-FrameRelay.md)  
➡️ **다음:** [06. EIGRP 100 구성](./06-EIGRP-Config.md)
