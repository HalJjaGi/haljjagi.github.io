---
layout: post
title: "과외사이트 만들어보기"
categories: programming
---

# 시작하기 전에

Node.js, mysql, 기타 등등으로 '김과외'같은 과외플랫폼을 만들어 갈겁니다.

이곳에는 제가 제작을 하면서 드는 생각들을 의식의 흐름대로 기록할겁니다. 따라서 내용이 뒤죽박죽일 수 있습니다.

과연 할짜기는 사이트를 만들어 낼 수 있을까요?

# 있어야 할 것들

### 필요한 기능

1. 학생이 원하는 과외 선생님 찾기

   과외 선생님 목록을 보여주고 관심이 가는 선생님에게 연락할 수 있게 한다면 편하겠죠?

2. 선생님이 학생 찾기

   오히려 학생이 '저는 이런이런 학생인데 가르쳐줄 선생님 계신가요?'하는 글을 올리면 선생님이 연락하는 기능.

   학생입장에서는 하루종일 선생님에게 연락 돌리는 것도 힘들꺼에요.

3. 과외비 결제

   카카오 API를 이용할 계획이지만 잘 해낼 수 있을까요.
   

이정도가 필요한 기능의 전부같습니다. 

### 사용자 흐름도(User Flow Diagram)

draw.io 를 이용해 사용자 흐름도를 그렸습니다.

