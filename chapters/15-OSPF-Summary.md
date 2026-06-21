# Chapter 15. OSPF 경로 요약 (Summarization)

> ABR/ASBR에서 OSPF 경로 요약을 통한 LSA 광고량 축약

---

## 📌 개념

- 위성 Area는 Stub/Totally Stub으로 LSA 광고량 축약 가능
- **Backbone Area(Area 0)는 Stub 불가** → Summarization으로 축약
- **ABR**: `area X range` → Internal 경로 요약 (LSA Type-3)
- **ASBR**: `summary-address` → External 경로 요약 (LSA Type-5)

---

## 🎯 사전 작업 - 추가 네트워크 구성

### ⚙️ R4 (Area 14)

```bash
interface Loopback100
 ip address 100.100.0.4 255.255.255.0
 ip address 100.100.1.4 255.255.255.0 secondary
 ip address 100.100.2.4 255.255.255.0 secondary
 ip address 100.100.3.4 255.255.255.0 secondary
 ip address 100.100.4.4 255.255.255.0 secondary
 ip address 100.100.5.4 255.255.255.0 secondary
 ip address 100.100.6.4 255.255.255.0 secondary
 ip address 100.100.7.4 255.255.255.0 secondary
 ip ospf network point-to-point
!
router ospf 1
 network 100.100.0.0 0.0.7.255 area 14
```

### ⚙️ R5 (EIGRP)

```bash
interface Loopback200
 ip address 200.200.8.5 255.255.255.0
 ip address 200.200.9.5 255.255.255.0 secondary
 ip address 200.200.10.5 255.255.255.0 secondary
 ip address 200.200.11.5 255.255.255.0 secondary
 ip address 200.200.12.5 255.255.255.0 secondary
 ip address 200.200.13.5 255.255.255.0 secondary
 ip address 200.200.14.5 255.255.255.0 secondary
 ip address 200.200.15.5 255.255.255.0 secondary
!
router eigrp 100
 network 200.200.8.0 0.0.7.255
```

---

## 🔍 요약 전 - R2

```bash
R2# show ip route ospf | include 100
     100.0.0.0/24 is subnetted, 8 subnets
O IA    100.100.0.0 [110/658] via 13.13.9.1, 00:03:55, Serial1/0.123
O IA    100.100.1.0 [110/658] via 13.13.9.1, 00:03:55, Serial1/0.123
O IA    100.100.2.0 [110/658] via 13.13.9.1, 00:03:55, Serial1/0.123
O IA    100.100.3.0 [110/658] via 13.13.9.1, 00:03:55, Serial1/0.123
O IA    100.100.4.0 [110/658] via 13.13.9.1, 00:03:55, Serial1/0.123
O IA    100.100.5.0 [110/658] via 13.13.9.1, 00:03:55, Serial1/0.123
O IA    100.100.6.0 [110/658] via 13.13.9.1, 00:03:55, Serial1/0.123
O IA    100.100.7.0 [110/658] via 13.13.9.1, 00:03:55, Serial1/0.123

R2# show ip route ospf | include 200
O E1 200.200.12.0/24 [110/667] via 13.13.9.3, 00:04:08, Serial1/0.123
O E1 200.200.13.0/24 [110/667] via 13.13.9.3, 00:04:08, Serial1/0.123
O E1 200.200.14.0/24 [110/667] via 13.13.9.3, 00:04:08, Serial1/0.123
O E1 200.200.15.0/24 [110/667] via 13.13.9.3, 00:04:08, Serial1/0.123
O E1 200.200.8.0/24  [110/667] via 13.13.9.3, 00:04:09, Serial1/0.123
O E1 200.200.9.0/24  [110/667] via 13.13.9.3, 00:04:08, Serial1/0.123
O E1 200.200.10.0/24 [110/667] via 13.13.9.3, 00:04:08, Serial1/0.123
O E1 200.200.11.0/24 [110/667] via 13.13.9.3, 00:04:08, Serial1/0.123
```

---

## ⚙️ R1 (ABR) - Internal 요약

