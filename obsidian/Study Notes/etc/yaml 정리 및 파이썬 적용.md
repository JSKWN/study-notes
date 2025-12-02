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
2.  test_string: Hello     # 문자열에 따옴표는 선택사항
3. 키(Key) 이름에 공백이 들어가도 상관없음
	- my name is: ‘jskwon’
4. 이스케이프 문자(\n 등) 사용시 주의
	- my name is: "js\nkwon"     # 큰따옴표("): \n을 줄바꿈으로 해석
	- my name is: 'js\nkwon'      # 작은따옴표('): \n을 문자 그대로(\와 n) 취급
	- my name is: js\nkwon       # 따옴표 없음: 문자 그대로 취급
5. 값 부분에 콜론(:)이 또 사용되면 에러 발생
	-  :, #, *, &, [], {} 등의 기호가 값에 포함된다면 따옴표(" ")로 감싸는 것이 안전함
	- (예: colon: "string:")
6. 긴 문장을 표현할 때는 >(Folded), |(Literal) 사용
	- > : 중간의 줄바꿈을 공백(Space)으로 바꿈 (단, 빈 줄은 줄바꿈으로 인식 → 문단 구분 가능)
	```
	lorem ipsum1: >
	    Lorem ipsum dolor sit amet, consectetur adipiscing elit.
	    Quisque dictum lorem a tempor feugiat. Cras sit amet semper mi.
	
	    Phasellus dignissim lobortis nisl, id varius metus.
    ```
	- \| : 보이는 그대로 줄바꿈(\n)과 빈 줄을 모두 유지

	```
	lorem ipsum2: |
	    Lorem ipsum dolor sit amet, consectetur adipiscing elit.
	    Quisque dictum lorem a tempor feugiat. Cras sit amet semper mi.
	
	    Phasellus dignissim lobortis nisl, id varius metus.
	```

7.  배열 (Array) → “순서가 있는 목록”
	- 하이픈(-)을 이용해서 목록의 한 요소를 표시
	- json 스타일도 인식 (대괄호’[]’ 안에 쉼표로 나열)
	- 
		```Yaml
	   array:
	    - apple    # 1번: 사과
	    - banana   # 2번: 바나나
	    - 12345    # 3번: 숫자
	    - box_type: fruit     # 4번: 'box_type' 키와 'fruit' 값을 가진 객체 (딕셔너리 { "box_type": "fruit" })
	   ```


8. 객체 (Key-Value): “이름표와 내용물”
	- 콜론( : )을 사용하여 이름과 값을 연결함
	- 들여쓰기(indentation)를 이용해 포함 관계 표현 (하이픈 사용 X)
	- json 스타일도 인식 (중괄호’{ }’ 를 사용하는 방식)
		```
			object:
				name: wiki # 이름 -> wiki
				type: dictionary # 타입 -> 사전
				primary color: # 색상정보 -> 아래에 세부 정보 존재
					gradient: # 그라데이션 -> (하위항목존재)
						start: 0x... # 시작 색
						end: 0x... # 끝 색
		```


# 파이썬 사용시
---
YAML 변환 모듈은 PyYAML을 사용

- YAML 파일을 파이썬으로 읽어오면
	- YAML 객체는 파이썬의 딕셔너리(dict)로 변환
	- YAML 배열은 파이썬의 리스트(list)로 변환

- 예시로 아래와 같은 config.yaml 파일을 불러와 사용한다면
  ```
  # config.yaml 파일 내용
	database:
	  host: "localhost"
	  port: 3306
	  
	users:
	  - name: "김철수"
	    role: "admin"
	  - name: "이영희"
	    role: "user"
	locations:
	  - California
	  - Seoul
	  - Tokyo
	```
1. with open(file_path, ‘r’, encoding=’utf-8) as f: 구문 사용
	- 파일 객체인 f를 yaml.safe_load(f)로 사용
	- config = yaml.safe_load(f)
2. 그 이후, config 객체를 키(Key)로 우선 접근 후 사용가능
	- 객체로만 이루어진 경우
		- config\[‘database’]\[‘host’] # “localhost”
	- 단순 배열일 경우
		- config\[‘locations’] # \[‘California’, ‘Seoul’, ‘Tokyo’]
		- 이후 인덱스로 접근 가능
	- 객체가 들어있는 배열일 경우
		- config\[‘users’] → 리스트 형태로 나옴
			- \[{‘name’: “김철수, ‘role’: ‘admin}, {‘name‘: “이영희, ‘role’: ‘user}]
		- config\[‘users’]\[0] → 한개의 딕셔너리 반환
			- {‘name’: “김철수, ‘role’: ‘admin}
		- config\[‘users’]\[0]\[‘name’] → ‘김철수’