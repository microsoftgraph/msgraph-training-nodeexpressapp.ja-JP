<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="8f099-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="8f099-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="8f099-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="8f099-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="8f099-103">この手順では、アプリケーションに [msal ノード](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) ライブラリを統合します。</span><span class="sxs-lookup"><span data-stu-id="8f099-103">In this step you will integrate the [msal-node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) library into the application.</span></span>

1. <span data-ttu-id="8f099-104">アプリケーションのルートに「 **env** 」という名前の新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="8f099-104">Create a new file named **.env** in the root of your application, and add the following code.</span></span>

    :::code language="ini" source="../demo/graph-tutorial/example.env":::

    <span data-ttu-id="8f099-105">を `YOUR_CLIENT_SECRET_HERE` アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し `YOUR_APP_SECRET_HERE` たクライアントシークレットに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8f099-105">Replace `YOUR_CLIENT_SECRET_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the client secret you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="8f099-106">Git などのソース管理を使用している場合は、後でアプリ ID とパスワードを誤ってリークしないように、ソース管理からその env ファイルを除外することをお勧めし **ます** 。</span><span class="sxs-lookup"><span data-stu-id="8f099-106">If you're using source control such as git, now would be a good time to exclude the **.env** file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="8f099-107">**/app.js** を開き、ファイルの先頭に次の行を追加して、 **env** ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="8f099-107">Open **./app.js** and add the following line to the top of the file to load the **.env** file.</span></span>

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="8f099-108">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="8f099-108">Implement sign-in</span></span>

1. <span data-ttu-id="8f099-109">`var indexRouter = require('./routes/index');` **/app.js** の行を検索します。</span><span class="sxs-lookup"><span data-stu-id="8f099-109">Locate the line `var indexRouter = require('./routes/index');` in **./app.js**.</span></span> <span data-ttu-id="8f099-110">その行の **前に** 次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="8f099-110">Insert the following code **before** that line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="MsalInitSnippet":::

    <span data-ttu-id="8f099-111">このコードでは、アプリのアプリ ID とパスワードを使用して、msal ノードライブラリを初期化します。</span><span class="sxs-lookup"><span data-stu-id="8f099-111">This code initializes the msal-node library with the app ID and password for the app.</span></span>

