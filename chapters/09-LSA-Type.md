# Chapter 09. LSA-Type 정리

> OSPF LSA(Link-State Advertisement) Type 별 역할 및 데이터베이스 확인

---

## 📌 개념

- OSPF는 **DataBase Table**을 사용하여 모든 네트워크 정보를 관리
- LSDB(Link-State DataBase)를 **Area 단위**로 구분하여 관리
- Area 내에서 **LSA-Type별**로 구분하여 관리
- LSA를 사용하여 LSDB를 동기화

---

## 📋 R/T (Routing Table) 표기

| 표기 | 의미 |
|------|------|
| `O` | OSPF Intra-Area (동일 Area 네트워크) |
| `O IA` | OSPF Inter-Area (다른 Area 네트워크) |
| `O E1` | External Type 1 (재분배, 누적 Metric) |
| `O E2` | External Type 2 (재분배, 고정 Metric) - **기본값** |
| `O N1` | NSSA Type 1 (NSSA Area의 ASBR 재분배) |
| `O N2` | NSSA Type 2 (NSSA Area의 ASBR 재분배) |

---

## 📊 LSA-Type 표

| LSA-Type | 생성 주체 | 용도 | 전파 범위 | R/T 표기 | DB 표기 |
|----------|-----------|------|-----------|----------|---------|
| **Type-1** | OSPF All Router | 동일 Area 업데이트 | 동일 Area | `O` | Router |
| **Type-2** | DR | DR 정보 전파 | DR 포함 Network | (표기X) | Net |
| **Type-3** | ABR | 다른 Area 업데이트 | ABR 포함 Area | `O IA` | Summary Net |
| **Type-4** | ABR | ASBR 정보 전파 | ABR 포함 Area | (표기X) | Summary ASB |
| **Type-5** | ASBR | 외부 재분배 정보 | ASBR 포함 Area | `O E1`, `O E2` | Type-5 (External) |
| **Type-7** | NSSA ASBR | NSSA 재분배 정보 | NSSA Area | `O N1`, `O N2` | NSSA External |

---

## 🔍 데이터베이스 확인 명령어

```bash
show ip ospf database                ! 전체 LSDB
show ip ospf database router         ! Type-1
show ip ospf database network        ! Type-2
show ip ospf database summary        ! Type-3
show ip ospf database asbr-summary   ! Type-4
show ip ospf database external       ! Type-5
show ip ospf database nssa-external  ! Type-7
show ip ospf border-routers          ! ABR/ASBR 확인
```

---

## 💡 LSA Type별 차단 정리

| Stub 종류 | 차단 LSA |
|-----------|----------|
| Stub | Type 4, 5 |
| Totally Stub | Type 3, 4, 5 |
| NSSA | Type 4, 5 (Type-7 사용) |
| Totally NSSA | Type 3, 4, 5 (Type-7 사용) |

---

## 📌 E1 vs E2

| Type | Metric 계산 |
|------|-------------|
| **E1** | ASBR까지의 OSPF Cost + 재분배 Metric (누적) |
| **E2** | 재분배 Metric만 (고정) - **기본값** |

- ASBR로 가는 경로가 **복수개**라면 **E1** 사용 권장 (정확한 경로 선택)
- 단일 경로인 경우 E2로 충분

---

⬅️ **이전:** [08. Reference-Bandwidth 조정](./08-Reference-Bandwidth.md)  
➡️ **다음:** [10. OSPF ↔ EIGRP 재분배](./10-Redistribution.md)
