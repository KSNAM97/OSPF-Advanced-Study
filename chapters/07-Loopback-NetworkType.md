# Chapter 07. Loopback Network Type 변경

> OSPF에 포함된 모든 네트워크가 인터페이스에 할당된 서브넷마스크로 광고되도록 설정

---

## 🎯 요구사항

OSPF에 포함된 모든 네트워크는 **인터페이스에 할당된 서브넷마스크**로 확인되어야 한다.  
(기본적으로 Loopback은 `/32`로 광고됨)

---

## ⚙️ R1 ~ R4 공통 설정

```bash
interface Loopback0
 ip ospf network point-to-point
```

---

## 🔍 확인 - R4

```bash
R4# show ip route
     13.0.0.0/24 is subnetted, 2 subnets
C       13.13.4.0 is directly connected, Loopback0
C       13.13.14.0 is directly connected, FastEthernet0/1
     150.1.0.0/24 is subnetted, 1 subnets
C       150.1.13.0 is directly connected, FastEthernet0/0
```

✅ Loopback이 `/24` 그대로 광고됨

---

## 💡 핵심 포인트

| Network Type | Loopback 광고 마스크 |
|--------------|---------------------|
| Loopback (기본값) | `/32` (Host route) |
| Point-to-Point | 실제 설정된 서브넷마스크 |

- Loopback 인터페이스는 OSPF 기본 동작에서 무조건 `/32`로 광고
- 실제 마스크 광고를 원하면 `ip ospf network point-to-point` 적용

---

⬅️ **이전:** [06. EIGRP 100 구성](./06-EIGRP-Config.md)  
➡️ **다음:** [08. Reference-Bandwidth 조정](./08-Reference-Bandwidth.md)
