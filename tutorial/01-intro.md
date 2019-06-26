<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="36c90-101">このチュートリアルでは、Microsoft Graph API を使用してユーザーの予定表情報を取得する node.js Express web app を構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="36c90-101">This tutorial teaches you how to build a Node.js Express web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="36c90-102">完成したチュートリアルをダウンロードするだけで済む場合は、2つの方法でダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="36c90-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="36c90-103">[ノード .js のクイックスタート](https://developer.microsoft.com/graph/quick-start?platform=option-node)をダウンロードして、作業中のコードを分単位で取得します。</span><span class="sxs-lookup"><span data-stu-id="36c90-103">Download the [Node.js quick start](https://developer.microsoft.com/graph/quick-start?platform=option-node) to get working code in minutes.</span></span>
> - <span data-ttu-id="36c90-104">[GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)をダウンロードするか、クローンを作成します。</span><span class="sxs-lookup"><span data-stu-id="36c90-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="36c90-105">前提条件</span><span class="sxs-lookup"><span data-stu-id="36c90-105">Prerequisites</span></span>

<span data-ttu-id="36c90-106">このデモを開始する前に、開発用のコンピューターに[node.js](https://nodejs.org)をインストールしておく必要があります。</span><span class="sxs-lookup"><span data-stu-id="36c90-106">Before you start this demo, you should have [Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="36c90-107">Node.js を持っていない場合は、「ダウンロードオプション」の前のリンクにアクセスしてください。</span><span class="sxs-lookup"><span data-stu-id="36c90-107">If you do not have Node.js, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="36c90-108">このチュートリアルは、ノードバージョン10.15.3 を使用して作成されました。</span><span class="sxs-lookup"><span data-stu-id="36c90-108">This tutorial was written with Node version 10.15.3.</span></span> <span data-ttu-id="36c90-109">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。</span><span class="sxs-lookup"><span data-stu-id="36c90-109">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="watch-the-tutorial"></a><span data-ttu-id="36c90-110">チュートリアルを見る</span><span class="sxs-lookup"><span data-stu-id="36c90-110">Watch the tutorial</span></span>

<span data-ttu-id="36c90-111">このモジュールは、Office 開発 YouTube チャネルで記録されており、利用できます。</span><span class="sxs-lookup"><span data-stu-id="36c90-111">This module has been recorded and is available in the Office Development YouTube channel.</span></span>

<!-- markdownlint-disable MD033 MD034 -->
<br/>

> [!VIDEO https://www.youtube-nocookie.com/embed/n6q8Cm-pTYY]
<!-- markdownlint-enable MD033 MD034 -->

## <a name="feedback"></a><span data-ttu-id="36c90-112">フィードバック</span><span class="sxs-lookup"><span data-stu-id="36c90-112">Feedback</span></span>

<span data-ttu-id="36c90-113">このチュートリアルに関するフィードバックは、 [GitHub リポジトリ](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)に記入してください。</span><span class="sxs-lookup"><span data-stu-id="36c90-113">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span></span>
