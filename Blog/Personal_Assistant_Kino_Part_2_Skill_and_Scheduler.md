# Personal Assistant Kino Part 2 - Skill & Scheduling.

Kino 프로젝트는 QS를 통해서 자신에 대해서 알고, 불필요한 일들을 자동화시키고 삶의 질을 증진시키기 위한 프로젝트 입니다.

![images](../images/quantified_self_logo_2x.gif)

출처 : http://quantifiedself.com/

## Skill & Scheduling

[Kino에 대해서 간단히 소개를 했던 1편]()에 이어서, 내부의 핵심이 되는 Skill과 Scheduler 기능에 대해서 이야기하고자 합니다. 이 두가지 기능은 다음과 같은 생각들을 하다가 나오게 되었습니다.

어떻게 하면 나에게 맞는 똑똑한 개인용 봇을 만들 수 있을까?

1. IFTTT 같은 서비스 처럼 내가 사용하는 서비스들을 자유롭게 Customizing 해서 사용할 수 있도록 해야겠다.  
2. 그렇다면 이렇게 만든 함수들을 Skill로 명명하고 관리해야겠다.
3. 내가 원하는 시간에 이 Skill이 돌아가도록 하면?

이 Skill과 Scheduler 를 조금 더 세부적으로 보면 이렇습니다. 외부 서비스의 API를 wrapping해서 custom한 function을 만든다. 그리고 crontab 기능을 이용해서 정해진 시간에 그 기능이 돌아가도록 한다.

이렇게 2가지 기능에 대한 세부적인 내용들이 나오고, 어떻게 구현하는 것이 좋을지 간단한 설계 후 작업을 진행하였습니다. 저는 이 kino 프로젝트를 진행하면서 한가지 중요하게 생각했던 부분이 있습니다. '**Python 3.6**을 이용해서 새롭게 나온 Feature들을 최대한 활용하자.' 그래서 구현에 대한 설명에도 새로운 Feature들이 포함되어 있습니다. 이 글을 읽으시는 독자 분들이 Python 3.6의 매력을 느낀다면 좋겠습니다.

### Skill

Skill은 계속해서 추가될 수 있기 때문에, **프로젝트 구조**에서부터 나누는 것이 필요했습니다.

```
- functions.py : skills에 있는 함수들을 등록하는 파일
- skills : Skill들을 모아놓는 디렉토리
	- weather.py : 날씨 skill
	- todoist.py : Todoist skill
	- ....
```

다음으로는 어떻게 Skill을 사용할 것 인가 입니다.
저는 처음부터 간단한 Keyword 매칭 만으로 Skill 을 사용할 생각이였으나.. 모든 Keyword 들을 다 입력해 놓는 것은 좋은 방법이 아니였습니다. Keyword 들을 입력하는 것도 큰 수고가 필요하기 때문에.. 조금 더 간결하게 사용할 수 있는 방법을 생각해보았습니다. 그래서 추가된 것이 **disintegrator** 입니다.

여기서 간단한 NLP 를 사용합니다. 한글의 경우 [konlpy]()가 형태소 분석이나 품사태깅의 기능을 지원합니다. konlpy를 이용해서 하려는 작업은 간단합니다. 문장을 그대로 받아서 Simple하게 만드는 것.

```python
class KorDisintegrator:

    def __init__(self):
        self.ko_twitter = Twitter()

    def convert2simple(self, sentence="", norm=True, stem=True):
        disintegrated_sentence = self.ko_twitter.pos(
            sentence, norm=norm, stem=stem)
        convert_sentence = []

        for w, t in disintegrated_sentence:
            if t not in ['Eomi', 'Josa', 'KoreanParticle', 'Punctuation']:
                convert_sentence.append(w)
        return " ".join(convert_sentence)
```

