---
layout: post
title:  "CAN Protocol 02 - [Paper Review 01]"
date:   2024-07-16
author: Yongjun Kim
categories: Automotive
tags:	In-Vehicle-Network CAN CAN-bus PaperReview ShadowAuth
cover:  "/assets/korea_river_byKYJ_gray.jpg"
---

### ShadowAuth: Backward-Compatible Automotive CAN Authentication for Legacy ECUs
### 00. Authors
Sungwoo Kim, Gisu Yeo, Taegy Kim, Junghwan "John" Rhee, Yuseok Jeon, Antonio Bianchi, Dongyan Xu, Dave (Jing) Tian

### 01. Introduction
본 논문은 CAN Protocol의 Security mechanism 부족을 본질적 취약점으로 제시하며 ECU's impersonation 가능성을 제시한다. 따라서, 이를 해결할 수 있는 ECU 인증 기법을 소개하며 해당 기법의 독창성을 강조한다. <br>
제시하는 방법론은 ECU's firmware code의 수정 없이, 그리고 CAN Packet의 수정 없이 backward-compatible하게 CAN Protocol에서 인증 기법을 추가할 수 있다고 강조하고 있다. <br><br>
<img src="/assets/posts/PR01/Abstract.PNG" width="450" height="250" title="Abstract"> 
<!-- 이미지 출처: [<u>Abstract</u>](https://dl.acm.org/doi/pdf/10.1145/3488932.3523263)<br> -->

최근 DNN 기술을 활용한 IDS기술은 많이 소개 됐으나 인증 기법을 추가하는 부분은 새롭게 다가오긴 했다, 아래에서 어떤 방식으로 인증 기법을 추가하는지와 논문에서 강조하는 "Backward-Compatible"에 대해 살펴볼 필요가 있을것 같다!
<br>

### 02. Methodology
#### 2.1 Overview
<img src="/assets/posts/PR01/Methodology1_overview.PNG" width="850" height="250" title="Overview"><br>
 <!-- 이미지 출처: [<u>Overview</u>](https://dl.acm.org/doi/pdf/10.1145/3488932.3523263)<br><br> -->
Figure02로 제시된 위 그림과 같이, 논문은 크게 두개의 단계를 Shadow-authentication 방법론으로 제시한다.

1) Firmware Re-writing
- CAN에 연결된 ECU를 대상으로 정적, 동적분석을 진행하고, LLVM-IR(Intermediate Representation) 상태에서 CAN Transmission Function을 찾는다.

- 이후, 트램펄린 Code를 생성하여 Target Ecu가 논문에서 제시하는 Authentication Code를 생성 및 전송할 수 있도록 함수를 추가한다.

- ECU Firmware를 Patch

2) Authentication
- CAN Bus에 추가된 Authenticator가 인증 패킷을 보내도록 수정된 ECU의 메시지를 보고 정상 ECU가 보낸 Packet인지 검증한다.

#### 2.2 How to generate Authentication code (packet)?
<img src="/assets/posts/PR01/Methodology2_FirmwarePatch.PNG" width="500" height="400" title="Firmware Patch"><br>
<br>
Figure03을 통해 어떻게 Authentication code를 생성하는지 알 수 있다. //Rewritten firmware example에서 Code section 및 Stack, heap은 유지한 상태로 정적, 동적 분석을 통해 파악한 CAN Transmission function call 이후에 Trampoline code로 jump한다. <br> 그럼 Trampoline code에서는 (2)이전 State를 저장하고, (3)인증을 위한 HMAC을 연산하고, (4)연산한 인증 패킷을 송신한 후, (5)이전 State를 pop하여 (6) 복귀한다. 이러한 방식으로 논문은 ECU를 인증 코드를 송신할 수 있도록 수정할 것을 제안한다.

개인적인 생각이지만 CAN bus에 연결된 ECU가 엄청 많을텐데,, 모든 Firmware를 대상으로 수정하기 보단 인증이 필요한 주요 ECU에만 적용이 될 수 있을 것 같다..!

