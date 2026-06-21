# Chapter 06. EIGRP 100 구성 (R3-R5)

> R3, R5 FastEthernet 구간에 EIGRP AS 100 구성

---

## 🎯 요구사항

- EIGRP AS 100
- **Auto-summary 비활성화**
- 필요한 인터페이스로만 패킷 송신 (`passive-interface default`)
- R3의 `Fa0/1` 포함 네트워크는 EIGRP에 포함

---

## ⚙️ R3 설정

```bash
router eigrp 100
 no auto-summary
 passive-interface default
 no passive-interface fa0/0
 network 13.13.13.0 0.0.0.255
 network 150.3.13.0 0.0.0.255
```

---

## ⚙️ R5 설정

```bash
router eigrp 100
 no auto-summary
 no passive-interface fa0/0
 network 13.13.5.0 0.0.0.255
 network 13.13.15.0 0.0.0.255
 network 150.3.13.0 0.0.0.255
```

---

## 🔍 확인 - R3

```bash
R3# show ip eigrp neighbors
IP-EIGRP neighbors for process 100
H   Address       Interface   Hold Uptime    SRTT  RTO   Q  Seq
                              (sec)          (ms)        Cnt Num
0   150.3.13.5    Fa0/0       14   00:00:32  60    360   0  4
```

---

## 🔍 확인 - R5

```bash
R5# show ip eigrp neighbors
IP-EIGRP neighbors for process 100
H   Address       Interface   Hold Uptime    SRTT  RTO   Q  Seq
                              (sec)          (ms)        Cnt Num
0   150.3.13.3    Fa0/0       12   00:00:07  54    324   0  3
```

---

## 💡 핵심 포인트

- **`no auto-summary`** = Classful 자동 요약 비활성화 (필수)
- **`passive-interface default`** = 모든 인터페이스 Hello 차단 후 필요한 것만 활성화
- 이후 R3가 **ASBR**이 되어 OSPF ↔ EIGRP 재분배 수행 (Chapter 10)

---

⬅️ **이전:** [05. Area 14 구성](./05-Area14-Config.md)  
➡️ **다음:** [07. Loopback Network Type 변경](./07-Loopback-NetworkType.md)