1. <span data-ttu-id="8f099-112">**auth.js** という名前の **ルート** ディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="8f099-112">Create a new file in the **./routes** directory named **auth.js** and add the following code.</span></span>

    ```javascript
    var router = require('express-promise-router')();

    /* GET auth callback. */
    router.get('/signin',
      async function (req, res) {
        const urlParameters = {
          scopes: process.env.OAUTH_SCOPES.split(','),
          redirectUri: process.env.OAUTH_REDIRECT_URI
        };

        try {
          const authUrl = await req.app.locals
            .msalClient.getAuthCodeUrl(urlParameters);
          res.redirect(authUrl);
        }
        catch (error) {
          console.log(`Error: ${error}`);
          req.flash('error_msg', {
            message: 'Error getting auth URL',
            debug: JSON.stringify(error, Object.getOwnPropertyNames(error))
          });
          res.redirect('/');
        }
      }
    );

    router.get('/callback',
      async function(req, res) {
        const tokenRequest = {
          code: req.query.code,
          scopes: process.env.OAUTH_SCOPES.split(','),
          redirectUri: process.env.OAUTH_REDIRECT_URI
        };

        try {
          const response = await req.app.locals
            .msalClient.acquireTokenByCode(tokenRequest);

          // TEMPORARY!
          // Flash the access token for testing purposes
          req.flash('error_msg', {
            message: 'Access token',
            debug: response.accessToken
          });
        } catch (error) {
          req.flash('error_msg', {
            message: 'Error completing authentication',
            debug: JSON.stringify(error, Object.getOwnPropertyNames(error))
          });
        }

        res.redirect('/');
      }
    );

    router.get('/signout',
      async function(req, res) {
        // Sign out
        if (req.session.userId) {
          // Look up the user's account in the cache
          const accounts = await req.app.locals.msalClient
            .getTokenCache()
            .getAllAccounts();

          const userAccount = accounts.find(a => a.homeAccountId === req.session.userId);

          // Remove the account
          if (userAccount) {
            req.app.locals.msalClient
              .getTokenCache()
              .removeAccount(userAccount);
          }
        }

        // Destroy the user's session
        req.session.destroy(function (err) {
          res.redirect('/');
        });
      }
    );

    module.exports = router;
    ```

    <span data-ttu-id="8f099-113">これは `signin` 、、、およびの3つのルートでルーターを定義します。 `callback` `signout`</span><span class="sxs-lookup"><span data-stu-id="8f099-113">This defines a router with three routes: `signin`, `callback`, and `signout`.</span></span>

    <span data-ttu-id="8f099-114">この `signin` ルートは、関数を呼び出してログイン url を生成し、 `getAuthCodeUrl` その url にブラウザーをリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="8f099-114">The `signin` route calls the `getAuthCodeUrl` function to generate the login URL, then redirects the browser to that URL.</span></span>

    <span data-ttu-id="8f099-115">この `callback` ルートは、サインインの完了後に Azure がリダイレクトされる場所です。</span><span class="sxs-lookup"><span data-stu-id="8f099-115">The `callback` route is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="8f099-116">このコードでは、 `acquireTokenByCode` アクセストークンの認証コードを交換するための関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="8f099-116">The code calls the `acquireTokenByCode` function to exchange the authorization code for an access token.</span></span> <span data-ttu-id="8f099-117">トークンが取得されると、一時的なエラー値でアクセストークンを使用して、ホームページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="8f099-117">Once the token is obtained, it redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="8f099-118">これを使用して、サインインが機能していることを確認してから、に進みます。</span><span class="sxs-lookup"><span data-stu-id="8f099-118">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="8f099-119">テストを開始する前に、 **/routes/auth.js** から新しいルーターを使用するようにエクスプレスアプリを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8f099-119">Before we test, we need to configure the Express app to use the new router from **./routes/auth.js**.</span></span>

    <span data-ttu-id="8f099-120">メソッドは、 `signout` ユーザーをログに記録し、セッションを破棄します。</span><span class="sxs-lookup"><span data-stu-id="8f099-120">The `signout` method logs the user out and destroys the session.</span></span>

1. <span data-ttu-id="8f099-121">/app.jsを開き、行の **前に** 次のコードを挿入し **ます。** `var app = express();`</span><span class="sxs-lookup"><span data-stu-id="8f099-121">Open **./app.js** and insert the following code **before** the `var app = express();` line.</span></span>

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. <span data-ttu-id="8f099-122">行の **後** に次のコードを挿入し `app.use('/', indexRouter);` ます。</span><span class="sxs-lookup"><span data-stu-id="8f099-122">Insert the following code **after** the `app.use('/', indexRouter);` line.</span></span>

    ```javascript
    app.use('/auth', authRouter);
    ```

<span data-ttu-id="8f099-123">サーバーを起動し、を参照し `https://localhost:3000` ます。</span><span class="sxs-lookup"><span data-stu-id="8f099-123">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="8f099-124">[サインイン] ボタンをクリックすると、`https://login.microsoftonline.com` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="8f099-124">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="8f099-125">Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。</span><span class="sxs-lookup"><span data-stu-id="8f099-125">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="8f099-126">ブラウザーがアプリにリダイレクトし、トークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="8f099-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="8f099-127">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="8f099-127">Get user details</span></span>

