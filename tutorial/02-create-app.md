<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="bef2b-101">この演習では [、Express を使用して Web](http://expressjs.com/) アプリを構築します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-101">In this exercise you will use [Express](http://expressjs.com/) to build a web app.</span></span>

1. <span data-ttu-id="bef2b-102">CLI を開き、ファイルを作成する権限を持つディレクトリに移動し、次のコマンドを実行して、レンダリング エンジンとして [Handlebars](http://handlebarsjs.com/) を使用する新しい Express アプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-102">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Express app that uses [Handlebars](http://handlebarsjs.com/) as the rendering engine.</span></span>

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    <span data-ttu-id="bef2b-103">Express ジェネレーターは、Express アプリと呼ばれる新しいディレクトリ `graph-tutorial` を作成し、スキャフォールディングします。</span><span class="sxs-lookup"><span data-stu-id="bef2b-103">The Express generator creates a new directory called `graph-tutorial` and scaffolds an Express app.</span></span>

1. <span data-ttu-id="bef2b-104">ディレクトリに移動 `graph-tutorial` し、次のコマンドを入力して依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="bef2b-104">Navigate to the `graph-tutorial` directory and enter the following command to install dependencies.</span></span>

    ```Shell
    npm install
    ```

1. <span data-ttu-id="bef2b-105">次のコマンドを実行して、報告された脆弱性を持つノード パッケージを更新します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-105">Run the following command to update Node packages with reported vulnerabilities.</span></span>

    ```Shell
    npm audit fix
    ```

1. <span data-ttu-id="bef2b-106">次のコマンドを実行して、Express のバージョンと他の依存関係を更新します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-106">Run the following command to update the version of Express and other dependencies.</span></span>

    ```Shell
    npm install express@4.17.1 http-errors@1.8.0 morgan@1.10.0 debug@4.1.1
    ```

1. <span data-ttu-id="bef2b-107">ローカル Web サーバーを起動するには、次のコマンドを使用します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-107">Use the following command to start a local web server.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="bef2b-108">ブラウザーを開き、`http://localhost:3000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-108">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="bef2b-109">すべてが動作している場合は、"Express へようこそ" というメッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="bef2b-109">If everything is working, you will see a "Welcome to Express" message.</span></span> <span data-ttu-id="bef2b-110">If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).</span><span class="sxs-lookup"><span data-stu-id="bef2b-110">If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).</span></span>

## <a name="install-node-packages"></a><span data-ttu-id="bef2b-111">ノード パッケージのインストール</span><span class="sxs-lookup"><span data-stu-id="bef2b-111">Install Node packages</span></span>

