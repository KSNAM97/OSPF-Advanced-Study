# Chapter 04. R1-R3 Frame-Relay 구간 OSPF

> R1-R3 간 별도 Frame-Relay 구간 (`13.13.10.0/24`)에 OSPF 적용

---

## 🎯 요구사항

- R1, R3 Frame-Relay 구간 OSPF Process 1, Area 0
- BDR 미선출 → 이후 DR/BDR 모두 미선출로 변경

---

## 📌 4-1. BDR 미선출 (Priority 0 방식)

### ⚙️ R1
```bash
router ospf 1
 network 13.13.10.0 0.0.0.255 area 0
 no passive-interface s1/1
 neighbor 13.13.10.3
```

### ⚙️ R3
```bash
router ospf 1
 network 13.13.10.0 0.0.0.255 area 0
 no passive-interface s1/1
!
interface Serial1/1
 ip ospf priority 0
```

### 🔍 확인
```bash
R3# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address       Interface
13.13.1.1         1   FULL/DR         00:00:50    13.13.10.1    Serial1/1
13.13.2.2       255   FULL/DR         00:00:42    13.13.9.2     Serial1/0.123
```

```bash
R1# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address       Interface
13.13.3.3         0   FULL/DROTHER    00:01:58    13.13.10.3    Serial1/1
13.13.2.2       255   FULL/DR         00:01:57    13.13.9.2     Serial1/0.123
```

---

## 📌 4-2. DR/BDR 모두 미선출 (Point-to-Point 방식)

### ⚙️ R1
```bash
router ospf 1
 network 13.13.10.0 0.0.0.255 area 0
 no passive-interface s1/1
!
interface Serial1/1
 ip ospf network point-to-point
```

### ⚙️ R3
```bash
router ospf 1
 network 13.13.10.0 0.0.0.255 area 0
 no passive-interface s1/1
!
interface Serial1/1
 ip ospf network point-to-point
```

### 🔍 확인
```bash
R1# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address       Interface
13.13.3.3         0   FULL/  -        00:00:37    13.13.10.3    Serial1/1
13.13.2.2       255   FULL/DR         00:01:54    13.13.9.2     Serial1/0.123
```

```bash
R3# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address       Interface
13.13.1.1         0   FULL/  -        00:00:38    13.13.10.1    Serial1/1
13.13.2.2       255   FULL/DR         00:01:50    13.13.9.2     Serial1/0.123
```

### 🔍 인터페이스 확인
```bash
R3# show ip ospf interface s1/1
Serial1/1 is up, line protocol is up
  Internet Address 13.13.10.3/24, Area 0
  Process ID 1, Router ID 13.13.3.3, Network Type POINT_TO_POINT, Cost: 64
  Timer intervals configured, Hello 10, Dead 40, Wait 40, Retransmit 5
  Neighbor Count is 1, Adjacent neighbor count is 1
    Adjacent with neighbor 13.13.1.1
```

---

## 💡 핵심 포인트

| 방식 | DR/BDR | Network Type |
|------|--------|--------------|
| 4-1 | DR만 선출 (R1) | NBMA (기본값) |
| 4-2 | 모두 미선출 | Point-to-Point |

- Point-to-Point는 Hello 10초 / Dead 40초로 변경됨
- 1:1 연결인 경우 Point-to-Point가 가장 효율적

---

⬅️ **이전:** [03. DR/BDR 선출 제어](./03-DR-BDR-Control.md)  
➡️ **다음:** [05. Area 14 구성](./05-Area14-Config.md)
