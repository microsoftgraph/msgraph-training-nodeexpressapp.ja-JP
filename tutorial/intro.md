<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="a182f-101">このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する node.js Express web app を構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="a182f-101">This tutorial teaches you how to build a Node.js Express web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="a182f-102">完成したチュートリアルをダウンロードするだけで済む場合は、2つの方法でダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="a182f-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="a182f-103">[ノード .js のクイックスタート](https://developer.microsoft.com/graph/quick-start?platform=option-node)をダウンロードして、作業中のコードを分単位で取得します。</span><span class="sxs-lookup"><span data-stu-id="a182f-103">Download the [Node.js quick start](https://developer.microsoft.com/graph/quick-start?platform=option-node) to get working code in minutes.</span></span>
> - <span data-ttu-id="a182f-104">[GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)をダウンロードするか、クローンを作成します。</span><span class="sxs-lookup"><span data-stu-id="a182f-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="a182f-105">前提条件</span><span class="sxs-lookup"><span data-stu-id="a182f-105">Prerequisites</span></span>

<span data-ttu-id="a182f-106">このデモを開始する前に、開発用のコンピューターに[node.js](https://nodejs.org)をインストールしておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="a182f-106">Before you start this demo, you should have [Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="a182f-107">node.js を持っていない場合は、「ダウンロードオプション」の前のリンクにアクセスしてください。</span><span class="sxs-lookup"><span data-stu-id="a182f-107">If you do not have Node.js, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="a182f-108">このチュートリアルは、ノードバージョン10.7.0 を使用して作成されました。</span><span class="sxs-lookup"><span data-stu-id="a182f-108">This tutorial was written with Node version 10.7.0.</span></span> <span data-ttu-id="a182f-109">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。</span><span class="sxs-lookup"><span data-stu-id="a182f-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="a182f-110">フィードバック</span><span class="sxs-lookup"><span data-stu-id="a182f-110">Feedback</span></span>

<span data-ttu-id="a182f-111">このチュートリアルに関するフィードバックは、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)に記入してください。</span><span class="sxs-lookup"><span data-stu-id="a182f-111">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span></span>