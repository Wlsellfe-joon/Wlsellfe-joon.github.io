---
layout: post
title:  "CAN Protocol 01"
date:   2024-04-07
author: Yongjun Kim
categories: Automotive
tags:	In-Vehicle-Network CAN CAN-bus CRC BitStuffing
cover:  "/assets/korea_river_byKYJ_gray.jpg"
---

자동차는 다양한 차량용 네트워크(In-Vehicle Networks)로 구성돼 있다. 차량용 네트워크의 구성, 종류, 등은 모두 OEM(Original Equipment Manufacturer)에 따라 상이하며 여기서 OEM이란 완성차 제조업체(현대, 벤츠, BMW.. 등등)를 의미한다. 

보통 개념적으로는 차량용 Ethernet이 Backbone으로 구성돼 있으며 해당 네트워크를 기반으로 CAN(Controller Area Network), LIN(Local Interconnect Network), FlexRay, 그리고 MOST(Medeia Oriented Systems Transport) 등이 Gateway로 연결돼 있는 형태이다. 이번 포스팅에서는 CAN 통신을 다루고자 한다.
<br><br>


## 1. CAN Protocol Outline
- CAN Bus 연결방식:<br>
다양한 ECUs(Nodes)가 두개의 Line을 공유하는 방식으로 연결됨 (CAN-High & CAN-Low),<br>
CAN-H & L 는 종단에 각각 120ohm의 저항을 가지고 있어 두개 전선 사이의 전압차로 0과 1을 구분하여 전송한다. 이러한 전송 방식 덕분에 각종 노이즈에 강인한 장점이 있다. 참고 사항으로 연결된 Node들은 Broadcasting 방식으로 통신하며 별도의 destination 정보 없이 Sender정보(Identifier, Arbitration이라고도 함)와 payload 및 부수적인 Field만 가지고 CAN Packet을 송수신 한다.<br><br>
  <img src="/assets/posts/CAN(From_Wikipedia).png" width="500" height="250" title="CAN Bus">
