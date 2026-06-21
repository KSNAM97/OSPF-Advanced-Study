# Chapter 13. NSSA (Not-So-Stubby-Area)

> ASBR이 존재하는 Area에서 사용하는 Stub 기능

---

## 📌 개념

- NSSA가 설정된 Area로 ABR이 **LSA Type 4, 5** 차단
- OSPF Intra/Inter-Area 정보(`O`, `O IA`)는 상세 정보로 통신
- 외부 재분배 정보는 **Default-route**로 통신
- **Stub Area에 ASBR이 포함된 경우** NSSA 사용
- NSSA Area의 ASBR은 재분배 시 **LSA Type-7** 사용
- ⚠️ Default-route는 **자동 생성되지 않음** (ABR에서 수동 생성 필요)

---

## 🔑 왜 NSSA가 필요한가?

- Stub는 **LSA Type 5**를 수신하지 않는 기능
- ASBR이 존재하면 재분배 정보가 Type 5로 광고됨 → Stub와 충돌
- NSSA는 외부 정보를 **Type-7**로 재분배 → ASBR이 있어도 Stub 효과 가능

---

## 📋 LSA Type-7

| 표기 | 의미 |
|------|------|
| `O N1` | NSSA Metric-Type 1 (누적 Metric, ASBR 경로 복수) |
| `O N2` | NSSA Metric-Type 2 (고정 Metric, 단일 경로) - **기본값** |

---

## 🎯 사전 작업 - R4에 RIP 환경 구성

```
   |---EIGRP---|  |---OSPF A 0---|  |---OSPF A 14---| |---RIP---|
   R5----------R3----------------R1----------------R4-----------|
```

### ⚙️ R4 RIP + 재분배

```bash
interface Loopback140
 ip add 140.40.0.4 255.255.255.0
 ip add 140.40.1.4 255.255.255.0 secondary
 ip add 140.40.2.4 255.255.255.0 secondary
 ip add 140.40.3.4 255.255.255.0 secondary
!
router rip
 version 2
 no auto-summary
 network 140.40.0.0
 passive-interface default
!
router ospf 1
 redistribute rip metric-type 1 subnets
```

---

## 🔍 NSSA 적용 전 - R1 확인

```bash
R1# show ip route
     140.40.0.0/24 is subnetted, 4 subnets
O E1    140.40.0.0 [110/30] via 150.1.13.4, 00:04:31, FastEthernet0/0
O E1    140.40.1.0 [110/30] via 150.1.13.4, 00:04:31, FastEthernet0/0
O E1    140.40.2.0 [110/30] via 150.1.13.4, 00:04:31, FastEthernet0/0
O E1    140.40.3.0 [110/30] via 150.1.13.4, 00:04:31, FastEthernet0/0
```

✅ R4가 ASBR → Area 14에 ASBR 존재 → Stub 불가 → **NSSA 필요!**

---

## ⚙️ R1 (ABR) - Default-route 수동 생성

```bash
router ospf 1
 area 14 nssa default-information-originate
```

---

## ⚙️ R4 설정

```bash
router ospf 1
 area 14 nssa
```

---

## 🔍 확인 - R1 (Type-7 → Type-5 변환)

```bash
R1# show ip route
     140.40.0.0/24 is subnetted, 4 subnets
O N1    140.40.0.0 [110/31] via 150.1.13.4, 00:00:02, FastEthernet0/0
O N1    140.40.1.0 [110/31] via 150.1.13.4, 00:00:02, FastEthernet0/0
O N1    140.40.2.0 [110/31] via 150.1.13.4, 00:00:02, FastEthernet0/0
O N1    140.40.3.0 [110/31] via 150.1.13.4, 00:00:02, FastEthernet0/0
```

---

## 🔍 확인 - R4 (Default-route 생성)

```bash
R4# show ip route ospf
     13.0.0.0/24 is subnetted, 9 subnets
O IA    13.13.1.0  [110/11]  via 150.1.13.1, 00:03:32, FastEthernet0/0
O IA    13.13.2.0  [110/658] via 150.1.13.1, 00:03:32, FastEthernet0/0
O IA    13.13.3.0  [110/658] via 150.1.13.1, 00:03:32, FastEthernet0/0
O IA    13.13.9.0  [110/657] via 150.1.13.1, 00:03:32, FastEthernet0/0
O IA    13.13.10.0 [110/657] via 150.1.13.1, 00:03:32, FastEthernet0/0
O       13.13.11.0 [110/20]  via 150.1.13.1, 00:03:32, FastEthernet0/0
O IA    13.13.12.0 [110/667] via 150.1.13.1, 00:03:32, FastEthernet0/0
O*N2 0.0.0.0/0 [110/1] via 150.1.13.1, 00:00:57, FastEthernet0/0
```

---

## 🔍 R4 LSDB (Type-7 확인)

```bash
R4# show ip ospf database
                Type-7 AS External Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum Tag
0.0.0.0         13.13.1.1       347   0x80000001 0x002273 0
140.40.0.0      13.13.4.4       515   0x80000001 0x0051CC 0
140.40.1.0      13.13.4.4       516   0x80000001 0x0046D6 0
140.40.2.0      13.13.4.4       516   0x80000001 0x003BE0 0
140.40.3.0      13.13.4.4       516   0x80000001 0x0030EA 0
```

---

## 🔍 R2 LSDB (Type-5로 변환됨)

```bash
R2# show ip ospf database
                Type-5 AS External Link States
Link ID         ADV Router      Age   Seq#       Checksum Tag
13.13.13.0      13.13.3.3       249   0x80000007 0x007D56 0
13.13.15.0      13.13.3.3       250   0x80000007 0x00676A 0
140.40.0.0      13.13.1.1       623   0x80000001 0x000D21 0   ← R1이 변환
140.40.1.0      13.13.1.1       623   0x80000001 0x00022B 0
140.40.2.0      13.13.1.1       623   0x80000001 0x00F635 0
140.40.3.0      13.13.1.1       623   0x80000001 0x00EB3F 0
150.3.13.0      13.13.3.3       250   0x80000007 0x00F95A 0
```

✅ ABR(R1)이 Type-7 → Type-5로 변환하여 Area 0로 전파

---

## 💡 핵심 포인트

- NSSA Area 내 ASBR은 **LSA Type-7**로 재분배
- ABR이 Type-7 → Type-5로 변환하여 다른 Area로 전파
- **Default-route 수동 생성 필수** (`default-information-originate`)

---

⬅️ **이전:** [12. Totally Stub Area](./12-Totally-Stub.md)  
➡️ **다음:** [14. Totally NSSA](./14-Totally-NSSA.md)
