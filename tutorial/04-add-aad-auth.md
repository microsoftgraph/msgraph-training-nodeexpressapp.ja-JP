<!-- markdownlint-disable MD002 MD041 -->

この演習では、前の演習のアプリケーションを拡張して、Azure AD での認証をサポートします。 これは、Microsoft Graph を呼び出すのに必要な OAuth アクセス トークンを取得するために必要です。 この手順では [、msal-node ライブラリを](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) アプリケーションに統合します。

1. アプリケーションのルートに **.env** という名前の新しいファイルを作成し、次のコードを追加します。

    :::code language="ini" source="../demo/graph-tutorial/example.env":::

    アプリケーション `YOUR_CLIENT_SECRET_HERE` 登録ポータルのアプリケーション ID に置き換え、生成したクライアント シークレット `YOUR_APP_SECRET_HERE` に置き換える。

    > [!IMPORTANT]
    > git などのソース管理を使用している場合は **、.env** ファイルをソース管理から除外して、アプリ ID とパスワードが誤って漏洩しないようにする良い時期です。

1. **./app.js** 開き、ファイルの一番上に次の行を追加して **.env ファイルを読み込** む。

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a>サインインの実装

1. `var app = express();` **./app.js** で行を見app.js。 その行の後に次 **のコードを** 挿入します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="MsalInitSnippet":::

    このコードは、アプリのアプリ ID とパスワードを使用して msal-node ライブラリを初期化します。

1. **./routes** ディレクトリに新しいファイルを作成し、auth.js **コードを** 追加します。

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

    これにより、次の 3 つのルートを持つ `signin` ルーター `callback` が定義されます。 `signout`

    ルート `signin` は関数を呼び出してログイン URL を生成し、ブラウザーをその `getAuthCodeUrl` URL にリダイレクトします。

    ルート `callback` は、サインインの完了後に Azure がリダイレクトする場所です。 コードは、アクセス `acquireTokenByCode` トークンの認証コードを交換する関数を呼び出します。 トークンを取得すると、一時的なエラー値でアクセス トークンを使用してホーム ページにリダイレクトされます。 これを使用して、次に進む前にサインインが機能しているのを確認します。 テストする前に、./routes/auth.jsから新しいルーターを使用する Express アプリ **を構成する必要auth.js。**

    この `signout` メソッドは、ユーザーをログアウトし、セッションを破棄します。

1. **./app.jsを開** き、行の前に次 **のコードを** 挿入 `var app = express();` します。

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. 行の後に次 **のコードを** `app.use('/', indexRouter);` 挿入します。

    ```javascript
    app.use('/auth', authRouter);
    ```

サーバーを起動し、参照します `https://localhost:3000` 。 [サインイン] ボタンをクリックすると、`https://login.microsoftonline.com` にリダイレクトされます。 Microsoft アカウントでログインし、要求されたアクセス許可に同意します。 ブラウザーがアプリにリダイレクトし、トークンが表示されます。

### <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. プロジェクトのルートに新しいファイルを作成し、graph.js **コードを** 追加します。

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

    これにより、Microsoft Graph SDK を使用してエンドポイントを呼び出し、結果を返す関数 `getUserDetails` `/me` がエクスポートされます。

1. **./routes/auth.js** 開き、ファイルの一番上に次の `require` ステートメントを追加します。

    ```javascript
    var graph = require('../graph');
    ```

1. 既存のコールバック ルートを次のコードに置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackSnippet" highlight="13-23":::

    新しいコードは、ユーザーのアカウント ID をセッションに保存し、Microsoft Graph からユーザーの詳細を取得して、アプリのユーザー ストレージに保存します。

1. サーバーを再起動し、サインイン プロセスを実行します。 ホーム ページに戻る必要がありますが、サインイン中を示すために UI が変更される必要があります。

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. 右上隅にあるユーザー アバターをクリックして、[サインアウト **] リンクにアクセス** します。 **[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>トークンの保存と更新

この時点で、アプリケーションはアクセス トークンを持ち、API 呼び出しのヘッダー `Authorization` で送信されます。 これは、アプリがユーザーの代わりに Microsoft Graph にアクセスできるトークンです。

ただし、このトークンは一時的なものです。 トークンは発行後 1 時間で期限切れになります。 ここで、更新トークンが役に立ちます。 OAuth の仕様には更新トークンが導入されています。これにより、アプリはユーザーが再びサインインする必要なしに新しいアクセス トークンを要求できます。

アプリは msal-node パッケージを使うので、トークンストレージや更新ロジックを実装する必要は一切必要ではありません。 アプリは、サンプル アプリケーションで十分な既定の msal-node メモリ内トークン キャッシュを使用します。 実稼働アプリケーションは、安全で[](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)信頼性の高いストレージ メディアでトークン キャッシュをシリアル化するための独自のキャッシュ プラグインを提供する必要があります。
