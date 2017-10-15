# Personal Assistant Kino Part 3 - T3.

Kino 프로젝트는 QS를 통해서 자신에 대해서 알고, 불필요한 일들을 자동화시키고 삶의 질을 증진시키기 위한 프로젝트 입니다.

![images](../images/quantified_self_logo_2x.gif)

출처 : http://quantifiedself.com/

저번 편의 [Skill & Scheduler](https://github.com/DongjunLee/BeAwesomeToday/blob/master/Blog/Personal_Assistant_Kino_Part_2_Skill_and_Scheduler.md)까지는 Kino를 내가 원하는 대로 돌아가도록 준비하는 과정이였습니다. 이제 이 프로젝트의 목표인 Quantified Self를 다루려고 합니다. 나 자신에 대한 데이터를 손쉽게 모으고, 차트를 보며 피드백을 나 자신에게 주고, 이를 통해 삶의 질을 증진시키는 과정을 말이지요.


## T3 = Task Master

오늘 다루려는 이야기는 Task에 관한 이야기입니다. 저는 [Todoist](https://ko.todoist.com/)를 굉장히 애용하고 있습니다. Premium 기능으로 업그레이드해서 사용할 정도로 말이죠. 모바일, 데스크탑 전부 사용할 수 있는 app이 있어서 언제 어디서든 간편하게 To do list를 관리할 수 있었습니다. 여기서 더 나아가 나는 작업들을 진행할 때, 시간이 얼마나 걸리는지.. 그리고 이 작업에 대해서는 얼마나 집중을 했는지, 하루의 시간을 어떻게 보냈는지 알고 싶었습니다. 

제가 원하는 것들을 충족하기 위해서는 Todoist를 사용하는 것만으로는 부족했습니다. 그래서 필요한 서비스들을 찾아보게 되었습니다. 시간 측정에는 [Toggl](https://www.toggl.com/)이 가장 잘 만들어진 서비스였고, 작업을 시작하고, 끝내는 것을 가장 쉽게 다룰 수 있는 것은 [Trello](https://trello.com)의 칸반 보드(팀으로 일하면서 Trello의 칸반보드를 사용하고 있었습니다)를 통해서 Task, Doing, Done 의 리스트로 하는 것이라는 결론을 낼 수 있었습니다. 

그렇게해서 만들어진 것이 **T3**

즉, T3 = Todoist + Toggl + Trello 를 통한 Task 간편 관리 기능입니다.  
아래는 T3에 사용되는 **Skill**들 입니다.

### Todoist

- 🌆 today_briefing : Todoist에 등록된 Task들을 브리핑합니다.
- 📃 todoist_remain : 남은 작업들에 대해서 안내합니다.

### Toggl

- ⌚️ toggl_timer : Toggl Timer 시작 혹은 정지.
- 🔔 toggl_checker : 30분 마다 시간 체크. (100분 이상 작업 시, 휴식 추천)
- 📊 toggl_report : Toggl task 리포트.

### Trello

- 📋 kanban_init : Trello 보드를 초기화 합니다.
- 📋 kanban_sync : Todoist의 Task들과 Trello 보드의 싱크를 맞춥니다.

### Question

- ✍️ attention_question : 작업 후, 집중도 물어보기 (100점 만점)
- ✍️ attention_report : 집중도 리포트


그러면 저의 하루를 예시로 T3가 어떻게 돌아가는지 보시죠 :D


## Scenario

해가 뜨고.. 아침이 됩니다. 알람이 울리고, 일어나서 Slack에 접속하면 Kino가 아침 인사를 건냅니다.

![images](../images/ko/kino-good-morning.png)

이떄, 하루의 날씨를 알려주고 Trello 보드를 초기화 + Todoist와 싱크(kanban_init + kanban_sync)를 맞춥니다.

![images](../images/ko/kino-kanban-board.png)

오늘의 날씨를 확인하고, 출근할 준비를 하다가.. 8시가 됩니다.  
8시에는 Kino가 하루에 어떤 일들이 있는지 브리핑을 해줍니다.  

![images](../images/ko/kino-today-briefing.png)

아침시간도 허투루 보낼 수 없죠. 지하철에서 책을 읽을 준비를 합니다.  
책을 읽기 전에 Trello를 키고, Task 리스트에 있는 Book 카드를 Doing으로 옮깁니다.  
Kino가 타이머 시작을 알려주네요!

한시간 정도 지나니, 환승할 역에 도착해가네요.  
다시 Trello를 키고 Doing에 있던 작업을 Done으로 옮깁니다.
Kino가 집중도를 물어보네요!

![images](../images/ko/kino-toggle-start-and.png)

많이 졸면서 책을 봤던지라.. 82점을 주었어요 ㅎㅎ  
Kino가 Todoist 에 있는 작업도 완료처리를 해주었네요 기특한 녀석.

그렇게 회사에 도착하고, 회사에서도 작업을 하면서 Trello의 카드들을 옮겨주며 집중해서 일을 착착 진행합니다. 

오후에 너무 일에 집중했더니.. 1시간 반은 넘게 계속해서 일하고 있었네요.  
아, Kino가 휴식을 추천하네요. 잠시 쉬어야겠습니다.

![images](../images/ko/kino-timer-check.png)

...

어느덧 저녁 7시 퇴근할 시간이네요. 집에 도착해서 하루를 마무리하고 있던 중..  
저녁 10시에 키노가 남아있던 일들을 알려줍니다. 아, 까먹고 있던 작업이 있었네요.

![images](../images/ko/kino-todoist-remain.png)

그렇게 11시가 되고.. Kino가 하루 마무리를 해주네요!  

![images](../images/ko/kino-today-summary.png)

![images](../images/kino-toggl-report.png)


Todoist에 등록되어 있는 일들도 거의 처리했고, 집중도 잘 했습니다! 후후  
차트를 보니까 하루를 알차게 잘 보냈네요! 내일을 Deep Learning 실습으로 프로젝트를 구현할 생각을 하며, Todoist에 Deep Learning 프로젝트 Task들을 추가합니다. 내일도 화이팅!


---


이상이 제가 하루의 Task를 관리하는 시나리오입니다.   
제가 하는 것이란 Trello의 카드를 옮기는 것 뿐이에요. 참 간단하죠?
  
Quantified Self에서 가장 중요한 것은 데이터를 손쉽게 모을 수 있어야하고, 한눈에 볼 수 있는 차트를 제공함으로서 간편하게 자기자신에게 피드백을 줄 수 있어야 한다는 것 입니다. 그런 의미로 T3는 굉장히 간편하고 유용한 기능이죠! 그리고 Task 관련 데이터들을 Toggl에 쌓여있으므로, 언제든 데이터를 받을 수 있고 더 복잡한 분석 또한 할 수 있을 것 입니다.

마지막으로 개발적인 측면에 대해서 간략하게 다루고 마무리하려고 합니다.


## Develop

Skill들을 Python으로 wrapping 되어있는 package 들을 사용하면 간편하게 내가 원하는 커스텀 스킬들을 만들 수 있습니다. 각각 서비스에 필요한 TOKEN, ACCESS_KEY 등을 준비하고 연결하면 손쉽게 끝낼 수 있습니다. 

그 외에 작업이 필요한 부분은 Webhook입니다. IFTTT에서도 Trello를 연결해서 사용할 수 있지만, 기본적으로 IFTTT는 실시간이 아닙니다. 하지만 Trello로 Task들을 관리하려면 실시간으로 반응을 해야합니다. 작업을 시작한다고 Doing에 올린지 10분이나 지나서 Toggl Timer가 작동하는 것은 너무나도 불편하니까요.

Trello에서는 Webhook를 추가할 수 있도록 지원하고 있습니다. 이 Webhook을 처리하려면 callback을 처리하는 간단한 서버가 있어야 합니다. 이 callback을 처리하기 위해서 Server를 빌리는 것은 너무 아깝습니다. 이럴때는 [Serverless Framework](https://serverless.com/)를 사용해서 간단하게 처리할 수 있습니다. AWS에 대한 간략한 설정을 마치고, callback에 대한 함수를 정의한다음 배포를 하면 AWS API Gateway + Lambda가 자동설정 되는 것을 보실 수 있습니다.

```python
def kanban_webhook(event, context):
    input_body = json.loads(event['body'])
    print(event['body'])

    action = input_body["action"]
    action_type = action["type"]

    if action_type == "createCard":
        list_name, card_name = get_create_card(action["data"])
    elif action_type == "updateCard":
        list_name, card_name = get_update_card(action["data"])

    kanban_list = ["DOING", "BREAK", "DONE"]
    if list_name in kanban_list:
        payload = make_payload(action=list_name, msg=card_name)
        r = send_to_kino({"text": payload})
    ...
```

[kino-webhook](https://github.com/DongjunLee/kino-bot/tree/master/kino-webhook) 여기에서 **kanban_webhook**이 구현되어 있습니다.

이제 Webhook을 다 정의했으니, 이 Webhook을 Kino가 처리하면 모든 문제는 끝이 납니다! 아래는 카드 이동에 따라서 Skill들의 연결을 정리해놓은 것입니다.

```python
   def KANBAN_handle(self, event):
        toggl_manager = TogglManager()

        action = event['action']
        description = event['msg']
        if action.endswith("DOING"):
            toggl_manager.timer(
                description=description,
                doing=True,
                done=False)
        elif action.endswith("BREAK"):
            toggl_manager.timer(doing=False, done=False)
        elif action.endswith("DONE"):
            toggl_manager.timer(doing=False, done=True) ## Todoist 연결되어 있음
```


- Doing : Toggl Timer 시작
- Done : Toggl Timer 정지 & Todoist 작업 완료
- Break : Toggl Timer 정지 


## Conclusion

이번 T3에서는 여러가지 서비스들과 Kino를 전부 연결해서 작업을 간편하게 관리하고, 차트를 제공하는 T3 기능에 대해서 살펴보았습니다. 이렇게 하루하루 Task에 대한 데이터들을 모아서 내가 어떤 작업에 잘 집중하는지, 어느 시간대에 집중을 잘 하는지.. 이런 여러가지 분석을 할 수 있을 것 입니다. 저도 데이터를 모으는 중이라, 나중에 꼭 분석하려고 합니다. :D


모든 코드는 [여기서](https://github.com/DongjunLee/kino-bot) 확인하실 수 있습니다.  
Kino를 더욱 똑똑하게 만들도록 도와주시는 분들은 언제든 환영입니다^^

다음에는 최신 Feed를 바로바로 알려주고, 자동으로 모은 데이터를 통해서 분류까지 알아서 하는 **Feed NC** 기능에 대해서 알아보겠습니다.

