<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 この手順では、アプリケーションに[passport と azure ad](https://github.com/AzureAD/passport-azure-ad)ライブラリを統合します。

1. アプリケーションのルートに file `.env`という名前の新しいファイルを作成し、次のコードを追加します。

    :::code language="ini" source="../demo/graph-tutorial/.env.example":::

    を`YOUR APP ID HERE`アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し`YOUR APP SECRET HERE`たパスワードに置き換えます。

    > [!IMPORTANT]
    > Git などのソース管理を使用している場合は、アプリ ID とパスワードを誤っ`.env`てリークしないように、ソース管理からファイルを除外することをお勧めします。

1. を`./app.js`開き、ファイルの先頭に次の行を追加して、 `.env`ファイルを読み込みます。

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a>サインインの実装

1. の`var indexRouter = require('./routes/index');` `./app.js`行を検索します。 その行の**前に**次のコードを挿入します。

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

    このコードでは[Passport.js](http://www.passportjs.org/) 、 `passport-azure-ad`ライブラリを使用するように、パスポートライブラリを初期化し、アプリのアプリ ID とパスワードを設定します。

1. の`app.use('/', indexRouter);` `./app.js`行を検索します。 その行の**前に**次のコードを挿入します。

    ```javascript
    // Initialize passport
    app.use(passport.initialize());
    app.use(passport.session());
    ```

1. という名前`./routes` `auth.js`のディレクトリに新しいファイルを作成し、次のコードを追加します。

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

    これは、、 `signin` `callback`、および`signout`の3つのルートでルーターを定義します。

    ルート`signin`からメソッドが`passport.authenticate`呼び出され、アプリが Azure ログインページにリダイレクトされます。

    この`callback`ルートは、サインインの完了後に Azure がリダイレクトされる場所です。 コードによって`passport.authenticate`メソッドが再度呼び出され`passport-azure-ad` 、アクセストークンを要求するようになります。 トークンが取得されると、次のハンドラーが呼び出されます。これにより、一時的なエラー値でアクセストークンを持つホームページにリダイレクトされます。 これを使用して、サインインが機能していることを確認してから、に進みます。 テストを開始する前に、から`./routes/auth.js`新しいルーターを使用するようにエクスプレスアプリを構成する必要があります。

    メソッド`signout`は、ユーザーをログに記録し、セッションを破棄します。

1. を`./app.js`開いて、行の`var app = express();` **前に**次のコードを挿入します。

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. 行の`app.use('/', indexRouter);` **後**に次のコードを挿入します。

    ```javascript
    app.use('/auth', authRouter);
    ```

サーバーを起動し、を`https://localhost:3000`参照します。 [サインイン] ボタンをクリックすると、`https://login.microsoftonline.com` にリダイレクトされます。 Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。 ブラウザーがアプリにリダイレクトし、トークンが表示されます。

### <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. という名前`graph.js`のプロジェクトのルートに新しいファイルを作成し、次のコードを追加します。

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

    これにより`getUserDetails` 、関数がエクスポートされます。これは、 `/me` Microsoft Graph SDK を使用してエンドポイントを呼び出し、結果を返します。

1. を`/app.js`開き、次`require`のステートメントをファイルの先頭に追加します。

    ```javascript
    var graph = require('./graph');
    ```

1. 既存の `signInComplete` 関数を、以下のコードで置換します。

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

    新しいコードでは、 `profile` Microsoft Graph によって返さ`email`れるデータを使用してプロパティを追加するために、Passport によって提供されたを更新します。

1. 行の`app.use(passport.session());` **後**に次の行を追加します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="AddProfileSnippet":::

    このコードでは、ユーザープロファイルを`locals`応答のプロパティに読み込みます。 これにより、アプリのすべてのビューで使用できるようになります。

## <a name="storing-the-tokens"></a>トークンの格納

トークンを取得できるようになったので、トークンをアプリに格納する手順を実装します。 現時点では、アプリは、raw アクセストークンをメモリ内ユーザーの記憶域に格納しています。 これはサンプルアプリなので、わかりやすくするために、そのまま保存しておきます。 実際のアプリでは、データベースのような、より信頼性の高い安全なストレージ ソリューションを使用します。

ただし、アクセストークンのみを保存しても、有効期限を確認したり、トークンを更新したりすることはできません。 これを有効にするには、 `AccessToken` `simple-oauth2`ライブラリからオブジェクト内のトークンをラップするようにサンプルを更新します。

1. を`./app.js`開き、関数の`signInComplete` **前に**次のコードを追加します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="ConfigureOAuth2Snippet":::

1. 既存の `signInComplete` 関数を、以下の関数で置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SignInCompleteSnippet" highlight="17-18, 21":::

1. の`./routes/auth.js`既存のコールバックルートを次のものに置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackRouteSnippet" highlight="17-18":::

1. サーバーを再起動し、サインインプロセスを実行します。 ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. 右上隅にあるユーザーアバターをクリックして、[**サインアウト**] リンクにアクセスします。 **[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="refreshing-tokens"></a>トークンの更新

この時点で、アプリケーションには、API 呼び出しの`Authorization`ヘッダーで送信されるアクセストークンがあります。 これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。

ただし、このトークンは一時的なものです。 トークンが発行された後、有効期限が切れる時間になります。 ここで、更新トークンが役に立ちます。 更新トークンを使用すると、ユーザーが再度サインインしなくても、アプリは新しいアクセス トークンを要求できます。

1. トークン管理機能を保持するために、という`tokens.js`名前のプロジェクトのルートに新しいファイルを作成します。 次のコードを追加します。

    :::code language="javascript" source="../demo/graph-tutorial/tokens.js" id="TokensSnippet":::

このメソッドは、最初にアクセストークンの有効期限が切れているか、期限切れになるかを確認します。 その場合は、更新トークンを使用して新しいトークンを取得し、次にキャッシュを更新して、新しいアクセストークンを返します。 アクセストークンの保存を停止する必要がある場合は、このメソッドを使用します。
