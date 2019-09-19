<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ff761-101">この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。</span><span class="sxs-lookup"><span data-stu-id="ff761-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="ff761-102">これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。</span><span class="sxs-lookup"><span data-stu-id="ff761-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="ff761-103">この手順では、アプリケーションに[passport と azure ad](https://github.com/AzureAD/passport-azure-ad)ライブラリを統合します。</span><span class="sxs-lookup"><span data-stu-id="ff761-103">In this step you will integrate the [passport-azure-ad](https://github.com/AzureAD/passport-azure-ad) library into the application.</span></span>

<span data-ttu-id="ff761-104">アプリケーションのルートに file `.env`という名前の新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ff761-104">Create a new file named `.env` file in the root of your application, and add the following code.</span></span>

```text
OAUTH_APP_ID=YOUR_APP_ID_HERE
OAUTH_APP_PASSWORD=YOUR_APP_PASSWORD_HERE
OAUTH_REDIRECT_URI=http://localhost:3000/auth/callback
OAUTH_SCOPES='profile offline_access user.read calendars.read'
OAUTH_AUTHORITY=https://login.microsoftonline.com/common
OAUTH_ID_METADATA=/v2.0/.well-known/openid-configuration
OAUTH_AUTHORIZE_ENDPOINT=/oauth2/v2.0/authorize
OAUTH_TOKEN_ENDPOINT=/oauth2/v2.0/token
```

<span data-ttu-id="ff761-105">を`YOUR APP ID HERE`アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し`YOUR APP SECRET HERE`たパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="ff761-105">Replace `YOUR APP ID HERE` with the application ID from the Application Registration Portal, and replace `YOUR APP SECRET HERE` with the password you generated.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ff761-106">Git などのソース管理を使用している場合は、アプリ ID とパスワードを誤っ`.env`てリークしないように、ソース管理からファイルを除外することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="ff761-106">If you're using source control such as git, now would be a good time to exclude the `.env` file from source control to avoid inadvertently leaking your app ID and password.</span></span>

<span data-ttu-id="ff761-107">を`./app.js`開き、ファイルの先頭に次の行を追加して、 `.env`ファイルを読み込みます。</span><span class="sxs-lookup"><span data-stu-id="ff761-107">Open `./app.js` and add the following line to the top of the file to load the `.env` file.</span></span>

```js
require('dotenv').config();
```

## <a name="implement-sign-in"></a><span data-ttu-id="ff761-108">サインインの実装</span><span class="sxs-lookup"><span data-stu-id="ff761-108">Implement sign-in</span></span>

<span data-ttu-id="ff761-109">の`var indexRouter = require('./routes/index');` `./app.js`行を検索します。</span><span class="sxs-lookup"><span data-stu-id="ff761-109">Locate the line `var indexRouter = require('./routes/index');` in `./app.js`.</span></span> <span data-ttu-id="ff761-110">その行の**前に**次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ff761-110">Insert the following code **before** that line.</span></span>