1. <span data-ttu-id="8f099-128">**graph.js** という名前のプロジェクトのルートに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="8f099-128">Create a new file in the root of the project named **graph.js** and add the following code.</span></span>

    ```javascript
    var graph = require('@microsoft/microsoft-graph-client');
    require('isomorphic-fetch');

    module.exports = {
      getUserDetails: async function(accessToken) {
        const client = getAuthenticatedClient(accessToken);

        const user = await client
          .api('/me')
          .select('displayName,mail,mailboxSettings,userPrincipalName')
          .get();
        return user;
      },
    };

    function getAuthenticatedClient(accessToken) {
      // Initialize Graph client
      const client = graph.Client.init({
        // Use the provided access token to authenticate
        // requests
        authProvider: (done) => {
          done(null, accessToken);
        }
      });

      return client;
    }
    ```

    <span data-ttu-id="8f099-129">これにより、関数がエクスポートされます。これは、 `getUserDetails` Microsoft GRAPH SDK を使用してエンドポイントを呼び出し、 `/me` 結果を返します。</span><span class="sxs-lookup"><span data-stu-id="8f099-129">This exports the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="8f099-130">**/Routes/auth.js** を開き、次の `require` ステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="8f099-130">Open **./routes/auth.js** and add the following `require` statements to the top of the file.</span></span>

    ```javascript
    var graph = require('../graph');
    ```

1. <span data-ttu-id="8f099-131">既存のコールバックルートを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="8f099-131">Replace the existing callback route with the following code.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackSnippet" highlight="13-23":::

    <span data-ttu-id="8f099-132">新しいコードによって、ユーザーのアカウント ID がセッションに保存され、Microsoft Graph からユーザーの詳細が取得され、アプリのユーザーストレージに保存されます。</span><span class="sxs-lookup"><span data-stu-id="8f099-132">The new code saves the user's account ID in the session, gets the user's details from Microsoft Graph, and saves it in the app's user storage.</span></span>

1. <span data-ttu-id="8f099-133">サーバーを再起動し、サインインプロセスを実行します。</span><span class="sxs-lookup"><span data-stu-id="8f099-133">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="8f099-134">ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8f099-134">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. <span data-ttu-id="8f099-136">右上隅にあるユーザーアバターをクリックして、[ **サインアウト** ] リンクにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="8f099-136">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="8f099-137">**[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="8f099-137">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="8f099-139">トークンの格納と更新</span><span class="sxs-lookup"><span data-stu-id="8f099-139">Storing and refreshing tokens</span></span>

<span data-ttu-id="8f099-140">この時点で、アプリケーションには、API 呼び出しのヘッダーで送信されるアクセストークンがあり `Authorization` ます。</span><span class="sxs-lookup"><span data-stu-id="8f099-140">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="8f099-141">これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。</span><span class="sxs-lookup"><span data-stu-id="8f099-141">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="8f099-142">ただし、このトークンは一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="8f099-142">However, this token is short-lived.</span></span> <span data-ttu-id="8f099-143">トークンが発行された後、有効期限が切れる時間になります。</span><span class="sxs-lookup"><span data-stu-id="8f099-143">The token expires an hour after it is issued.</span></span> <span data-ttu-id="8f099-144">ここで、更新トークンが役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="8f099-144">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="8f099-145">OAuth 仕様では、更新トークンが導入されています。これにより、ユーザーが再度サインインする必要なく、新しいアクセストークンを要求することができます。</span><span class="sxs-lookup"><span data-stu-id="8f099-145">The OAuth specification introduces a refresh token, which allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="8f099-146">アプリでは msal ノードパッケージが使用されているため、トークンストレージや更新ロジックを実装する必要はありません。</span><span class="sxs-lookup"><span data-stu-id="8f099-146">Because the app is using the msal-node package, you do not need to implement any token storage or refresh logic.</span></span> <span data-ttu-id="8f099-147">アプリは、サンプルアプリケーションに十分な既定の msal ノードのメモリ内トークンキャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="8f099-147">The app uses the default msal-node in-memory token cache, which is sufficient for a sample application.</span></span> <span data-ttu-id="8f099-148">運用アプリケーションは、トークンキャッシュをセキュリティで保護された信頼性の高いストレージメディアにシリアル化するための独自の [キャッシュプラグイン](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) を提供する必要があります。</span><span class="sxs-lookup"><span data-stu-id="8f099-148">Production applications should provide their own [caching plugin](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) to serialize the token cache in a secure, reliable storage medium.</span></span>
