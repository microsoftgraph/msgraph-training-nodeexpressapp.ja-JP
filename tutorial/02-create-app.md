<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="7f4ce-101">この手順では、 [Express](http://expressjs.com/)を使用して web アプリを構築します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-101">In this exercise you will use [Express](http://expressjs.com/) to build a web app.</span></span>

1. <span data-ttu-id="7f4ce-102">CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して、 [Handlebars](http://handlebarsjs.com/)をレンダリングエンジンとして使用する新しいエクスプレスアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-102">Open your CLI, navigate to a directory where you have rights to create files, and run the following command to create a new Express app that uses [Handlebars](http://handlebarsjs.com/) as the rendering engine.</span></span>

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    <span data-ttu-id="7f4ce-103">エクスプレスジェネレーターは、という名前の`graph-tutorial`新しいディレクトリを作成し、スキャフォールディングというエクスプレスアプリを作成します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-103">The Express generator creates a new directory called `graph-tutorial` and scaffolds an Express app.</span></span>

1. <span data-ttu-id="7f4ce-104">`graph-tutorial`ディレクトリに移動し、次のコマンドを入力して依存関係をインストールします。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-104">Navigate to the `graph-tutorial` directory and enter the following command to install dependencies.</span></span>

    ```Shell
    npm install
    ```

1. <span data-ttu-id="7f4ce-105">報告された脆弱性でノードパッケージを更新するには、次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-105">Run the following command to update Node packages with reported vulnerabilities.</span></span>

    ```Shell
    npm audit fix
    ```

1. <span data-ttu-id="7f4ce-106">次のコマンドを使用して、ローカル web サーバーを開始します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-106">Use the following command to start a local web server.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="7f4ce-107">ブラウザーを開き、`http://localhost:3000` に移動します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-107">Open your browser and navigate to `http://localhost:3000`.</span></span> <span data-ttu-id="7f4ce-108">すべてが動作している場合は、"Welcome to Express" メッセージが表示されます。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-108">If everything is working, you will see a "Welcome to Express" message.</span></span> <span data-ttu-id="7f4ce-109">このメッセージが表示されない場合は、『 [Express 入門ガイド』](http://expressjs.com/starter/generator.html)を確認してください。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-109">If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).</span></span>

## <a name="install-node-packages"></a><span data-ttu-id="7f4ce-110">ノードパッケージをインストールする</span><span class="sxs-lookup"><span data-stu-id="7f4ce-110">Install Node packages</span></span>

<span data-ttu-id="7f4ce-111">に進む前に、後で使用する追加のパッケージをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-111">Before moving on, install some additional packages that you will use later:</span></span>

- <span data-ttu-id="7f4ce-112">[dotenv](https://github.com/motdotla/dotenv)ファイルから値を読み込むためのものです。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-112">[dotenv](https://github.com/motdotla/dotenv) for loading values from a .env file.</span></span>
- <span data-ttu-id="7f4ce-113">日付/時刻の値を書式設定する[モーメント](https://github.com/moment/moment/)</span><span class="sxs-lookup"><span data-stu-id="7f4ce-113">[moment](https://github.com/moment/moment/) for formatting date/time values.</span></span>
- <span data-ttu-id="7f4ce-114">[接続-](https://github.com/jaredhanson/connect-flash)アプリでフラッシュエラーメッセージにフラッシュします。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-114">[connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.</span></span>
- <span data-ttu-id="7f4ce-115">メモリ内のサーバー側セッションに値を格納するための[エクスプレスセッション](https://github.com/expressjs/session)。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-115">[express-session](https://github.com/expressjs/session) to store values in an in-memory server-side session.</span></span>
- <span data-ttu-id="7f4ce-116">[パスポート-ad](https://github.com/AzureAD/passport-azure-ad)は、アクセストークンを認証および取得するために使用します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-116">[passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) for authenticating and getting access tokens.</span></span>
- <span data-ttu-id="7f4ce-117">トークン管理のための[oauth2](https://github.com/lelylan/simple-oauth2) 。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-117">[simple-oauth2](https://github.com/lelylan/simple-oauth2) for token management.</span></span>
- <span data-ttu-id="7f4ce-118">[microsoft graph-](https://github.com/microsoftgraph/msgraph-sdk-javascript) microsoft graph に電話をかけるためのクライアントです。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-118">[microsoft-graph-client](https://github.com/microsoftgraph/msgraph-sdk-javascript) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="7f4ce-119">[isomorphic](https://github.com/matthew-andrews/isomorphic-fetch) 。ノードのフェッチを polyfill にフェッチします。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-119">[isomorphic-fetch](https://github.com/matthew-andrews/isomorphic-fetch) to polyfill the fetch for Node.</span></span> <span data-ttu-id="7f4ce-120">`microsoft-graph-client`ライブラリには fetch polyfill が必要です。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-120">A fetch polyfill is required for the `microsoft-graph-client` library.</span></span> <span data-ttu-id="7f4ce-121">詳細については、「 [Microsoft Graph JavaScript クライアントライブラリ wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) 」を参照してください。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-121">See the [Microsoft Graph JavaScript client library wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) for more information.</span></span>

1. <span data-ttu-id="7f4ce-122">CLI で次のコマンドを実行します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-122">Run the following command in your CLI.</span></span>

    ```Shell
    npm install dotenv@8.2.0 moment@2.24.0 connect-flash@0.1.1 express-session@1.17.0 isomorphic-fetch@2.2.1
    npm install passport-azure-ad@4.2.1 simple-oauth2@3.3.0 @microsoft/microsoft-graph-client@2.0.0
    ```

    > [!TIP]
    > <span data-ttu-id="7f4ce-123">Windows にこれらのパッケージをインストールしようとすると、windows ユーザーに次のエラーメッセージが表示されることがあります。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-123">Windows users may get the following error message when trying to install these packages on Windows.</span></span>
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > <span data-ttu-id="7f4ce-124">このエラーを解決するには、次のコマンドを実行して、VS ビルドツールおよび Python をインストールする管理者 (管理者) ターミナルウィンドウを使用して Windows ビルドツールをインストールします。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-124">To resolve the error, run the following command to install the Windows Build Tools using an elevated (Administrator) terminal window which installs the VS Build Tools and Python.</span></span>
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. <span data-ttu-id="7f4ce-125">`connect-flash`と`express-session`ミドルウェアを使用するようにアプリケーションを更新します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-125">Update the application to use the `connect-flash` and `express-session` middleware.</span></span> <span data-ttu-id="7f4ce-126">`./app.js`ファイルを開き、次`require`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-126">Open the `./app.js` file and add the following `require` statement to the top of the file.</span></span>

    ```javascript
    var session = require('express-session');
    var flash = require('connect-flash');
    ```

1. <span data-ttu-id="7f4ce-127">行の`var app = express();`直後に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-127">Add the following code immediately after the `var app = express();` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a><span data-ttu-id="7f4ce-128">アプリを設計する</span><span class="sxs-lookup"><span data-stu-id="7f4ce-128">Design the app</span></span>

<span data-ttu-id="7f4ce-129">このセクションでは、アプリの UI を実装します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-129">In this section you will implement the UI for the app.</span></span>

1. <span data-ttu-id="7f4ce-130">`./views/layout.hbs`ファイルを開き、内容全体を次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-130">Open the `./views/layout.hbs` file and replace the entire contents with the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    <span data-ttu-id="7f4ce-131">このコードはシンプルなスタイルのために [Bootstrap](http://getbootstrap.com/) を追加し、シンプルなアイコンのために [Font Awesome](https://fontawesome.com/) を追加します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-131">This code adds [Bootstrap](http://getbootstrap.com/) for simple styling, and [Font Awesome](https://fontawesome.com/) for some simple icons.</span></span> <span data-ttu-id="7f4ce-132">また、ナビゲーションバーのあるグローバルレイアウトを定義します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-132">It also defines a global layout with a nav bar.</span></span>

1. <span data-ttu-id="7f4ce-133">を`./public/stylesheets/style.css`開き、内容全体を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-133">Open `./public/stylesheets/style.css` and replace its entire contents with the following.</span></span>

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. <span data-ttu-id="7f4ce-134">`./views/index.hbs`ファイルを開き、その内容を次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-134">Open the `./views/index.hbs` file and replace its contents with the following.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. <span data-ttu-id="7f4ce-135">`./routes/index.js`ファイルを開き、既存のコードを次のように置き換えます。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-135">Open the `./routes/index.js` file and replace the existing code with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. <span data-ttu-id="7f4ce-136">変更内容をすべて保存し、サーバーを再起動します。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-136">Save all of your changes and restart the server.</span></span> <span data-ttu-id="7f4ce-137">この時点で、アプリの外観は大きく異なります。</span><span class="sxs-lookup"><span data-stu-id="7f4ce-137">Now, the app should look very different.</span></span>

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
