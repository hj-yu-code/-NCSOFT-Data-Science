## Mobius를 활용한 IoT 데이터 수집부 구현

Mobius는 oneM2M 국제 표준을 기반으로 한 IoT 플랫폼이다. 이때 oneM2M은 IoT 기술을 위한 국제표준을 만든 단체이다. 이 단체는 클라우드 서버와 관련하여, 데이터 정규화를 위해 TTA(한국), ETSI(유럽), ATIS/TIA(미국), CCSA(중국), ARIB/TTC(일본) 등 7개 기관이 공동으로 구성되어 있으며, IoT 국제표준 단체 중 가장 큰 파이를 가지고 있다. 이 oneM2M 국제 표준을 기반으로 KETi(한국전자기술연구원)에서 만든 플랫폼이 ‘Mobius’이다. 이 플랫폼을 바탕으로 서버와 MCU와 연동하여 IoT 구축을 진행하였다.

![image](https://user-images.githubusercontent.com/70775670/136464692-ff32829e-71a9-463e-addc-6fb424f35735.png)

 KETI에서 제공된 Mobius 아키텍처를 보면, ‘Mobius’는 IoT의 서버 플랫폼에 해당하는 용어이며, ‘&Cube’는 IoT 디바이스 플랫폼을 지칭하는 용어라고 보면 된다. 이때 oneM2M은 &Cube와 Mobius간 통신에서 사용된 네트워크 통신표준에 해당된다. 또한 이 Mobius 서버에 있는 데이터는 ‘IoT App’을 이용해서 확인하고 컨트롤할 수 있다. oneM2M 표준을 기반으로, Mobius를 거쳐 &Cube까지 전달되고 최종적으로 디바이스를 컨트롤하는 것이다.
 
 이 업무는 두 플랫폼을 따로 구축하면서 진행하였다. 먼저 Mobius 서버 구축을 하고, 제대로 서버가 구축되었는지 테스트하였다. 그 뒤에 &Cube 코드를 살펴보며 제공받은 MCU(마이크로컨트롤러)에 맞게 호환시켰다. 또한, IoT App은 KETI에서 제공해준 코드를 약간 수정하여 MCU가 컨트롤되는지 확인하였다.
 
![image](https://user-images.githubusercontent.com/70775670/136464720-beb9e1d1-3ad2-44ad-9599-f70d4425a2ad.png)
![image](https://user-images.githubusercontent.com/70775670/136464766-0f4ba3d7-2890-495f-ac36-feeae131b7e3.png)

 위 아키텍처를 참고하면, Mobius는 node js 프레임워크 기반에서 MQTT 통신 프로토콜을 사용한다. 따라서 Mobius 플랫폼은 사전에 MQTT와 node js를 설치해주어야 한다. 회사에서 제공받은 서버는 CentOS 7 OS이며, MySQL 언어와 호환이 되는 MariaDB가 설치되어 있었다. 이 서버의 OS에 맞게 MQTT와 MySQL을 설치하였고, 버전과 간단한 테스트를 하여 제대로 설치되었는지 확인하였다.
 
 그 다음 본격적으로 Mobius를 설치했다. 먼저 방화벽을 열어주고, git을 설치하여 KETI github에 공유된 Mobius를 다운로드받았다. 그 디랙토리로 들어가 Mobius를 설치하고 해당 DB를 만들고, 만들어진 DB와 연동하기 위해 환경설정도 설정했다. 최종적으로 Postman 프로그램을 통해 제대로 설치되었는지를 확인하면서 Mobius 서버구축을 완료하였다.
 
 Mobius 서버 구축이 완료되었으니 &Cube 코드를 손봐서 MCU에 펌웨어 하면 된다. 회사에서 제공해 준 MCU 보드는 nodeMCU로, KETI에서 사용한 보드(feather M0)와 다른 보드였다. 그 &Cube를 사용하기 위해 전체적으로 코드를 손봐야했고, 그 코드를 이해하기 위해 여러 테스트를 진해하였다. LED 및 CCS811 센서 제어, C보드에서 PHP를 통한 DB 연결, 보드에서 바로 DB 연결, 보드에서 MQTT를 통한 모바일 연결, 모바일에서 MQTT를 통해 보드 제어 등을 테스트하였다. 또한, oneM2M 국제 표준을 공부하면서 원리를 파악하고자 했다.
 
 기본적인 공부를 끝낸 후에 &Cube 코드 분석을 시작했다. 어느 파트가 어떤 동작을 담당하는지 확인하면서 수정할 부분을 체크하였다. 헤더들도 확인하여 nodeMCU도 작동가능한지 확인하고, 안되면 되는 헤더들도 바꾸는 작업들을 하였다. 그 과정에서 WiFi 설정 모드(AP, STA), OTA, mDNS 등의 동작원리도 찾아갔다. 여러 수정작업 끝에 nodeMCU와의 호환이 가능해졌다.
 
 주어진 업무가 끝났기에 해당 내용을 정리하여 문서화하였다. 이 내용을 다른 사원들에게 교육하기도 하여 발표하기 적당한 PPT로 정리하였다. 따라서 조금 시간이 지난 5월에 사원들과 팀장님, 차장님 대상으로 교육을 2번 진행하였다. 그 교육을 위해 다시 Mobius 서버 구축부터 다시 시작했고, 보드들도 잘 작동하는지 확인하였다. 또한, 발표 자료도 다시 확인하여 전기전자 파트부분은 최대한 줄이고, 그들이 이해할 수 있게 끔 하였다.
 
 # 파일 설명

- mobius 코드 분석.pptx

 MCU 보드의 최적화를 위해 main 코드를 분석한 내용이다.
 

- mobius_guide.pptx

 진행한 프로젝트를 정리하고, 이를 남들이 알아보기 쉽도록 작성한 자료이다.
 

- nCube-Air-featherM0.zip

 featherM0 MCU에 최적화된 코드이다.
 

- nCube-Air-nodemcu.zip

 nodemcu MCU에 최적화된 코드이다.
 

- nodemcu_아두이노lib_test.zip

 Mobius와 MCU를 연동하는 과정에서 진행한 테스트 코드이다.
 
 Mobius를 거치지 않고 바로 DB에 접근할 수 있는 코드나 핸드폰으로 컨트롤링하는 코드 등이 들어있다.
