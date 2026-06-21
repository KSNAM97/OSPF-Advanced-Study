# Chapter 12. Totally Stub Area

> 강화된 Stub 기능 - LSA Type 3, 4, 5 모두 차단

---

## 📌 개념

- LSA **Type 3** = Inter-Area (외부 Area 네트워크)
- LSA **Type 4** = ASBR 정보
- LSA **Type 5** = 재분배 네트워크

ABR이 Totally Stub Area로 **LSA Type 3, 4, 5** 모두 차단 → Default-route 제공  
Totally Stub Area 내부 라우터는 동일 Area 정보만 상세 광고, 나머지는 Default-route로 통신

---

## 🎯 요구사항

Area 14는 OSPF Intra-Area 정보만 상세 정보로 통신,  
Inter-Area, EIGRP 재분배 정보는 모두 Default-route로 통신

---

## ⚙️ R1 (ABR) 설정

```bash
router ospf 1
 area 14 stub no-summary
```

- **`no-summary`** = ABR이 Type-3 LSA까지 차단

---

## ⚙️ R4 설정

```bash
router ospf 1
 area 14 stub
```

⚠️ R4는 일반 Stub 설정만 하면 됨 (내부 라우터는 ABR의 통제 받음)

---

## 🔍 확인 - R4

```bash
R4# show ip route
Gateway of last resort is 150.1.13.1 to network 0.0.0.0

     13.0.0.0/24 is subnetted, 3 subnets
C       13.13.4.0  is directly connected, Loopback0
O       13.13.11.0 [110/20] via 150.1.13.1, 00:00:35, FastEthernet0/0
C       13.13.14.0 is directly connected, FastEthernet0/1
     150.1.0.0/24 is subnetted, 1 subnets
C       150.1.13.0 is directly connected, FastEthernet0/0
O*IA 0.0.0.0/0 [110/11] via 150.1.13.1, 00:00:05, FastEthernet0/0
```

✅ `O IA`, `O E1`, `O E2` 모두 차단되고 Default-route만 존재

---

## 🔍 R4 LSDB (Default만 존재)

```bash
R4# show ip ospf database

            OSPF Router with ID (13.13.4.4) (Process ID 1)

                Router Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum Link count
13.13.1.1       13.13.1.1       678   0x80000010 0x00D549 3
13.13.4.4       13.13.4.4       678   0x8000000E 0x0019CD 4

                Summary Net Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum
0.0.0.0         13.13.1.1       36    0x80000002 0x00C45C
```

---

## 💡 Stub vs Totally Stub 비교

| 구분 | Stub | Totally Stub |
|------|------|--------------|
| Type 3 (`O IA`) | ✅ 보임 | ❌ 차단 |
| Type 4 | ❌ 차단 | ❌ 차단 |
| Type 5 (`O E1/E2`) | ❌ 차단 | ❌ 차단 |
| Default-route | ✅ 자동 | ✅ 자동 |
| ABR 명령어 | `area X stub` | `area X stub no-summary` |

---

⬅️ **이전:** [11. Stub Area](./11-Stub-Area.md)  
➡️ **다음:** [13. NSSA](./13-NSSA.md)
