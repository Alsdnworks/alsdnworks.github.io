---
title:기숙사 일일자가검진 자동화 with 셀레니움
categories:
  - python
tags:
  - bot
  - selenium
toc: false
classes: wide
---

시험기간에 나를 귀찮게 만드는 기숙사일일검진을 자동화시켰다. <br>
ID와 PSW는 자신의것으로 입력하면된다.<br> 
headless옵션의 주석을 삭제하면 실행과정을 직접 확인 가능하다.<br> 
headless로 사용할경우 제대로 제출했는지 확인하기 번거로워<br>
셀레니움이 제공하는 스크린샷 기능을 사용해 정상 제출 여부를 확인가능하도록하였다.<br>
이 자동화기능은 삼척캠퍼스 3개 생활관에서 사용 가능하다.<br>

셀레니움을사용하기위해서는 웹드라이버가 필요하며 크롬웹드라이버 기준으로 설명하자면 https://chromedriver.chromium.org/downloads 에서 최신 버전을 받아 사용하도록한다. 웹드라이버 다운후 셀레니움에 크롬 웹드라이버의 위치를 등록해준다. 웹드라이버 옵션은 `.add_argument('headless')`로 프로그램 구동중 화면에 크롬웹드라이버가 뜨지 않도록한다는 옵션이다.

이 자동화 기능에서는 셀레니움의 4가지 기능을 사용한다.

|기능|설명|
|-|-|
|get('*URL*')|URL로 페이지 이동|
|find_element_by_*type*.click()|엘리먼트 클릭|
|find_element_by_*type*.send_keys('*text*')|엘리먼트에 text입력|
|save_screenshot('*name*')|소스코드위치에 스크린샷저장|

dr.find_element_by_*type*으로 웹페이지의 요소를 찾기위해 크롬기준 `f12`개발자 도구의 최좌측최상단에 위치한 기능인 `검사할 페이지 요소 선택`을 활용한다. 이 기능을 활성화후 브라우저에 마우스를 올리면 해당 요소로 이동 가능하며 해당 html문에서 우클릭해 복사하여 소스를 복사 가능하다. 요소를 선택하는 방식으로는 셀렉터, xpath, JS경로 등 여러가지가있으므로 가장 편한 기능을 사용하면 될것같다.

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