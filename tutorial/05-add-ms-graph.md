<!-- markdownlint-disable MD002 MD041 -->

この演習では、アプリケーションに Microsoft Graph を組み込みます。 このアプリケーションでは、microsoft graph [クライアント](https://github.com/microsoftgraph/msgraph-sdk-javascript) ライブラリを使用して microsoft graph への呼び出しを行います。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

1. **/graph.js** を開き、に次の関数を追加 `module.exports` します。

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="GetCalendarViewSnippet":::

    このコードの実行内容を考えましょう。

    - 呼び出される URL は `/me/events` です。
    - `select`このメソッドは、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。
    - メソッドは、 `orderby` 生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。

1. **calendar.js** という名前の **ルート** ディレクトリに新しいファイルを作成し、次のコードを追加します。

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

1. この新しいルートを使用するには、[更新] を app.jsします **。** 行の **前に** 次の行を追加し `var app = express();` ます。

    ```javascript
    var calendarRouter = require('./routes/calendar');
    ```

1. 行の **後** に次の行を追加し `app.use('/auth', authRouter);` ます。

    ```javascript
    app.use('/calendar', calendarRouter);
    ```

1. サーバーを再起動します。 サインインして、ナビゲーションバーの [ **予定表** ] リンクをクリックします。 すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

結果を表示するとき、よりユーザー フレンドリなビューを追加できます。

1. 行の後に、次のコードを追加 **します。./app.js** `app.set('view engine', 'hbs');`

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="FormatDateSnippet":::

    これにより、Microsoft Graph によって返される ISO 8601 日付をよりわかりやすいものに書式設定する [Handlebars ヘルパー](http://handlebarsjs.com/#helpers) が実装されています。

1. 次のコードを追加して、 **calendar** という名前の **./views** ディレクトリに新しいファイルを作成します。

    :::code language="html" source="../demo/graph-tutorial/views/calendar.hbs" id="LayoutSnippet":::

    これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。

1. ここで、このビューを使用するように、 **/routes/calendar.js** のルートを更新します。 既存のルートを次のコードに置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetRouteSnippet" highlight="33-36,49,51-54,61":::

1. 変更を保存し、サーバーを再起動して、アプリにサインインします。 [ **予定表** ] リンクをクリックすると、アプリがイベントの表を表示するようになります。

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