```bash
router ospf 1
 area 14 range 100.100.0.0 255.255.248.0
```

### 🧮 요약 계산
```
100.100.0.0/24 ~ 100.100.7.0/24 (8개)
→ 100.100.0.0/21 (255.255.248.0)
```

### 🔍 R2 확인 (Internal 요약 적용)
```bash
R2# show ip route ospf
     100.0.0.0/21 is subnetted, 1 subnets
O IA    100.100.0.0 [110/658] via 13.13.9.1, 00:01:43, Serial1/0.123
```

✅ 8개 → 1개로 축약!

---

## ⚙️ R3 (ASBR) - External 요약

```bash
router ospf 1
 summary-address 200.200.8.0 255.255.240.0
```

### 🧮 요약 계산
```
200.200.8.0/24 ~ 200.200.15.0/24 (8개)
→ 200.200.0.0/20 (255.255.240.0)
```

---

## 🔍 최종 확인 - R2

```bash
R2# show ip route
     100.0.0.0/21 is subnetted, 1 subnets
O IA    100.100.0.0 [110/658] via 13.13.9.1, 00:08:44, Serial1/0.123
     140.40.0.0/24 is subnetted, 4 subnets
O E1    140.40.0.0 [110/678] via 13.13.9.1, 00:27:56, Serial1/0.123
O E1    140.40.1.0 [110/678] via 13.13.9.1, 00:27:56, Serial1/0.123
O E1    140.40.2.0 [110/678] via 13.13.9.1, 00:27:56, Serial1/0.123
O E1    140.40.3.0 [110/678] via 13.13.9.1, 00:27:56, Serial1/0.123
     13.0.0.0/24 is subnetted, 11 subnets
O       13.13.1.0  [110/648] via 13.13.9.1, 04:30:55, Serial1/0.123
C       13.13.2.0  is directly connected, Loopback0
O       13.13.3.0  [110/648] via 13.13.9.3, 04:30:55, Serial1/0.123
O IA    13.13.4.0  [110/658] via 13.13.9.1, 00:28:06, Serial1/0.123
C       13.13.9.0  is directly connected, Serial1/0.123
O       13.13.10.0 [110/1294] via 13.13.9.3, Serial1/0.123
O IA    13.13.11.0 [110/657] via 13.13.9.1, Serial1/0.123
C       13.13.12.0 is directly connected, FastEthernet0/1
O E1    13.13.13.0 [110/667] via 13.13.9.3, Serial1/0.123
O IA    13.13.14.0 [110/667] via 13.13.9.1, Serial1/0.123
O E1    13.13.15.0 [110/667] via 13.13.9.3, Serial1/0.123
     150.1.0.0/24 is subnetted, 1 subnets
O IA    150.1.13.0 [110/657] via 13.13.9.1, Serial1/0.123
     150.3.0.0/24 is subnetted, 1 subnets
O E1    150.3.13.0 [110/667] via 13.13.9.3, Serial1/0.123
O E1 200.200.0.0/20 [110/667] via 13.13.9.3, 00:01:16, Serial1/0.123
```

✅ **`200.200.8.0/24 ~ 200.200.15.0/24` (8개) → `200.200.0.0/20` (1개)로 축약!**  
✅ **`100.100.0.0/24 ~ 100.100.7.0/24` (8개) → `100.100.0.0/21` (1개)로 축약!**

---

## 💡 핵심 포인트

| 요약 위치 | 명령어 | 대상 LSA | 적용 모드 |
|-----------|--------|----------|-----------|
| **ABR** | `area X range A.B.C.D mask` | Type-3 (Internal) | `router ospf` |
| **ASBR** | `summary-address A.B.C.D mask` | Type-5 (External) | `router ospf` |

- ABR은 다른 Area에서 들어오는 정보를 요약
- ASBR은 외부에서 재분배되는 정보를 요약
- Backbone Area로의 정보 축약에 매우 유용

---

⬅️ **이전:** [14. Totally NSSA](./14-Totally-NSSA.md)  
➡️ **다음:** [16. 전체 Pre-Config](./16-Pre-Config-All.md)
