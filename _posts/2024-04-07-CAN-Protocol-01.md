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

  <img src="/assets/posts/IVN(From_qaconsultants_dot_com).png" title="In vehicle networks">
이미지 출처: [<u>IVNs</u>](https://qaconsultants.com/blog/vehicle-data-and-different-communication-networks/)

<br><br>

## 1. CAN Protocol
- CAN Bus 연결방식: 다양한 ECUs(Nodes)가 두개의 Line을 공유하는 방식으로 연결됨 (CAN-High & CAN-Low)
  <img src="/assets/posts/CAN(From_Wikipedia).png" width="500" height="250" title="CAN Bus">
이미지 출처: [<u>CAN Bus</u>](https://en.wikipedia.org/wiki/CAN_bus#References)
<br><br>

CAN-H & L 는 종단에 각각 120ohm의 저항을 가지고 있어 두개 전선 사이의 전압차로 0과 1을 구분하여 전송한다. 이러한 전송 방식 덕분에 각종 노이즈에 강인한 장점이 있다.