```js
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
    return done(new Error("No OID found in user profile."), null);
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

<span data-ttu-id="ff761-111">このコードでは[](http://www.passportjs.org/) 、 `passport-azure-ad`ライブラリを使用するように、パスポートライブラリを初期化し、アプリのアプリ ID とパスワードを設定します。</span><span class="sxs-lookup"><span data-stu-id="ff761-111">This code initializes the [Passport.js](http://www.passportjs.org/) library to use the `passport-azure-ad` library, and configures it with the app ID and password for the app.</span></span>

<span data-ttu-id="ff761-112">次に、 `passport`オブジェクトをエクスプレスアプリに渡します。</span><span class="sxs-lookup"><span data-stu-id="ff761-112">Now pass the `passport` object to the Express app.</span></span> <span data-ttu-id="ff761-113">の`app.use('/', indexRouter);` `./app.js`行を検索します。</span><span class="sxs-lookup"><span data-stu-id="ff761-113">Locate the line `app.use('/', indexRouter);` in `./app.js`.</span></span> <span data-ttu-id="ff761-114">その行の**前に**次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ff761-114">Insert the following code **before** that line.</span></span>

```js
// Initialize passport
app.use(passport.initialize());
app.use(passport.session());
```

<span data-ttu-id="ff761-115">という名前`./routes` `auth.js`のディレクトリに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ff761-115">Create a new file in the `./routes` directory named `auth.js` and add the following code.</span></span>

```js
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
        failureFlash: true
      }
    )(req,res,next);
  },
  function(req, res) {
    res.redirect('/');
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

<span data-ttu-id="ff761-116">これは、、 `signin` `callback`、および`signout`の3つのルートでルーターを定義します。</span><span class="sxs-lookup"><span data-stu-id="ff761-116">This defines a router with three routes: `signin`, `callback`, and `signout`.</span></span>

<span data-ttu-id="ff761-117">ルート`signin`からメソッドが`passport.authenticate`呼び出され、アプリが Azure ログインページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ff761-117">The `signin` route calls the `passport.authenticate` method, causing the app to redirect to the Azure login page.</span></span>

<span data-ttu-id="ff761-118">この`callback`ルートは、サインインの完了後に Azure がリダイレクトされる場所です。</span><span class="sxs-lookup"><span data-stu-id="ff761-118">The `callback` route is where Azure redirects after the signin is complete.</span></span> <span data-ttu-id="ff761-119">コードによって`passport.authenticate`メソッドが再度呼び出され`passport-azure-ad` 、アクセストークンを要求するようになります。</span><span class="sxs-lookup"><span data-stu-id="ff761-119">The code calls the `passport.authenticate` method again, causing the `passport-azure-ad` strategy to request an access token.</span></span> <span data-ttu-id="ff761-120">トークンが取得されると、次のハンドラーが呼び出されます。これにより、一時的なエラー値でアクセストークンを持つホームページにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ff761-120">Once the token is obtained, the next handler is called, which redirects back to the home page with the access token in the temporary error value.</span></span> <span data-ttu-id="ff761-121">これを使用して、サインインが機能していることを確認してから、に進みます。</span><span class="sxs-lookup"><span data-stu-id="ff761-121">We'll use this to verify that our sign-in is working before moving on.</span></span> <span data-ttu-id="ff761-122">テストを開始する前に、から`./routes/auth.js`新しいルーターを使用するようにエクスプレスアプリを構成する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff761-122">Before we test, we need to configure the Express app to use the new router from `./routes/auth.js`.</span></span>

<span data-ttu-id="ff761-123">メソッド`signout`は、ユーザーをログに記録し、セッションを破棄します。</span><span class="sxs-lookup"><span data-stu-id="ff761-123">The `signout` method logs the user out and destroys the session.</span></span>

<span data-ttu-id="ff761-124">行の`var app = express();` **前に**次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ff761-124">Insert the following code **before** the `var app = express();` line.</span></span>

```js
var authRouter = require('./routes/auth');
```

<span data-ttu-id="ff761-125">その後、行`app.use('/', indexRouter);`の**後**に次のコードを挿入します。</span><span class="sxs-lookup"><span data-stu-id="ff761-125">Then insert the following code **after** the `app.use('/', indexRouter);` line.</span></span>

```js
app.use('/auth', authRouter);
```

<span data-ttu-id="ff761-126">サーバーを起動し、を`https://localhost:3000`参照します。</span><span class="sxs-lookup"><span data-stu-id="ff761-126">Start the server and browse to `https://localhost:3000`.</span></span> <span data-ttu-id="ff761-127">[サインイン] ボタンをクリックすると、に`https://login.microsoftonline.com`リダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="ff761-127">Click the sign-in button and you should be redirected to `https://login.microsoftonline.com`.</span></span> <span data-ttu-id="ff761-128">Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。</span><span class="sxs-lookup"><span data-stu-id="ff761-128">Login with your Microsoft account and consent to the requested permissions.</span></span> <span data-ttu-id="ff761-129">ブラウザーがアプリにリダイレクトし、トークンが表示されます。</span><span class="sxs-lookup"><span data-stu-id="ff761-129">The browser redirects to the app, showing the token.</span></span>

### <a name="get-user-details"></a><span data-ttu-id="ff761-130">ユーザーの詳細を取得する</span><span class="sxs-lookup"><span data-stu-id="ff761-130">Get user details</span></span>

<span data-ttu-id="ff761-131">最初に、Microsoft Graph のすべての呼び出しを保持する新しいファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff761-131">Start by creating a new file to hold all of your Microsoft Graph calls.</span></span> <span data-ttu-id="ff761-132">という名前`graph.js`のプロジェクトのルートに新しいファイルを作成し、次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ff761-132">Create a new file in the root of the project named `graph.js` and add the following code.</span></span>

```js
var graph = require('@microsoft/microsoft-graph-client');

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

<span data-ttu-id="ff761-133">これにより`getUserDetails` 、関数がエクスポートされます。これは、 `/me` Microsoft Graph SDK を使用してエンドポイントを呼び出し、結果を返します。</span><span class="sxs-lookup"><span data-stu-id="ff761-133">This exports the `getUserDetails` function, which uses the Microsoft Graph SDK to call the `/me` endpoint and return the result.</span></span>

<span data-ttu-id="ff761-134">この関数`signInComplete`を呼び出す`/app.js`には、のメソッドを更新します。</span><span class="sxs-lookup"><span data-stu-id="ff761-134">Update the `signInComplete` method in `/app.js` to call this function.</span></span> <span data-ttu-id="ff761-135">最初に、次`require`のステートメントをファイルの先頭に追加します。</span><span class="sxs-lookup"><span data-stu-id="ff761-135">First, add the following `require` statements to the top of the file.</span></span>

```js
var graph = require('./graph');
```

<span data-ttu-id="ff761-136">既存の `signInComplete` 関数を、以下のコードで置換します。</span><span class="sxs-lookup"><span data-stu-id="ff761-136">Replace the existing `signInComplete` function with the following code.</span></span>

```js
async function signInComplete(iss, sub, profile, accessToken, refreshToken, params, done) {
  if (!profile.oid) {
    return done(new Error("No OID found in user profile."), null);
  }

  try{
    const user = await graph.getUserDetails(accessToken);

    if (user) {
      // Add properties to profile
      profile['email'] = user.mail ? user.mail : user.userPrincipalName;
    }
  } catch (err) {
    done(err, null);
  }

  // Save the profile and tokens in user storage
  users[profile.oid] = { profile, accessToken };
  return done(null, users[profile.oid]);
}
```

<span data-ttu-id="ff761-137">新しいコードでは、 `profile` Microsoft Graph によって返さ`email`れるデータを使用してプロパティを追加するために、Passport によって提供されたを更新します。</span><span class="sxs-lookup"><span data-stu-id="ff761-137">The new code updates the `profile` provided by Passport to add an `email` property, using the data returned by Microsoft Graph.</span></span>

<span data-ttu-id="ff761-138">最後に、応答の`./app.js` `locals`プロパティにユーザープロファイルを読み込むコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ff761-138">Finally, add code to `./app.js` to load the user profile into the `locals` property of the response.</span></span> <span data-ttu-id="ff761-139">これにより、アプリのすべてのビューで使用できるようになります。</span><span class="sxs-lookup"><span data-stu-id="ff761-139">This will make it available to all of the views in the app.</span></span>

<span data-ttu-id="ff761-140">行の`app.use(passport.session());` **後**に次の行を追加します。</span><span class="sxs-lookup"><span data-stu-id="ff761-140">Add the following **after** the `app.use(passport.session());` line.</span></span>

```js
app.use(function(req, res, next) {
  // Set the authenticated user in the
  // template locals
  if (req.user) {
    res.locals.user = req.user.profile;
  }
  next();
});
```

## <a name="storing-the-tokens"></a><span data-ttu-id="ff761-141">トークンの格納</span><span class="sxs-lookup"><span data-stu-id="ff761-141">Storing the tokens</span></span>

<span data-ttu-id="ff761-142">トークンを入手できるようになったので、これをアプリに保存する方法を実装します。</span><span class="sxs-lookup"><span data-stu-id="ff761-142">Now that you can get tokens, it's time to implement a way to store them in the app.</span></span> <span data-ttu-id="ff761-143">現時点では、アプリは、raw アクセストークンをメモリ内ユーザーの記憶域に格納しています。</span><span class="sxs-lookup"><span data-stu-id="ff761-143">Currently, the app is storing the raw access token in the in-memory user storage.</span></span> <span data-ttu-id="ff761-144">これはサンプルアプリなので、わかりやすくするために、そのまま保存しておきます。</span><span class="sxs-lookup"><span data-stu-id="ff761-144">Since this is a sample app, for simplicity's sake, you'll continue to store them there.</span></span> <span data-ttu-id="ff761-145">実際のアプリケーションでは、データベースのような、より信頼性の高いセキュリティで保護されたストレージソリューションを使用します。</span><span class="sxs-lookup"><span data-stu-id="ff761-145">A real-world app would use a more reliable secure storage solution, like a database.</span></span>

<span data-ttu-id="ff761-146">ただし、アクセストークンのみを保存しても、有効期限を確認したり、トークンを更新したりすることはできません。</span><span class="sxs-lookup"><span data-stu-id="ff761-146">However, storing just the access token doesn't allow you to check expiration or refresh the token.</span></span> <span data-ttu-id="ff761-147">これを有効にするには、 `AccessToken` `simple-oauth2`ライブラリからオブジェクト内のトークンをラップするようにサンプルを更新します。</span><span class="sxs-lookup"><span data-stu-id="ff761-147">In order to enable that, update the sample to wrap the tokens in an `AccessToken` object from the `simple-oauth2` library.</span></span>

<span data-ttu-id="ff761-148">最初に、 `./app.js`では、関数の\*\*\*\* `signInComplete`前に次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ff761-148">First, in `./app.js`, add the following code **before** the `signInComplete` function.</span></span>

```js
// Configure simple-oauth2
const oauth2 = require('simple-oauth2').create({
  client: {
    id: process.env.OAUTH_APP_ID,
    secret: process.env.OAUTH_APP_PASSWORD
  },
  auth: {
    tokenHost: process.env.OAUTH_AUTHORITY,
    authorizePath: process.env.OAUTH_AUTHORIZE_ENDPOINT,
    tokenPath: process.env.OAUTH_TOKEN_ENDPOINT
  }
});
```

<span data-ttu-id="ff761-149">その後、 `signInComplete`関数を更新して`AccessToken` 、渡された生のトークンから、ユーザーストレージに格納されているを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff761-149">Then, update the `signInComplete` function to create an `AccessToken` from the raw tokens passed in and store that in the user storage.</span></span> <span data-ttu-id="ff761-150">既存の `signInComplete` 関数を、以下の関数で置換します。</span><span class="sxs-lookup"><span data-stu-id="ff761-150">Replace the existing `signInComplete` function with the following.</span></span>

```js
async function signInComplete(iss, sub, profile, accessToken, refreshToken, params, done) {
  if (!profile.oid) {
    return done(new Error("No OID found in user profile."), null);
  }

  try{
    const user = await graph.getUserDetails(accessToken);

    if (user) {
      // Add properties to profile
      profile['email'] = user.mail ? user.mail : user.userPrincipalName;
    }
  } catch (err) {
    done(err, null);
  }

  // Create a simple-oauth2 token from raw tokens
  let oauthToken = oauth2.accessToken.create(params);

  // Save the profile and tokens in user storage
  users[profile.oid] = { profile, oauthToken };
  return done(null, users[profile.oid]);
}
```

<span data-ttu-id="ff761-151">の`callback` `./routes/auth.js`ルートを更新して、 `req.flash`アクセストークンを含む行を削除します。</span><span class="sxs-lookup"><span data-stu-id="ff761-151">Update the `callback` route in `./routes/auth.js` to remove the `req.flash` line with the access token.</span></span> <span data-ttu-id="ff761-152">ルート`callback`は次のようになります。</span><span class="sxs-lookup"><span data-stu-id="ff761-152">The `callback` route should look like the following.</span></span>

```js
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
    res.redirect('/');
  }
);
```

<span data-ttu-id="ff761-153">サーバーを再起動し、サインインプロセスを実行します。</span><span class="sxs-lookup"><span data-stu-id="ff761-153">Restart the server and go through the sign-in process.</span></span> <span data-ttu-id="ff761-154">ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。</span><span class="sxs-lookup"><span data-stu-id="ff761-154">You should end up back on the home page, but the UI should change to indicate that you are signed-in.</span></span>

![サインイン後のホームページのスクリーンショット](./images/add-aad-auth-01.png)

<span data-ttu-id="ff761-156">右上隅にあるユーザーアバターをクリックして、[**サインアウト**] リンクにアクセスします。</span><span class="sxs-lookup"><span data-stu-id="ff761-156">Click the user avatar in the top right corner to access the **Sign Out** link.</span></span> <span data-ttu-id="ff761-157">[**サインアウト**] をクリックすると、セッションがリセットされ、ホームページに戻ります。</span><span class="sxs-lookup"><span data-stu-id="ff761-157">Clicking **Sign Out** resets the session and returns you to the home page.</span></span>

![[サインアウト] リンクがあるドロップダウンメニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a><span data-ttu-id="ff761-159">トークンの更新</span><span class="sxs-lookup"><span data-stu-id="ff761-159">Refreshing tokens</span></span>

<span data-ttu-id="ff761-160">この時点で、アプリケーションには、API 呼び出しの`Authorization`ヘッダーで送信されるアクセストークンがあります。</span><span class="sxs-lookup"><span data-stu-id="ff761-160">At this point your application has an access token, which is sent in the `Authorization` header of API calls.</span></span> <span data-ttu-id="ff761-161">これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。</span><span class="sxs-lookup"><span data-stu-id="ff761-161">This is the token that allows the app to access the Microsoft Graph on the user's behalf.</span></span>

<span data-ttu-id="ff761-162">ただし、このトークンは存続期間が短くなります。</span><span class="sxs-lookup"><span data-stu-id="ff761-162">However, this token is short-lived.</span></span> <span data-ttu-id="ff761-163">トークンが発行された後、有効期限が切れる時間になります。</span><span class="sxs-lookup"><span data-stu-id="ff761-163">The token expires an hour after it is issued.</span></span> <span data-ttu-id="ff761-164">ここでは、更新トークンが有効になります。</span><span class="sxs-lookup"><span data-stu-id="ff761-164">This is where the refresh token becomes useful.</span></span> <span data-ttu-id="ff761-165">更新トークンを使用すると、ユーザーが再度サインインする必要なく、新しいアクセストークンをアプリで要求できます。</span><span class="sxs-lookup"><span data-stu-id="ff761-165">The refresh token allows the app to request a new access token without requiring the user to sign in again.</span></span>

<span data-ttu-id="ff761-166">これを管理するには、トークン管理機能を保持するという`tokens.js`名前のプロジェクトのルートに新しいファイルを作成します。</span><span class="sxs-lookup"><span data-stu-id="ff761-166">To manage this, create a new file in the root of the project named `tokens.js` to hold token management functions.</span></span> <span data-ttu-id="ff761-167">次のコードを追加します。</span><span class="sxs-lookup"><span data-stu-id="ff761-167">Add the following code.</span></span>

```js
module.exports = {
  getAccessToken: async function(req) {
    if (req.user) {
      // Get the stored token
      var storedToken = req.user.oauthToken;

      if (storedToken) {
        if (storedToken.expired()) {
          // refresh token
          var newToken = await storedToken.refresh();

          // Update stored token
          req.user.oauthToken = newToken;
          return newToken.token.access_token;
        }

        // Token still valid, just return it
        return storedToken.token.access_token;
      }
    }
  }
};
```

<span data-ttu-id="ff761-168">このメソッドは、最初にアクセストークンの有効期限が切れているか、期限切れになるかを確認します。</span><span class="sxs-lookup"><span data-stu-id="ff761-168">This method first checks if the access token is expired or close to expiring.</span></span> <span data-ttu-id="ff761-169">その場合は、更新トークンを使用して新しいトークンを取得し、次にキャッシュを更新して、新しいアクセストークンを返します。</span><span class="sxs-lookup"><span data-stu-id="ff761-169">If it is, then it uses the refresh token to get new tokens, then updates the cache and returns the new access token.</span></span> <span data-ttu-id="ff761-170">アクセストークンの保存を停止する必要がある場合は、このメソッドを使用します。</span><span class="sxs-lookup"><span data-stu-id="ff761-170">You'll use this method whenever you need to get the access token out of storage.</span></span>
