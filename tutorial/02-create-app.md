<!-- markdownlint-disable MD002 MD041 -->

この演習では [、Express を使用して Web](http://expressjs.com/) アプリをビルドします。

1. CLI を開き、ファイルを作成する権限を持つディレクトリに移動し、次のコマンドを実行して、ハンドルバーを[](http://handlebarsjs.com/)レンダリング エンジンとして使用する新しい Express アプリを作成します。

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    Express ジェネレーターは、Express アプリと呼ばれる新しいディレクトリを作成し `graph-tutorial` 、スキャフォールディングします。

1. ディレクトリに移動 `graph-tutorial` し、次のコマンドを入力して依存関係をインストールします。

    ```Shell
    npm install
    ```

1. 次のコマンドを実行して、報告された脆弱性でノード パッケージを更新します。

    ```Shell
    npm audit fix
    ```

1. 次のコマンドを実行して、Express および他の依存関係のバージョンを更新します。

    ```Shell
    npm install express@4.17.1 http-errors@1.8.0 morgan@1.10.0 debug@4.3.1 hbs@4.1.2
    ```

1. ローカル Web サーバーを起動するには、次のコマンドを使用します。

    ```Shell
    npm start
    ```

1. ブラウザーを開き、`http://localhost:3000` に移動します。 すべてが機能している場合は、"Express へようこそ" というメッセージが表示されます。 そのメッセージが表示できない場合は、「Express の開始ガイド [」を参照してください](http://expressjs.com/starter/generator.html)。

## <a name="install-node-packages"></a>ノード パッケージのインストール

次に進む前に、後で使用する追加のパッケージをインストールします。

- .env ファイルから値を読み込む場合の[dotenv。](https://github.com/motdotla/dotenv)
- [日付/時刻の値を書式設定する date-fns。](https://github.com/date-fns/date-fns)
- [windows-iana](https://github.com/rubenillodo/windows-iana)を使用して、Windowsタイム ゾーン名を IANA タイム ゾーンの ID に変換します。
- [アプリ内のフラッシュ](https://github.com/jaredhanson/connect-flash) エラー メッセージに接続します。
- [メモリ内のサーバー](https://github.com/expressjs/session) 側セッションに値を格納する express-session。
- ルート ハンドラーが Promise を返すのを許可する[express-promise-router。](https://github.com/express-promise-router/express-promise-router)
- [フォーム データを解析](https://github.com/express-validator/express-validator) および検証するための express-validator。
- [アクセス トークンを認証](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) および取得する msal-node。
- [microsoft-graph-client for making](https://github.com/microsoftgraph/msgraph-sdk-javascript) calls to Microsoft Graph.
- [isomorphic-fetch to](https://github.com/matthew-andrews/isomorphic-fetch) polyfill the fetch for Node. ライブラリにはフェッチ ポリフィルが必要 `microsoft-graph-client` です。 詳細については[、「Microsoft Graph JavaScript クライアント ライブラリ wiki」](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required)を参照してください。

1. CLI で次のコマンドを実行します。

    ```Shell
    npm install dotenv@8.2.0 date-fns@2.21.1 date-fns-tz@1.1.4 connect-flash@0.1.1 express-validator@6.10.0
    npm install express-session@1.17.1 express-promise-router@4.1.0 isomorphic-fetch@3.0.0
    npm install @azure/msal-node@1.0.2 @microsoft/microsoft-graph-client@2.2.1 windows-iana@5.0.1
    ```

    > [!TIP]
    > Windowsにこれらのパッケージをインストールしようとするときに、次のエラー メッセージが表示される場合Windows。
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > エラーを解決するには、次のコマンドを実行して、VS ビルド ツールと Python をインストールする管理者特権 (管理者) ターミナル ウィンドウを使用して Windows ビルド ツールをインストールします。
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. アプリケーションを更新して、ミドルウェア `connect-flash` を使用 `express-session` します。 **./app.js** を開き、ファイルの `require` 上部に次のステートメントを追加します。

    ```javascript
    const session = require('express-session');
    const flash = require('connect-flash');
    const msal = require('@azure/msal-node');
    ```

1. 行の直後に次のコードを追加 `var app = express();` します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、アプリの UI を実装します。

1. **./views/layout.hbs** を開き、コンテンツ全体を次のコードに置き換えます。

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    このコードはシンプルなスタイルのために [Bootstrap](http://getbootstrap.com/) を追加し、シンプルなアイコンのために [Font Awesome](https://fontawesome.com/) を追加します。 また、ナビゲーション バーを持つグローバル レイアウトも定義します。

1. **./public/stylesheets/style.css** を開き、その内容全体を次に置き換えてください。

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. **./views/index.hbs** を開き、その内容を次に置き換えます。

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. **./routes/index.js** 開き、既存のコードを次に置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. 変更内容をすべて保存し、サーバーを再起動します。 これで、アプリは非常に異なって見える必要があります。

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
