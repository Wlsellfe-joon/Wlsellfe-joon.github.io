---
layout: post
title:  "CAN Protocol 01"
date:   2024-04-07
author: Yongjun Kim
categories: Automotive
tags:	In-Vehicle-Network CAN CAN-bus
cover:  "/assets/korea_river_byKYJ.jpg"
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

