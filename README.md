# RaspberryPi_3b-DHT11-Ubidots
라즈베리파이 온습도 센서 Ubidots(웹사이트) 연동 및 출력, 시각화

## Blog : https://ropering.tistory.com/67?category=860751
<br></br>
# 사용환경
- RaspberryPi 4
- DHT11 (GPIO 4번 포트)

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcIoYUD%2FbtqSmBHauLG%2FaK9KDiMapXTk97zeLRsKF1%2Fimg.png" width="300">

# 결과
- IoT플랫폼인 ubidots를 통해 간단하게 라즈베리파이의 온습도 데이터를 실시간으로 웹에서 출력 및 시각화 할 수 있었다

- 웹, 휴대폰과의 연동을 통해 많은 센서 데이터를 어디에서나 볼 수 있게 되었다 -> 다양한 작업에 응용 가능

# Ubitos 접속방법
<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbz8XrZ%2FbtqSdtQHph8%2FyippKziSBXmX8FZkVRKR91%2Fimg.png" width="700">
ubidots 로그인 페이지 → 회원가입
<br></br>

# 코드 (python)
``` python
import time
import requests
import math
import random

import Adafruit_DHT as dht

TOKEN = "xxxxxxxxx"  # Put your TOKEN here
DEVICE_LABEL = "machine"  # Put your device label here 
VARIABLE_LABEL_1 = "temperature"  # Put your first variable label here
VARIABLE_LABEL_2 = "humidity"  # Put your second variable label here

def build_payload(variable_1, variable_2):
    
    hum, temp = dht.read_retry(dht.DHT11, 4)

    value_1 = temp
    value_2 = hum

    payload = {variable_1: value_1,
               variable_2: value_2}
    return payload

def post_request(payload):
    # Creates the headers for the HTTP requests
    url = "http://industrial.api.ubidots.com"
    url = "{}/api/v1.6/devices/{}".format(url, DEVICE_LABEL)
    headers = {"X-Auth-Token": TOKEN, "Content-Type": "application/json"}

    # Makes the HTTP requests
    status = 400
    attempts = 0
    while status >= 400 and attempts <= 5:
        req = requests.post(url=url, headers=headers, json=payload)
        status = req.status_code
        attempts += 1
        time.sleep(1)

    # Processes results
    if status >= 400:
        print("[ERROR] Could not send data after 5 attempts, please check \
            your token credentials and internet connection")
        return False

    print("[INFO] request made properly, your device is updated")
    return True


def main():
    payload = build_payload(
        VARIABLE_LABEL_1, VARIABLE_LABEL_2)

    print("[INFO] Attemping to send data")
    post_request(payload)
    print("[INFO] finished")


if __name__ == '__main__':
    while (True):
        main()
        time.sleep(1)

```
해당 코드에 TOKEN 값만 추가하고 라즈베리파이 환경에서 실행하면 자동으로 ubidots에 장치가 추가된다

![image description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fdr83UA%2FbtqR9lTdVGO%2FG4ljPYu7eS2EG2YCg2ZlF0%2Fimg.png)가장 중요한 자신의 TOKEN 값을 TOKEN변수에 넣어야 한다

- TOKEN : 아래의 자신의 TOKEN값을 찾는 방법을 참고하세요!

- DEVICE_LABEL : 자신이 원하는 장치명 입력 (아무거나 가능!, 필자는 machine이라고 이름 지었다)

- VARIABLE_LABEL_1 : 자신이 원하는 데이터명 입력 (아무거나 가능!, 필자는 temperature이라고 이름 지었다)

- VARIABLE_LABEL_2 : 자신이 원하는 데이터명 입력 (아무거나 가능!, 필자는 humidity이라고 이름 지었다)
<br></br>
# 자신의 TOKEN값 찾는 방법
![image description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbRPjmx%2FbtqR6vhBuSj%2FwHWmiNFngKxm9BBPaqchz1%2Fimg.png)오른쪽 상단 사람모양 클릭 -> API Credentials 클릭
<br></br>
![image description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fs9YeZ%2FbtqSpG2u044%2FWGQKnD34nbYsZKe2r9Mm1K%2Fimg.png)네모박스안의 Tokens 값을 TOKEN 변수에 넣으면 된다 (문자열 형식으로! ex "xxxxxxx" )
<br></br>
# 실행 결과
![image description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F7PYc2%2FbtqSdu27OhM%2FMuTkyWNwDBuAI8yKY5suL0%2Fimg.png)라즈베리파이 Thonny에서 코드를 실행중인 모습
<br></br>
![image description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbkuxuI%2FbtqR9nwMyoU%2FGF8hxMRNFp3E5eHvlF6TgK%2Fimg.png)코드를 실행하면, machine 장치가 추가된 것을 볼 수 있다!
<br></br>
![image description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FkBSBr%2FbtqSpHtAx6a%2FISxu0yh4CFq2ONB1Wkeeyk%2Fimg.png)machine 장치 내에서 습도값, 온도값을 볼 수 있다 (습도 60% / 온도 20도)
<br></br>
![image description](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcKs47E%2FbtqR3SYm500%2FMkbzU4SymLQhq82Ot8xe01%2Fimg.png)온도, 습도 데이터를 위젯화 한 모습
<br></br>
# 참고자료
fishpoint.tistory.com/5212

Ubidots REST API 레퍼런스 문서 : ubidots.com/docs/sw/#operation/List%20Datasource%20Variables

Connect the Raspberry Pi with Ubidots 문서 : help.ubidots.com/en/articles/513309-connect-the-raspberry-pi-with-ubidots
