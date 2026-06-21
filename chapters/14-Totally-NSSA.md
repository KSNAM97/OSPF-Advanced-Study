# Chapter 14. Totally NSSA

> 강화된 NSSA 기능 - LSA Type 3, 4, 5 모두 차단

---

## 📌 개념

- ABR이 Totally NSSA Area로 **LSA Type 3, 4, 5** 모두 차단
- Default-route **자동 생성**
- NSSA Area 내 ASBR은 LSA Type-7로 재분배
- ASBR이 존재하는 Area에서 **Totally Stub 효과**를 얻기 위한 기능

---

## 🎯 요구사항

Area 14는 Intra-Area 정보만 상세 정보로 통신,  
Inter-Area + EIGRP 재분배 정보는 모두 Default-route로 통신

---

## ⚙️ R1 (ABR) 설정

```bash
router ospf 1
 area 14 nssa no-summary
```

- **`no-summary`** = Type-3까지 차단

---

## ⚙️ R4 설정

```bash
router ospf 1
 area 14 nssa
```

---

## 🔍 확인 - R1 (Area 0 측 정상)

```bash
R1# show ip route ospf
     140.40.0.0/24 is subnetted, 4 subnets
O N1    140.40.0.0 [110/31] via 150.1.13.4, 00:00:10, FastEthernet0/0
O N1    140.40.1.0 [110/31] via 150.1.13.4, 00:00:10, FastEthernet0/0
O N1    140.40.2.0 [110/31] via 150.1.13.4, 00:00:10, FastEthernet0/0
O N1    140.40.3.0 [110/31] via 150.1.13.4, 00:00:10, FastEthernet0/0
     13.0.0.0/24 is subnetted, 11 subnets
O       13.13.2.0  [110/648] via 13.13.9.2,  00:00:25, Serial1/0.123
O       13.13.4.0  [110/11]  via 150.1.13.4, 00:00:10, FastEthernet0/0
O E1    13.13.13.0 [110/667] via 13.13.10.3, 00:00:25, Serial1/1
O E1    13.13.15.0 [110/667] via 13.13.10.3, 00:00:25, Serial1/1
```

---

## 🔍 확인 - R4 (Totally NSSA)

```bash
R4# show ip route ospf
     13.0.0.0/24 is subnetted, 3 subnets
O       13.13.11.0 [110/20] via 150.1.13.1, 00:00:41, FastEthernet0/0
O*IA 0.0.0.0/0 [110/11] via 150.1.13.1, 00:00:41, FastEthernet0/0
```

✅ Inter-Area, External 모두 차단되고 Default-route만 존재

---

## 🔍 R1 LSDB (전체)

```bash
R1# show ip ospf database

            OSPF Router with ID (13.13.1.1) (Process ID 1)

                Router Link States (Area 0)
Link ID         ADV Router      Age   Seq#       Checksum Link count
13.13.1.1       13.13.1.1       226   0x80000012 0x000747 4
13.13.2.2       13.13.2.2       743   0x8000000E 0x00BDEB 3
13.13.3.3       13.13.3.3       1945  0x80000015 0x000E34 4

                Net Link States (Area 0)
Link ID         ADV Router      Age   Seq#       Checksum
13.13.9.2       13.13.2.2       231   0x8000000C 0x000B82

                Summary Net Link States (Area 0)
Link ID         ADV Router      Age   Seq#       Checksum
13.13.4.0       13.13.1.1       212   0x80000001 0x009A5D
13.13.11.0      13.13.1.1       861   0x8000000A 0x0031B7
13.13.14.0      13.13.1.1       212   0x80000001 0x00865E
150.1.13.0      13.13.1.1       861   0x80000009 0x00B1B8

                Router Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum Link count
13.13.1.1       13.13.1.1       215   0x80000019 0x0051BA 3
13.13.4.4       13.13.4.4       214   0x80000018 0x009240 4

                Summary Net Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum
0.0.0.0         13.13.1.1       228   0x80000001 0x004ECB

                Type-7 AS External Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum Tag
140.40.0.0      13.13.4.4       218   0x80000001 0x0051CC 0
140.40.1.0      13.13.4.4       218   0x80000001 0x0046D6 0
140.40.2.0      13.13.4.4       218   0x80000001 0x003BE0 0
140.40.3.0      13.13.4.4       218   0x80000001 0x0030EA 0

                Type-5 AS External Link States
Link ID         ADV Router      Age   Seq#       Checksum Tag
13.13.13.0      13.13.3.3       1947  0x80000007 0x007D56 0
13.13.15.0      13.13.3.3       1947  0x80000007 0x00676A 0
140.40.0.0      13.13.1.1       209   0x80000001 0x000D21 0
140.40.1.0      13.13.1.1       209   0x80000001 0x00022B 0
140.40.2.0      13.13.1.1       209   0x80000001 0x00F635 0
140.40.3.0      13.13.1.1       209   0x80000001 0x00EB3F 0
150.3.13.0      13.13.3.3       1949  0x80000007 0x00F95A 0
```

---

## 🔍 R4 LSDB (요약된 정보만)

```bash
R4# show ip ospf database

            OSPF Router with ID (13.13.4.4) (Process ID 1)

                Router Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum Link count
13.13.1.1       13.13.1.1       199   0x80000019 0x0051BA 3
13.13.4.4       13.13.4.4       195   0x80000018 0x009240 4

                Summary Net Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum
0.0.0.0         13.13.1.1       211   0x80000001 0x004ECB

                Type-7 AS External Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum Tag
140.40.0.0      13.13.4.4       199   0x80000001 0x0051CC 0
140.40.1.0      13.13.4.4       199   0x80000001 0x0046D6 0
140.40.2.0      13.13.4.4       199   0x80000001 0x003BE0 0
140.40.3.0      13.13.4.4       199   0x80000001 0x0030EA 0
```

---

## 💡 NSSA vs Totally NSSA 비교

| 구분 | NSSA | Totally NSSA |
|------|------|--------------|
| Type 3 (`O IA`) | ✅ 보임 | ❌ 차단 |
| Type 4 | ❌ 차단 | ❌ 차단 |
| Type 5 | ❌ 차단 | ❌ 차단 |
| Type 7 | ✅ 사용 | ✅ 사용 |
| Default | 수동 | 자동 |
| ABR 명령어 | `area X nssa` + `default-information-originate` | `area X nssa no-summary` |

---

⬅️ **이전:** [13. NSSA](./13-NSSA.md)  
➡️ **다음:** [15. OSPF 경로 요약](./15-OSPF-Summary.md)
