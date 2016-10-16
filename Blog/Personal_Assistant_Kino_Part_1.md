# Personal Assistant Kino Part 1.

## Introduction

 최근에 Bot에 대한 글도 많이 읽고.. [조그만 미니 프로젝트](http://humanbrain.in/2016/08/21/slack_bot_for_salady/)로 Bot도 개발해보면서, 그 동안 마음 속에 계속해서 자리잡고 있던! 제가 가장 만들어보고 싶던 개인 프로젝트를 진행할 때가 되었구나 생각이 되었습니다. 
 
 그 프로젝트는 바로.. 개인용 비서 Bot을 만드는 프로젝트입니다. 다른 누구도 아닌 오직 나 자신만을 위한 개인 비서를 만들어보자라는 생각은 예전부터 했었고, 그것에 대비하여 [Toggl](https://www.toggl.com/) 을 통해서 내가 보내는 시간을 기록하고, [RescueTime](https://www.rescuetime.com/) 을 통해서는 어떤 프로그램을 사용하고, 생산성은 어떠한지 기록하고 있었습니다. 그 외에는 [Pebble Time](https://www.pebble.com/)을 통해서 걸음걸이와 수면시간 또한 Tracking이 되고 있었습니다. 그리고 [Todoist](https://ko.todoist.com/) 를 통해서는 일정관리를 하고 있었습니다. 이렇듯 저에 대한 정보들은 수치화된 데이터가 되어 여기저기 쌓이고 있던 것이죠.
 
 그래서 이렇게 쌓은 나에 대한 Data를 바탕으로.. 나를 아는 Bot을 만들고 싶다는 생각을 해왔습니다. 물론 위의 서비스들은 대부분 API를 제공하고 있는 상황입니다.
 
 <p align="center">
    <img src="https://lh3.ggpht.com/CbreFsJPetU3O6cZ20avBKTkDuks4eOZZwPeLaq8X8tWw-vE6YIlnZh2dx4tluPWAQ=w300" alt="Project Introduction" width=100>
    <img src="https://lh5.ggpht.com/tCojEbNBb3EP9mS6BCoyhmLcJIP9yNWy_k5xyDbEheAjlTAHdN4w4G0X2BZnzxo_rg=w300" width=100>
    <img src="http://i-cdn.phonearena.com/images/articles/188184-gallery/New-icon-for-the-iOS-and-Android-Pebble-Time-Watch-app.jpg" width=100>
    <img src="https://d1x0mwiac2rqwt.cloudfront.net/bab0a0c4b1c3135a24bd0518417b66e3/as/logo_todoist_schema.png" width=100 style="margin-left: 10px">
</p>

### Data 수집 정리
1. [Toggl](https://www.toggl.com/): Best time tracking system for a small business. A simple online timer with a powerful timesheet calculator. Syncs with iOS & Android app.
2. [RescueTime](https://www.rescuetime.com/): A personal analytics service that shows you how you spend your time and provides tools to help you be more productive.
3. [Pebble Time](https://www.pebble.com/): 걸음걸이와 수면시간을 Tracking 함. Data는 스마트워치 안에 기록되는 시스템으로 보임.
4. [Todoist](https://ko.todoist.com/): 수백만명이 신뢰하는, Todoist는 최고의 온라인 작업 관리 앱 및 할일 목록입니다.

## Bot Platform
 
 Bot을 개발하기 이전에 Data 수집에 대한 셋팅은 위와 같이 맞춰놓고, 다음으로 무엇으로 Bot 만들 것인가.. 고민을 해보았습니다. 최근에 [Telegram](https://www.telegram.org/), [Facebook Messenger](https://www.messenger.com/), [Line](https://line.me/ko/
) 등.. 많은 Messaing App 기업들이 Bot API를 공개하고 있지만, 개인용으로 사용하기에는 조금 부적합한 면들이 있었습니다.
 
 그렇게 알아보던 중, 제가 사용하고 있는 개인용 Slack이 눈에 들어왔습니다. [IFTTT](https://www.ifttt.com/)를 연동하여 여러 서비스들에 대한 정보를 Slack에 기록하고 있었고, 개인적인 메모를 하거나 정보를 볼 때 사용하고 있었습니다. 또한 Slack은 굉장히 간단하게 BOT_TOKEN 만 있어도 통신을 주고 받을 수 있고, Slack으로 [Salady Bot]((http://humanbrain.in/2016/08/21/slack_bot_for_salady/))을 만들면서 이미 개발경험을 가지고 있기에 더욱 적합하다고 생각을 했습니다.
 
 <p align="center">
    <img src="https://forger.typo3.org/images/slack.svg" width=400>
 </p>
 
### Slack의 선정이유
1. 개인용으로 만들어서 운영하는 Slack이 있다. (개인용으로 사용가능)
2. 다른 App들은 Webhook, Server등의 구성해야할 것들이 있지만, Slack은 Token만 있으면 통신이 가능.
3. 이미 Slack Bot을 개발해본 경험이 있다.

## Chat Bot

