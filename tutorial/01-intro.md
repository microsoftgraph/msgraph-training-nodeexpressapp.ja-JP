<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c58ee-101">このチュートリアルでは、Microsoft Node.js API を使用してユーザーの予定表情報を取得する Graph Express Web アプリを構築する方法について説明します。</span><span class="sxs-lookup"><span data-stu-id="c58ee-101">This tutorial teaches you how to build a Node.js Express web app that uses the Microsoft Graph API to retrieve calendar information for a user.</span></span>

> [!TIP]
> <span data-ttu-id="c58ee-102">完成したチュートリアルをダウンロードするだけの場合は、2 つの方法でダウンロードできます。</span><span class="sxs-lookup"><span data-stu-id="c58ee-102">If you prefer to just download the completed tutorial, you can download it in two ways.</span></span>
>
> - <span data-ttu-id="c58ee-103">クイック スタート [Node.jsダウンロードして](https://developer.microsoft.com/graph/quick-start?platform=option-node) 、作業コードを数分で取得します。</span><span class="sxs-lookup"><span data-stu-id="c58ee-103">Download the [Node.js quick start](https://developer.microsoft.com/graph/quick-start?platform=option-node) to get working code in minutes.</span></span>
> - <span data-ttu-id="c58ee-104">リポジトリをダウンロードまたは複製[GitHubします](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)。</span><span class="sxs-lookup"><span data-stu-id="c58ee-104">Download or clone the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="c58ee-105">前提条件</span><span class="sxs-lookup"><span data-stu-id="c58ee-105">Prerequisites</span></span>

<span data-ttu-id="c58ee-106">このデモを開始する前に、開発 [Node.js](https://nodejs.org) インストールする必要があります。</span><span class="sxs-lookup"><span data-stu-id="c58ee-106">Before you start this demo, you should have [Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="c58ee-107">インストールされていない場合はNode.js前のリンクにアクセスしてダウンロード オプションを確認してください。</span><span class="sxs-lookup"><span data-stu-id="c58ee-107">If you do not have Node.js, visit the previous link for download options.</span></span>

> [!NOTE]
> <span data-ttu-id="c58ee-108">Windows C/C++ からコンパイルする必要Visual Studio Build Tools NPM モジュールをサポートするために、Python と windows をインストールする必要がある場合があります。</span><span class="sxs-lookup"><span data-stu-id="c58ee-108">Windows users may need to install Python and Visual Studio Build Tools to support NPM modules that need to be compiled from C/C++.</span></span> <span data-ttu-id="c58ee-109">このNode.jsインストーラー Windowsこれらのツールを自動的にインストールするオプションを提供します。</span><span class="sxs-lookup"><span data-stu-id="c58ee-109">The Node.js installer on Windows gives an option to automatically install these tools.</span></span> <span data-ttu-id="c58ee-110">または、以下の手順に従います [https://github.com/nodejs/node-gyp#on-windows](https://github.com/nodejs/node-gyp#on-windows) 。</span><span class="sxs-lookup"><span data-stu-id="c58ee-110">Alternatively, you can follow instructions at [https://github.com/nodejs/node-gyp#on-windows](https://github.com/nodejs/node-gyp#on-windows).</span></span>

<span data-ttu-id="c58ee-111">また、Outlook.com 上のメールボックスを持つ個人用 Microsoft アカウント、または Microsoft の仕事用または学校用のアカウントを持っている必要があります。</span><span class="sxs-lookup"><span data-stu-id="c58ee-111">You should also have either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span> <span data-ttu-id="c58ee-112">Microsoft アカウントをお持ちでない場合は、無料アカウントを取得するためのオプションが 2 つご利用できます。</span><span class="sxs-lookup"><span data-stu-id="c58ee-112">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="c58ee-113">新しい [個人用 Microsoft アカウントにサインアップできます](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)。</span><span class="sxs-lookup"><span data-stu-id="c58ee-113">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="c58ee-114">開発者プログラム[にサインアップしてOffice 365無料](https://developer.microsoft.com/office/dev-program)のサブスクリプションをOffice 365できます。</span><span class="sxs-lookup"><span data-stu-id="c58ee-114">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

> [!NOTE]
> <span data-ttu-id="c58ee-115">このチュートリアルは、ノード バージョン 14.15.0 で記述されています。</span><span class="sxs-lookup"><span data-stu-id="c58ee-115">This tutorial was written with Node version 14.15.0.</span></span> <span data-ttu-id="c58ee-116">このガイドの手順は、他のバージョンでも動作しますが、テストされていない場合があります。</span><span class="sxs-lookup"><span data-stu-id="c58ee-116">The steps in this guide may work with other versions, but that has not been tested.</span></span>

## <a name="feedback"></a><span data-ttu-id="c58ee-117">フィードバック</span><span class="sxs-lookup"><span data-stu-id="c58ee-117">Feedback</span></span>

<span data-ttu-id="c58ee-118">このチュートリアルに関するフィードバックは、GitHub[してください](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp)。</span><span class="sxs-lookup"><span data-stu-id="c58ee-118">Please provide any feedback on this tutorial in the [GitHub repository](https://github.com/microsoftgraph/msgraph-training-nodeexpressapp).</span></span>