위 코드는 [신정규님께서 Python 2016에서 발표하신 프로그램](https://www.pycon.kr/2016apac/program/14) 참고하였습니다.  
이 KorDisintegrator 를 실제 문장이 통과하면 어떻게 되는지 보시죠!

```
>>> disintegrator.convert2simple(sentence="키노야 날씨 어때?")
'키노 날씨 어떻다'
>>> disintegrator.convert2simple(sentence="키노야 날씨 알려줘!")
'키노 날씨 알다'
```

이렇게 간단한 문장으로 변하게 되고, 이제 키워드를 쓸때 고려하면 되는 문장은 원형을 사용하면 되는 것이죠.

이제 문제는 Keyword를 어디에 저장할까? 그리고 skill에 필요한 param은 어떻게 관리할까? 였습니다.
처음에는 '외부에 새로 파일을 하나 만들어 skill에 대한 정보를 가지고 있도록 할까?' 였습니다. 음.. 그렇다면 Bot을 시작할 때, Skill들에 대한 정보를 읽어서 ```skills.json```이라는 파일을 만들면 어떨까? 저는 이 방법이 괜찮다고 생각이 들었고, 여기서 python의 ```__doc__```, ```__annotations__```을 이용하게 됩니다.

아래는 실제 Skill로 사용하고 있는 Code입니다.

```python
def forecast(self, timely: str="current"):
    """
    keyword: ["날씨", "예보", "weather", "forecast"]
    description: "Weather forecast"
    icon: ":sun_with_face: "
    """

    ...
    weather.forecast(timely=timely)
```

여기서 ```timely: str``` 은 Python 3.6에서 새로 추가된 type annotation 기능입니다.  
여기서 timely의 타입을 입력해놓으면..

```python
>>> forecast.__annotations__
{"timely": str}
```

```__annotations__```를 통해서 필요한 param 정보를 얻어올 수 있습니다. 마찬가지로 __doc__을 이용하면 아래 적혀있는 keyword, description, icon에 대해서 얻어올 수 있습니다. 그래서 이 정보들을 모아서 skills.json 파일을 만들면 Skill 등록은 끝이나게 됩니다.

다음으로는 Python에서 제공하는 ```any```, ```all```을 사용해서 키워드 매칭 코드를 작성하고,
```Regex(정규식)```을 통해서 Skill의 Param을 전달하는 코드를 작성하면!  
아래와 같이 날씨 스킬을 손쉽게 사용할 수 있습니다. 참 쉽죠?

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/en/kino-skill-example-en1.png?raw=true)


### Scheduler

Skill을 이제 추가하고 사용할 수 있는 구조를 정립했으니, scheduler를 만드려고 합니다.
Python에는 [schedule](https://github.com/dbader/schedule) 이라는 스케쥴링을 손쉽게 사용할 수 있도록 도와주는 Package가 있습니다.

아래는 schedule Github페이지에 나와있는 간단한 예제코드입니다. 다른 설명이 필요없이 아주 직관적으로 구성되어있습니다.

```python
schedule.every(10).minutes.do(job)
schedule.every().hour.do(job)
schedule.every().day.at("10:30").do(job)
schedule.every().monday.do(job)
schedule.every().wednesday.at("13:15").do(job)
```

schedule Package를 이용하면 제가 작업하면 되는 부분은 

1. 백그라운드에서 스케쥴링을 돌린다.
	- **Threading** 을 사용.
2. 동적으로 스케쥴링을 돌리도록 하는 것. 
	- Python에서 제공하는 built-in funciton인 **getattr**을 사용

이 부분에 대한 자세한 코드를 알고 싶으시다면, [Github 저장소](https://github.com/DongjunLee/kino-bot)를 참고해주세요! 다음으로 이 함수대로만 사용하는 것은 조금 제한적인 부분들이 있어서 아래와 같은 조건을 추가하게 됩니다.

**Between** : 시간대를 나타냄. schedule에서는 10분 마다, 1시간 마다 이렇게도 조작할 수 있기 때문에 특정 시간대에 10분 마다, 1시간 마다를 가능하게 하도록 하기 위하여 추가하였습니다.

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/ko/kino-read-between.png?raw=true)

이제 제가 원하는 대로 Scheduling을 사용할 수 있는 준비가 되었습니다!
다음으로는 간단하게 자연어로 Scheduling을 등록하고 작업을 진행시킬 수 있으면 되겠네요.

여기서도 간단한 Keyword 매칭을 사용합니다.

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/ko/kino-create-by-ner.png?raw=true)

이렇게 Scheduling 기능까지 추가를 완료하였습니다!

## Example

이제 이 기능들이 실제로 어떻게 쓰이는지 같이 보시죠!

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/ko/kino-create-job.png?raw=true)

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/ko/kino-start-job.png?raw=true)

이렇게 일을 시작하면, 등록된 Scheduling Job 들에 따라서 진행이 됩니다.

![images](https://github.com/DongjunLee/BeAwesomeToday/blob/master/images/ko/kino-read-schedule.png?raw=true)

- 10시 ~ 22시까지 2시간마다 날씨
- 오전 8시 하루 프리핑
- 오후 10시 남은 작업 알려주기 등..

위의 예시들은 실제로 제가 사용하고 있는 Scheduling Job 들 입니다.

이 정도면 제법 개인 비서처럼 행동할 수 있을 것 같지 않나요??  
이렇게 Kino 는 똑똑해지고 저를 위한 비서가 되고 있습니다!

다음에는 손쉽게 Todoist에 있는 작업들을, Trello에 카드를 통해 관리하며.. 자동으로 Toggl에 시간 기록까지 되고, 마지막에는 작업 리포트도 받을 수 있는 **T3** 기능에 대해서 다루도록 하겠습니다. 

모든 코드는 [여기서](https://github.com/DongjunLee/kino-bot) 확인하실 수 있습니다.  
Kino를 더욱 똑똑하게 만들도록 도와주시는 분들은 언제든 환영입니다^^