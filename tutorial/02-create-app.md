<!-- markdownlint-disable MD002 MD041 -->

この演習では [、Express を使用して Web](http://expressjs.com/) アプリを構築します。

1. CLI を開き、ファイルを作成する権限を持つディレクトリに移動し、次のコマンドを実行して、レンダリング エンジンとして [Handlebars](http://handlebarsjs.com/) を使用する新しい Express アプリを作成します。

    ```Shell
    npx express-generator@4.16.1 --hbs graph-tutorial
    ```

    Express ジェネレーターは、Express アプリと呼ばれる新しいディレクトリ `graph-tutorial` を作成し、スキャフォールディングします。

1. ディレクトリに移動 `graph-tutorial` し、次のコマンドを入力して依存関係をインストールします。

    ```Shell
    npm install
    ```

1. 次のコマンドを実行して、報告された脆弱性を持つノード パッケージを更新します。

    ```Shell
    npm audit fix
    ```

1. 次のコマンドを実行して、Express のバージョンと他の依存関係を更新します。

    ```Shell
    npm install express@4.17.1 http-errors@1.8.0 morgan@1.10.0 debug@4.1.1
    ```

1. ローカル Web サーバーを起動するには、次のコマンドを使用します。

    ```Shell
    npm start
    ```

1. ブラウザーを開き、`http://localhost:3000` に移動します。 すべてが動作している場合は、"Express へようこそ" というメッセージが表示されます。 If you don't see that message, check the [Express getting started guide](http://expressjs.com/starter/generator.html).

## <a name="install-node-packages"></a>ノード パッケージのインストール

次に進む前に、後で使用する追加のパッケージをインストールします。

- .env ファイルから値を読み込む場合の[dotenv。](https://github.com/motdotla/dotenv)
- [moment](https://github.com/moment/moment/) for formatting date/time values.
- [Windows タイム ゾーン名を](https://github.com/rubenillodo/windows-iana) IANA タイム ゾーンの ID に変換する windows-iana。
- [connect-flash](https://github.com/jaredhanson/connect-flash) to flash error messages in the app.
- [メモリ内の](https://github.com/expressjs/session) サーバー側セッションに値を格納する高速セッション。
- ルート ハンドラーが Promise を返す[express-promise-router。](https://github.com/express-promise-router/express-promise-router)
- [フォーム データを解析](https://github.com/express-validator/express-validator) および検証するための高速検証ツール。
- [アクセス トークンを認証](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) および取得する msal-node。
- GUID を生成するために msal-node によって使用される[uuid。](https://github.com/uuidjs/uuid)
- [Microsoft Graph を呼び](https://github.com/microsoftgraph/msgraph-sdk-javascript) 出す microsoft-graph-client。
- [node のフェッチをポリフィルする isomorphic-fetch。](https://github.com/matthew-andrews/isomorphic-fetch) ライブラリにはフェッチ ポリフィルが必要 `microsoft-graph-client` です。 詳細については [、Microsoft Graph JavaScript クライアント ライブラリ Wiki](https://github.com/microsoftgraph/msgraph-sdk-javascript/wiki/Migration-from-1.x.x-to-2.x.x#polyfill-only-when-required) を参照してください。

1. CLI で次のコマンドを実行します。

    ```Shell
    npm install dotenv@8.2.0 moment@2.29.1 moment-timezone@0.5.31 connect-flash@0.1.1 express-session@1.17.1 express-promise-router@4.0.1 isomorphic-fetch@3.0.0
    npm install @azure/msal-node@1.0.0-beta.0 @microsoft/microsoft-graph-client@2.1.1 windows-iana@4.2.1 express-validator@6.6.1 uuid@8.3.1
    ```

    > [!TIP]
    > これらのパッケージを Windows にインストールしようとするときに、Windows ユーザーに次のエラー メッセージが表示されることがあります。
    >
    > ```Shell
    > gyp ERR! stack Error: Can't find Python executable "python", you can set the PYTHON env variable.
    > ```
    >
    > このエラーを解決するには、次のコマンドを実行して、管理者特権の (管理者) ターミナル ウィンドウを使用して Windows ビルド ツールをインストールします。このウィンドウでは、VS Build Tools と Python がインストールされます。
    >
    > ```Shell
    > npm install --global --production windows-build-tools
    > ```

1. アプリケーションを更新して、ミドルウェアを `connect-flash` 使用 `express-session` します。 **./app.js** を開き、ファイルの一番上に `require` 次のステートメントを追加します。

    ```javascript
    const session = require('express-session');
    const flash = require('connect-flash');
    const msal = require('@azure/msal-node');
    ```

1. 行の直後に次のコードを追加 `var app = express();` します。

    :::code language="javascript" source="../demo/graph-tutorial/app.js" id="SessionSnippet":::

## <a name="design-the-app"></a>アプリを設計する

このセクションでは、アプリの UI を実装します。

1. **./views/layout.hs** を開き、内容全体を次のコードに置き換えます。

    :::code language="html" source="../demo/graph-tutorial/views/layout.hbs" id="LayoutSnippet":::

    このコードはシンプルなスタイルのために [Bootstrap](http://getbootstrap.com/) を追加し、シンプルなアイコンのために [Font Awesome](https://fontawesome.com/) を追加します。 また、ナビゲーション バーを含むグローバル レイアウトも定義します。

1. **./public/stylesheets/style.css** を開き、その内容全体を次の内容に置き換えてください。

    :::code language="css" source="../demo/graph-tutorial/public/stylesheets/style.css":::

1. **./views/index.hs** を開き、その内容を次の内容に置き換えます。

    :::code language="html" source="../demo/graph-tutorial/views/index.hbs" id="IndexSnippet":::

1. **./routes/index.js** 開き、既存のコードを次のコードに置き換えます。

    :::code language="javascript" source="../demo/graph-tutorial/routes/index.js" id="IndexRouterSnippet" highlight="6-10":::

1. 変更内容をすべて保存し、サーバーを再起動します。 これで、アプリは非常に異なって表示されます。

    ![デザインが変更されたホーム ページのスクリーンショット](./images/create-app-01.png)
