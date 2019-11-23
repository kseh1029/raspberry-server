# :strawberry:라즈베리파이4로 개인서버 만들기
>토이프로젝트를 올릴 서버를 2019년 7월에 새로 출시한 라즈베리파이4로 만들어 보았다.

## 0. 목차
1. **재료 준비 및 시작**  
  1.0. 구입한 제품들  
  1.1. 전원 연결  
  1.2. 안전하게 종료하기
  
2. **OS 설치**  
  2.0. Raspbian 설치 방법 선택  
  2.1. Raspbian 이미지 다운로드  
  2.2. SD카드에 Raspbian 이미지 굽기  
  2.3. 라즈베리파이에 OS 설치

3. **기본 환경 설정**  
  3.0. 설정 도구 실행  
  3.1. 비밀번호 변경  
  3.2. Locale, Timezone, Keyboard, Wi-fi Country 설정  
  3.3. SSH 허용  
  3.4. Hostname, Wi-fi 설정

---

## 1. 재료 준비 및 시작
>구입할 당시에 한국에 라즈베리파이4 판매처가 아예 없어서, 미국에 사는 처남에게 부탁해서 제품을 구매했다.  
>구글링을 꽤 해보고 리뷰들도 읽어 본 후에 구매한 제품들이지만, 최고의 선택이 아닐 수도 있다.  
>(제품 홍보 절대 아닙니다~) 

### 1.0. 구입한 제품들

<img src="https://github.com/Integerous/images/blob/master/raspberry-pi/whole_mini.png?raw=true" width="60%" height="60%">

