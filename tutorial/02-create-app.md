<!-- markdownlint-disable MD002 MD041 -->

この手順では、 [Express](http://expressjs.com/) を使用して web アプリを構築します。

1. CLI を開き、ファイルを作成する権限があるディレクトリに移動し、次のコマンドを実行して、 [Handlebars](http://handlebarsjs.com/) をレンダリングエンジンとして使用する新しいエクスプレスアプリを作成します。

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    エクスプレスジェネレーターは、という名前の新しいディレクトリを作成 `graph-tutorial` し、スキャフォールディングというエクスプレスアプリを作成します。

1. ディレクトリに移動 `graph-tutorial` し、次のコマンドを入力して依存関係をインストールします。

    ```Shell
    npm install
    ```

1. 報告された脆弱性でノードパッケージを更新するには、次のコマンドを実行します。

    ```Shell
    npm audit fix
    ```

1. 次のコマンドを実行して、Express とその他の依存関係のバージョンを更新します。

    ```Shell
    npm install express@4.17.1 http-errors@1.8.0 morgan@1.10.0 debug@4.1.1
    ```

1. 次のコマンドを使用して、ローカル web サーバーを開始します。

    ```Shell
    npm start
    ```

1. ブラウザーを開き、`http://localhost:3000` に移動します。 すべてが動作している場合は、"Welcome to Express" メッセージが表示されます。 このメッセージが表示されない場合は、『 [Express 入門ガイド』](http://expressjs.com/starter/generator.html)を確認してください。

## <a name="install-node-packages"></a>ノードパッケージをインストールする

に進む前に、後で使用する追加のパッケージをインストールします。

- [dotenv](https://github.com/motdotla/dotenv) ファイルから値を読み込むためのものです。
- 日付/時刻の値を書式設定する[モーメント](https://github.com/moment/moment/)
- windows のタイムゾーン名を IANA のタイムゾーン Id に変換するための[windows の iana](https://github.com/rubenillodo/windows-iana) 。
- [接続-](https://github.com/jaredhanson/connect-flash) アプリでフラッシュエラーメッセージにフラッシュします。
- メモリ内のサーバー側セッションに値を格納するための[エクスプレスセッション](https://github.com/expressjs/session)。
- [速達-ルーター](https://github.com/express-promise-router/express-promise-router) は、ルートハンドラーが promise を返すことができるようにします。
- フォームデータを解析および検証するための、[エクスプレス検証](https://github.com/express-validator/express-validator)。
- アクセストークンを認証および取得するための[msal ノード](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node)。
- Guid を生成するために msal ノードで使用される[uuid](https://github.com/uuidjs/uuid) 。
- [microsoft graph-](https://github.com/microsoftgraph/msgraph-sdk-javascript) microsoft graph に電話をかけるためのクライアントです。
- [isomorphic](https://github.com/matthew-andrews/isomorphic-fetch) 。ノードのフェッチを polyfill にフェッチします。 ライブラリには fetch polyfill が必要です `microsoft-graph-client` 。 詳細については、「 [Microsoft Graph JavaScript クライアントライブラリ wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) 」を参照してください。

1. CLI で次のコマンドを実行します。

    ```Shell
    npm install dotenv@8.2.0 moment@2.29.1 moment-timezone@0.5.31 connect-flash@0.1.1 express-session@1.17.1 isomorphic-fetch@3.0.0
    npm install @azure/msal-node@1.0.0-beta.0 @microsoft/microsoft-graph-client@2.1.1 windows-iana@4.2.1 express-validator@6.6.1 uuid@8.3.1
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

1. とミドルウェアを使用するようにアプリケーションを更新し `connect-flash` `express-session` ます。 **/app.js** を開き、次の `require` ステートメントをファイルの先頭に追加します。

    ```javascript
    const session = require('express-session');
    const flash = require('connect-flash');
    const msal = require('@azure/msal-node');
    ```

1. 行の直後に次のコードを追加し `var app = express();` ます。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、アプリの UI を実装します。

1. を開き、次のコードを使用して、内容全体を次のコードで置き換え **ます。**

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    このコードはシンプルなスタイルのために [Bootstrap](http://getbootstrap.com/) を追加し、シンプルなアイコンのために [Font Awesome](https://fontawesome.com/) を追加します。 また、ナビゲーションバーのあるグローバルレイアウトを定義します。

1. /Public/stylesheets/style.css を開き、内容全体を次のように置き換えます **。**

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. を開き、その内容を次のように置き換えます **。**

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. **/Routes/index.js** を開き、既存のコードを次のように置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. 変更内容をすべて保存し、サーバーを再起動します。 この時点で、アプリの外観は大きく異なります。

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
