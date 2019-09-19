<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 この手順では、アプリケーションに[passport と azure ad](https://github.com/AzureAD/passport-azure-ad)ライブラリを統合します。

アプリケーションのルートに file `.env`という名前の新しいファイルを作成し、次のコードを追加します。

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

を`YOUR APP ID HERE`アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し`YOUR APP SECRET HERE`たパスワードに置き換えます。

> [!IMPORTANT]
> Git などのソース管理を使用している場合は、アプリ ID とパスワードを誤っ`.env`てリークしないように、ソース管理からファイルを除外することをお勧めします。

を`./app.js`開き、ファイルの先頭に次の行を追加して、 `.env`ファイルを読み込みます。

```js
require('dotenv').config();
```

## <a name="implement-sign-in"></a>サインインの実装

の`var indexRouter = require('./routes/index');` `./app.js`行を検索します。 その行の**前に**次のコードを挿入します。

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

このコードでは[](http://www.passportjs.org/) 、 `passport-azure-ad`ライブラリを使用するように、パスポートライブラリを初期化し、アプリのアプリ ID とパスワードを設定します。

次に、 `passport`オブジェクトをエクスプレスアプリに渡します。 の`app.use('/', indexRouter);` `./app.js`行を検索します。 その行の**前に**次のコードを挿入します。

```js
// Initialize passport
app.use(passport.initialize());
app.use(passport.session());
```

という名前`./routes` `auth.js`のディレクトリに新しいファイルを作成し、次のコードを追加します。

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

これは、、 `signin` `callback`、および`signout`の3つのルートでルーターを定義します。

ルート`signin`からメソッドが`passport.authenticate`呼び出され、アプリが Azure ログインページにリダイレクトされます。

この`callback`ルートは、サインインの完了後に Azure がリダイレクトされる場所です。 コードによって`passport.authenticate`メソッドが再度呼び出され`passport-azure-ad` 、アクセストークンを要求するようになります。 トークンが取得されると、次のハンドラーが呼び出されます。これにより、一時的なエラー値でアクセストークンを持つホームページにリダイレクトされます。 これを使用して、サインインが機能していることを確認してから、に進みます。 テストを開始する前に、から`./routes/auth.js`新しいルーターを使用するようにエクスプレスアプリを構成する必要があります。

メソッド`signout`は、ユーザーをログに記録し、セッションを破棄します。

行の`var app = express();` **前に**次のコードを挿入します。

```js
var authRouter = require('./routes/auth');
```

その後、行`app.use('/', indexRouter);`の**後**に次のコードを挿入します。

```js
app.use('/auth', authRouter);
```

サーバーを起動し、を`https://localhost:3000`参照します。 [サインイン] ボタンをクリックすると、に`https://login.microsoftonline.com`リダイレクトされます。 Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。 ブラウザーがアプリにリダイレクトし、トークンが表示されます。

### <a name="get-user-details"></a>ユーザーの詳細を取得する

最初に、Microsoft Graph のすべての呼び出しを保持する新しいファイルを作成します。 という名前`graph.js`のプロジェクトのルートに新しいファイルを作成し、次のコードを追加します。

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

これにより`getUserDetails` 、関数がエクスポートされます。これは、 `/me` Microsoft Graph SDK を使用してエンドポイントを呼び出し、結果を返します。

この関数`signInComplete`を呼び出す`/app.js`には、のメソッドを更新します。 最初に、次`require`のステートメントをファイルの先頭に追加します。

```js
var graph = require('./graph');
```

既存の `signInComplete` 関数を、以下のコードで置換します。

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

新しいコードでは、 `profile` Microsoft Graph によって返さ`email`れるデータを使用してプロパティを追加するために、Passport によって提供されたを更新します。

最後に、応答の`./app.js` `locals`プロパティにユーザープロファイルを読み込むコードを追加します。 これにより、アプリのすべてのビューで使用できるようになります。

行の`app.use(passport.session());` **後**に次の行を追加します。

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

## <a name="storing-the-tokens"></a>トークンの格納

トークンを入手できるようになったので、これをアプリに保存する方法を実装します。 現時点では、アプリは、raw アクセストークンをメモリ内ユーザーの記憶域に格納しています。 これはサンプルアプリなので、わかりやすくするために、そのまま保存しておきます。 実際のアプリケーションでは、データベースのような、より信頼性の高いセキュリティで保護されたストレージソリューションを使用します。

ただし、アクセストークンのみを保存しても、有効期限を確認したり、トークンを更新したりすることはできません。 これを有効にするには、 `AccessToken` `simple-oauth2`ライブラリからオブジェクト内のトークンをラップするようにサンプルを更新します。

最初に、 `./app.js`では、関数の**** `signInComplete`前に次のコードを追加します。

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

その後、 `signInComplete`関数を更新して`AccessToken` 、渡された生のトークンから、ユーザーストレージに格納されているを作成します。 既存の `signInComplete` 関数を、以下の関数で置換します。

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

の`callback` `./routes/auth.js`ルートを更新して、 `req.flash`アクセストークンを含む行を削除します。 ルート`callback`は次のようになります。

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

サーバーを再起動し、サインインプロセスを実行します。 ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。

![サインイン後のホームページのスクリーンショット](./images/add-aad-auth-01.png)

右上隅にあるユーザーアバターをクリックして、[**サインアウト**] リンクにアクセスします。 [**サインアウト**] をクリックすると、セッションがリセットされ、ホームページに戻ります。

![[サインアウト] リンクがあるドロップダウンメニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>トークンの更新

この時点で、アプリケーションには、API 呼び出しの`Authorization`ヘッダーで送信されるアクセストークンがあります。 これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。

ただし、このトークンは存続期間が短くなります。 トークンが発行された後、有効期限が切れる時間になります。 ここでは、更新トークンが有効になります。 更新トークンを使用すると、ユーザーが再度サインインする必要なく、新しいアクセストークンをアプリで要求できます。

これを管理するには、トークン管理機能を保持するという`tokens.js`名前のプロジェクトのルートに新しいファイルを作成します。 次のコードを追加します。

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

このメソッドは、最初にアクセストークンの有効期限が切れているか、期限切れになるかを確認します。 その場合は、更新トークンを使用して新しいトークンを取得し、次にキャッシュを更新して、新しいアクセストークンを返します。 アクセストークンの保存を停止する必要がある場合は、このメソッドを使用します。
