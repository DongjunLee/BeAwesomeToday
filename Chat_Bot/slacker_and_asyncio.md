# Slacker and Asyncio

## Slacker 에 대해서..

그동안 슬랙을 개발하면서 [slackbot](https://github.com/lins05/slackbot) 를 github에서 찾아서 개발하고 있었는데..
더 괜찮은 오픈소스가 있었다..! 

바로 [slacker](https://github.com/os/slacker/).

Slack 에서도 API 문서에서 slacker를 추천한다고 한다..! 그래서 새로 개발하고 있는 [kino-bot]() 은 slacker를 사용하고 있다.

slacker에서 가장 좋다고 생각하는 점은..! 문서가 [Slack API 공식 문서]((https://api.slack.com/methods))와 동일하다는 것.

사용법은 문서를 참고하면 되므로 넘어가도록 하겠다.

## Asyncio로 Slack bot 비동기로 실행

```python
import asyncio
import websockets

...

# Start RTM
endpoint = slackbot.start_real_time_messaging_session()

async def execute_bot():
    ws = await websockets.connect(endpoint)
    listener = SlackListener()
    while True:
        message_json = await ws.recv()
        listener.handle_only_message(message_json)

loop = asyncio.new_event_loop()
asyncio.set_event_loop(loop)
asyncio.get_event_loop().run_until_complete(execute_bot())
asyncio.get_event_loop().run_forever()
```

## Reference

- [[Python] Slacker를 이용한 Slack Bot 만들기](https://corikachu.github.io/articles/python/python-slack-bot-slacker)