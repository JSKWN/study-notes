---
title: yaml 정리 및 파이썬 적용
date: 2025-12-02
categories: []
tags: []
keywords: []
---
# YAML 내용 정리
---
1. \# yaml은 주석을 지원
2.  test_string: Hello # 문자열에 따옴표는 선택사항
3. 프로퍼티 이름에 공백이 들어가도 상관없음
	- my name is: ‘jskwon’
4. 따옴표를 쓰게 되면 \n을 줄바꿈으로 해석, 안쓰면 문자그대로 취급
	- my name is: ‘js\nkwon’
	- my name is: js\nkwon
5. 값 부분에 콜론(:)이 또 사용되면 에러 발생
	- :, #, \*, &, [], {} 등의 기호가 값에 포함된다면 따옴표(‘ ‘)로 감싸는게 좋음
	- colon: string:
6. 긴 문장을 표현할 떄는 >, | 을 사용
	- \> 를 사용하면 한줄로만 인식 (개행(\n) 및 빈 줄 (\n\n) 인식 x) 
	```
	lorem ipsum1: >
	    Lorem ipsum dolor sit amet, consectetur adipiscing elit.
	    Quisque dictum lorem a tempor feugiat. Cras sit amet semper mi.
	
	    Phasellus dignissim lobortis nisl, id varius metus.
    ```
	- \| 를 사용시 개행(\n) 및 빈줄(\n\n)을 모두 인식

	```
	lorem ipsum2: |
	    Lorem ipsum dolor sit amet, consectetur adipiscing elit.
	    Quisque dictum lorem a tempor feugiat. Cras sit amet semper mi.
	
	    Phasellus dignissim lobortis nisl, id varius metus.
	```

7.  배열 





# 파이썬 사용시

yaml 변환 모듈은 PyYAML을 사용