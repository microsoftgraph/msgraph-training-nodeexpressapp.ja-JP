<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ac809-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="ac809-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="ac809-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="ac809-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="ac809-103">この手順では、アプリケーションに[passport と azure ad](https://github.com/AzureAD/passport-azure-ad)ライブラリを統合します。</span><span class="sxs-lookup"><span data-stu-id="ac809-103">In this step you will integrate the [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) library into the application.</span></span>

1. <span data-ttu-id="ac809-104">アプリケーションのルートに file `.env`という名前の新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ac809-104">Create a new file named `.env` file in the root of your application, and add the following code.</span></span>

    :::code language="ini" source="../demo/graph-tutorial/.env.example":::

    <span data-ttu-id="ac809-105">を`YOUR APP ID HERE`アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し`YOUR APP SECRET HERE`たパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ac809-105">Replace `YOUR APP ID HERE` with the application ID from the Application Registration Portal, and replace `YOUR APP SECRET HERE` with the password you generated.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="ac809-106">Git などのソース管理を使用している場合は、アプリ ID とパスワードを誤っ`.env`てリークしないように、ソース管理からファイルを除外することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ac809-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

1. <span data-ttu-id="ac809-107">を`./app.js`開き、ファイルの先頭に次の行を追加して、 `.env`ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ac809-107">Open `./app.js` and add the following line to the top of the file to load the `.env` file.</span></span>

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a><span data-ttu-id="ac809-108">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="ac809-108">Implement sign-in</span></span>

