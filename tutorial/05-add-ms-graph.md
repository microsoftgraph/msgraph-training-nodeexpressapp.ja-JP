<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="bbf5e-101">この演習では、アプリケーションに Microsoft Graph を組み込みます。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-101">In this exercise you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="bbf5e-102">このアプリケーションでは、microsoft graph [クライアント](https://github.com/microsoftgraph/msgraph-sdk-javascript) ライブラリを使用して microsoft graph への呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="bbf5e-103">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="bbf5e-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="bbf5e-104">**/graph.js** を開き、に次の関数を追加 `module.exports` します。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-104">Open **./graph.js** and add the following function inside `module.exports`.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="GetCalendarViewSnippet":::

    <span data-ttu-id="bbf5e-105">このコードの実行内容を考えましょう。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="bbf5e-106">呼び出される URL は `/me/events` です。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-106">The URL that will be called is `/me/events`.</span></span>
    - <span data-ttu-id="bbf5e-107">`select`このメソッドは、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-107">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="bbf5e-108">メソッドは、 `orderby` 生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-108">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="bbf5e-109">**calendar.js** という名前の **ルート** ディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-109">Create a new file in the **./routes** directory named **calendar.js** , and add the following code.</span></span>

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

1. <span data-ttu-id="bbf5e-110">この新しいルートを使用するには、[更新] を app.jsします **。**</span><span class="sxs-lookup"><span data-stu-id="bbf5e-110">Update **./app.js** to use this new route.</span></span> <span data-ttu-id="bbf5e-111">行の **前に** 次の行を追加し `var app = express();` ます。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-111">Add the following line **before** the `var app = express();` line.</span></span>

    ```javascript
    var calendarRouter = require('./routes/calendar');
    ```

1. <span data-ttu-id="bbf5e-112">行の **後** に次の行を追加し `app.use('/auth', authRouter);` ます。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-112">Add the following line **after** the `app.use('/auth', authRouter);` line.</span></span>

    ```javascript
    app.use('/calendar', calendarRouter);
    ```

1. <span data-ttu-id="bbf5e-113">サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-113">Restart the server.</span></span> <span data-ttu-id="bbf5e-114">サインインして、ナビゲーションバーの [ **予定表** ] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-114">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="bbf5e-115">すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-115">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="bbf5e-116">結果の表示</span><span class="sxs-lookup"><span data-stu-id="bbf5e-116">Display the results</span></span>

<span data-ttu-id="bbf5e-117">結果を表示するとき、よりユーザー フレンドリなビューを追加できます。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-117">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="bbf5e-118">行の後に、次のコードを追加 **します。./app.js** `app.set('view engine', 'hbs');`</span><span class="sxs-lookup"><span data-stu-id="bbf5e-118">Add the following code in **./app.js after** the `app.set('view engine', 'hbs');` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="FormatDateSnippet":::

    <span data-ttu-id="bbf5e-119">これにより、Microsoft Graph によって返される ISO 8601 日付をよりわかりやすいものに書式設定する [Handlebars ヘルパー](http://handlebarsjs.com/#helpers) が実装されています。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-119">This implements a [Handlebars helper](http://handlebarsjs.com/#helpers) to format the ISO 8601 date returned by Microsoft Graph into something more human-friendly.</span></span>

1. <span data-ttu-id="bbf5e-120">次のコードを追加して、 **calendar** という名前の **./views** ディレクトリに新しいファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-120">Create a new file in the **./views** directory named **calendar.hbs** and add the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/calendar.hbs" id="LayoutSnippet":::

    <span data-ttu-id="bbf5e-121">これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-121">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="bbf5e-122">ここで、このビューを使用するように、 **/routes/calendar.js** のルートを更新します。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-122">Now update the route in **./routes/calendar.js** to use this view.</span></span> <span data-ttu-id="bbf5e-123">既存のルートを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-123">Replace the existing route with the following code.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetRouteSnippet" highlight="33-36,49,51-54,61":::

1. <span data-ttu-id="bbf5e-124">変更を保存し、サーバーを再起動して、アプリにサインインします。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-124">Save your changes, restart the server, and sign in to the app.</span></span> <span data-ttu-id="bbf5e-125">[ **予定表** ] リンクをクリックすると、アプリがイベントの表を表示するようになります。</span><span class="sxs-lookup"><span data-stu-id="bbf5e-125">Click on the **Calendar** link and the app should now render a table of events.</span></span>

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