1. **Raspberry Pi 4 보드** ([링크](https://www.canakit.com/raspberry-pi-4-4gb.html))
2. **USB C Type 충전기 (5V 3A)** ([링크](http://innomart.co.kr/goods/view?no=2030&market=naver&NaPm=ct%3Dk03ya1kw%7Cci%3Dfba2d08e22dc9f7d8922a9fcb37344b17954e7bc%7Ctr%3Dsls%7Csn%3D244253%7Chk%3D28ee28dec6817eca0cb3c06acab11b1450612912))
    - 생각보다 5V 3A PSU를 구하기가 힘들었다.
    - 라즈베리파이4 공식 PSU의 경우 유럽 제품을 사용해야 하는데, 유럽 사이트에서 직구해야해서 비싸다.
    - [이 제품](https://www.devicemart.co.kr/goods/view?no=1342118)을 사용해도 될 것 같다. 
3. **모니터 케이블 (Micro-HDMI to HDMI)** ([링크](https://www.amazon.com/AmazonBasics-High-Speed-Micro-HDMI-HDMI-Cable/dp/B014I8U33I/ref=cm_cr_arp_d_product_top?ie=UTF8))
4. **Micro SD 카드 (64GB)** ([링크](http://www.11st.co.kr/product/SellerProductDetail.tmall?method=getSellerProductDetail&prdNo=2544143323&NaPm=ct=k03v873s|ci=fc6845cda72728607bd190e48de2419986152e3c|tr=slct|sn=17703|hk=aee1cc28b613ef90d05f1b7a43e41e9303215e76&utm_term=&utm_campaign=%B3%D7%C0%CC%B9%F6pc_%B0%A1%B0%DD%BA%F1%B1%B3%B1%E2%BA%BB&utm_source=%B3%D7%C0%CC%B9%F6_PC_PCS&utm_medium=%B0%A1%B0%DD%BA%F1%B1%B3))
5. **케이스 + 쿨링팬** ([링크](https://www.amazon.com/gp/offer-listing/B07VDCT57F/ref=dp_olp_new_mbc?ie=UTF8&condition=new))
    - 반드시 필요한 것은 아니지만, 라즈베리파이4의 발열 이슈가 있는데 해외 유저들은 쿨링팬으로 해결한다고 한다.
    - 발열 이슈 때문에 보드 설계를 수정할 것이라는 제작사측의 발표도 있었다. (젠장)   

### 1.1. 전원 연결
1. 라즈베리파이 보드를 케이스와 결합
2. 쿨링팬을 라즈베리파이 보드의 GPIO에 연결
    - [이 곳](https://pinout.xyz/)에서 핀의 위치를 확인한다.
    - 팬의 속도를 3.3v의 low speed와 5v의 high speed 중 선택할 수 있다.
    - 3.3v(low speed)의 위치는 위의 링크에서 1번(빨간선), 6번(검정선)
    - 5v(high speed)의 위치는 4번(빨간선), 6번(검정선)
3. 전원을 연결하면 빨간불과 녹색불이 켜지고, 팬이 돌아간다.

<img src="https://github.com/Integerous/images/blob/master/raspberry-pi/power_on.png?raw=true" width="60%" height="60%">

### 1.2. 안전하게 종료하기
- 처음에는 라즈베리파이의 전원코드를 뽑는 방법으로 종료를 해왔는데, 위험한 방법이었다.
- [라즈베리파이 종료하는 방법](https://pimylifeup.com/how-to-shutdown-a-raspberry-pi/)이라는 글에 의하면, 
SD카드가 고장날 수 있고, 상황에 따라 데이터가 손실될 수 있다고 한다.
- `$ sudo shutdown -h now` 명령을 하면, 아래의 과정을 거쳐 안전하게 종료된다.
  1. 실행중인 모든 프로세스에 **SIGTERM** 명령을 보내서 안전하게 저장하고 종료(Exit)하게 한다.
  2. 약간의 간격을 두고, **SIGKILL** 명령을 보내서 남아있는 프로세스들을 종료(Halt)한다.
  3. 모든 파일 시스템들을 분리(unmount)한다.
  4. 화면에 **System Halted**라고 표시된다. (라즈베리파이4의 경우 표시되는 메세지를 볼 겨를 없이 모니터 연결이 종료된다.)
  5. 이제 전원코드를 뽑아도 된다.
  6. 다시 부팅하려면 전원코드를 연결하면 된다.

## 2. OS 설치
>[다른 선택지들](https://www.raspberrypi.org/downloads/)도 있지만, 첫 경험이므로 얌전하게 공식 OS인 Raspbian을 설치한다.

### 2.0. Raspbian 설치 방법 선택
1. NOOBS 사용
    - [NOOBS](https://www.raspberrypi.org/downloads/noobs/)(New Out Of the Box Software)라는 운영체제 설치 관리자를 사용해서 설치할 OS를 선택하는 방법
    - 부팅과 설치 속도가 느리다.
    - 다양한 OS 중에 선택하여 설치 가능.
2. OS 이미지를 SD카드에 기록하여 설치
    - 이미지 기록 프로그램(Etcher 또는 Win32 Disk Imager)을 사용하여 SD카드에 OS 이미지를 구워서 설치하는 방법
    - 부팅과 설치 속도가 빠르다.
    
>나의 경우 설치하려는 OS와 설치 목적이 분명하므로 2번 방법으로 설치를 진행했다.  

#### 2번 방법으로 Raspbian 설치 절차
1. OS 이미지 다운로드  
2. 다운받은 이미지(zip 또는 torrent)를 Micro SD 카드에 굽기
3. OS 이미지가 구워진 Micro SD카드를 라즈베리파이에 삽입하고 부팅하여 설치하기

### 2.1. Raspbian 이미지 다운로드
- [이 곳](https://www.raspberrypi.org/downloads/raspbian/)에서 Raspbian 이미지를 다운로드 받는다.
- 나는 서버로만 사용할 예정이기 때문에 Desktop GUI가 없지만 리소스 소모가 적은 Lite 버전을 선택했다.

<img src="https://github.com/Integerous/images/blob/master/raspberry-pi/raspbian.png?raw=true">


### 2.2. SD카드에 Raspbian 이미지 굽기

0. 기존에 사용했었던 SD카드는 포맷한 후에 사용하는 것이 안전하다. ([SD Memory Card Formatter](https://www.sdcard.org/downloads/formatter/))
1. 준비해둔 Micro SD카드를 SD Adapter 혹은 USB 리더기에 꽂아서 랩탑/데스크탑에 연결한다.  
2. [Etcher](https://www.balena.io/etcher/)를 사용해서 다운받은 OS 이미지를 Micro SD카드에 굽는다.(flash)
    - Etcher 사용법은 간단하다.
    - 이미지를 선택하고, SD카드를 선택하고, Flash(굽기)!
      - 이 때, 다운받은 Raspbian OS 이미지가 zip 파일일텐데, 압축을 풀 필요없이 그대로 사용하면 된다. 
    - <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/etcher1.png?raw=true" width="60%" height="60%">  

### 2.3. 라즈베리파이에 OS 설치
1. Rasbian 이미지가 구워진 SD카드를 라즈베리파이에 삽입한다.
2. 라즈베리파이에 모니터와 키보드를 연결한 후, 전원을 연결한다.
3. 전원을 연결하면 빨간 라즈베리 4개가 화면에 표시되면서 부팅이 시작되고, Rasbian이 자동으로 설치된다.
4. 설치가 정상적으로 완료되면 (Lite 버전의 경우) `raspberrypi login: `이 보인다.
5. 초기 아이디는 `pi`, 비밀번호는 `raspberry` 


## 3. 기본 환경 설정
>Raspbian Lite 버전을 기준으로 서버로 활용하기 위한 기본 환경을 설정한다.

### 3.0. 설정 도구 실행
1. `$ sudo raspi-config` 명령을 통해 라즈베리파이 설정 도구(Raspberry Pi Software Configuration Tool)를 실행한다.  
2. 그럼 아래와 같이 ~~블루라이트 차단 안경을 무력화하는~~ 새파란 설정 도구가 실행된다.
   
  <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/raspberry_config.png?raw=true" width="60%" height="60%">

### 3.1. 비밀번호 변경
1. `1. Change User Password`를 선택하여 비밀번호를 변경한다.    
2. 초기 비밀번호를 사용해도 되지만, 라즈베리파이를 분실했을 경우를 고려하면 바꾸는 것이 마음 편하다.
3. (주의) 키보드 설정을 하기 전에는 !@#$%^&* 등의 문자를 사용하지 않는 비밀번호를 사용한다.

### 3.2. Locale 변경
1. `4. Localisation Options` -> `I1 Change Locale` 클릭
2. 쭉 내려가서 `[*] en_US.UTF-8 UTF-8` 선택 (스페이스바 사용) 후 OK
    - `ko_KR.UTF-8 UTF-8`을 사용하면 에러 메세지로 구글링하기가 더 어려워서 미국으로 선택했다.
3. Default locale for the system environment 를 묻는 화면에서 `en.US.UTF-8` 선택 후 OK

### 3.3. Timezone 변경
1. `4. Localisation Options` -> `I2 Change Timezone` 클릭
2. Asia 선택, Seoul 선택
    - 서버 시간을 그리니치 표준시(GMT+0) 또는 협정 세계시(UTC+0)로 맞추려면 London을 선택한다.

### 3.4. Keyboard Layout 변경
1. `4. Localisation Options` -> `I3 Change Keyboard Layout` 클릭
2. [자세히 설명된 글](https://dullwolf.tistory.com/17)을 참고하여 키보드를 설정한다.
3. 설정하지 않으면 !@#$%^&*() 등의 Shift+숫자키로 사용하는 키를 사용할 수 없다고 한다.
4. ~~해피해킹도 선택지에 있어서 기뻤다.~~

### 3.5. Wi-fi Country 변경  
>(주의) 반드시 변경해야 되는 것은 아니다.  
>변경할 경우 `/etc/wpa_supplicant/wpa_supplicant.conf` 파일에 `country={국가코드}`가 작성되는데, 이것이 없어야만 무선 네트워크가 검색되는 경우도 있다고 한다.

1. `4. Localisation Options` -> `I4 Change Wi-fi Country` 클릭
2. `US United States` 혹은 `GB Britain (UK)` 선택
    - GB Britain (UK)를 선택해야만 정상 동작 한다는 사용자들도 있다.
    - u를 입력하면 United States(미국)을 금방 찾을 수 있다.
3. **`KR Korea (South)`를 선택하면 안된다.**
    - 한국으로 선택했을때 무선 네트워크를 검색하지 못한다는 블로그 글이 많다.
    - 나의 경우
      - 한국으로 선택해도 우리집 무선 네트워크는 검색이 된다.
        - 검색 방법 : `sudo iwlist wlan0 scan` 
      - 그런데 한국을 선택하고 `$ ping www.google.com`으로 ping을 날려보면,
      - 패킷이 전송되지 않고 `ping: www.google.com: Temporary failure in name resolution` 메세지가 뜬다.
      - 미국, 영국, 일본을 선택하면 패킷이 정상적으로 전송된다.
      - 또는 국가코드를 아예 삭제해도 패킷이 정상적으로 전송된다.
        - 국가코드 삭제 방법 : `/etc/wpa_supplicant/wpa_supplicant.conf` 파일에 `country={국가코드}` 삭제
      
### 3.6. Wi-fi 설정 (raspi-config 사용)
>와이파이를 raspi-config로 설정하는 방법과 wpa_passphrase로 설정하는 방법이 있다.

1. `2. Network Options` -> `N2 Wi-fi    Enter SSID and passphrase` 클릭
2. 라즈베리파이가 사용할 공유기의 SSID(와이파이 이름) 입력. 예시) KT_GiGA_2G_XXXX
3. 비밀번호 입력
4. 공유기 접속 확인
    - `$ iwconfig` 명령의 결과에서 `ESSID`가 입력한 것과 같으면 정상 접속. 
5. 설정 확인
    - 설정 파일(/etc/wpa_supplicant/wpa_supplicant.conf)에서 SSID와 비밀번호 확인 가능    

### 3.7. Wi-fi 설정 (wpa_passphrase 사용)
>raspi-config로 와이파이 설정 시, password가 설정 파일(/etc/wpa_supplicant/wpa_supplicant.conf)에 그대로 노출된다.  
>비밀번호를 암호화하기 위해서는 raspi-config 대신 wpa_passphrase 명령으로 와이파이를 설정하는 것이 좋다. 

1. `$ wpa_passphrase {SSID} {비밀번호}` 명령을 치면, network 정보가 아래와 같이 콘솔에 출력된다.
    ~~~
    network={
            ssid="KT_GiGA_5G_HOME"
            #psk="{비밀번호}"
            psk=7ac0c35da93c82d ....(생략)
    }
    ~~~

2. 출력된 network 정보를 설정 파일(/etc/wpa_supplicant/wpa_supplicant.conf)에 추가한다.
    - 이 때, 마우스가 없으니 출력될 정보를 Redirecting output(>>)으로 설정 파일에 넣는다.  
    - [라즈베리파이 공식 문서](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)에 자세하게 설명되어있다. 
    ~~~sh
    $ sudo su  # root 권한으로 변경
    $ wpa_passphrase {SSID} {비밀번호} >> /etc/wpa_supplicant/wpa_supplicant.conf
    ~~~ 

3. wpa_supplicant.conf 파일은 아래와 같다.
    ~~~conf
    ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
    update_config=1
    
    network={
              ssid="KT_GiGA_5G_HOME"
              #psk="{비밀번호}"
              psk=7ac0c35da93c82d ....(생략)
    }
    ~~~
    - (주의) network 윗부분의 내용도 반드시 포함되어야 한다.
    - 비밀번호가 노출된 `#psk="{비밀번호}"`는 지우는 것이 좋다.
    
### 3.7. SSH 허용 및 접속
>~~시력 보호를 위해~~ 라즈베리파이에 SSH로 원격 접속해서 사용하는 것이 훨씬 편리하다.  
>SSH 접속을 허용하면 모니터와 키보드를 연결할 필요 없이,  
>PC/랩탑에서 SSH를 통해 라즈베리파이에 접속하면 된다. (마치 AWS EC2 인스턴스가 내 책상에..)
1. SSH 허용
    - `5. Interfacing Options` -> `P2 SSH` -> `YES`
2. 라즈베리파이 IP 확인
    - `$ ifconfig`
    - `wlan0` 의 `inet addr: xxx.xx.x.xx`에 적힌 IP 확인
3. SSH 접속
    - 주로 사용하는 PC/랩탑에서 `$ ssh pi@xxx.xx.x.xx`로 접속

## 4. IP 고정 할당, 포트포워딩, DDNS 설정
>이 부분은 사용하는 공유기에 따라 설정 방법이 다르다.  
>ipTIME A3004NS-M 모델 (펌웨어 버전 11.00.4) 기준으로 작성했다.

### 4.1. 내부IP 고정
>공유기를 통해 wi-fi를 사용하는 기기들에게 공유기는 임의로 내부IP(사설IP)를 할당한다.  
>ipTIME의 경우 맥북은 192.168.0.2, 휴대폰은 192.168.0.3, 라즈베리파이는 192.168.0.4 이런식이다.  
>그런데 공유기에 전원이 차단되어 공유기가 재부팅되는 경우 등으로 인해 내부 IP가 초기화 될 수 있다.  
>이런 상황을 대비해서 내부 IP를 공유기 내에서 고정시키는 것이 좋다.

1. ipTIME 접속 (192.168.0.1)
2. 고급설정 - 네트워크 관리 - DHCP 서버 설정
3. 하단에 [사용중인 IP 주소 정보] 중 라즈베리파이의 체크박스를 클릭하고 위에 등록 버튼을 누른다.
    <img src="https://github.com/Integerous/images/blob/master/raspberry-pi/iptime01.png?raw=true" width="60%" height="60%">


### 4.1. IP 고정할당
>헷갈리면 안되는 것이, 고정 IP와 IP 고정할당은 다른 개념이다.  
>가정집에서는 대부분 유동 IP가 할당되고, ISP 사업자(KT, LG U+, SKT 등)에 추가요금을 내야 고정 IP를 제공받을 수 있다.  
>그런데 월 3만원 지불하고 고정 IP를 제공받을 바에, 클라우드 서비스를 이용하거나 서버를 임대하는 것이 나은 것 같다.

유동 IP는 DHCP(동적 호스트를 제공하는 프로토콜)를 통해 IP를 할당받고


- 참고 : [개인서버에서 유동 IP를 고정 IP처럼 (DDNS 아님)](https://studyforus.tistory.com/137)

### 4.2. 포트포워딩 설정
>외부에서 라즈베리파이로 ssh 접속하기 위한 포트포워딩  
>

1. ipTIME 접속 (192.168.0.1)
2. 고급설정 - NAT/라우터 관리 - 포트포워드 설정
3. 새 규칙 추가
    - 규칙이름: 사용자 정의
    - 내부 IP주소: 3.7에서 확인한 라즈베리파이의 IP주소 입력
    - 프로토콜: TCP
    - 외부 포트: 사용자 정의
    - 내부 포트: 22(ssh), 443(https), 80(http) 등
      - [http의 기본 포트가 80, https의 기본 포트가 443인 이유는 무엇일까?](https://johngrib.github.io/wiki/why-http-80-https-443/) 

### 4.3. DDNS 설정

### ipTIME 원격 접속
    - 고급설정 - 보안기능 - 공유기 접속/보안관리
    - 원격 관리 포트 사용 체크 및 포트번호 설정
    - 1nteger.iptime.org:포트번호로 원격에서 iptime 접근 가능

## 5. Nginx 설치

### 5.1 한글 폰트 및 입력기 설치
- `$ sudo apt-get install fonts-unfonts-core`
- `$ sudo apt-get install ibus-hangul`

### 5.2 Nginx 문자셋 설정
- `$ sudo vim /etc/nginx/nginx.conf`
- Basic Settings 하단에 아래 내용 추가
  ~~~
  charset   utf-8;
  server {
    charset utf-8;
  }
  ~~~
  
### 3.11 iptime 설정


### 3.9. 온도 측정용 쉘 스크립트 작성
1. 쉘 스크립트 파일 생성
    - `$ sudo vim thermometer.sh`
2. 쉘 스크립트 작성
    ~~~sh
    #!/bin/bash
    cpuTemp=$(cat /sys/class/thermal/thermal_zone0/temp)
    cpuTemp1=$(($cpuTemp/1000))
    cpuTemp2=$(($cpuTemp/100))
    cpuTempM=$(($cpuTemp2 % $cpuTemp1))
    
    gpuTemp=$(/opt/vc/bin/vcgencmd measure_temp)
    gpuTemp=${gpuTemp}
    gpuTemp=${gpuTemp//temp=/}
    
    echo $(date "+%Y-%m-%d %H:%M")
    echo Temperature CPU : $cpuTemp1"."$cpuTempM"'C, GPU : "$gpuTemp
    ~~~
    - 참고 : [라즈베리파이 시스템 온도(발열) 확인](https://geeksvoyage.com/raspberry%20pi/get-temp-for-pi/)


## Reference
- https://brunch.co.kr/@topasvga/701
- https://geeksvoyage.com/
- https://blog.jeuke.com/49?category=818287
- https://seolin.tistory.com/99
- https://blog.rajephon.dev/2019/07/12/setup-raspberrypi-home-server/
- https://www.youtube.com/watch?v=8grooZWbH9Y
- https://withcoding.com/45
- https://pimylifeup.com/how-to-shutdown-a-raspberry-pi/
- https://dullwolf.tistory.com/17
- http://progtrend.blogspot.com/2017/08/raspberry-pi-wifi.html
- https://webnautes.tistory.com/903
- http://egloos.zum.com/lunar456th/v/6397975