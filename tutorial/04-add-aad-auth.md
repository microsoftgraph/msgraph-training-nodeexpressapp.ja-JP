<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="3d1aa-101">この演習では、前の演習のアプリケーションを拡張して、Azure AD での認証をサポートします。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="3d1aa-102">これは、Microsoft Graph を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="3d1aa-103">この手順では [、msal-node ライブラリを](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) アプリケーションに統合します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-103">In this step you will integrate the [msal-node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) library into the application.</span></span>

1. <span data-ttu-id="3d1aa-104">アプリケーションのルートに **.env** という名前の新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-104">Create a new file named **.env** in the root of your application, and add the following code.</span></span>

    :::code language="ini" source="../demo/graph-tutorial/example.env":::

    <span data-ttu-id="3d1aa-105">アプリケーション `YOUR_CLIENT_SECRET_HERE` 登録ポータルのアプリケーション ID に置き換え、生成したクライアント シークレット `YOUR_APP_SECRET_HERE` に置き換える。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-105">Replace `YOUR_CLIENT_SECRET_HERE` with the application ID from the Application Registration Portal, and replace `YOUR_APP_SECRET_HERE` with the client secret you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="3d1aa-106">git などのソース管理を使用している場合は **、.env** ファイルをソース管理から除外して、アプリ ID とパスワードが誤って漏洩しないようにする良い時期です。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-106">If you're using source control such as git, now would be a good time to exclude the **.env** file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="3d1aa-107">**./app.js** 開き、ファイルの一番上に次の行を追加して **.env ファイルを読み込** む。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-107">Open **./app.js** and add the following line to the top of the file to load the **.env** file.</span></span>

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="3d1aa-108">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="3d1aa-108">Implement sign-in</span></span>

1. <span data-ttu-id="3d1aa-109">`var app = express();` **./app.js** で行を見app.js。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-109">Locate the line `var app = express();` in **./app.js**.</span></span> <span data-ttu-id="3d1aa-110">その行の後に次 **のコードを** 挿入します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-110">Insert the following code **after** that line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="MsalInitSnippet":::

    <span data-ttu-id="3d1aa-111">このコードは、アプリのアプリ ID とパスワードを使用して msal-node ライブラリを初期化します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-111">This code initializes the msal-node library with the app ID and password for the app.</span></span>

1. <span data-ttu-id="3d1aa-112">**./routes** ディレクトリに新しいファイルを作成し、auth.js **コードを** 追加します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-112">Create a new file in the **./routes** directory named **auth.js** and add the following code.</span></span>

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

    <span data-ttu-id="3d1aa-113">これにより、次の 3 つのルートを持つ `signin` ルーター `callback` が定義されます。 `signout`</span><span class="sxs-lookup"><span data-stu-id="3d1aa-113">This defines a router with three routes: `signin`, `callback`, and `signout`.</span></span>

    <span data-ttu-id="3d1aa-114">ルート `signin` は関数を呼び出してログイン URL を生成し、ブラウザーをその `getAuthCodeUrl` URL にリダイレクトします。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-114">The `signin` route calls the `getAuthCodeUrl` function to generate the login URL, then redirects the browser to that URL.</span></span>

    <span data-ttu-id="3d1aa-115">ルート `callback` は、サインインの完了後に Azure がリダイレクトする場所です。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-115">The `callback` route is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="3d1aa-116">コードは、アクセス `acquireTokenByCode` トークンの認証コードを交換する関数を呼び出します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-116">The code calls the `acquireTokenByCode` function to exchange the authorization code for an access token.</span></span> <span data-ttu-id="3d1aa-117">トークンを取得すると、一時的なエラー値でアクセス トークンを使用してホーム ページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-117">Once the token is obtained, it redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="3d1aa-118">これを使用して、次に進む前にサインインが機能しているのを確認します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-118">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="3d1aa-119">テストする前に、./routes/auth.jsから新しいルーターを使用する Express アプリ **を構成する必要auth.js。**</span><span class="sxs-lookup"><span data-stu-id="3d1aa-119">Before we test, we need to configure the Express app to use the new router from **./routes/auth.js**.</span></span>

    <span data-ttu-id="3d1aa-120">この `signout` メソッドは、ユーザーをログアウトし、セッションを破棄します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-120">The `signout` method logs the user out and destroys the session.</span></span>