1. <span data-ttu-id="ac809-109">の`var indexRouter = require('./routes/index');` `./app.js`行を検索します。</span><span class="sxs-lookup"><span data-stu-id="ac809-109">Locate the line `var indexRouter = require('./routes/index');` in `./app.js`.</span></span> <span data-ttu-id="ac809-110">その行の**前に**次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ac809-110">Insert the following code **before** that line.</span></span>

    ```javascript
    var passport = require('passport');
    var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

    // Configure passport

    // In-memory storage of logged-in users
    // For demo purposes only, production apps should store
    // this in a reliable storage
    var users = {};

    // Passport calls serializeUser and deserializeUser to
    // manage users
    passport.serializeUser(function(user, done) {
      // Use the OID property of the user as a key
      users[user.profile.oid] = user;
      done (null, user.profile.oid);
    });

    passport.deserializeUser(function(id, done) {
      done(null, users[id]);
    });

    // Callback function called once the sign-in is complete
    // and an access token has been obtained
    async function signInComplete(iss, sub, profile, accessToken, refreshToken, params, done) {
      if (!profile.oid) {
        return done(new Error("No OID found in user profile."));
      }

      // Save the profile and tokens in user storage
      users[profile.oid] = { profile, accessToken };
      return done(null, users[profile.oid]);
    }

    // Configure OIDC strategy
    passport.use(new OIDCStrategy(
      {
        identityMetadata: `${process.env.OAUTH_AUTHORITY}${process.env.OAUTH_ID_METADATA}`,
        clientID: process.env.OAUTH_APP_ID,
        responseType: 'code id_token',
        responseMode: 'form_post',
        redirectUrl: process.env.OAUTH_REDIRECT_URI,
        allowHttpForRedirectUrl: true,
        clientSecret: process.env.OAUTH_APP_PASSWORD,
        validateIssuer: false,
        passReqToCallback: false,
        scope: process.env.OAUTH_SCOPES.split(' ')
      },
      signInComplete
    ));
    ```

    <span data-ttu-id="ac809-111">このコードでは[Passport.js](http://www.passportjs.org/) 、 `passport-azure-ad`ライブラリを使用するように、パスポートライブラリを初期化し、アプリのアプリ ID とパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="ac809-111">This code initializes the [Passport.js](http://www.passportjs.org/) library to use the `passport-azure-ad` library, and configures it with the app ID and password for the app.</span></span>

1. <span data-ttu-id="ac809-112">の`app.use('/', indexRouter);` `./app.js`行を検索します。</span><span class="sxs-lookup"><span data-stu-id="ac809-112">Locate the line `app.use('/', indexRouter);` in `./app.js`.</span></span> <span data-ttu-id="ac809-113">その行の**前に**次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ac809-113">Insert the following code **before** that line.</span></span>

    ```javascript
    // Initialize passport
    app.use(passport.initialize());
    app.use(passport.session());
    ```

1. <span data-ttu-id="ac809-114">という名前`./routes` `auth.js`のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ac809-114">Create a new file in the `./routes` directory named `auth.js` and add the following code.</span></span>

    ```javascript
    var express = require('express');
    var passport = require('passport');
    var router = express.Router();

    /* GET auth callback. */
    router.get('/signin',
      function  (req, res, next) {
        passport.authenticate('azuread-openidconnect',
          {
            response: res,
            prompt: 'login',
            failureRedirect: '/',
            failureFlash: true,
            successRedirect: '/'
          }
        )(req,res,next);
      }
    );

    router.post('/callback',
      function(req, res, next) {
        passport.authenticate('azuread-openidconnect',
          {
            response: res,
            failureRedirect: '/',
            failureFlash: true
          }
        )(req,res,next);
      },
      function(req, res) {
        // TEMPORARY!
        // Flash the access token for testing purposes
        req.flash('error_msg', {message: 'Access token', debug: req.user.accessToken});
        res.redirect('/');
      }
    );

    router.get('/signout',
      function(req, res) {
        req.session.destroy(function(err) {
          req.logout();
          res.redirect('/');
        });
      }
    );

    module.exports = router;
    ```

    <span data-ttu-id="ac809-115">これは、、 `signin` `callback`、および`signout`の3つのルートでルーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="ac809-115">This defines a router with three routes: `signin`, `callback`, and `signout`.</span></span>

    <span data-ttu-id="ac809-116">ルート`signin`からメソッドが`passport.authenticate`呼び出され、アプリが Azure ログインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ac809-116">The `signin` route calls the `passport.authenticate` method, causing the app to redirect to the Azure login page.</span></span>

    <span data-ttu-id="ac809-117">この`callback`ルートは、サインインの完了後に Azure がリダイレクトされる場所です。</span><span class="sxs-lookup"><span data-stu-id="ac809-117">The `callback` route is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="ac809-118">コードによって`passport.authenticate`メソッドが再度呼び出され`passport-azure-ad` 、アクセストークンを要求するようになります。</span><span class="sxs-lookup"><span data-stu-id="ac809-118">The code calls the `passport.authenticate` method again, causing the `passport-azure-ad` strategy to request an access token.</span></span> <span data-ttu-id="ac809-119">トークンが取得されると、次のハンドラーが呼び出されます。これにより、一時的なエラー値でアクセストークンを持つホームページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ac809-119">Once the token is obtained, the next handler is called, which redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="ac809-120">これを使用して、サインインが機能していることを確認してから、に進みます。</span><span class="sxs-lookup"><span data-stu-id="ac809-120">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="ac809-121">テストを開始する前に、から`./routes/auth.js`新しいルーターを使用するようにエクスプレスアプリを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ac809-121">Before we test, we need to configure the Express app to use the new router from `./routes/auth.js`.</span></span>

    <span data-ttu-id="ac809-122">メソッド`signout`は、ユーザーをログに記録し、セッションを破棄します。</span><span class="sxs-lookup"><span data-stu-id="ac809-122">The `signout` method logs the user out and destroys the session.</span></span>

1. <span data-ttu-id="ac809-123">を`./app.js`開いて、行の`var app = express();` **前に**次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ac809-123">Open `./app.js` and insert the following code **before** the `var app = express();` line.</span></span>

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. <span data-ttu-id="ac809-124">行の`app.use('/', indexRouter);` **後**に次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ac809-124">Insert the following code **after** the `app.use('/', indexRouter);` line.</span></span>

    ```javascript
    app.use('/auth', authRouter);
    ```

<span data-ttu-id="ac809-125">サーバーを起動し、を`https://localhost:3000`参照します。</span><span class="sxs-lookup"><span data-stu-id="ac809-125">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="ac809-126">[サインイン] ボタンをクリックすると、`https://login.microsoftonline.com` にリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ac809-126">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="ac809-127">Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。</span><span class="sxs-lookup"><span data-stu-id="ac809-127">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="ac809-128">ブラウザーがアプリにリダイレクトし、トークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ac809-128">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="ac809-129">ユーザーの詳細情報を取得する</span><span class="sxs-lookup"><span data-stu-id="ac809-129">Get user details</span></span>

1. <span data-ttu-id="ac809-130">という名前`graph.js`のプロジェクトのルートに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ac809-130">Create a new file in the root of the project named `graph.js` and add the following code.</span></span>

    ```javascript
    var graph = require('@microsoft/microsoft-graph-client');
    require('isomorphic-fetch');

    module.exports = {
      getUserDetails: async function(accessToken) {
        const client = getAuthenticatedClient(accessToken);

        const user = await client.api('/me').get();
        return user;
      }
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

    <span data-ttu-id="ac809-131">これにより`getUserDetails` 、関数がエクスポートされます。これは、 `/me` Microsoft Graph SDK を使用してエンドポイントを呼び出し、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="ac809-131">This exports the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

1. <span data-ttu-id="ac809-132">を`/app.js`開き、次`require`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="ac809-132">Open `/app.js` and add the following `require` statements to the top of the file.</span></span>

    ```javascript
    var graph = require('./graph');
    ```

1. <span data-ttu-id="ac809-133">既存の `signInComplete` 関数を、以下のコードで置換します。</span><span class="sxs-lookup"><span data-stu-id="ac809-133">Replace the existing `signInComplete` function with the following code.</span></span>

    ```javascript
    async function signInComplete(iss, sub, profile, accessToken, refreshToken, params, done) {
      if (!profile.oid) {
        return done(new Error("No OID found in user profile."));
      }

      try{
        const user = await graph.getUserDetails(accessToken);

        if (user) {
          // Add properties to profile
          profile['email'] = user.mail ? user.mail : user.userPrincipalName;
        }
      } catch (err) {
        return done(err);
      }

      // Save the profile and tokens in user storage
      users[profile.oid] = { profile, accessToken };
      return done(null, users[profile.oid]);
    }
    ```

    <span data-ttu-id="ac809-134">新しいコードでは、 `profile` Microsoft Graph によって返さ`email`れるデータを使用してプロパティを追加するために、Passport によって提供されたを更新します。</span><span class="sxs-lookup"><span data-stu-id="ac809-134">The new code updates the `profile` provided by Passport to add an `email` property, using the data returned by Microsoft Graph.</span></span>

1. <span data-ttu-id="ac809-135">行の`app.use(passport.session());` **後**に次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="ac809-135">Add the following **after** the `app.use(passport.session());` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="AddProfileSnippet":::

    <span data-ttu-id="ac809-136">このコードでは、ユーザープロファイルを`locals`応答のプロパティに読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ac809-136">This code loads the user profile into the `locals` property of the response.</span></span> <span data-ttu-id="ac809-137">これにより、アプリのすべてのビューで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="ac809-137">This will make it available to all of the views in the app.</span></span>

## <a name="storing-the-tokens"></a><span data-ttu-id="ac809-138">トークンの格納</span><span class="sxs-lookup"><span data-stu-id="ac809-138">Storing the tokens</span></span>

<span data-ttu-id="ac809-139">トークンを取得できるようになったので、トークンをアプリに格納する手順を実装します。</span><span class="sxs-lookup"><span data-stu-id="ac809-139">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="ac809-140">現時点では、アプリは、raw アクセストークンをメモリ内ユーザーの記憶域に格納しています。</span><span class="sxs-lookup"><span data-stu-id="ac809-140">Currently, the app is storing the raw access token in the in-memory user storage.</span></span> <span data-ttu-id="ac809-141">これはサンプルアプリなので、わかりやすくするために、そのまま保存しておきます。</span><span class="sxs-lookup"><span data-stu-id="ac809-141">Since this is a sample app, for simplicity's sake, you'll continue to store them there.</span></span> <span data-ttu-id="ac809-142">実際のアプリでは、データベースのような、より信頼性の高い安全なストレージ ソリューションを使用します。</span><span class="sxs-lookup"><span data-stu-id="ac809-142">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="ac809-143">ただし、アクセストークンのみを保存しても、有効期限を確認したり、トークンを更新したりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ac809-143">However, storing just the access token doesn't allow you to check expiration or refresh the token.</span></span> <span data-ttu-id="ac809-144">これを有効にするには、 `AccessToken` `simple-oauth2`ライブラリからオブジェクト内のトークンをラップするようにサンプルを更新します。</span><span class="sxs-lookup"><span data-stu-id="ac809-144">In order to enable that, update the sample to wrap the tokens in an `AccessToken` object from the `simple-oauth2` library.</span></span>

1. <span data-ttu-id="ac809-145">を`./app.js`開き、関数の`signInComplete` **前に**次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ac809-145">Open `./app.js` and add the following code **before** the `signInComplete` function.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="ConfigureOAuth2Snippet":::

1. <span data-ttu-id="ac809-146">既存の `signInComplete` 関数を、以下の関数で置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ac809-146">Replace the existing `signInComplete` function with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SignInCompleteSnippet" highlight="17-18, 21":::

1. <span data-ttu-id="ac809-147">の`./routes/auth.js`既存のコールバックルートを次のものに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ac809-147">Replace the existing callback route in  `./routes/auth.js` with the following.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackRouteSnippet" highlight="17-18":::

1. <span data-ttu-id="ac809-148">サーバーを再起動し、サインインプロセスを実行します。</span><span class="sxs-lookup"><span data-stu-id="ac809-148">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="ac809-149">ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ac809-149">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. <span data-ttu-id="ac809-151">右上隅にあるユーザーアバターをクリックして、[**サインアウト**] リンクにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="ac809-151">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="ac809-152">**[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="ac809-152">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="ac809-154">トークンの更新</span><span class="sxs-lookup"><span data-stu-id="ac809-154">Refreshing tokens</span></span>

<span data-ttu-id="ac809-155">この時点で、アプリケーションには、API 呼び出しの`Authorization`ヘッダーで送信されるアクセストークンがあります。</span><span class="sxs-lookup"><span data-stu-id="ac809-155">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="ac809-156">これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。</span><span class="sxs-lookup"><span data-stu-id="ac809-156">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="ac809-157">ただし、このトークンは一時的なものです。</span><span class="sxs-lookup"><span data-stu-id="ac809-157">However, this token is short-lived.</span></span> <span data-ttu-id="ac809-158">トークンが発行された後、有効期限が切れる時間になります。</span><span class="sxs-lookup"><span data-stu-id="ac809-158">The token expires an hour after it is issued.</span></span> <span data-ttu-id="ac809-159">ここで、更新トークンが役に立ちます。</span><span class="sxs-lookup"><span data-stu-id="ac809-159">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="ac809-160">更新トークンを使用すると、ユーザーが再度サインインしなくても、アプリは新しいアクセス トークンを要求できます。</span><span class="sxs-lookup"><span data-stu-id="ac809-160">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

1. <span data-ttu-id="ac809-161">トークン管理機能を保持するために、という`tokens.js`名前のプロジェクトのルートに新しいファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ac809-161">Create a new file in the root of the project named `tokens.js` to hold token management functions.</span></span> <span data-ttu-id="ac809-162">次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ac809-162">Add the following code.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/tokens.js" id="TokensSnippet":::

<span data-ttu-id="ac809-163">このメソッドは、最初にアクセストークンの有効期限が切れているか、期限切れになるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="ac809-163">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="ac809-164">その場合は、更新トークンを使用して新しいトークンを取得し、次にキャッシュを更新して、新しいアクセストークンを返します。</span><span class="sxs-lookup"><span data-stu-id="ac809-164">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span> <span data-ttu-id="ac809-165">アクセストークンの保存を停止する必要がある場合は、このメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ac809-165">You'll use this method whenever you need to get the access token out of storage.</span></span>
