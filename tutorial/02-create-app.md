<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="af0d1-101">この演習では [、Express を使用して Web](http://expressjs.com/) アプリをビルドします。</span><span class="sxs-lookup"><span data-stu-id="af0d1-101">In this exercise you will use [Express](http://expressjs.com/) to build a web app.</span></span>

1. <span data-ttu-id="af0d1-102">CLI を開き、ファイルを作成する権限を持つディレクトリに移動し、次のコマンドを実行して、ハンドルバーを[](http://handlebarsjs.com/)レンダリング エンジンとして使用する新しい Express アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-102">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Express app that uses [Handlebars](http://handlebarsjs.com/) as the rendering engine.</span></span>

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    <span data-ttu-id="af0d1-103">Express ジェネレーターは、Express アプリと呼ばれる新しいディレクトリを作成し `graph-tutorial` 、スキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="af0d1-103">The Express generator creates a new directory called `graph-tutorial` and scaffolds an Express app.</span></span>

1. <span data-ttu-id="af0d1-104">ディレクトリに移動 `graph-tutorial` し、次のコマンドを入力して依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="af0d1-104">Navigate to the `graph-tutorial` directory and enter the following command to install dependencies.</span></span>

    ```Shell
    npm install
    ```

1. <span data-ttu-id="af0d1-105">次のコマンドを実行して、報告された脆弱性でノード パッケージを更新します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-105">Run the following command to update Node packages with reported vulnerabilities.</span></span>

    ```Shell
    npm audit fix
    ```

1. <span data-ttu-id="af0d1-106">次のコマンドを実行して、Express および他の依存関係のバージョンを更新します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-106">Run the following command to update the version of Express and other dependencies.</span></span>

    ```Shell
    npm install express@4.17.1 http-errors@1.8.0 morgan@1.10.0 debug@4.3.1 hbs@4.1.2
    ```

1. <span data-ttu-id="af0d1-107">ローカル Web サーバーを起動するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-107">Use the following command to start a local web server.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="af0d1-108">ブラウザーを開き、`http://localhost:3000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-108">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="af0d1-109">すべてが機能している場合は、"Express へようこそ" というメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="af0d1-109">If everything is working, you will see a "Welcome to Express" message.</span></span> <span data-ttu-id="af0d1-110">そのメッセージが表示できない場合は、「Express の開始ガイド [」を参照してください](http://expressjs.com/starter/generator.html)。</span><span class="sxs-lookup"><span data-stu-id="af0d1-110">If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).</span></span>

## <a name="install-node-packages"></a><span data-ttu-id="af0d1-111">ノード パッケージのインストール</span><span class="sxs-lookup"><span data-stu-id="af0d1-111">Install Node packages</span></span>

<span data-ttu-id="af0d1-112">次に進む前に、後で使用する追加のパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="af0d1-112">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="af0d1-113">.env ファイルから値を読み込む場合の[dotenv。](https://github.com/motdotla/dotenv)</span><span class="sxs-lookup"><span data-stu-id="af0d1-113">[dotenv](https://github.com/motdotla/dotenv) for loading values from a .env file.</span></span>
- <span data-ttu-id="af0d1-114">[日付/時刻の値を書式設定する date-fns。](https://github.com/date-fns/date-fns)</span><span class="sxs-lookup"><span data-stu-id="af0d1-114">[date-fns](https://github.com/date-fns/date-fns) for formatting date/time values.</span></span>
- <span data-ttu-id="af0d1-115">[windows-iana](https://github.com/rubenillodo/windows-iana)を使用して、Windowsタイム ゾーン名を IANA タイム ゾーンの ID に変換します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-115">[windows-iana](https://github.com/rubenillodo/windows-iana) for translating Windows time zone names to IANA time zone IDs.</span></span>
- <span data-ttu-id="af0d1-116">[アプリ内のフラッシュ](https://github.com/jaredhanson/connect-flash) エラー メッセージに接続します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-116">[connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.</span></span>
- <span data-ttu-id="af0d1-117">[メモリ内のサーバー](https://github.com/expressjs/session) 側セッションに値を格納する express-session。</span><span class="sxs-lookup"><span data-stu-id="af0d1-117">[express-session](https://github.com/expressjs/session) to store values in an in-memory server-side session.</span></span>
- <span data-ttu-id="af0d1-118">ルート ハンドラーが Promise を返すのを許可する[express-promise-router。](https://github.com/express-promise-router/express-promise-router)</span><span class="sxs-lookup"><span data-stu-id="af0d1-118">[express-promise-router](https://github.com/express-promise-router/express-promise-router) to allow route handlers to return a Promise.</span></span>
- <span data-ttu-id="af0d1-119">[フォーム データを解析](https://github.com/express-validator/express-validator) および検証するための express-validator。</span><span class="sxs-lookup"><span data-stu-id="af0d1-119">[express-validator](https://github.com/express-validator/express-validator) for parsing and validating form data.</span></span>
- <span data-ttu-id="af0d1-120">[アクセス トークンを認証](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) および取得する msal-node。</span><span class="sxs-lookup"><span data-stu-id="af0d1-120">[msal-node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) for authenticating and getting access tokens.</span></span>
- <span data-ttu-id="af0d1-121">[microsoft-graph-client for making](https://github.com/microsoftgraph/msgraph-sdk-javascript) calls to Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="af0d1-121">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="af0d1-122">[isomorphic-fetch to](https://github.com/matthew-andrews/isomorphic-fetch) polyfill the fetch for Node.</span><span class="sxs-lookup"><span data-stu-id="af0d1-122">[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) to polyfill the fetch for Node.</span></span> <span data-ttu-id="af0d1-123">ライブラリにはフェッチ ポリフィルが必要 `microsoft-graph-client` です。</span><span class="sxs-lookup"><span data-stu-id="af0d1-123">A fetch polyfill is required for the `microsoft-graph-client` library.</span></span> <span data-ttu-id="af0d1-124">詳細については[、「Microsoft Graph JavaScript クライアント ライブラリ wiki」](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required)を参照してください。</span><span class="sxs-lookup"><span data-stu-id="af0d1-124">See the [Microsoft Graph JavaScript client library wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) for more information.</span></span>

1. <span data-ttu-id="af0d1-125">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-125">Run the following command in your CLI.</span></span>

    ```Shell
    npm install dotenv@8.2.0 date-fns@2.21.1 date-fns-tz@1.1.4 connect-flash@0.1.1 express-validator@6.10.0
    npm install express-session@1.17.1 express-promise-router@4.1.0 isomorphic-fetch@3.0.0
    npm install @azure/msal-node@1.0.2 @microsoft/microsoft-graph-client@2.2.1 windows-iana@5.0.1
    ```

    > [!TIP]
    > <span data-ttu-id="af0d1-126">Windowsにこれらのパッケージをインストールしようとするときに、次のエラー メッセージが表示される場合Windows。</span><span class="sxs-lookup"><span data-stu-id="af0d1-126">Windows users may get the following error message when trying to install these packages on Windows.</span></span>
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > <span data-ttu-id="af0d1-127">エラーを解決するには、次のコマンドを実行して、VS ビルド ツールと Python をインストールする管理者特権 (管理者) ターミナル ウィンドウを使用して Windows ビルド ツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="af0d1-127">To resolve the error, run the following command to install the Windows Build Tools using an elevated (Administrator) terminal window which installs the VS Build Tools and Python.</span></span>
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. <span data-ttu-id="af0d1-128">アプリケーションを更新して、ミドルウェア `connect-flash` を使用 `express-session` します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-128">Update the application to use the `connect-flash` and `express-session` middleware.</span></span> <span data-ttu-id="af0d1-129">**./app.js** を開き、ファイルの `require` 上部に次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-129">Open **./app.js** and add the following `require` statement to the top of the file.</span></span>

    ```javascript
    const session = require('express-session');
    const flash = require('connect-flash');
    const msal = require('@azure/msal-node');
    ```

1. <span data-ttu-id="af0d1-130">行の直後に次のコードを追加 `var app = express();` します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-130">Add the following code immediately after the `var app = express();` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a><span data-ttu-id="af0d1-131">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="af0d1-131">Design the app</span></span>

<span data-ttu-id="af0d1-132">このセクションでは、アプリの UI を実装します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-132">In this section you will implement the UI for the app.</span></span>

1. <span data-ttu-id="af0d1-133">**./views/layout.hbs** を開き、コンテンツ全体を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="af0d1-133">Open **./views/layout.hbs** and replace the entire contents with the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    <span data-ttu-id="af0d1-134">このコードはシンプルなスタイルのために [Bootstrap](http://getbootstrap.com/) を追加し、シンプルなアイコンのために [Font Awesome](https://fontawesome.com/) を追加します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-134">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="af0d1-135">また、ナビゲーション バーを持つグローバル レイアウトも定義します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-135">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="af0d1-136">**./public/stylesheets/style.css** を開き、その内容全体を次に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="af0d1-136">Open **./public/stylesheets/style.css** and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. <span data-ttu-id="af0d1-137">**./views/index.hbs** を開き、その内容を次に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="af0d1-137">Open **./views/index.hbs** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. <span data-ttu-id="af0d1-138">**./routes/index.js** 開き、既存のコードを次に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="af0d1-138">Open **./routes/index.js** and replace the existing code with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. <span data-ttu-id="af0d1-139">変更内容をすべて保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="af0d1-139">Save all of your changes and restart the server.</span></span> <span data-ttu-id="af0d1-140">これで、アプリは非常に異なって見える必要があります。</span><span class="sxs-lookup"><span data-stu-id="af0d1-140">Now, the app should look very different.</span></span>

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
