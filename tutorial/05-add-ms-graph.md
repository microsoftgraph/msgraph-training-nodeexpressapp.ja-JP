<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e4283-101">この演習では、アプリケーションに Microsoft Graph を組み込みます。</span><span class="sxs-lookup"><span data-stu-id="e4283-101">In this exercise you will incorporate Microsoft Graph into the application.</span></span> <span data-ttu-id="e4283-102">このアプリケーションでは、microsoft graph[クライアント](https://github.com/microsoftgraph/msgraph-sdk-javascript)ライブラリを使用して microsoft graph への呼び出しを行います。</span><span class="sxs-lookup"><span data-stu-id="e4283-102">For this application, you will use the [microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) library to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="e4283-103">Outlook からカレンダー イベントを取得する</span><span class="sxs-lookup"><span data-stu-id="e4283-103">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="e4283-104">を`./graph.js`開いて、次の関数`module.exports`を内に追加します。</span><span class="sxs-lookup"><span data-stu-id="e4283-104">Open `./graph.js` and add the following function inside `module.exports`.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="GetEventsSnippet":::

    <span data-ttu-id="e4283-105">このコードの実行内容を考えましょう。</span><span class="sxs-lookup"><span data-stu-id="e4283-105">Consider what this code is doing.</span></span>

    - <span data-ttu-id="e4283-106">呼び出される URL は `/me/events` です。</span><span class="sxs-lookup"><span data-stu-id="e4283-106">The URL that will be called is `/me/events`.</span></span>
    - <span data-ttu-id="e4283-107">この`select`メソッドは、各イベントに対して返されるフィールドを、ビューが実際に使用するものだけに制限します。</span><span class="sxs-lookup"><span data-stu-id="e4283-107">The `select` method limits the fields returned for each events to just those the view will actually use.</span></span>
    - <span data-ttu-id="e4283-108">メソッド`orderby`は、生成された日付と時刻で結果を並べ替えます。最新のアイテムが最初に表示されます。</span><span class="sxs-lookup"><span data-stu-id="e4283-108">The `orderby` method sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="e4283-109">という名前`./routes` `calendar.js`のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e4283-109">Create a new file in the `./routes` directory named `calendar.js`, and add the following code.</span></span>

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

1. <span data-ttu-id="e4283-110">この`./app.js`新しいルートを使用するように更新します。</span><span class="sxs-lookup"><span data-stu-id="e4283-110">Update `./app.js` to use this new route.</span></span> <span data-ttu-id="e4283-111">行の`var app = express();` **前に**次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="e4283-111">Add the following line **before** the `var app = express();` line.</span></span>

    ```javascript
    var calendarRouter = require('./routes/calendar');
    ```

1. <span data-ttu-id="e4283-112">行の`app.use('/auth', authRouter);` **後**に次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="e4283-112">Add the following line **after** the `app.use('/auth', authRouter);` line.</span></span>

    ```javascript
    app.use('/calendar', calendarRouter);
    ```

1. <span data-ttu-id="e4283-113">サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="e4283-113">Restart the server.</span></span> <span data-ttu-id="e4283-114">サインインして、ナビゲーションバーの [**予定表**] リンクをクリックします。</span><span class="sxs-lookup"><span data-stu-id="e4283-114">Sign in and click the **Calendar** link in the nav bar.</span></span> <span data-ttu-id="e4283-115">すべてが正常に機能していれば、ユーザーのカレンダーにイベントの JSON ダンプが表示されます。</span><span class="sxs-lookup"><span data-stu-id="e4283-115">If everything works, you should see a JSON dump of events on the user's calendar.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="e4283-116">結果の表示</span><span class="sxs-lookup"><span data-stu-id="e4283-116">Display the results</span></span>

<span data-ttu-id="e4283-117">結果を表示するとき、よりユーザー フレンドリなビューを追加できます。</span><span class="sxs-lookup"><span data-stu-id="e4283-117">Now you can add a view to display the results in a more user-friendly manner.</span></span>

1. <span data-ttu-id="e4283-118">行の`./app.js` **after** `app.set('view engine', 'hbs');`後に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e4283-118">Add the following code in `./app.js` **after** the `app.set('view engine', 'hbs');` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="FormatDateSnippet":::

    <span data-ttu-id="e4283-119">これにより、Microsoft Graph によって返される ISO 8601 日付をよりわかりやすいものに書式設定する[Handlebars ヘルパー](http://handlebarsjs.com/#helpers)が実装されています。</span><span class="sxs-lookup"><span data-stu-id="e4283-119">This implements a [Handlebars helper](http://handlebarsjs.com/#helpers) to format the ISO 8601 date returned by Microsoft Graph into something more human-friendly.</span></span>

1. <span data-ttu-id="e4283-120">という名前`./views` `calendar.hbs`のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="e4283-120">Create a new file in the `./views` directory named `calendar.hbs` and add the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/calendar.hbs" id="LayoutSnippet":::

    <span data-ttu-id="e4283-121">これにより、イベントのコレクションがループされ、各イベントにテーブル行が追加されます。</span><span class="sxs-lookup"><span data-stu-id="e4283-121">That will loop through a collection of events and add a table row for each one.</span></span>

1. <span data-ttu-id="e4283-122">このビューを使用する`./routes/calendar.js`には、のルートを更新します。</span><span class="sxs-lookup"><span data-stu-id="e4283-122">Now update the route in `./routes/calendar.js` to use this view.</span></span> <span data-ttu-id="e4283-123">既存のルートを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="e4283-123">Replace the existing route with the following code.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetRouteSnippet" highlight="16-19,26,28-31,37":::

1. <span data-ttu-id="e4283-124">変更を保存し、サーバーを再起動して、アプリにサインインします。</span><span class="sxs-lookup"><span data-stu-id="e4283-124">Save your changes, restart the server, and sign in to the app.</span></span> <span data-ttu-id="e4283-125">[**予定表**] リンクをクリックすると、アプリがイベントの表を表示するようになります。</span><span class="sxs-lookup"><span data-stu-id="e4283-125">Click on the **Calendar** link and the app should now render a table of events.</span></span>

    ![イベント表のスクリーンショット](./images/add-msgraph-01.png)
