---
layout: post
title: "미궁 사이트 만들어 보기"
categories: programming
---

# 시작

## 미궁이란?

웹(HTML, Javascript, ...)을 기반으로 한 퍼즐게임입니다. 보통 수수께끼를 풀어 다음 스테이지로 넘어가는 것을 목적으로 합니다.

## 기획

### 프로젝트 명

미노타우로스 : 미궁게임 (Minotaur : Web riddle game)

### 개발환경

OS: Windows 10

DB: Mysql

Development Tool: Visual Studio Code

Tech stack: Github, Node.js, Jquery, Ajax, HTML, Javascript, CSS3

### 기획 목적

기존의 한국에 존재하는 미궁사이트는 미궁 제작기(editor)에는 약간 아쉬운 부분이 있습니다. 미궁 제작자가 약간의 웹 프로그래밍 지식을 보유해야 제작기를 완전히 사용 할 수 있을 정도로 추상화가 제대로 이루어 지지 않았습니다. 저는 이를 보완하기 위해 실제 게임의 구현 방식, 제작기에 초점을 두어 제작하려 합니다.

### 프로젝트 정의

미궁 게임을 개인이 제작하고 플레이 할 수 있어야 합니다. 각각의 미궁에 대한 힌트와 기타 정보를 제공하는 커뮤니티가 존재해야 합니다.

로그인, 회원가입, 개인정보 수정 및 열람, 미궁제작, 미궁 플레이, 미궁 플레이 기록, 공지사항, 자유게시판, 각 미궁별 게시판(힌트게시판, 공지게시판)

![구조도](https://haljjagi.github.io/assets/img/labyrinth/1.png)

