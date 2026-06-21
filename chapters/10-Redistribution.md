# Chapter 10. OSPF ↔ EIGRP 재분배

> R3 (ASBR)에서 OSPF ↔ EIGRP 상호 재분배

---

## 🎯 요구사항

재분배 라우팅 프로토콜 2개 이상 라우터에서 OSPF ↔ EIGRP 상호 재분배

---

## ⚙️ R3 (ASBR) - EIGRP → OSPF

```bash
router ospf 1
 redistribute eigrp 100 metric-type 1 subnets
```

- **`metric-type 1`** : E1 (누적 Metric)
- **`subnets`** : Classful 경계 무시하고 모든 서브넷 재분배 (필수!)

---

## ⚙️ R3 (ASBR) - OSPF → EIGRP

```bash
router eigrp 100
 redistribute ospf 1 metric 1544 2000 255 1 1500
```

---

## 📋 EIGRP Metric 5요소

| 위치 | 값 | 의미 | 단위 |
|------|-----|------|------|
| 1번째 | 1544 | Bandwidth (대역폭) | Kbps |
| 2번째 | 2000 | Delay (지연) | 10 마이크로초 |
| 3번째 | 255 | Reliability (신뢰성) | 0~255 (255 최고) |
| 4번째 | 1 | Load (부하) | 1~255 (1 최저) |
| 5번째 | 1500 | MTU | Bytes |

---

## 🧮 Metric 계산법

```
BW     = 10^7 / 1544 = 6476  (소수점 버림)
Delay  = 2000
Metric = (BW + Delay) × 256
       = (6476 + 2000) × 256
       = 8476 × 256
       = 2,169,856
```

---

## 🔍 확인 - R1 (OSPF 측)

```bash
R1# show ip route ospf
     13.0.0.0/24 is subnetted, 11 subnets
O       13.13.2.0  [110/648] via 13.13.9.2, 00:19:57, Serial1/0.123
O       13.13.3.0  [110/648] via 13.13.10.3, 00:19:57, Serial1/1
                   [110/648] via 13.13.9.3, 00:19:57, Serial1/0.123
O       13.13.4.0  [110/11]  via 150.1.13.4, 00:19:57, FastEthernet0/0
O       13.13.12.0 [110/657] via 13.13.9.2, 00:19:47, Serial1/0.123
O E1    13.13.13.0 [110/667] via 13.13.10.3, 00:00:02, Serial1/1
O       13.13.14.0 [110/20]  via 150.1.13.4, 00:19:37, FastEthernet0/0
O E1    13.13.15.0 [110/667] via 13.13.10.3, 00:00:02, Serial1/1
     150.3.0.0/24 is subnetted, 1 subnets
O E1    150.3.13.0 [110/667] via 13.13.10.3, 00:00:02, Serial1/1
```

---

## 🔍 확인 - R4 (OSPF 측, Area 14)

```bash
R4# show ip route ospf
     13.0.0.0/24 is subnetted, 11 subnets
O IA    13.13.1.0  [110/11]  via 150.1.13.1, 00:20:32, FastEthernet0/0
O IA    13.13.2.0  [110/658] via 150.1.13.1, 00:20:32, FastEthernet0/0
O IA    13.13.3.0  [110/658] via 150.1.13.1, 00:20:32, FastEthernet0/0
O IA    13.13.9.0  [110/657] via 150.1.13.1, 00:20:32, FastEthernet0/0
O IA    13.13.10.0 [110/657] via 150.1.13.1, 00:20:32, FastEthernet0/0
O       13.13.11.0 [110/20]  via 150.1.13.1, 00:20:32, FastEthernet0/0
O IA    13.13.12.0 [110/667] via 150.1.13.1, 00:20:32, FastEthernet0/0
O E1    13.13.13.0 [110/677] via 150.1.13.1, 00:00:48, FastEthernet0/0
O E1    13.13.15.0 [110/677] via 150.1.13.1, 00:00:48, FastEthernet0/0
     150.3.0.0/24 is subnetted, 1 subnets
O E1    150.3.13.0 [110/677] via 150.1.13.1, 00:00:48, FastEthernet0/0
```

---

## 🔍 확인 - R5 (EIGRP 측)

```bash
R5# show ip route
     13.0.0.0/24 is subnetted, 11 subnets
D EX    13.13.1.0  [170/2172416] via 150.3.13.3, 00:00:07, Fa0/0
D EX    13.13.2.0  [170/2172416] via 150.3.13.3, 00:00:07, Fa0/0
D EX    13.13.3.0  [170/2172416] via 150.3.13.3, 00:00:07, Fa0/0
D EX    13.13.4.0  [170/2172416] via 150.3.13.3, 00:00:07, Fa0/0
D EX    13.13.9.0  [170/2172416] via 150.3.13.3, 00:00:07, Fa0/0
D EX    13.13.10.0 [170/2172416] via 150.3.13.3, 00:00:07, Fa0/0
D EX    13.13.11.0 [170/2172416] via 150.3.13.3, 00:00:07, Fa0/0
D EX    13.13.12.0 [170/2172416] via 150.3.13.3, 00:00:07, Fa0/0
D       13.13.13.0 [90/30720]    via 150.3.13.3, 01:01:44, Fa0/0
D EX    13.13.14.0 [170/2172416] via 150.3.13.3, 00:00:07, Fa0/0
C       13.13.15.0 is directly connected, Fa0/1
     150.1.0.0/24 is subnetted, 1 subnets
D EX    150.1.13.0 [170/2172416] via 150.3.13.3, 00:00:08, Fa0/0
```

---

## 💡 핵심 포인트

- **`subnets`** 키워드 없으면 Classful 네트워크만 재분배 (CRITICAL)
- EIGRP External AD = 170 (Internal 90보다 높음)
- OSPF External 기본 Cost = 20

---

⬅️ **이전:** [09. LSA-Type 정리](./09-LSA-Type.md)  
➡️ **다음:** [11. Stub Area](./11-Stub-Area.md)
