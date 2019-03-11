<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込みます。 このアプリケーションでは、microsoft graph[クライアント](https://github.com/microsoftgraph/msgraph-sdk-javascript)ライブラリを使用して microsoft graph への呼び出しを行います。

## <a name="get-calendar-events-from-outlook"></a>Outlook から予定表のイベントを取得する

最初に、新しいメソッドを`./graph.js`ファイルに追加して、予定表からイベントを取得します。 の内`module.exports`に`./graph.js`次の関数を追加します。

```js
getEvents: async function(accessToken) {
  const client = getAuthenticatedClient(accessToken);

  const events = await client
    .api('/me/events')
    .select('subject,organizer,start,end')
    .orderby('createdDateTime DESC')
    .get();

  return events;
}
```

このコードの内容を検討してください。

- 呼び出し先の URL は`/me/events`になります。
- この`select`メソッドは、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。
- メソッド`orderby`は、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。

という名前`./routes` `calendar.js`のディレクトリに新しいファイルを作成し、次のコードを追加します。

```js
var express = require('express');
var router = express.Router();
var tokens = require('../tokens.js');
var graph = require('../graph.js');

/* GET /calendar */
router.get('/',
  async function(req, res) {
    if (!req.isAuthenticated()) {
      // Redirect unauthenticated requests to home page
      res.redirect('/')
    } else {
      let params = {
        active: { calendar: true }
      };

      // Get the access token
      var accessToken;
      try {
        accessToken = await tokens.getAccessToken(req);
      } catch (err) {
        res.json(err);
      }

      if (accessToken && accessToken.length > 0) {
        try {
          // Get the events
          var events = await graph.getEvents(accessToken);

          res.json(events.value);
        } catch (err) {
          res.json(err);
        }
      }
    }
  }
);

module.exports = router;
```

この`./app.js`新しいルートを使用するように更新します。 行の`var app = express();` **前に**次の行を追加します。

```js
var calendarRouter = require('./routes/calendar');
```

その後、行`app.use('/auth', authRouter);`の**後**に次の行を追加します。

```js
app.use('/calendar', calendarRouter);
```

これで、これをテストできます。 サインインして、ナビゲーションバーの [**予定表**] リンクをクリックします。 すべてが動作する場合は、ユーザーの予定表にイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果を表示する

これで、ビューを追加して、よりわかりやすい方法で結果を表示することができます。 最初に、行の`./app.js` **** `app.set('view engine', 'hbs');`後に次のコードを追加します。

```js
var hbs = require('hbs');
var moment = require('moment');
// Helper to format date/time sent by Graph
hbs.registerHelper('eventDateTime', function(dateTime){
  return moment(dateTime).format('M/D/YY h:mm A');
});
```

これにより、Microsoft Graph によって返される ISO 8601 日付をよりわかりやすいものに書式設定する[Handlebars ヘルパー](http://handlebarsjs.com/#helpers)が実装されています。

という名前`./views` `calendar.hbs`のディレクトリに新しいファイルを作成し、次のコードを追加します。

```html
<h1>Calendar</h1>
<table class="table">
  <thead>
    <tr>
      <th scope="col">Organizer</th>
      <th scope="col">Subject</th>
      <th scope="col">Start</th>
      <th scope="col">End</th>
    </tr>
  </thead>
  <tbody>
    {{#each events}}
      <tr>
        <td>{{this.organizer.emailAddress.name}}</td>
        <td>{{this.subject}}</td>
        <td>{{eventDateTime this.start.dateTime}}</td>
        <td>{{eventDateTime this.end.dateTime}}</td>
      </tr>
    {{/each}}
  </tbody>
</table>
```

これにより、イベントのコレクションをループ処理して、テーブル行を1つずつ追加します。 このビューを使用する`./routes/calendar.js`には、のルートを更新します。 既存のルートを次のコードに置き換えます。

```js
router.get('/',
  async function(req, res) {
    if (!req.isAuthenticated()) {
      // Redirect unauthenticated requests to home page
      res.redirect('/')
    } else {
      let params = {
        active: { calendar: true }
      };

      // Get the access token
      var accessToken;
      try {
        accessToken = await tokens.getAccessToken(req);
      } catch (err) {
        req.flash('error_msg', {
          message: 'Could not get access token. Try signing out and signing in again.',
          debug: JSON.stringify(err)
        });
      }

      if (accessToken && accessToken.length > 0) {
        try {
          // Get the events
          var events = await graph.getEvents(accessToken);
          params.events = events.value;
        } catch (err) {
          req.flash('error_msg', {
            message: 'Could not fetch events',
            debug: JSON.stringify(err)
          });
        }
      }

      res.render('calendar', params);
    }
  }
);
```

変更を保存し、サーバーを再起動して、アプリにサインインします。 [**予定表**] リンクをクリックすると、アプリがイベントの表を表示するようになります。

![イベントの表のスクリーンショット](./images/add-msgraph-01.png)