#### 2.3 Generated Auth code
논문에서 제시하는 인증 패킷은 Target ECU가 전송하는 CAN Packet이 전송된 이후 생성되어 인증을 위해 CAN-bus에 broadcast되는 방식이다. <br><br>
<img src="/assets/posts/PR01/Methodology3_generatedCode.PNG" width="500" height="500" title="Authentication Packets"><br>
<br>
Figure04를 살펴보면 Authentication Packet은 기존 CAN Protocol을 따르며, ID section에 n-bit의 Recessive bit가 사용되고, 11-n bit의 Randomized bit가 채워진다. Data section에는 Target ECU가 인증 패킷을 전송할 때 마다 증가시킨 Counter 값을 기반으로 연산한 HMAC값이 채워진다.<br> 이러한 방식으로 생성된 인증 패킷은 CAN-Bus에 추가되는 Authenticator를 통해 인증되는 과정을 거치게 된다. <br>
CAN Protocol 공격방식은 대부분 Identifier의 우선순위 취약점을 활용하거나 Payload를 변조하여 차량을 제어하는 방식인데, 이러한 방식으로 인증 코드를 생성하게 되면 이러한 공격은 불가능할 것으로 기대된다.. 다만 Target Ecu가 송신하는 원본 메시지에 대한 암호화 등이 아니므로 근본적인 해결 방식은 아닌듯 하다.

#### 2.4 How to Authenticate?
이제 CAN-Bus에는 Target ECU가 송신한 원본 패킷과 인증 패킷이 Broadcast되고 있을 것이다. 이때 '인증자'가 해당 패킷을 수신하여 패킷을 인증한다.
<img src="/assets/posts/PR01/Methodology4_Authentication1.PNG" width="500" height="250" title="Authenticator01"><br>
패킷 인증 과정은 다소 Overhead가 높아 보인다, 자체적으로 보유한 CAN-ECU map 을 통해 전송 가능한 ECU를 서치하고, 가지고 있는 Counter로 HMAC을 연산하여 이를 대조하여 인증하는 방식이다...<br><br>
<img src="/assets/posts/PR01/Methodology5_Authentication2.PNG" width="500" height="250" title="Authenticator02"><br>
논문에서는 Figure06과 같이 edge를 비교하여 빠르게 탐색하는 방법을 제시하고 있긴 하다!,, 크게 중요한 것 같진 않으니 오버헤드를 낮게하는 인증 방안이 추가 돼 있다 정도만 이해하면 될 듯 하다.

### 03. Result
논문에서 제시하는 평가 항목은 세가지이다.<br>
1) 얼마나 공격을 잘 막는지?
2) CAN Transmission function 분석이 잘 됐는지?
3) ECU 성능에 영향을 미치는지?

논문에서는 Impersonation attack을 실행하여 (1) 항목에 대해 잘 탐지하고 있는 부분을 제시하고 있고,<br>

(2) 항목에서는 fusEFI, ms3, and Styreenhet 3가지 ECU를 분석하며 정확도를 평가하고 있다. 
<img src="/assets/posts/PR01/Result01.PNG" width="500" height="400" title="Authenticator02"><br>

마지막을 (3) 항목에 대해서는 Runtime CDF와 HMAC Overhead를 평가 척도로 제시한다. Figure12와 같이 43%의 인증 패킷은 wait time 없이 수신되었고, HMAC 연산 또한 ECU CAN transmission 주기가 1.5ms인데 반해 약 4micro sec정도 소요 되었다.
<img src="/assets/posts/PR01/Result02.PNG" width="500" height="400" title="Authenticator02"><br>

결론적으로, 모든 평가항목을 종합해 봤을때, 큰 부하를 야기하지 않으면서 안전한 인증 메커니즘을 CAN Protocol에 추가할 수 있는 방법론이라 할 수 있다.

다만, 앞서 언급한 바와 같이 모든 Target ECU에 대한 작업은 사실상 어려우므로 주요 ECU에만 적용을 하는 등의 방안이 필요할 것으로 보인다. 또한 CAN Protocol의 취약점을 근본적으로 해결하는 방법은 아닌 것으로 보인다.

### Reference
- [Paper link] https://dl.acm.org/doi/pdf/10.1145/3488932.3523263
