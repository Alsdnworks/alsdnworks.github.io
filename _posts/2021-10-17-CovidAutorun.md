---
title: 기숙사 일 일자가 검진 자동화 with 셀레니움
categories:
  - python
tags:
  - bot
  - selenium
toc: false
classes: wide
---

시험 기간에 나를 귀찮게 만드는 기숙사일 일 검진을 자동화시켰다. <br>
ID과 PSW는 자신의 것으로 입력하면 된다.<br> 
headless 옵션의 주석을 삭제하면 실행과정을 직접 확인할 수 있다.<br> 
headless로 사용할 경우 제대로 제출했는지 확인하기 번거로워<br>
셀레니움이 제공하는 스크린샷 기능을 사용해 정상 제출 여부를 확인 가능하도록 하였다.<br>
이 자동화 기능은 삼척캠퍼스 3개 생활관에서 사용할 수 있다.<br>

셀레니움을 사용하기 위해서는 웹 드라이버가 필요하며 크롬 웹 드라이버 기준으로 설명하자면 <https://chromedriver.chromium.org/downloads> <br> 에서 최신 버전을 받아 사용하도록 한다. 웹 드라이버 다운 후 셀레니움에 크롬 웹 드라이버의 위치를 등록해준다.<br> 웹 드라이버 옵션은 `.add_argument('headless')`로 프로그램 구동 중 화면에 크롬 웹 드라이버가 뜨지 않도록 한다는 옵션이다.

이 자동화 기능에서는 셀레니움의 5가지 기능을 사용한다.

|기능|설명|
|-|-|
|get('*URL*')|URL로 페이지 이동|
|find_element_by_*type*.click()|엘리먼트 클릭|
|find_element_by_*type*.send_keys('*text*')|엘리먼트에 text 입력|
|save_screenshot('*name*')|소스 코드 위치에 스크린숏저장|
|implicitly_wait(*int*)|로드될 때까지 기다리는 시간(초) 한 번의 실행으로 전체적용된다|

find_element_by_*type*으로 웹페이지의 요소를 찾기 위해 크롬 기준 `f12` 개발자 도구의 좌측 최상단에 위치한 기능인<br> `검사할 페이지 요소 선택`을 활용한다. 이 기능을 활성화 후 브라우저에 마우스를 올리면 해당 요소로 이동 가능하며<br> 해당 html 문에서 우클릭해 복사하여 소스를 복사할 수 있다.<br> 요소를 선택하는 방식으로는 선택 터, xpath, JS 경로 등 여러 가지가 있음으로 가장 편한 기능을 사용하면 될 것 같다.

![element](/assets/img/screeenshot520.png) 

- **src**

~~~python
#init
from selenium import webdriver
from time import sleep
options = webdriver.ChromeOptions()
#options.add_argument('headless')
options.add_argument('window-size=1920x1080')
options.add_experimental_option("excludeSwitches", ["enable-logging"])
driver = webdriver.Chrome(r'D:\PYTHON\KNUDormitoryAutoChk\chromedriver',options=options) 
driver.implicitly_wait(3)

print("\n강원대 기숙사 자가검진 Autorun")
#print("\n기숙사 ID입력")
ID=0#20*******
#print("\n기숙사 PW입력")
PSW=0#*****

#로그인
driver.get('https://dormitory.kangwon.ac.kr/bbs/h_login.php')
driver.find_element_by_name('login_id').send_keys(ID)
driver.find_element_by_name('login_pwd').send_keys(PSW)
driver.find_element_by_css_selector('#container > div.section_wrap > div.right_section > div.article > form > div > div > a').click()


#자가검진입력
driver.get('https://dormitory.kangwon.ac.kr/bbs/write.php?bo_table=COVID')
for i in  (range(5,10)):
    driver.find_element_by_xpath(f'/html/body/section/form/div[1]/table/tbody/tr[{i}]/td/div/table/tbody/tr[2]/td/input[1]').click()
driver.find_element_by_css_selector('#btn_submit').click()
driver.find
sleep(1) 

#스크린샷저장후 종료
driver.save_screenshot("result.png")
print("\n 작업을 잘 수행했습니다. 설치경로에 스크린샷을 저장하였습니다.")
~~~