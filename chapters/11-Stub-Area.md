# Chapter 11. Stub Area

> Area 14를 Stub Area로 구성하여 외부 LSA 차단

---

## 📌 개념

- OSPF에서 지원하는 강력한 Area 요약 기능
- ABR이 Stub Area로 **LSA Type 4, 5** 차단 → **Default-route** 제공
- Stub Area 내부 라우터는 `O`, `O IA`만 확인됨
- 외부에서 OSPF로 재분배된 네트워크 정보(`O E1`, `O E2`)는 **Default-route**로 통신

---

## ⚠️ Stub 사용 조건

- ❌ Backbone Area(Area 0)는 Stub 불가
- ✅ Stub Area 내 **모든 라우터**에서 공통 설정 필요 (인접성 조건)
- ❌ Transit Area (Virtual-link 존재 Area) 불가
- ❌ ASBR이 존재하는 Area 불가 → **NSSA로 대체**

---

## 🎯 요구사항

Area 14 라우터는 OSPF 내부 정보(`O`, `O IA`)는 상세 정보로 통신,  
EIGRP 재분배 정보는 Routing Table에 확인되지 않아야 한다.  
(Area 14 라우터들은 EIGRP 환경으로 통신 가능해야 함)

---

## ⚙️ R1, R4 (Area 14 Stub) 설정

```bash
router ospf 1
 area 14 stub
```

---

## 🔍 확인 - R1 (Area 0 측 → 정상)

```bash
R1# show ip route ospf
     13.0.0.0/24 is subnetted, 9 subnets
O       13.13.2.0  [110/648] via 13.13.9.2, 00:00:11, Serial1/0.123
O       13.13.3.0  [110/648] via 13.13.10.3, 00:00:11, Serial1/1
                   [110/648] via 13.13.9.3, 00:00:11, Serial1/0.123
O       13.13.12.0 [110/657] via 13.13.9.2, 00:00:11, Serial1/0.123
O E1    13.13.13.0 [110/667] via 13.13.10.3, 00:00:11, Serial1/1
O E1    13.13.15.0 [110/667] via 13.13.10.3, 00:00:11, Serial1/1
O E1    150.3.13.0 [110/667] via 13.13.10.3, 00:00:11, Serial1/1
```

---

## 🔍 확인 - R4 (Stub Area)

```bash
R4# show ip route ospf
     13.0.0.0/24 is subnetted, 9 subnets
O IA    13.13.1.0  [110/11]  via 150.1.13.1, 00:01:11, FastEthernet0/0
O IA    13.13.2.0  [110/658] via 150.1.13.1, 00:01:11, FastEthernet0/0
O IA    13.13.3.0  [110/658] via 150.1.13.1, 00:01:11, FastEthernet0/0
O IA    13.13.9.0  [110/657] via 150.1.13.1, 00:01:11, FastEthernet0/0
O IA    13.13.10.0 [110/657] via 150.1.13.1, 00:01:11, FastEthernet0/0
O       13.13.11.0 [110/20]  via 150.1.13.1, 00:01:11, FastEthernet0/0
O IA    13.13.12.0 [110/667] via 150.1.13.1, 00:01:11, FastEthernet0/0
O*IA 0.0.0.0/0 [110/11] via 150.1.13.1, 00:01:11, FastEthernet0/0
```

✅ `O E1`, `O E2` 정보 차단됨, Default-route 자동 생성

---

## 🔍 R4 LSDB (Type-1, 3만 존재)

```bash
R4# show ip ospf database

            OSPF Router with ID (13.13.4.4) (Process ID 1)

                Router Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum Link count
13.13.1.1       13.13.1.1       388   0x80000010 0x00D549 3
13.13.4.4       13.13.4.4       388   0x8000000E 0x0019CD 4

                Summary Net Link States (Area 14)
Link ID         ADV Router      Age   Seq#       Checksum
0.0.0.0         13.13.1.1       393   0x80000001 0x00C65B
13.13.1.0       13.13.1.1       393   0x80000008 0x006798
13.13.2.0       13.13.1.1       393   0x80000009 0x00B7BC
13.13.3.0       13.13.1.1       393   0x80000009 0x00ACC6
13.13.9.0       13.13.1.1       393   0x80000009 0x00600E
13.13.10.0      13.13.1.1       393   0x80000009 0x005518
13.13.12.0      13.13.1.1       393   0x8000000A 0x00A1BE
```

---

## 💡 핵심 포인트

- Stub 설정은 **Area 내 모든 라우터**에 동일하게 적용 필수
- Default-route는 ABR이 **자동 생성**
- LSDB에서 Type-5 (External) 사라짐 확인

---

⬅️ **이전:** [10. OSPF ↔ EIGRP 재분배](./10-Redistribution.md)  
➡️ **다음:** [12. Totally Stub Area](./12-Totally-Stub.md)
