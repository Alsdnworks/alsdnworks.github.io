---
title:강원대(삼척) 기숙사 일일자가검진 자동화(with 셀레니움)
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
print("\n강원대 기숙사 자가검진 Autorun")
#print("\n기숙사 ID입력")
ID=0#20*******
#print("\n기숙사 PW입력")
PSW=0#*****

#로그인
driver.implicitly_wait(3)
driver.get('https://dormitory.kangwon.ac.kr/bbs/h_login.php')
driver.find_element_by_name('login_id').send_keys(ID)
driver.find_element_by_name('login_pwd').send_keys(PSW)
driver.find_element_by_css_selector('#container > div.section_wrap > div.right_section > div.article > form > div > div > a').click()

#자가검진입력
driver.get('https://dormitory.kangwon.ac.kr/bbs/write.php?bo_table=COVID')
driver.implicitly_wait(3)
for i in  (range(5,10)):
    driver.find_element_by_xpath(f'/html/body/section/form/div[1]/table/tbody/tr[{i}]/td/div/table/tbody/tr[2]/td/input[1]').click()
driver.implicitly_wait(3)
driver.find_element_by_css_selector('#btn_submit').click()
driver.implicitly_wait(300)
sleep(1) 

#스크린샷저장후 종료
driver.save_screenshot("result.png")
print("\n 작업을 잘 수행했습니다. 설치경로에 스크린샷을 저장하였습니다.")
~~~