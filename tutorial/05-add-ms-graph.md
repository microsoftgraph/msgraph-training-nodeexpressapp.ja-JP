<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="af97e-101">この演習では、Microsoft Graph をアプリケーションに組み込む必要があります。</span><span class="sxs-lookup"><span data-stu-id="af97e-101">In this exercise you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="af97e-102">このアプリケーションでは [、microsoft-graph-client ライブラリ](https://github.com/microsoftgraph/msgraph-sdk-javascript) を使用して Microsoft Graph を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="af97e-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="af97e-103">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="af97e-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="af97e-104">**./graph.js開き**、次の関数を内部に追加します `module.exports` 。</span><span class="sxs-lookup"><span data-stu-id="af97e-104">Open **./graph.js** and add the following function inside `module.exports`.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="GetCalendarViewSnippet":::

    <span data-ttu-id="af97e-105">このコードの実行内容を考えましょう。</span><span class="sxs-lookup"><span data-stu-id="af97e-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="af97e-106">呼び出される URL は `/me/calendarview` です。</span><span class="sxs-lookup"><span data-stu-id="af97e-106">The URL that will be called is `/me/calendarview`.</span></span>
    - <span data-ttu-id="af97e-107">このメソッドは要求にヘッダーを追加し、開始時刻と終了時刻をユーザーのタイム ゾーン `header` `Prefer: outlook.timezone` で返します。</span><span class="sxs-lookup"><span data-stu-id="af97e-107">The `header` method adds the `Prefer: outlook.timezone` header to the request, causing the start and end times to be returned in the user's time zone.</span></span>
    - <span data-ttu-id="af97e-108">この `query` メソッドは、カレンダー `startDateTime` ビュー `endDateTime` のパラメーターとパラメーターを設定します。</span><span class="sxs-lookup"><span data-stu-id="af97e-108">The `query` method sets the `startDateTime` and `endDateTime` parameters for the calendar view.</span></span>
    - <span data-ttu-id="af97e-109">この `select` メソッドは、各イベントで返されるフィールドを、ビューが実際に使用するフィールドに制限します。</span><span class="sxs-lookup"><span data-stu-id="af97e-109">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="af97e-110">この `orderby` メソッドは、結果を開始時刻で並べ替える。</span><span class="sxs-lookup"><span data-stu-id="af97e-110">The `orderby` method sorts the results by the start time.</span></span>
    - <span data-ttu-id="af97e-111">この `top` メソッドは、結果を 50 イベントに制限します。</span><span class="sxs-lookup"><span data-stu-id="af97e-111">The `top` method limits the results to 50 events.</span></span>

1. <span data-ttu-id="af97e-112">calendar.jsという名前の **./routes** ディレクトリに **新しいファイルを作成** し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="af97e-112">Create a new file in the **./routes** directory named **calendar.js**, and add the following code.</span></span>

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

1. <span data-ttu-id="af97e-113">この **新しいルートをapp.js./app.js** 更新します。</span><span class="sxs-lookup"><span data-stu-id="af97e-113">Update **./app.js** to use this new route.</span></span> <span data-ttu-id="af97e-114">行の前に次 **の行を** `var app = express();` 追加します。</span><span class="sxs-lookup"><span data-stu-id="af97e-114">Add the following line **before** the `var app = express();` line.</span></span>

    ```javascript
    var calendarRouter = require('./routes/calendar');
    ```

1. <span data-ttu-id="af97e-115">行の後に次 **の行を** `app.use('/auth', authRouter);` 追加します。</span><span class="sxs-lookup"><span data-stu-id="af97e-115">Add the following line **after** the `app.use('/auth', authRouter);` line.</span></span>

    ```javascript
    app.use('/calendar', calendarRouter);
    ```

1. <span data-ttu-id="af97e-116">サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="af97e-116">Restart the server.</span></span> <span data-ttu-id="af97e-117">サインインし、ナビゲーション バー **の [予定表** ] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="af97e-117">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="af97e-118">すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="af97e-118">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="af97e-119">結果の表示</span><span class="sxs-lookup"><span data-stu-id="af97e-119">Display the results</span></span>

<span data-ttu-id="af97e-120">結果を表示するとき、よりユーザー フレンドリなビューを追加できます。</span><span class="sxs-lookup"><span data-stu-id="af97e-120">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="af97e-121">./app.js 行の後に **次のコードを** 追加 `app.set('view engine', 'hbs');` します。</span><span class="sxs-lookup"><span data-stu-id="af97e-121">Add the following code in **./app.js after** the `app.set('view engine', 'hbs');` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="FormatDateSnippet":::

    <span data-ttu-id="af97e-122">これにより、Microsoft Graph から返される ISO 8601 の日付を、より人間に優しい形式に書式設定するための [Handlebars](http://handlebarsjs.com/#helpers) ヘルパーが実装されます。</span><span class="sxs-lookup"><span data-stu-id="af97e-122">This implements a [Handlebars helper](http://handlebarsjs.com/#helpers) to format the ISO 8601 date returned by Microsoft Graph into something more human-friendly.</span></span>

1. <span data-ttu-id="af97e-123">**calendar.hs** という **名前の ./views** ディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="af97e-123">Create a new file in the **./views** directory named **calendar.hbs** and add the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/calendar.hbs" id="LayoutSnippet":::

    <span data-ttu-id="af97e-124">これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。</span><span class="sxs-lookup"><span data-stu-id="af97e-124">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="af97e-125">**./routes/calendar.js** を更新して、このビューを使用します。</span><span class="sxs-lookup"><span data-stu-id="af97e-125">Now update the route in **./routes/calendar.js** to use this view.</span></span> <span data-ttu-id="af97e-126">既存のルートを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="af97e-126">Replace the existing route with the following code.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetRouteSnippet" highlight="33-36,49,51-54,61":::

1. <span data-ttu-id="af97e-127">変更内容を保存し、サーバーを再起動して、アプリにサインインします。</span><span class="sxs-lookup"><span data-stu-id="af97e-127">Save your changes, restart the server, and sign in to the app.</span></span> <span data-ttu-id="af97e-128">[カレンダー] **リンクを** クリックすると、アプリはイベントのテーブルをレンダリングする必要があります。</span><span class="sxs-lookup"><span data-stu-id="af97e-128">Click on the **Calendar** link and the app should now render a table of events.</span></span>

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
