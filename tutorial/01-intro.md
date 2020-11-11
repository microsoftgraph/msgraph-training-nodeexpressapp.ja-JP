<!-- markdownlint-disable MD002 MD041 -->

このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する Node.js Express web アプリを構築する方法について説明します。

> [!TIP]
> 完成したチュートリアルをダウンロードするだけで済む場合は、2つの方法でダウンロードできます。
>
> - Node.js の [ クイックスタート](https://developer.microsoft.com/graph/quick-start?platform=option-node) をダウンロードして、時間単位で作業コードを取得します。
> - [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)をダウンロードするか、クローンを作成します。

## <a name="prerequisites"></a>前提条件

このデモを開始する前に、開発用コンピューターに [Node.js](https://nodejs.org) をインストールしておく必要があります。 Node.js がない場合は、「ダウンロードオプション」の「前へ」のリンクを参照してください。

> [!NOTE]
> Windows ユーザーは、C/c + + でコンパイルする必要がある NPM モジュールをサポートするために、Python と Visual Studio のビルドツールをインストールする必要がある場合があります。 Windows の Node.js インストーラーでは、これらのツールを自動的にインストールするオプションが提供されます。 または、に記載されている手順に従うこともでき [https://github.com/nodejs/node-gyp#on-windows](https://github.com/nodejs/node-gyp#on-windows) ます。

また、Outlook.com 上のメールボックスを持つ個人の Microsoft アカウント、または Microsoft 職場または学校のアカウントを所有している必要があります。 Microsoft アカウントを持っていない場合は、無料のアカウントを取得するためのオプションがいくつかあります。

- [新しい個人用 Microsoft アカウントにサインアップ](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)することができます。
- [Office 365 開発者プログラムにサインアップ](https://developer.microsoft.com/office/dev-program)して、無料の office 365 サブスクリプションを取得することができます。

> [!NOTE]
> このチュートリアルは、ノードバージョン12.18.4 を使用して作成されました。 このガイドの手順は、他のバージョンでは動作しますが、テストされていません。

## <a name="feedback"></a>フィードバック

このチュートリアルに関するフィードバックは、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)に記入してください。
