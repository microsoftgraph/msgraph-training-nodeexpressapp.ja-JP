<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="15a7b-101">この手順では、 [Express](http://expressjs.com/)を使用して web アプリを構築します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-101">In this exercise you will use [Express](http://expressjs.com/) to build a web app.</span></span> <span data-ttu-id="15a7b-102">Express ジェネレーターがまだインストールされていない場合は、コマンドラインインターフェイス (CLI) から次のコマンドを使用してインストールできます。</span><span class="sxs-lookup"><span data-stu-id="15a7b-102">If you don't already have the Express generator installed, you can install it from your command-line interface (CLI) with the following command.</span></span>

```Shell
npm install express-generator -g
```

<span data-ttu-id="15a7b-103">CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して、 [Handlebars](http://handlebarsjs.com/)をレンダリングエンジンとして使用する新しいエクスプレスアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-103">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Express app that uses [Handlebars](http://handlebarsjs.com/) as the rendering engine.</span></span>

```Shell
express --hbs graph-tutorial
```

<span data-ttu-id="15a7b-104">エクスプレスジェネレーターは、という名前の`graph-tutorial`新しいディレクトリを作成し、スキャフォールディングというエクスプレスアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-104">The Express generator creates a new directory called `graph-tutorial` and scaffolds an Express app.</span></span> <span data-ttu-id="15a7b-105">この新しいディレクトリに移動し、次のコマンドを入力して依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="15a7b-105">Navigate to this new directory and enter the following command to install dependencies.</span></span>

```Shell
npm install
```

<span data-ttu-id="15a7b-106">コマンドが完了したら、次のコマンドを使用してローカル web サーバーを開始します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-106">Once that command completes, use the following command to start a local web server.</span></span>

```Shell
npm start
```

<span data-ttu-id="15a7b-107">ブラウザーを開き、`http://localhost:3000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-107">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="15a7b-108">すべてが動作している場合は、"Welcome to Express" メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="15a7b-108">If everything is working, you will see a "Welcome to Express" message.</span></span> <span data-ttu-id="15a7b-109">このメッセージが表示されない場合は、『 [Express 入門ガイド』](http://expressjs.com/starter/generator.html)を確認してください。</span><span class="sxs-lookup"><span data-stu-id="15a7b-109">If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).</span></span>

<span data-ttu-id="15a7b-110">に進む前に、後で使用する gem をいくつかインストールします。</span><span class="sxs-lookup"><span data-stu-id="15a7b-110">Before moving on, install some additional gems that you will use later:</span></span>

- <span data-ttu-id="15a7b-111">[dotenv](https://github.com/motdotla/dotenv)ファイルから値を読み込むためのものです。</span><span class="sxs-lookup"><span data-stu-id="15a7b-111">[dotenv](https://github.com/motdotla/dotenv) for loading values from a .env file.</span></span>
- <span data-ttu-id="15a7b-112">日付/時刻の値を書式設定する[モーメント](https://github.com/moment/moment/)</span><span class="sxs-lookup"><span data-stu-id="15a7b-112">[moment](https://github.com/moment/moment/) for formatting date/time values.</span></span>
- <span data-ttu-id="15a7b-113">[接続-](https://github.com/jaredhanson/connect-flash)アプリでフラッシュエラーメッセージにフラッシュします。</span><span class="sxs-lookup"><span data-stu-id="15a7b-113">[connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.</span></span>
- <span data-ttu-id="15a7b-114">メモリ内のサーバー側セッションに値を格納するための[エクスプレスセッション](https://github.com/expressjs/session)。</span><span class="sxs-lookup"><span data-stu-id="15a7b-114">[express-session](https://github.com/expressjs/session) to store values in an in-memory server-side session.</span></span>
- <span data-ttu-id="15a7b-115">[パスポート-ad](https://github.com/AzureAD/passport-azure-ad)は、アクセストークンを認証および取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-115">[passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) for authenticating and getting access tokens.</span></span>
- <span data-ttu-id="15a7b-116">トークン管理のための[oauth2](https://github.com/lelylan/simple-oauth2) 。</span><span class="sxs-lookup"><span data-stu-id="15a7b-116">[simple-oauth2](https://github.com/lelylan/simple-oauth2) for token management.</span></span>
- <span data-ttu-id="15a7b-117">[microsoft graph-](https://github.com/microsoftgraph/msgraph-sdk-javascript) microsoft graph に電話をかけるためのクライアントです。</span><span class="sxs-lookup"><span data-stu-id="15a7b-117">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>

<span data-ttu-id="15a7b-118">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-118">Run the following command in your CLI.</span></span>

```Shell
npm install dotenv@6.2.0 moment@2.24.0 connect-flash@0.1.1 express-session@1.15.6
npm install passport-azure-ad@4.0.0 simple-oauth2@2.2.1 @microsoft/microsoft-graph-client@1.5.2
```

> [!TIP]
> <span data-ttu-id="15a7b-119">Windows にこれらのパッケージをインストールしようとすると、Windows ユーザーに次のエラーメッセージが表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="15a7b-119">Windows users may get the following error message when trying to install these packages on Windows.</span></span>
>
> ```Shell
> gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
> ```
>
> <span data-ttu-id="15a7b-120">このエラーを解決するには、次のコマンドを実行して、VS ビルドツールおよび Python をインストールする管理者 (管理者) ターミナルウィンドウを使用して Windows ビルドツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="15a7b-120">To resolve the error, run the following command to install the Windows Build Tools using an elevated (Administrator) terminal window which installs the VS Build Tools and Python.</span></span>
>
> ```Shell
> npm install --global --production windows-build-tools
> ```

<span data-ttu-id="15a7b-121">アプリケーションを更新して、 `connect-flash` `express-session`ミドルウェアを使用できるようにします。</span><span class="sxs-lookup"><span data-stu-id="15a7b-121">Now update the application to use the `connect-flash` and `express-session` middleware.</span></span> <span data-ttu-id="15a7b-122">`./app.js`ファイルを開き、次`require`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-122">Open the `./app.js` file and add the following `require` statement to the top of the file.</span></span>

```js
var session = require('express-session');
var flash = require('connect-flash');
```

<span data-ttu-id="15a7b-123">行の`var app = express();`直後に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-123">Add the following code immediately after the `var app = express();` line.</span></span>

```js
// Session middleware
// NOTE: Uses default in-memory session store, which is not
// suitable for production
app.use(session({
  secret: 'your_secret_value_here',
  resave: false,
  saveUninitialized: false,
  unset: 'destroy'
}));

// Flash middleware
app.use(flash());

// Set up local vars for template layout
app.use(function(req, res, next) {
  // Read any flashed errors and save
  // in the response locals
  res.locals.error = req.flash('error_msg');

  // Check for simple error string and
  // convert to layout's expected format
  var errs = req.flash('error');
  for (var i in errs){
    res.locals.error.push({message: 'An error occurred', debug: errs[i]});
  }

  next();
});
```

## <a name="design-the-app"></a><span data-ttu-id="15a7b-124">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="15a7b-124">Design the app</span></span>

<span data-ttu-id="15a7b-125">最初に、アプリのグローバルレイアウトを作成します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-125">Start by creating the global layout for the app.</span></span> <span data-ttu-id="15a7b-126">`./views/layout.hbs`ファイルを開き、内容全体を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="15a7b-126">Open the `./views/layout.hbs` file and replace the entire contents with the following code.</span></span>

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Node.js Graph Tutorial</title>

    <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/css/bootstrap.min.css"
      integrity="sha384-WskhaSGFgHYWDcbwN70/dfYBj47jz9qbsMId/iRN3ewGhXQFZCSftd1LZCfmhktB" crossorigin="anonymous">
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.1.0/css/all.css"
      integrity="sha384-lKuwvrZot6UHsBSfcMvOkWwlCMgc0TaWr+30HWe3a4ltaBwTZhyTEggF5tJv8tbt" crossorigin="anonymous">
    <link rel='stylesheet' href='/stylesheets/style.css' />
    <script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js"
      integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js"
      integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>
  </head>

  <body>
    <nav class="navbar navbar-expand-md navbar-dark fixed-top bg-dark">
      <div class="container">
        <a href="/" class="navbar-brand">Node.js Graph Tutorial</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarCollapse"
          aria-controls="navbarCollapse" aria-expanded="false" aria-label="Toggle navigation">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarCollapse">
          <ul class="navbar-nav mr-auto">
            <li class="nav-item">
              <a href="/" class="nav-link{{#if active.home}} active{{/if}}">Home</a>
            </li>
            {{#if user}}
              <li class="nav-item" data-turbolinks="false">
                <a href="/calendar" class="nav-link{{#if active.calendar}} active{{/if}}">Calendar</a>
              </li>
            {{/if}}
          </ul>
          <ul class="navbar-nav justify-content-end">
            <li class="nav-item">
              <a class="nav-link" href="https://developer.microsoft.com/graph/docs/concepts/overview" target="_blank">
                <i class="fas fa-external-link-alt mr-1"></i>Docs
              </a>
            </li>
            {{#if user}}
              <li class="nav-item dropdown">
                <a class="nav-link dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true"
                  aria-expanded="false">
                  {{#if user.avatar}}
                    <img src="{{ user.avatar }}" class="rounded-circle align-self-center mr-2" style="width: 32px;">
                  {{else}}
                    <i class="far fa-user-circle fa-lg rounded-circle align-self-center mr-2" style="width: 32px;"></i>
                  {{/if}}
                </a>
                <div class="dropdown-menu dropdown-menu-right">
                  <h5 class="dropdown-item-text mb-0">{{ user.displayName }}</h5>
                  <p class="dropdown-item-text text-muted mb-0">{{ user.email }}</p>
                  <div class="dropdown-divider"></div>
                  <a href="/auth/signout" class="dropdown-item">Sign Out</a>
                </div>
              </li>
            {{else}}
              <li class="nav-item">
                <a href="/auth/signin" class="nav-link">Sign In</a>
              </li>
            {{/if}}
          </ul>
        </div>
      </div>
    </nav>
    <main role="main" class="container">
      {{#each error}}
        <div class="alert alert-danger" role="alert">
          <p class="mb-3">{{ this.message }}</p>
          {{#if this.debug }}
            <pre class="alert-pre border bg-light p-2"><code>{{ this.debug }}</code></pre>
          {{/if}}
        </div>
      {{/each}}

      {{{body}}}
    </main>
  </body>
</html>
```

<span data-ttu-id="15a7b-127">このコードでは、単純なスタイル設定[](https://fontawesome.com/)のために[ブートストラップ](http://getbootstrap.com/)が追加されています。</span><span class="sxs-lookup"><span data-stu-id="15a7b-127">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="15a7b-128">また、ナビゲーションバーのあるグローバルレイアウトを定義します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-128">It also defines a global layout with a nav bar.</span></span>

<span data-ttu-id="15a7b-129">を開き`./public/stylesheets/style.css` 、コンテンツ全体を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="15a7b-129">Now open `./public/stylesheets/style.css` and replace its entire contents with the following.</span></span>

```css
body {
  padding-top: 4.5rem;
}

.alert-pre {
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
}
```

<span data-ttu-id="15a7b-130">ここで、既定のページを更新します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-130">Now update the default page.</span></span> <span data-ttu-id="15a7b-131">`./views/index.hbs`ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="15a7b-131">Open the `./views/index.hbs` file and replace its contents with the following.</span></span>

```html
<div class="jumbotron">
  <h1>Node.js Graph Tutorial</h1>
  <p class="lead">This sample app shows how to use the Microsoft Graph API to access Outlook and OneDrive data from Node.js</p>
  {{#if user}}
    <h4>Welcome {{ user.displayName }}!</h4>
    <p>Use the navigation bar at the top of the page to get started.</p>
  {{else}}
    <a href="/auth/signin" class="btn btn-primary btn-large">Click here to sign in</a>
  {{/if}}
</div>
```

<span data-ttu-id="15a7b-132">`./routes/index.js`ファイルを開き、既存のコードを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="15a7b-132">Open the `./routes/index.js` file and replace the existing code with the following.</span></span>

```js
var express = require('express');
var router = express.Router();

/* GET home page. */
router.get('/', function(req, res, next) {
  let params = {
    active: { home: true }
  };

  res.render('index', params);
});

module.exports = router;
```

<span data-ttu-id="15a7b-133">すべての変更を保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="15a7b-133">Save all of your changes and restart the server.</span></span> <span data-ttu-id="15a7b-134">この時点で、アプリの外観は大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="15a7b-134">Now, the app should look very different.</span></span>

![再設計されたホームページのスクリーンショット](./images/create-app-01.png)