1. <span data-ttu-id="3d1aa-121">**./app.jsを開** き、行の前に次 **のコードを** 挿入 `var app = express();` します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-121">Open **./app.js** and insert the following code **before** the `var app = express();` line.</span></span>

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. <span data-ttu-id="3d1aa-122">行の後に次 **のコードを** `app.use('/', indexRouter);` 挿入します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-122">Insert the following code **after** the `app.use('/', indexRouter);` line.</span></span>

    ```javascript
    app.use('/auth', authRouter);
    ```

<span data-ttu-id="3d1aa-123">サーバーを起動し、参照します `https://localhost:3000` 。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-123">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="3d1aa-124">[サインイン] ボタンをクリックすると、`https://login.microsoftonline.com` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-124">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="3d1aa-125">Microsoft アカウントでログインし、要求されたアクセス許可に同意します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-125">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="3d1aa-126">ブラウザーがアプリにリダイレクトし、トークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-126">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="3d1aa-127">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="3d1aa-127">Get user details</span></span>

1. <span data-ttu-id="3d1aa-128">プロジェクトのルートに新しいファイルを作成し、graph.js **コードを** 追加します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-128">Create a new file in the root of the project named **graph.js** and add the following code.</span></span>

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

    <span data-ttu-id="3d1aa-129">これにより、Microsoft Graph SDK を使用してエンドポイントを呼び出し、結果を返す関数 `getUserDetails` `/me` がエクスポートされます。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-129">This exports the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="3d1aa-130">**./routes/auth.js** 開き、ファイルの一番上に次の `require` ステートメントを追加します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-130">Open **./routes/auth.js** and add the following `require` statements to the top of the file.</span></span>

    ```javascript
    var graph = require('../graph');
    ```

1. <span data-ttu-id="3d1aa-131">既存のコールバック ルートを次のコードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-131">Replace the existing callback route with the following code.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackSnippet" highlight="13-23":::

    <span data-ttu-id="3d1aa-132">新しいコードは、ユーザーのアカウント ID をセッションに保存し、Microsoft Graph からユーザーの詳細を取得して、アプリのユーザー ストレージに保存します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-132">The new code saves the user's account ID in the session, gets the user's details from Microsoft Graph, and saves it in the app's user storage.</span></span>

1. <span data-ttu-id="3d1aa-133">サーバーを再起動し、サインイン プロセスを実行します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-133">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="3d1aa-134">ホーム ページに戻る必要がありますが、サインイン中を示すために UI が変更される必要があります。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-134">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. <span data-ttu-id="3d1aa-136">右上隅にあるユーザー アバターをクリックして、[サインアウト **] リンクにアクセス** します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-136">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="3d1aa-137">**[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-137">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a><span data-ttu-id="3d1aa-139">トークンの保存と更新</span><span class="sxs-lookup"><span data-stu-id="3d1aa-139">Storing and refreshing tokens</span></span>

<span data-ttu-id="3d1aa-140">この時点で、アプリケーションはアクセス トークンを持ち、API 呼び出しのヘッダー `Authorization` で送信されます。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-140">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="3d1aa-141">これは、アプリがユーザーの代わりに Microsoft Graph にアクセスできるトークンです。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-141">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="3d1aa-142">ただし、このトークンは一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-142">However, this token is short-lived.</span></span> <span data-ttu-id="3d1aa-143">トークンは発行後 1 時間で期限切れになります。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-143">The token expires an hour after it is issued.</span></span> <span data-ttu-id="3d1aa-144">ここで、更新トークンが役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-144">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="3d1aa-145">OAuth の仕様には更新トークンが導入されています。これにより、アプリはユーザーが再びサインインする必要なしに新しいアクセス トークンを要求できます。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-145">The OAuth specification introduces a refresh token, which allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="3d1aa-146">アプリは msal-node パッケージを使うので、トークンストレージや更新ロジックを実装する必要は一切必要ではありません。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-146">Because the app is using the msal-node package, you do not need to implement any token storage or refresh logic.</span></span> <span data-ttu-id="3d1aa-147">アプリは、サンプル アプリケーションで十分な既定の msal-node メモリ内トークン キャッシュを使用します。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-147">The app uses the default msal-node in-memory token cache, which is sufficient for a sample application.</span></span> <span data-ttu-id="3d1aa-148">実稼働アプリケーションは、安全で[](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)信頼性の高いストレージ メディアでトークン キャッシュをシリアル化するための独自のキャッシュ プラグインを提供する必要があります。</span><span class="sxs-lookup"><span data-stu-id="3d1aa-148">Production applications should provide their own [caching plugin](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) to serialize the token cache in a secure, reliable storage medium.</span></span>
