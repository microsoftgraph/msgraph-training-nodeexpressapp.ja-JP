<!-- markdownlint-disable MD002 MD041 -->

この演習では、アプリケーションに Microsoft Graphを組み込む必要があります。 このアプリケーションでは[、microsoft-graph-client ライブラリ](https://github.com/microsoftgraph/msgraph-sdk-javascript)を使用して Microsoft Graph。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

1. **./graph.jsを開** き、内部に次の関数を追加します `module.exports` 。

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="GetCalendarViewSnippet":::

    このコードの実行内容を考えましょう。

    - 呼び出される URL は `/me/calendarview` です。
    - このメソッドは要求にヘッダーを追加し、開始時刻と終了時刻をユーザーのタイム ゾーン `header` `Prefer: outlook.timezone` で返します。
    - この `query` メソッドは、予定表 `startDateTime` ビュー `endDateTime` のパラメーターとパラメーターを設定します。
    - メソッド `select` は、各イベントに返されるフィールドを、ビューが実際に使用するフィールドに制限します。
    - メソッド `orderby` は、開始時刻によって結果を並べ替える。
    - メソッド `top` は、結果を 50 イベントに制限します。

1. という名前の **./routes** ディレクトリに **新しいcalendar.js** を作成し、次のコードを追加します。

    ```javascript
    const router = require('express-promise-router')();
    const graph = require('../graph.js');
    const addDays = require('date-fns/addDays');
    const formatISO = require('date-fns/formatISO');
    const startOfWeek = require('date-fns/startOfWeek');
    const zonedTimeToUtc = require('date-fns-tz/zonedTimeToUtc');
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
          const timeZoneId = iana.findIana(user.timeZone)[0];
          console.log(`Time zone: ${timeZoneId.valueOf()}`);

          // Calculate the start and end of the current week
          // Get midnight on the start of the current week in the user's timezone,
          // but in UTC. For example, for Pacific Standard Time, the time value would be
          // 07:00:00Z
          var weekStart = zonedTimeToUtc(startOfWeek(new Date()), timeZoneId.valueOf());
          var weekEnd = addDays(weekStart, 7);
          console.log(`Start: ${formatISO(weekStart)}`);

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
                formatISO(weekStart),
                formatISO(weekEnd),
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

1. この **新しいルートをapp.js./app.js** を更新します。 行の前に次 **の行を** 追加 `var app = express();` します。

    ```javascript
    var calendarRouter = require('./routes/calendar');
    ```

1. 行の後に次 **の行を** `app.use('/auth', authRouter);` 追加します。

    ```javascript
    app.use('/calendar', calendarRouter);
    ```

1. サーバーを再起動します。 サインインして、ナビゲーション バー **の [予定表** ] リンクをクリックします。 すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

結果を表示するとき、よりユーザー フレンドリなビューを追加できます。

1. 行の後に **./app.jsコードを** 追加 `app.set('view engine', 'hbs');` します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="FormatDateSnippet":::

    これにより、Microsoft が返す ISO 8601 日付を人間にGraphに書式設定するための[Handlebars](http://handlebarsjs.com/#helpers)ヘルパーが実装されます。

1. **calendar.hbs** という **名前の ./views** ディレクトリに新しいファイルを作成し、次のコードを追加します。

    :::code language="html" source="../demo/graph-tutorial/views/calendar.hbs" id="LayoutSnippet":::

    これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。

1. このビューを使用するには **、./routes/calendar.jsルート** を更新します。 既存のルートを次のコードに置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetRouteSnippet" highlight="33-36,49,51-54,61":::

1. 変更を保存し、サーバーを再起動し、アプリにサインインします。 [予定表] **リンクをクリック** すると、アプリはイベントのテーブルを表示する必要があります。

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