이미지 출처: [<u>CAN Bus</u>](https://en.wikipedia.org/wiki/CAN_bus#References)
<br><br>

- CAN Packet 종류:<br>
> - Data Frame: Payloads 전송을 위해 사용
> - Remote Frame: 데이터 송신을 요청하기 위해 사용
> - Error Frame: ECU마다 가지고 있는 Error Count의 갯수가 특정 임계값에 도달했을 경우 Error Frame을 Network로 송신하고 전체 노드에 에러를 알린다. (여기서 에러 관련 카운터는 TEC(Transmission Error Counter)와 REC(Receive Error Counter) 두가지가 있음) 만약 이후에도 에러가 지속적으로 발생할 경우 자체적으로 CAN Bus를 Off할 수도 있다.
> - Overload Frame: Overload condition에서 사용.
<br><br>

## 2. CAN - DataFrame
### 2.1 Data Frame info
<img src="/assets/posts/CANPacket(From_Wikipedia).png" width="800" height="250" title="CAN Packet info">
이미지 출처: [<u>CAN Bus</u>](https://en.wikipedia.org/wiki/CAN_bus#References)

- SOF & EOF: Start of Frame & End of Frame
- Arbitration: CAN Bus는 Broadcasting 방식으로 통신하기 때문에 여러 Packets 간에 충돌 방지 Rule이 필요하다. 이때 해당 Field 값을 기반으로 중재 규칙이 동작한다. 해당 Field값이 낮을 수록 높은 우선순위를 가져, '0'은 우성, '1'은 열성으로 동작한다. (e.g., A: 0x0110101 & B: 0x0110100 -> A 와 B 중에서 B가 우선순위를 가지므로 B가 통신된다)
> - RTR: 해당 Frame이 Remote frame 인가?

- Control: 
> - IDE: 해당 Frame이 Extended인가?
> - r0: Reserved
> - DLC: Data(Payload) section의 Length
- Data: Payloads
- CRC: Cyclic Redundancy Check (Error handling)
- Ack: 응답 Field

### 2.2 Bit Stuffing
2.1에서 살펴본 것 처럼 Data Frame의 중간 중간에 Stuff bit가 있는데, CAN Protocol은 NRZ (None Return to Zero) Encoding 방식을 차용하므로 수신자가 같은 값으로 특정 갯수 이상의 bit를 수신하면 에러가 있다고 판단한다. 따라서 같은 값의 bit가 5번 이상 반복될 경우 그에 대한 Inverse값을 Stuffing bit로 추가하여 전송하게 된다.

> (e.g., 2.1의 DataFrame)<br>
> - ID1(0) ID0(0) RTR(0) IDE(0) R0(0) : 5번 '0' bit 가 수신됐으므로, Stuffing bit (1)을 추가하여 송신한다.
> - (Cf) Encoding 방식 참고<br>NRZ, 맨체스터, 바이페이즈 방식
<br><br>

### 2.3 CRC에 관하여
CRC(Cyclic redundancy check)는 데이터 전송 시, 전송된 데이터에 오류가 있는지 없는지를 확인하기 위한 에러 검증방식 중 하나로, 기본적으로 사용하는 Field, Data에 따라 서로 다른 수의 bit CRC를 사용한다. CAN 통신의 경우 15bit의 CRC를 사용한다.<br><br>

송신측은 Bit stuffing은 고려하지 않고 Payloads의 CRC를 계산하여 Data에 함께 붙여 송신하고, 수신측은 이를 수신하여 에러가 있는지 CRC를 통해 검증한다. 보통 Real-time 연산 보다는 미리 연산된 Table을 이용하는 방식으로 검증한다.<br><br>

#### 2.3.1 CRC 생성(Tx):
- 사용하는 Data, Bit 수마다 상이한 CRC, Polynomial을 사용.
- CAN의 경우 15bits의 CRC & 0x4599 Polynomial 사용.
- Polynomial의 종류 (3가지): MSB 우선 코드, LSB 우선코드, Koopman (여기서는 MSB 우선 방식 적용)<br>

> 1) CAN에서 사용하는 CRC Polynomial = 100 0101 1001 1001 (0x4599)<br>
> 2) 15bit에 해당하는 CRC를 표현해주기 위해 MSB에 1을 추가<br>
> 3) Polynomial = 1100 0101 1001 1011<br>
> 4) CRC를 생성하기 위해 CRC bit수에 해당하는 숫자 만큼의 ‘0’ bit을 전송할 Data뒤에 추가<br>
> 5) 전에 생성한 Polynomial으로 해당 Data를 계속해서 나누는 과정을 반복<br>
> 6) 나누는 과정: XOR연산<br><br>

#### 2.3.2 CRC 검증(Rx):
- 수신한 Data와 CRC에 대해 다시한번 Polynomial로 XOR 연산 수행
- 최종 나머지가 '0'인 경우 에러 없음!

#### 2.3.3 참고사항:
- CAN은 Hamming Distance '6'을 가지므로 6-1=5, 즉 5개 bit에 대해 100% 에러 탐지가 가능하다.
- Rx에서 검증한 후에 Ack를 전송.
- CRC는 15bit를 가지며, 이후 구분자(Delimiter) 1bit과 함께 총 16bit가 전송된다.
- CRC delimiter이후엔 ACK와, 항상 열성인 ACK delimiter가 따라온다.
- CRC에서 사용하는 bit수가 1일때, 즉 사용하는 다항식(Polynomial)이 "x+1"인 경우를 우리는 "Parity Bit"라고 한다
- Parity bit는 짝수/홀수 방식으로도 설명 가능하다.


### 2.4 정리:
- DataFrame = ~skip~ + Data Field + CRC + CRC delimiter + ACK + ACK delimiter<br><br>

## 3. Reference
[1] https://www.can-cia.org/can-knowledge/can/crc/<br>
[2] https://www.linkedin.com/pulse/crc-calculation-can-network-vivek-maurya<br>
[3] https://en.wikipedia.org/wiki/Cyclic_redundancy_check<br>
[4] https://en.wikipedia.org/wiki/CAN_bus<br>
