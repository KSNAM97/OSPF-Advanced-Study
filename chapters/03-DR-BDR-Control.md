# Chapter 03. DR/BDR 선출 제어

> R2를 DR로 강제 선출하고 R1, R3는 DROther로 설정

---

## 🎯 요구사항

- **R2를 DR로 선출** (Frame-Relay Hub 역할)
- R1, R3는 DR 후보에서 제외 (Priority 0)

---

## ⚙️ R2 (DR) 설정

```bash
interface Serial1/0.123
 ip ospf priority 255
!
router ospf 1
 neighbor 13.13.9.1
 neighbor 13.13.9.3
```

---

## ⚙️ R1, R3 (DROther) 설정

```bash
interface Serial1/0.123
 ip ospf priority 0
```

---

## 🔍 확인 - R1

```bash
R1# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address       Interface
13.13.2.2       255   FULL/DR         00:01:37    13.13.9.2     Serial1/0.123
```

---

## 🔍 확인 - R2 (DR)

```bash
R2# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address       Interface
13.13.1.1         0   FULL/DROTHER    00:01:54    13.13.9.1     Serial1/0.123
13.13.3.3         0   FULL/DROTHER    00:01:45    13.13.9.3     Serial1/0.123
```

---

## 🔍 확인 - R3

```bash
R3# show ip ospf neighbor

Neighbor ID     Pri   State           Dead Time   Address       Interface
13.13.2.2       255   FULL/DR         00:01:37    13.13.9.2     Serial1/0.123
```

---

## 💡 핵심 포인트

- **Priority 0** = DR/BDR 선출 후보에서 제외
- **Priority 255** = 최우선 DR 후보
- Frame-Relay NBMA 환경에서는 Hub를 DR로 강제 지정해야 안정적
- `neighbor` 명령어 = Unicast로 OSPF 패킷 송신 (NBMA 환경 필수)

---

⬅️ **이전:** [02. OSPF Frame-Relay 구성](./02-OSPF-FrameRelay-Area0.md)  
➡️ **다음:** [04. R1-R3 Frame-Relay 구간 OSPF](./04-R1-R3-FrameRelay.md)