![사용자 흐름도](https://haljjagi.github.io/assets/img/tutor-platform/1.png)

### 데이터베이스는 어떨까

1. 사용자 정보 (식별번호, 나이, 이름, 성별, 전화번호, 아이디, 비밀번호) : account (account_id, age, name, gender, phone_number, user_id, user_password)

   기본적인 개인정보는 과외를 진행하기 위해서 필요합니다.

3. 학생이 배우고 싶은 것 (누가 썼는지, 배우고 싶은 것에 대한 설명, 분류, 글쓴 날짜) : request (account_id, requestment, category, date)

   댓글 없는 게시판처럼 만들면 쉬울 것 같습니다. category 칼럼의 경우 국어, 수학처럼 이런 카테고리를 직접적 저장하기보다 국어는 0, 수학은 1 이런식으로 바꾸어 쓸 생각입니다. 이게 속도가 조금더 빠르지 않을까요? 일단 나중에 실험해봐야겠어요.

4. 선생님의 자기어필 (누가 썼는지, 어필할 내용, 분류, 자격증사진/재학 증명서 등등, 글쓴 날짜) : introduce (account_id, introduction, category, img, date)

   위의 request 테이블과의 차이점은 img의 존재 유무라고 할 수 있습니다. img칼럼에 이미지 경로를 저장할 계획입니다.

5. 선생님 리뷰 (누가 썼는지, 어떤 선생님, 내용, 별점, 날짜) : review (account_id1, account_id2, review, score, date)

   누가 리뷰를 썼는지 사실 정확하게 공개할 생각은 아닙니다. 그럼에도 tutee_id칼럼을 만든 이유는 동일인이 리뷰를 여러번 써서 조작하는 것을 막기 위해서 입니다.

6. 학생과 선생님과의 과외 체결 장부(?) (학생은 누구?, 선생님은 누구?, 계약내용, 금액, 언제) log (account_id1, account_id2, contract_content, price, date)

   학생과 선생님의 과외가 성사된 기록을 남겨서 리뷰를 쓸때 제대로 쓰는 것인지 확인하려고 합니다. 또한 선생님을 소개할때 "와! 이만큼 과외를 했어요"하고 알려줄겁니다.

### 채팅기능?

인터넷에 찾아보니 Socket.io를 활용하면 실시간 채팅을 구현 할 수 있는 것 같습니다. 써본적은 없지만...

# 데이터 베이스 만들기

위에서 정리한 내용을 바탕으로 데이터베이스를 만들었습니다.

```mysql
CREATE TABLE `account` (
  `account_id` INT NOT NULL,
  `age` INT NULL,
  `name` VARCHAR(45) NULL,
  `gender` TINYINT(1) NULL,
  `phone_number` INT NULL,
  `user_id` VARCHAR(45) NULL,
  `user_password` VARCHAR(45) NULL,
  PRIMARY KEY (`account_id`));
```

```mysql
CREATE TABLE `request` (
  `request_id` INT NOT NULL,
  `tutee_id` INT NULL,
  `requestment` TEXT NULL,
  `category` INT NULL,
  `date` DATETIME NULL,
  PRIMARY KEY (`request_id`));
```

```mysql
CREATE TABLE `introduce` (
  `introduce_id` INT NOT NULL,
  `tutor_id` INT NULL,
  `introduction` TEXT NULL,
  `category` INT NULL,
  `date` DATETIME NULL,
  `img` VARCHAR(255) NULL,
  PRIMARY KEY (`introduce_id`));
```

```mysql
CREATE TABLE `review` (
  `review_id` INT NOT NULL,
  `tutee_id` INT NULL,
  `tutor_id` INT NULL,
  `review` TEXT NULL,
  `score` TINYINT(2) NULL,
  `date` DATETIME NULL,
  PRIMARY KEY (`review_id`));
```

```mysql
CREATE TABLE `log` (
  `log_id` INT NOT NULL,
  `tutee_id` INT NULL,
  `tutor_id` INT NULL,
  `contract_content` TEXT NULL,
  `price` INT NULL,  
  `date` DATETIME NULL,
  PRIMARY KEY (`log_id`));
```
테이블 생성
```mysql
ALTER TABLE request
ADD FOREIGN KEY (tutee_id) REFERENCES account(account_id);
ALTER TABLE introduce
ADD FOREIGN KEY (tutor_id) REFERENCES account(account_id);
ALTER TABLE review
ADD FOREIGN KEY (tutee_id) REFERENCES account(account_id);
ALTER TABLE review
ADD FOREIGN KEY (tutor_id) REFERENCES account(account_id);
ALTER TABLE log
ADD FOREIGN KEY (tutee_id) REFERENCES account(account_id);
ALTER TABLE log
ADD FOREIGN KEY (tutor_id) REFERENCES account(account_id);
```
외래키 설정

# 구체적인 디자인

### 지금 그리는게 맞나?

지금 느끼는 건데 디자인을 너무 늦게 그리는 것 같습니다. 구체적인 디자인을 그리면서 제가 실수한 부분들을 찾아낼텐데 벌써 데이터베이스 구조를 짜고 만들기까지해서......

앞으로는 이런 미리 디자인까지 만들어야겠습니다. ㅠㅠ

### 어떤 느낌?

페이스북이나 트위터 같은 SNS를 참고하여 디자인 하려고 합니다.

SNS에서 사람들이 올리는 글을 각각 선생님, 학생들이 올린 글이라고 생각해본다면, 제가 만들려는 서비스와 비슷한 구조를 가지고 있다고 생각합니다.

### 아니 이메일

로그인 페이지를 만들다 비밀번호 찾기기능을 위해서는 사용자의 이메일을 알고 있어야 한다는 것을 깨달았습니다.

```mysql
ALTER TABLE account ADD `email` VARCHAR(100) NULL;
```

### 아니 이건 뭐야

선생님, 학생 리스트를 보여주는 페이지를 만들다가 아쉬운 점을 발견했습니다. 

각각의 리스트를 보여줄 때 간략한 설명을 보여주면 사용자 입장에서 좀 더 편할 것이라는 생각이 들었습니다.

```mysql
ALTER TABLE introduce ADD `brief_introduction` TEXT NULL;
```

```mysql
ALTER TABLE request ADD `brief_request` TEXT NULL;
```

### 끝내며 드는 생각

드디어 디자인이 끝났습니다. 비록 퀄리티는 떨어지지만 아무튼 끝냈다는 것에 의의를 두려고 합니다.

중간에 채팅부분을 디자인 하면서 든 생각인데, 당근, 중고나라 등등을 보면 웹에서도 이용은 가능하지만 채팅같은 일부 기능은 앱에서만 사용 할 수 있도록 만든 경우가 있습니다. 저도 그런식으로 구현을 해야 할 것 같습니다. 막상 개발한다고 생각하니 채팅 내역을 보여준다든지, 채팅이 왔을 때 알림을 띄우는 것 등에 있어서 앱이 웹보다 더 개발하기 편하고 사용자 입장에서도 문제가 없는 것 같습니다. 

그래서 이제부터는 android studio를 이용해 안드로이드 앱까지 개발해보려고 합니다.(IOS는...)

# 웹 개발

