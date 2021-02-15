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



### 데이터베이스는 어떨까

1. 학생 정보 (식별번호, 나이, 이름, 성별, 전화번호, 아이디, 비밀번호) : tutee (tutee_id, age, name, gender, phone_number, user_id, user_password)

   기본적인 개인정보는 과외를 진행하기 위해서 필요합니다.

2. 선생님 정보 (식별번호, 나이, 이름, 성별, 전화번호, 아이디, 비밀번호) : tutor (tutor_id, age, name, gender, phone_number, user_id, user_password)

   학생뿐만 아니라 선생님의 개인정보도 어느정도는 필요합니다.

3. 학생이 배우고 싶은 것 (누가 썼는지, 배우고 싶은 것에 대한 설명, 분류, 글쓴 날짜) : request (tutee_id, requestment, category, date)

   댓글 없는 게시판처럼 만들면 쉬울 것 같습니다. category 칼럼의 경우 국어, 수학처럼 이런 카테고리를 직접적 저장하기보다 국어는 0, 수학은 1 이런식으로 바꾸어 쓸 생각입니다. 이게 속도가 조금더 빠르지 않을까요? 일단 나중에 실험해봐야겠어요.

4. 선생님의 자기어필 (누가 썼는지, 어필할 내용, 분류, 자격증사진/재학 증명서 등등, 글쓴 날짜) : introduce (tutor_id, introduction, category, img, date)

   위의 request 테이블과의 차이점은 img의 존재 유무라고 할 수 있습니다. img칼럼에 이미지 경로를 저장할 계획입니다.

5. 선생님 리뷰 (누가 썼는지, 어떤 선생님, 내용, 별점, 날짜) : review (tutee_id, tutor_id, review, score, date)

   누가 리뷰를 썼는지 사실 정확하게 공개할 생각은 아닙니다. 그럼에도 tutee_id칼럼을 만든 이유는 동일인이 리뷰를 여러번 써서 조작하는 것을 막기 위해서 입니다.

6. 학생과 선생님과의 과외 체결 장부(?) (학생은 누구?, 선생님은 누구?, 언제) log (tutee_id, tutor_id, date)

   학생과 선생님의 과외가 성사된 기록을 남겨서 리뷰를 쓸때 제대로 쓰는 것인지 확인하려고 합니다. 또한 선생님을 소개할때 "와! 이만큼 과외를 했어요"하고 알려줄겁니다.

### 채팅기능?

인터넷에 찾아보니 Socket.io를 활용하면 실시간 채팅을 구현 할 수 있는 것 같습니다. 써본적은 없지만...

# 데이터 베이스 만들기

위에서 정리한 내용을 바탕으로 데이터베이스를 만들었습니다.

```sql
CREATE TABLE `tutee` (
  `tutee_id` INT NOT NULL,
  `age` INT NULL,
  `name` VARCHAR(45) NULL,
  `gender` TINYINT(1) NULL,
  `phone_number` INT NULL,
  `user_id` VARCHAR(45) NULL,
  `user_password` VARCHAR(45) NULL,
  PRIMARY KEY (`tutee_id`));
```

```sql
CREATE TABLE `tutor` (
  `tutor_id` INT NOT NULL,
  `age` INT NULL,
  `name` VARCHAR(45) NULL,
  `gender` TINYINT(1) NULL,
  `phone_number` INT NULL,
  `user_id` VARCHAR(45) NULL,
  `user_password` VARCHAR(45) NULL,
  PRIMARY KEY (`tutor_id`));
```

```sql
CREATE TABLE `request` (
  `request_id` INT NOT NULL,
  `tutee_id` INT NULL,
  `requestment` TEXT NULL,
  `category` INT NULL,
  `date` DATETIME NULL,
  PRIMARY KEY (`request_id`));
```

```sql
CREATE TABLE `introduce` (
  `introduce_id` INT NOT NULL,
  `tutor_id` INT NULL,
  `introduction` TEXT NULL,
  `category` INT NULL,
  `date` DATETIME NULL,
  `img` VARCHAR(255) NULL,
  PRIMARY KEY (`introduce_id`));
```

```sql
CREATE TABLE `review` (
  `review_id` INT NOT NULL,
  `tutee_id` INT NULL,
  `tutor_id` INT NULL,
  `review` TEXT NULL,
  `score` TINYINT(2) NULL,
  `date` DATETIME NULL,
  PRIMARY KEY (`review_id`));
```

```sql
CREATE TABLE `log` (
  `log_id` INT NOT NULL,
  `tutee_id` INT NULL,
  `tutor_id` INT NULL,
  `date` DATETIME NULL,
  PRIMARY KEY (`log_id`));
```
테이블 생성
```sql
ALTER TABLE request
ADD FOREIGN KEY (tutee_id) REFERENCES tutee(tutee_id);
ALTER TABLE introduce
ADD FOREIGN KEY (tutor_id) REFERENCES tutor(tutor_id);
ALTER TABLE review
ADD FOREIGN KEY (tutee_id) REFERENCES tutee(tutee_id);
ALTER TABLE review
ADD FOREIGN KEY (tutor_id) REFERENCES tutor(tutor_id);
ALTER TABLE log
ADD FOREIGN KEY (tutee_id) REFERENCES tutee(tutee_id);
ALTER TABLE log
ADD FOREIGN KEY (tutor_id) REFERENCES tutor(tutor_id);
```
외래키 설정

# 구체적인 디자인
