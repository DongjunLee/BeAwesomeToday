# Node.js를 통해 Slack bot만들기

[Be Happy 프로젝트](http://humanbrain.in/2016/06/26/behappy-project-start/)를 시작하면서, 4번이 저 자신만을 위한 인공지능 비서 Stalker bot을 만드는 것이였습니다. 어떻게 보면 가장 나중에 진행해야하는 단계이지만.. Slack bot을 구현하는 좋은 글을 발견하기도 했고, 행복도 측정하기에 마땅한 앱이 없어서 Slack bot을 먼저 구현해서 행복도 측정기능도 추가해보자는 생각을 하게 되었습니다.

이번 포스트에서는 [Node.js](https://nodejs.org/ko/)와 [slackbots](https://www.npmjs.com/package/slackbots)를 통해 간단한 bot을 구현하고, [Heroku](https://www.heroku.com/)를 통해서 배포하는 과정을 살펴보겠습니다.


## 1. Slack 에서 Bot 생성

먼저 Slack에서 Bot을 생성해서 API Token을 받아봐야 합니다.  
https://<yourslackdomain>.slack.com/services/new/bot  
<yourslackdomain>에 자신이 사용하는 Slack의 도메인을 넣어주시면 됩니다. 

![image](http://humanbrain.in/wp-content/uploads/2016/07/new_bot.png)

이와 같은 화면이 나오게 되면, bot의 이름을 넣어주시면 됩니다. 다음 화면으로 넘어가면 API Tocken값이 주어지고, 그 외에 Bot에 대해서 설정을 진행할 수 있습니다.

이제 API Token 값을 받아왔으므로, Node.js로 Slack bot을 구현해보겠습니다.



## 2. Node.js Setting

우선 npm, node.js 는 이미 설치가 되어있다고 가정하겠습니다. 

그러면 프로젝트 기본 셋팅을 시작하겠습니다.  
먼저 slack-bot폴더를 만들고, 디렉토리를 이동해줍니다.  

```bash
npm init
```
을 통해서 프로젝트의 셋팅을 진행합니다.  

저희가 필요한 라이브러리는 [slackbots](https://www.npmjs.com/package/slackbots) 입니다.

```bash
npm install --save slackbots
```

을 통해서 slackbots을 설치하고, package.json에도 저장을 해줍니다.



## 3. Slackbots 사용법

[slackbots API](https://www.npmjs.com/package/slackbots#methods)는 링크를 통해서 확인하실 수 있습니다. 일단은 간단한 사용법에 대해서 알아보겠습니다. 

```JavaScript
# bot_test.js

var SlackBot = require('slackbots');
 
// create a bot 
var bot = new SlackBot({
    token: 'xoxb-012345678-ABC1DFG2HIJ3', // Add a bot https://my.slack.com/services/new/bot and put the token  
    name: 'My Bot'
});
 
bot.on('start', function() {
    // more information about additional params https://api.slack.com/methods/chat.postMessage 
    var params = {
        icon_emoji: ':cat:'
    };
    
    // define channel, where bot exist. You can adjust it there https://my.slack.com/services  
    bot.postMessageToChannel('general', 'meow!', params);
    
    // define existing username instead of 'user_name' 
    bot.postMessageToUser('user_name', 'meow!', params); 
    
    // define private group instead of 'private_group', where bot exist 
    bot.postMessageToGroup('private_group', 'meow!', params); 
});
```

간단한 사용법이고, 직관적이기 때문에 따로 설명을 하지는 않겠습니다.  
bot_test.js 파일들 만들고 위의 코드들을 입력해줍니다. 그리고
```bash
node bot_test.js
```
를 입력하면 아래와 같이 Bot이 메시지를 보내는 것을 볼 수 있습니다.

![image](http://i.imgur.com/hqzTXHm.png)
이미지 출처 : https://www.npmjs.com/package/slackbots



## 4. Slack Bot 구현

위에서 간단한 slackbots의 사용법을 보셨다면, 간단하게 사용자가 대화를 입력하면 'Yo!'라고 대답을 해주는 Bot을 구현하겠습니다.

```JavaScript
# bin/bot.js

'use strict';

var SlackBot = require('../lib/slackbot');

var token = process.env.BOT_API_KEY;
var name = process.env.BOT_NAME;

var slackbot = new SlackBot({
    token: token,
    name: name
});

slackbot.run();
```

먼저 bot을 실행하는 코드를 작성하겠습니다. bin폴더에 bot.js를 생성하여 위와 같이 작성을 해줍니다. 프로그램을 시작할 때, token, name의 값들을 받아서 SlackBot을 생성하고 실행하는 로직으로 구성되어 있습니다. 그렇다면 다음으로 세부 SlackBot의 세부 구현물을 보겠습니다.


```JavaScript
# lib/slackbot.js

'use strict';

var util = require('util');
var Bot = require('slackbots');

var SlackBot = function Constructor(settings) {
    this.settings = settings;
    this.settings.name = this.settings.name;
};

// inherits methods and properties from the Bot constructor
util.inherits(SlackBot, Bot);

module.exports = SlackBot;
```

lib폴더에 slackbot.js를 만들고 위와 같이 입력해줍니다. 여기서 setting은 API Token과 name등의 Setting 값들을 의미합니다. SlackBot을 생성자로서 만들 수 있게 지정해주고 util.inherits를 통해 slackbots 라이브러리를 상속해줍니다.

다음은 run() 함수를 살펴보겠습니다.

```JavaScript
SlackBot.prototype.run = function () {
    SlackBot.super_.call(this, this.settings);

    this.on('start', this._onStart);
    this.on('message', this._onMessage);
};
```

run()함수는 생성자를 호출하고, 

- start - event fired, when Real Time Messaging API is started (via websocket) (Slack Bot을 시작할 시 발생하는 이벤트)
- message - event fired, when something happens in Slack. Description of all events [here](https://api.slack.com/rtm) (메시지를 주고 받는 등.. 의 이벤트)

이 두가지 event를 _onStart, _onMessage 함수로 할당해줍니다.

먼저 **start** 이벤트 입니다.

```JavaScript
SlackBot.prototype._onStart = function () {
	this._welcomeMessage();
};

StalkerBot.prototype._welcomeMessage = function () {
   this.postMessageToUser('username', 'Hello World!');
};
```

위의 함수들은 간단하게 username를 가진 사용자에게 'Hello World!'의 메시지를 전달합니다.

다음은 **message** 이벤트를 다루는 부분 입니다.

```JavaScript
StalkerBot.prototype._onMessage = function (message) {
   if (this._isChatMessage(message) && !this. _isSlackBot(message)) {
       this._replyYo(message);
   }
};

StalkerBot.prototype._isChatMessage = function (message) {
    return message.type === 'message' && Boolean(message.text);
};

StalkerBot.prototype._isSlackBot = function (message) {
    return message.user === this.user.id;
};

StalkerBot.prototype._replyYo = function (message) {
    this.postMessageToUser('username', "Yo!");
}
```

onMessage 함수에서는 사용자에게서 메시지를 받으면, "Yo!"라고 답변을 해줍니다. 

전체적인 플로우는 사용자에게 메시지를 받으면 message 이벤트가 발생합니다. 그리고 이 메시지가 ChatMessage 이면서, Bot이 보낸 것이 아니라고 판별이 나면 답변을 해주는 과정입니다.

이제 Slack Bot 구현을 마쳤습니다.

(참고 - 지금 여기서 구현한 Bot은 특정 채널에서 대화하는 것이 아닌 DIRECT MESSAGE 상에서 대화가 이루어지는 간단한 Bot입니다.) 



## 5. Slack Bot 테스트

이제 구현한 SlackBot을 테스트해보겠습니다. 테스트는 간단합니다. 

```bash
BOT_API_KEY=your_api_key BOT_NAME=your_bot_name node bin/bot.js
```

을 터미널에서 입력해주면 됩니다. 사용하시고 있는 Slack에 "Hello, World!" 메시지가 도착하면 잘 작동하는 것입니다! DIRECT MESSAGE에서 메시지를 입력했을 때, "Yo!"라는 대답을 하는지도 확인해보세요.

![image](http://humanbrain.in/wp-content/uploads/2016/07/reply_Yo.png)



## 6. Heroku를 통한 배포

그럼 저희가 구현한 SlackBot을 [Heroku](https://www.heroku.com/)라는 PaaS를 통해서 배포해보겠습니다. Heroku toolbelt, Git, Dropbox를 통해 배포하는 방법들이 있지만, 여기서는 [Heroku toolbelt](https://toolbelt.heroku.com/)를 통해서 배포하는 방법에 대해서 살펴보겠습니다.

구체적인 배포과정에 앞서, Heroku에 필요한 설정을 하겠습니다.  
Heroku는 Procfile 이라는 파일을 통해서 정보를 읽는다고 명시가 되어있습니다.

```bash
vim Procfile
```

Procfile을 만들고 ```worker: node bin/bot.js``` 라고 입력하고 저장해주는 것으로 worker를 설정하게 됩니다.

-----

다음으로는 SlackBot의 setting이 필요합니다.  
Settings 메뉴를 가면 Config를 설정할 수 있습니다.

![image](http://humanbrain.in/wp-content/uploads/2016/07/heroku_config.png)

위 이미지와 같이 setting값을 입력해주면 됩니다.


-----

Heroku toolbelt를 받으면 terminal로 로그인하고, Git을 통해서 Heroku 서버로 업로드 할 수 있습니다.


```bash
$ heroku login
```

slack-bot 저장소로 이동 후,

```bash
$ git add .
$ git commit -am "First Version"
$ git push heroku master
```

위 커맨드를 입력하면 Heroku에 업로드가 되면서 동시에 배포가 됩니다.



## 7. 마치며..

정말로 간단한 Slack Bot을 만드는 것들에 대해서 다루었습니다. 'Bot 만들기 어렵겠지..?' 라고고 생각하고 있었는데, 이미 제공하고 있는 API를 가져다 쓰는 것만으로 간편하게 만들 수가 있었습니다. 여기서 저는 제가 원하는 기능들을 붙여서 나가며 개인 프로젝트를 진행해나갈 예정입니다! 자신만의 Bot을 만들고.. 점점 똑똑해지게 만들어 보시는 것은 어떠신가요?



## Reference

-  [Building a Slack Bot with Node.js and Chuck Norris Super Powers](https://scotch.io/tutorials/building-a-slack-bot-with-node-js-and-chuck-norris-super-powers) 
- [Simple API for Slack](https://www.npmjs.com/package/slackbots)