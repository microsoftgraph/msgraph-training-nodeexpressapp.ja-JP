<!-- markdownlint-disable MD002 MD041 -->

この手順では、 [Express](http://expressjs.com/)を使用して web アプリを構築します。

1. CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して、 [Handlebars](http://handlebarsjs.com/)をレンダリングエンジンとして使用する新しいエクスプレスアプリを作成します。

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    エクスプレスジェネレーターは、という名前の`graph-tutorial`新しいディレクトリを作成し、スキャフォールディングというエクスプレスアプリを作成します。

1. `graph-tutorial`ディレクトリに移動し、次のコマンドを入力して依存関係をインストールします。

    ```Shell
    npm install
    ```

1. 報告された脆弱性でノードパッケージを更新するには、次のコマンドを実行します。

    ```Shell
    npm audit fix
    ```

1. 次のコマンドを使用して、ローカル web サーバーを開始します。

    ```Shell
    npm start
    ```

1. ブラウザーを開き、`http://localhost:3000` に移動します。 すべてが動作している場合は、"Welcome to Express" メッセージが表示されます。 このメッセージが表示されない場合は、『 [Express 入門ガイド』](http://expressjs.com/starter/generator.html)を確認してください。

## <a name="install-node-packages"></a>ノードパッケージをインストールする

に進む前に、後で使用する追加のパッケージをインストールします。

- [dotenv](https://github.com/motdotla/dotenv)ファイルから値を読み込むためのものです。
- 日付/時刻の値を書式設定する[モーメント](https://github.com/moment/moment/)
- [接続-](https://github.com/jaredhanson/connect-flash)アプリでフラッシュエラーメッセージにフラッシュします。
- メモリ内のサーバー側セッションに値を格納するための[エクスプレスセッション](https://github.com/expressjs/session)。
- [パスポート-ad](https://github.com/AzureAD/passport-azure-ad)は、アクセストークンを認証および取得するために使用します。
- トークン管理のための[oauth2](https://github.com/lelylan/simple-oauth2) 。
- [microsoft graph-](https://github.com/microsoftgraph/msgraph-sdk-javascript) microsoft graph に電話をかけるためのクライアントです。
- [isomorphic](https://github.com/matthew-andrews/isomorphic-fetch) 。ノードのフェッチを polyfill にフェッチします。 `microsoft-graph-client`ライブラリには fetch polyfill が必要です。 詳細については、「 [Microsoft Graph JavaScript クライアントライブラリ wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) 」を参照してください。

1. CLI で次のコマンドを実行します。

    ```Shell
    npm install dotenv@8.2.0 moment@2.24.0 connect-flash@0.1.1 express-session@1.17.0 isomorphic-fetch@2.2.1
    npm install passport-azure-ad@4.2.1 simple-oauth2@3.3.0 @microsoft/microsoft-graph-client@2.0.0
    ```

    > [!TIP]
    > Windows にこれらのパッケージをインストールしようとすると、windows ユーザーに次のエラーメッセージが表示されることがあります。
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > このエラーを解決するには、次のコマンドを実行して、VS ビルドツールおよび Python をインストールする管理者 (管理者) ターミナルウィンドウを使用して Windows ビルドツールをインストールします。
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. `connect-flash`と`express-session`ミドルウェアを使用するようにアプリケーションを更新します。 `./app.js`ファイルを開き、次`require`のステートメントをファイルの先頭に追加します。

    ```javascript
    var session = require('express-session');
    var flash = require('connect-flash');
    ```

1. 行の`var app = express();`直後に次のコードを追加します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、アプリの UI を実装します。

1. `./views/layout.hbs`ファイルを開き、内容全体を次のコードに置き換えます。

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    このコードはシンプルなスタイルのために [Bootstrap](http://getbootstrap.com/) を追加し、シンプルなアイコンのために [Font Awesome](https://fontawesome.com/) を追加します。 また、ナビゲーションバーのあるグローバルレイアウトを定義します。

1. を`./public/stylesheets/style.css`開き、内容全体を次のように置き換えます。

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. `./views/index.hbs`ファイルを開き、その内容を次のように置き換えます。

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. `./routes/index.js`ファイルを開き、既存のコードを次のように置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. 変更内容をすべて保存し、サーバーを再起動します。 この時点で、アプリの外観は大きく異なります。

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