<span data-ttu-id="bef2b-112">次に進む前に、後で使用する追加のパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="bef2b-112">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="bef2b-113">.env ファイルから値を読み込む場合の[dotenv。](https://github.com/motdotla/dotenv)</span><span class="sxs-lookup"><span data-stu-id="bef2b-113">[dotenv](https://github.com/motdotla/dotenv) for loading values from a .env file.</span></span>
- <span data-ttu-id="bef2b-114">[moment](https://github.com/moment/moment/) for formatting date/time values.</span><span class="sxs-lookup"><span data-stu-id="bef2b-114">[moment](https://github.com/moment/moment/) for formatting date/time values.</span></span>
- <span data-ttu-id="bef2b-115">[Windows タイム ゾーン名を](https://github.com/rubenillodo/windows-iana) IANA タイム ゾーンの ID に変換する windows-iana。</span><span class="sxs-lookup"><span data-stu-id="bef2b-115">[windows-iana](https://github.com/rubenillodo/windows-iana) for translating Windows time zone names to IANA time zone IDs.</span></span>
- <span data-ttu-id="bef2b-116">[connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.</span><span class="sxs-lookup"><span data-stu-id="bef2b-116">[connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.</span></span>
- <span data-ttu-id="bef2b-117">[メモリ内の](https://github.com/expressjs/session) サーバー側セッションに値を格納する高速セッション。</span><span class="sxs-lookup"><span data-stu-id="bef2b-117">[express-session](https://github.com/expressjs/session) to store values in an in-memory server-side session.</span></span>
- <span data-ttu-id="bef2b-118">ルート ハンドラーが Promise を返す[express-promise-router。](https://github.com/express-promise-router/express-promise-router)</span><span class="sxs-lookup"><span data-stu-id="bef2b-118">[express-promise-router](https://github.com/express-promise-router/express-promise-router) to allow route handlers to return a Promise.</span></span>
- <span data-ttu-id="bef2b-119">[フォーム データを解析](https://github.com/express-validator/express-validator) および検証するための高速検証ツール。</span><span class="sxs-lookup"><span data-stu-id="bef2b-119">[express-validator](https://github.com/express-validator/express-validator) for parsing and validating form data.</span></span>
- <span data-ttu-id="bef2b-120">[アクセス トークンを認証](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) および取得する msal-node。</span><span class="sxs-lookup"><span data-stu-id="bef2b-120">[msal-node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) for authenticating and getting access tokens.</span></span>
- <span data-ttu-id="bef2b-121">GUID を生成するために msal-node によって使用される[uuid。](https://github.com/uuidjs/uuid)</span><span class="sxs-lookup"><span data-stu-id="bef2b-121">[uuid](https://github.com/uuidjs/uuid) used by msal-node to generate GUIDs.</span></span>
- <span data-ttu-id="bef2b-122">[Microsoft Graph を呼び](https://github.com/microsoftgraph/msgraph-sdk-javascript) 出す microsoft-graph-client。</span><span class="sxs-lookup"><span data-stu-id="bef2b-122">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="bef2b-123">[node のフェッチをポリフィルする isomorphic-fetch。](https://github.com/matthew-andrews/isomorphic-fetch)</span><span class="sxs-lookup"><span data-stu-id="bef2b-123">[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) to polyfill the fetch for Node.</span></span> <span data-ttu-id="bef2b-124">ライブラリにはフェッチ ポリフィルが必要 `microsoft-graph-client` です。</span><span class="sxs-lookup"><span data-stu-id="bef2b-124">A fetch polyfill is required for the `microsoft-graph-client` library.</span></span> <span data-ttu-id="bef2b-125">詳細については [、Microsoft Graph JavaScript クライアント ライブラリ Wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) を参照してください。</span><span class="sxs-lookup"><span data-stu-id="bef2b-125">See the [Microsoft Graph JavaScript client library wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) for more information.</span></span>

1. <span data-ttu-id="bef2b-126">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-126">Run the following command in your CLI.</span></span>

    ```Shell
    npm install dotenv@8.2.0 moment@2.29.1 moment-timezone@0.5.31 connect-flash@0.1.1 express-session@1.17.1 express-promise-router@4.0.1 isomorphic-fetch@3.0.0
    npm install @azure/msal-node@1.0.0-beta.0 @microsoft/microsoft-graph-client@2.1.1 windows-iana@4.2.1 express-validator@6.6.1 uuid@8.3.1
    ```

    > [!TIP]
    > <span data-ttu-id="bef2b-127">これらのパッケージを Windows にインストールしようとするときに、Windows ユーザーに次のエラー メッセージが表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="bef2b-127">Windows users may get the following error message when trying to install these packages on Windows.</span></span>
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > <span data-ttu-id="bef2b-128">このエラーを解決するには、次のコマンドを実行して、管理者特権の (管理者) ターミナル ウィンドウを使用して Windows ビルド ツールをインストールします。このウィンドウでは、VS Build Tools と Python がインストールされます。</span><span class="sxs-lookup"><span data-stu-id="bef2b-128">To resolve the error, run the following command to install the Windows Build Tools using an elevated (Administrator) terminal window which installs the VS Build Tools and Python.</span></span>
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. <span data-ttu-id="bef2b-129">アプリケーションを更新して、ミドルウェアを `connect-flash` 使用 `express-session` します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-129">Update the application to use the `connect-flash` and `express-session` middleware.</span></span> <span data-ttu-id="bef2b-130">**./app.js** を開き、ファイルの一番上に `require` 次のステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-130">Open **./app.js** and add the following `require` statement to the top of the file.</span></span>

    ```javascript
    const session = require('express-session');
    const flash = require('connect-flash');
    const msal = require('@azure/msal-node');
    ```

1. <span data-ttu-id="bef2b-131">行の直後に次のコードを追加 `var app = express();` します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-131">Add the following code immediately after the `var app = express();` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a><span data-ttu-id="bef2b-132">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="bef2b-132">Design the app</span></span>

<span data-ttu-id="bef2b-133">このセクションでは、アプリの UI を実装します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-133">In this section you will implement the UI for the app.</span></span>

1. <span data-ttu-id="bef2b-134">**./views/layout.hs** を開き、内容全体を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bef2b-134">Open **./views/layout.hbs** and replace the entire contents with the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    <span data-ttu-id="bef2b-135">このコードはシンプルなスタイルのために [Bootstrap](http://getbootstrap.com/) を追加し、シンプルなアイコンのために [Font Awesome](https://fontawesome.com/) を追加します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-135">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="bef2b-136">また、ナビゲーション バーを含むグローバル レイアウトも定義します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-136">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="bef2b-137">**./public/stylesheets/style.css** を開き、その内容全体を次の内容に置き換えてください。</span><span class="sxs-lookup"><span data-stu-id="bef2b-137">Open **./public/stylesheets/style.css** and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. <span data-ttu-id="bef2b-138">**./views/index.hs** を開き、その内容を次の内容に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bef2b-138">Open **./views/index.hbs** and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. <span data-ttu-id="bef2b-139">**./routes/index.js** 開き、既存のコードを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="bef2b-139">Open **./routes/index.js** and replace the existing code with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. <span data-ttu-id="bef2b-140">変更内容をすべて保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="bef2b-140">Save all of your changes and restart the server.</span></span> <span data-ttu-id="bef2b-141">これで、アプリは非常に異なって表示されます。</span><span class="sxs-lookup"><span data-stu-id="bef2b-141">Now, the app should look very different.</span></span>

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
