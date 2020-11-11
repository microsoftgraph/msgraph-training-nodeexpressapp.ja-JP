<!-- markdownlint-disable MD002 MD041 -->

この演習では、Azure AD での認証をサポートするために、前の手順で作成したアプリケーションを拡張します。 これは、Microsoft Graph を呼び出すために必要な OAuth アクセストークンを取得するために必要です。 この手順では、アプリケーションに [msal ノード](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) ライブラリを統合します。

1. アプリケーションのルートに「 **env** 」という名前の新しいファイルを作成し、次のコードを追加します。

    :::code language="ini" source="../demo/graph-tutorial/example.env":::

    を `YOUR_CLIENT_SECRET_HERE` アプリケーション登録ポータルからのアプリケーション ID に置き換え、生成し `YOUR_APP_SECRET_HERE` たクライアントシークレットに置き換えます。

    > [!IMPORTANT]
    > Git などのソース管理を使用している場合は、後でアプリ ID とパスワードを誤ってリークしないように、ソース管理からその env ファイルを除外することをお勧めし **ます** 。

1. **/app.js** を開き、ファイルの先頭に次の行を追加して、 **env** ファイルを読み込みます。

    ```javascript
    require('dotenv').config();
    ```

## <a name="implement-sign-in"></a>サインインの実装

1. `var indexRouter = require('./routes/index');` **/app.js** の行を検索します。 その行の **前に** 次のコードを挿入します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="MsalInitSnippet":::

    このコードでは、アプリのアプリ ID とパスワードを使用して、msal ノードライブラリを初期化します。

1. **auth.js** という名前の **ルート** ディレクトリに新しいファイルを作成し、次のコードを追加します。

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

    これは `signin` 、、、およびの3つのルートでルーターを定義します。 `callback` `signout`

    この `signin` ルートは、関数を呼び出してログイン url を生成し、 `getAuthCodeUrl` その url にブラウザーをリダイレクトします。

    この `callback` ルートは、サインインの完了後に Azure がリダイレクトされる場所です。 このコードでは、 `acquireTokenByCode` アクセストークンの認証コードを交換するための関数を呼び出します。 トークンが取得されると、一時的なエラー値でアクセストークンを使用して、ホームページにリダイレクトされます。 これを使用して、サインインが機能していることを確認してから、に進みます。 テストを開始する前に、 **/routes/auth.js** から新しいルーターを使用するようにエクスプレスアプリを構成する必要があります。

    メソッドは、 `signout` ユーザーをログに記録し、セッションを破棄します。

1. /app.jsを開き、行の **前に** 次のコードを挿入し **ます。** `var app = express();`

    ```javascript
    var authRouter = require('./routes/auth');
    ```

1. 行の **後** に次のコードを挿入し `app.use('/', indexRouter);` ます。

    ```javascript
    app.use('/auth', authRouter);
    ```

サーバーを起動し、を参照し `https://localhost:3000` ます。 [サインイン] ボタンをクリックすると、`https://login.microsoftonline.com` にリダイレクトされます。 Microsoft アカウントを使用してログインし、要求されたアクセス許可に同意します。 ブラウザーがアプリにリダイレクトし、トークンが表示されます。

### <a name="get-user-details"></a>ユーザーの詳細情報を取得する

1. **graph.js** という名前のプロジェクトのルートに新しいファイルを作成し、次のコードを追加します。

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

    これにより、関数がエクスポートされます。これは、 `getUserDetails` Microsoft GRAPH SDK を使用してエンドポイントを呼び出し、 `/me` 結果を返します。

1. **/Routes/auth.js** を開き、次の `require` ステートメントをファイルの先頭に追加します。

    ```javascript
    var graph = require('../graph');
    ```

1. 既存のコールバックルートを次のコードに置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/auth.js" id="CallbackSnippet" highlight="13-23":::

    新しいコードによって、ユーザーのアカウント ID がセッションに保存され、Microsoft Graph からユーザーの詳細が取得され、アプリのユーザーストレージに保存されます。

1. サーバーを再起動し、サインインプロセスを実行します。 ホームページに戻る必要がありますが、UI は、サインインしていることを示すように変更する必要があります。

    ![サインイン後のホーム ページのスクリーンショット](./images/add-aad-auth-01.png)

1. 右上隅にあるユーザーアバターをクリックして、[ **サインアウト** ] リンクにアクセスします。 **[サインアウト]** をクリックすると、セッションがリセットされ、ホーム ページに戻ります。

    ![[サインアウト] リンクのドロップダウン メニューのスクリーンショット](./images/add-aad-auth-02.png)

## <a name="storing-and-refreshing-tokens"></a>トークンの格納と更新

この時点で、アプリケーションには、API 呼び出しのヘッダーで送信されるアクセストークンがあり `Authorization` ます。 これは、アプリがユーザーに代わって Microsoft Graph にアクセスできるようにするトークンです。

ただし、このトークンは一時的なものです。 トークンが発行された後、有効期限が切れる時間になります。 ここで、更新トークンが役に立ちます。 OAuth 仕様では、更新トークンが導入されています。これにより、ユーザーが再度サインインする必要なく、新しいアクセストークンを要求することができます。

アプリでは msal ノードパッケージが使用されているため、トークンストレージや更新ロジックを実装する必要はありません。 アプリは、サンプルアプリケーションに十分な既定の msal ノードのメモリ内トークンキャッシュを使用します。 運用アプリケーションは、トークンキャッシュをセキュリティで保護された信頼性の高いストレージメディアにシリアル化するための独自の [キャッシュプラグイン](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md) を提供する必要があります。
