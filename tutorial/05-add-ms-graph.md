<!-- markdownlint-disable MD002 MD041 -->

この演習では、Microsoft Graph をアプリケーションに組み込む必要があります。 このアプリケーションでは [、microsoft-graph-client ライブラリ](https://github.com/microsoftgraph/msgraph-sdk-javascript) を使用して Microsoft Graph を呼び出します。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

1. **./graph.js開き**、次の関数を内部に追加します `module.exports` 。

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="GetCalendarViewSnippet":::

    このコードの実行内容を考えましょう。

    - 呼び出される URL は `/me/calendarview` です。
    - このメソッドは要求にヘッダーを追加し、開始時刻と終了時刻をユーザーのタイム ゾーン `header` `Prefer: outlook.timezone` で返します。
    - この `query` メソッドは、カレンダー `startDateTime` ビュー `endDateTime` のパラメーターとパラメーターを設定します。
    - この `select` メソッドは、各イベントで返されるフィールドを、ビューが実際に使用するフィールドに制限します。
    - この `orderby` メソッドは、結果を開始時刻で並べ替える。
    - この `top` メソッドは、結果を 50 イベントに制限します。

1. calendar.jsという名前の **./routes** ディレクトリに **新しいファイルを作成** し、次のコードを追加します。

    ```javascript
    const router = require('express-promise-router')();
    const graph = require('../graph.js');
    const moment = require('moment-timezone');
    const iana = require('windows-iana');
    const { body, validationResult } = require('express-validator');
    const validator = require('validator');

    /* GET /calendar */
    router.get('/',
      async function(req, res) {
        if (!req.session.userId) {
          // Redirect unauthenticated requests to home page
          res.redirect('/')
        } else {
          const params = {
            active: { calendar: true }
          };

          // Get the user
          const user = req.app.locals.users[req.session.userId];
          // Convert user's Windows time zone ("Pacific Standard Time")
          // to IANA format ("America/Los_Angeles")
          // Moment needs IANA format
          const timeZoneId = iana.findOneIana(user.timeZone);
          console.log(`Time zone: ${timeZoneId.valueOf()}`);

          // Calculate the start and end of the current week
          // Get midnight on the start of the current week in the user's timezone,
          // but in UTC. For example, for Pacific Standard Time, the time value would be
          // 07:00:00Z
          var startOfWeek = moment.tz(timeZoneId.valueOf()).startOf('week').utc();
          var endOfWeek = moment(startOfWeek).add(7, 'day');
          console.log(`Start: ${startOfWeek.format()}`);

          // Get the access token
          var accessToken;
          try {
            accessToken = await getAccessToken(req.session.userId, req.app.locals.msalClient);
          } catch (err) {
            res.send(JSON.stringify(err, Object.getOwnPropertyNames(err)));
            return;
          }

          if (accessToken && accessToken.length > 0) {
            try {
              // Get the events
              const events = await graph.getCalendarView(
                accessToken,
                startOfWeek.format(),
                endOfWeek.format(),
                user.timeZone);

              res.json(events.value);
            } catch (err) {
              res.send(JSON.stringify(err, Object.getOwnPropertyNames(err)));
            }
          }
          else {
            req.flash('error_msg', 'Could not get an access token');
          }
        }
      }
    );

    async function getAccessToken(userId, msalClient) {
      // Look up the user's account in the cache
      try {
        const accounts = await msalClient
          .getTokenCache()
          .getAllAccounts();

        const userAccount = accounts.find(a => a.homeAccountId === userId);

        // Get the token silently
        const response = await msalClient.acquireTokenSilent({
          scopes: process.env.OAUTH_SCOPES.split(','),
          redirectUri: process.env.OAUTH_REDIRECT_URI,
          account: userAccount
        });

        return response.accessToken;
      } catch (err) {
        console.log(JSON.stringify(err, Object.getOwnPropertyNames(err)));
      }
    }

    module.exports = router;
    ```

1. この **新しいルートをapp.js./app.js** 更新します。 行の前に次 **の行を** `var app = express();` 追加します。

    ```javascript
    var calendarRouter = require('./routes/calendar');
    ```

1. 行の後に次 **の行を** `app.use('/auth', authRouter);` 追加します。

    ```javascript
    app.use('/calendar', calendarRouter);
    ```

1. サーバーを再起動します。 サインインし、ナビゲーション バー **の [予定表** ] リンクをクリックします。 すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

結果を表示するとき、よりユーザー フレンドリなビューを追加できます。

1. ./app.js 行の後に **次のコードを** 追加 `app.set('view engine', 'hbs');` します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="FormatDateSnippet":::

    これにより、Microsoft Graph から返される ISO 8601 の日付を、より人間に優しい形式に書式設定するための [Handlebars](http://handlebarsjs.com/#helpers) ヘルパーが実装されます。

1. **calendar.hs** という **名前の ./views** ディレクトリに新しいファイルを作成し、次のコードを追加します。

    :::code language="html" source="../demo/graph-tutorial/views/calendar.hbs" id="LayoutSnippet":::

    これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。

1. **./routes/calendar.js** を更新して、このビューを使用します。 既存のルートを次のコードに置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetRouteSnippet" highlight="33-36,49,51-54,61":::

1. 変更内容を保存し、サーバーを再起動して、アプリにサインインします。 [カレンダー] **リンクを** クリックすると、アプリはイベントのテーブルをレンダリングする必要があります。

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
