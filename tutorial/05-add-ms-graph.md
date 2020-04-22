<!-- markdownlint-disable MD002 MD041 -->

この演習では、アプリケーションに Microsoft Graph を組み込みます。 このアプリケーションでは、microsoft graph[クライアント](https://github.com/microsoftgraph/msgraph-sdk-javascript)ライブラリを使用して microsoft graph への呼び出しを行います。

## <a name="get-calendar-events-from-outlook"></a>Outlook からカレンダー イベントを取得する

1. を`./graph.js`開いて、次の関数`module.exports`を内に追加します。

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="GetEventsSnippet":::

    このコードの実行内容を考えましょう。

    - 呼び出される URL は `/me/events` です。
    - この`select`メソッドは、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。
    - メソッド`orderby`は、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。

1. という名前`./routes` `calendar.js`のディレクトリに新しいファイルを作成し、次のコードを追加します。

    ```javascript
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
          else {
            req.flash('error_msg', 'Could not get an access token');
          }
        }
      }
    );

    module.exports = router;
    ```

1. この`./app.js`新しいルートを使用するように更新します。 行の`var app = express();` **前に**次の行を追加します。

    ```javascript
    var calendarRouter = require('./routes/calendar');
    ```

1. 行の`app.use('/auth', authRouter);` **後**に次の行を追加します。

    ```javascript
    app.use('/calendar', calendarRouter);
    ```

1. サーバーを再起動します。 サインインして、ナビゲーションバーの [**予定表**] リンクをクリックします。 すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。

## <a name="display-the-results"></a>結果の表示

結果を表示するとき、よりユーザー フレンドリなビューを追加できます。

1. 行の`./app.js` **after** `app.set('view engine', 'hbs');`後に次のコードを追加します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="FormatDateSnippet":::

    これにより、Microsoft Graph によって返される ISO 8601 日付をよりわかりやすいものに書式設定する[Handlebars ヘルパー](http://handlebarsjs.com/#helpers)が実装されています。

1. という名前`./views` `calendar.hbs`のディレクトリに新しいファイルを作成し、次のコードを追加します。

    :::code language="html" source="../demo/graph-tutorial/views/calendar.hbs" id="LayoutSnippet":::

    これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。

1. このビューを使用する`./routes/calendar.js`には、のルートを更新します。 既存のルートを次のコードに置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetRouteSnippet" highlight="16-19,26,28-31,37":::

1. 変更を保存し、サーバーを再起動して、アプリにサインインします。 [**予定表**] リンクをクリックすると、アプリがイベントの表を表示するようになります。

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
