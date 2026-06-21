# Chapter 08. Reference-Bandwidth 조정

> 고대역 인터페이스 추가에 대비한 OSPF 메트릭 정확성 확보

---

## 🎯 요구사항

GigabitEthernet (Rx) 추가 예정 → OSPF에서 **정확한 메트릭 계산**을 위해 reference-bandwidth 조정.

---

## 📋 기본 Cost 계산

```
OSPF Cost = Reference-Bandwidth / Interface-Bandwidth
```

| 인터페이스 | 기본 BW | 기본 Cost (Ref=100Mbps) |
|-----------|---------|------------------------|
| FastEthernet | 100Mbps | 1 |
| GigabitEthernet | 1Gbps | 1 ← ❌ 구분 안됨! |
| 10Gigabit | 10Gbps | 1 ← ❌ 구분 안됨! |

✅ **Reference를 1000Mbps로 변경하면** GigaEthernet=1, FastEthernet=10으로 구분 가능

---

## ⚙️ R1 ~ R4 공통 설정

```bash
router ospf 1
 auto-cost reference-bandwidth 1000
```

---

## 🔍 확인 - FastEthernet

```bash
R1# show ip ospf interface fa0/0
FastEthernet0/0 is up, line protocol is up
  Internet Address 150.1.13.1/24, Area 14
  Process ID 1, Router ID 13.13.1.1, Network Type POINT_TO_POINT, Cost: 10
```

---

## 🔍 확인 - Serial

```bash
R1# show ip ospf interface s1/1
Serial1/1 is up, line protocol is up
  Internet Address 13.13.10.1/24, Area 0
  Process ID 1, Router ID 13.13.1.1, Network Type POINT_TO_POINT, Cost: 647
```

---

## 💡 핵심 포인트

- ⚠️ **모든 OSPF 라우터에서 동일한 reference-bandwidth 설정 필수**
- 일부만 설정하면 Cost 계산 불일치로 비효율적 경로 선택 발생
- IOS는 `auto-cost reference-bandwidth` 변경 시 경고 메시지 출력

---

⬅️ **이전:** [07. Loopback Network Type 변경](./07-Loopback-NetworkType.md)  
➡️ **다음:** [09. LSA-Type 정리](./09-LSA-Type.md)
