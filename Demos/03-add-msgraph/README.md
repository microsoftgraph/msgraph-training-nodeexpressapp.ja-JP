# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="9af69-101">完了したプロジェクトを実行する方法</span><span class="sxs-lookup"><span data-stu-id="9af69-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9af69-102">前提条件</span><span class="sxs-lookup"><span data-stu-id="9af69-102">Prerequisites</span></span>

<span data-ttu-id="9af69-103">このフォルダーで完了したプロジェクトを実行するには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="9af69-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="9af69-104">開発用のコンピューターにインストールされている[node.js](https://nodejs.org) 。</span><span class="sxs-lookup"><span data-stu-id="9af69-104">[Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="9af69-105">node.js を持っていない場合は、「ダウンロードオプション」の前のリンクにアクセスしてください。</span><span class="sxs-lookup"><span data-stu-id="9af69-105">If you do not have Node.js, visit the previous link for download options.</span></span> <span data-ttu-id="9af69-106">(**注:** このチュートリアルは、ノードバージョン10.7.0 を使用して作成されています。</span><span class="sxs-lookup"><span data-stu-id="9af69-106">(**Note:** This tutorial was written with Node version 10.7.0.</span></span> <span data-ttu-id="9af69-107">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。)</span><span class="sxs-lookup"><span data-stu-id="9af69-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="9af69-108">Outlook.com 上のメールボックスを持つ個人の Microsoft アカウント、または microsoft 職場または学校のアカウントのいずれか。</span><span class="sxs-lookup"><span data-stu-id="9af69-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="9af69-109">Microsoft アカウントを持っていない場合は、無料のアカウントを取得するためのオプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="9af69-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="9af69-110">[新しい個人用 Microsoft アカウントにサインアップ](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)することができます。</span><span class="sxs-lookup"><span data-stu-id="9af69-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="9af69-111">[office 365 開発者プログラムにサインアップ](https://developer.microsoft.com/office/dev-program)して、無料の office 365 サブスクリプションを取得することができます。</span><span class="sxs-lookup"><span data-stu-id="9af69-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-application-registration-portal"></a><span data-ttu-id="9af69-112">web アプリケーションをアプリケーション登録ポータルに登録する</span><span class="sxs-lookup"><span data-stu-id="9af69-112">Register a web application with the Application Registration Portal</span></span>

1. <span data-ttu-id="9af69-113">ブラウザーを開き、[アプリケーション登録ポータル](https://apps.dev.microsoft.com)に移動します。</span><span class="sxs-lookup"><span data-stu-id="9af69-113">Open a browser and navigate to the [Application Registration Portal](https://apps.dev.microsoft.com).</span></span> <span data-ttu-id="9af69-114">**個人アカウント**(別名: Microsoft アカウント) または**職場または学校のアカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="9af69-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="9af69-115">ページの上部にある [**アプリの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9af69-115">Select **Add an app** at the top of the page.</span></span>

    > <span data-ttu-id="9af69-116">**注:** ページに [**アプリの追加**] ボタンが複数表示されている場合は、[収束した**アプリ**] リストに対応するボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="9af69-116">**Note:** If you see more than one **Add an app** button on the page, select the one that corresponds to the **Converged apps** list.</span></span>

1. <span data-ttu-id="9af69-117">[**アプリケーションの登録**] ページで、[**アプリケーション名**] を「 **node.js Graph チュートリアル**」に設定し、[**作成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9af69-117">On the **Register your application** page, set the **Application Name** to **Node.js Graph Tutorial** and select **Create**.</span></span>

    ![アプリ登録ポータル web サイトで新しいアプリを作成するスクリーンショット](/tutorial/images/arp-create-app-01.png)

1. <span data-ttu-id="9af69-119">[ **node.js Graph のチュートリアル登録**] ページの [**プロパティ**] セクションで、後で必要になるように**アプリケーション Id**をコピーします。</span><span class="sxs-lookup"><span data-stu-id="9af69-119">On the **Node.js Graph Tutorial Registration** page, under the **Properties** section, copy the **Application Id** as you will need it later.</span></span>

    ![新しく作成されたアプリケーションの ID のスクリーンショット](/tutorial/images/arp-create-app-02.png)

1. <span data-ttu-id="9af69-121">[**アプリケーションシークレット**] セクションまで下にスクロールします。</span><span class="sxs-lookup"><span data-stu-id="9af69-121">Scroll down to the **Application Secrets** section.</span></span>

    1. <span data-ttu-id="9af69-122">[**新しいパスワードの生成**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9af69-122">Select **Generate New Password**.</span></span>
    1. <span data-ttu-id="9af69-123">[**新しいパスワードが生成さ**れました] ダイアログで、後で必要になるように、ボックスの内容をコピーします。</span><span class="sxs-lookup"><span data-stu-id="9af69-123">In the **New password generated** dialog, copy the contents of the box as you will need it later.</span></span>

        > <span data-ttu-id="9af69-124">**重要:** このパスワードは今後表示されないため、ここでコピーしてください。</span><span class="sxs-lookup"><span data-stu-id="9af69-124">**Important:** This password is never shown again, so make sure you copy it now.</span></span>

    ![新しく作成されたアプリケーションのパスワードのスクリーンショット](/tutorial/images/arp-create-app-03.png)

1. <span data-ttu-id="9af69-126">[**プラットフォーム**] セクションまで下にスクロールします。</span><span class="sxs-lookup"><span data-stu-id="9af69-126">Scroll down to the **Platforms** section.</span></span>

    1. <span data-ttu-id="9af69-127">[**プラットフォームの追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9af69-127">Select **Add Platform**.</span></span>
    1. <span data-ttu-id="9af69-128">[**プラットフォームの追加**] ダイアログで、[ **Web**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9af69-128">In the **Add Platform** dialog, select **Web**.</span></span>

        ![アプリのプラットフォームを作成するスクリーンショット](/tutorial/images/arp-create-app-04.png)

    1. <span data-ttu-id="9af69-130">[ **Web**プラットフォーム] ボックスに、 `http://localhost:3000/auth/callback` **リダイレクト url**の url を入力します。</span><span class="sxs-lookup"><span data-stu-id="9af69-130">In the **Web** platform box, enter the URL `http://localhost:3000/auth/callback` for the **Redirect URLs**.</span></span>

        ![アプリケーションに新たに追加された Web プラットフォームのスクリーンショット](/tutorial/images/arp-create-app-05.png)

1. <span data-ttu-id="9af69-132">ページの一番下までスクロールして、[**保存**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="9af69-132">Scroll to the bottom of the page and select **Save**.</span></span>

## <a name="configure-the-sample"></a><span data-ttu-id="9af69-133">サンプルを構成する</span><span class="sxs-lookup"><span data-stu-id="9af69-133">Configure the sample</span></span>

1. <span data-ttu-id="9af69-134">ファイルの`.env.example`名前を`.env`に変更します。</span><span class="sxs-lookup"><span data-stu-id="9af69-134">Rename the `.env.example` file to `.env`.</span></span>
1. <span data-ttu-id="9af69-135">`.env`ファイルを編集し、次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="9af69-135">Edit the `.env` file and make the following changes.</span></span>
    1. <span data-ttu-id="9af69-136">を`YOUR_APP_ID_HERE`アプリ登録ポータルで取得した**アプリケーション Id**に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9af69-136">Replace `YOUR_APP_ID_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="9af69-137">を`YOUR_APP_PASSWORD_HERE`アプリ登録ポータルから取得したパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="9af69-137">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="9af69-138">コマンドラインインターフェイス (CLI) で、このディレクトリに移動し、次のコマンドを実行して要件をインストールします。</span><span class="sxs-lookup"><span data-stu-id="9af69-138">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    npm install
    ```

## <a name="run-the-sample"></a><span data-ttu-id="9af69-139">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="9af69-139">Run the sample</span></span>

1. <span data-ttu-id="9af69-140">CLI で次のコマンドを実行して、アプリケーションを起動します。</span><span class="sxs-lookup"><span data-stu-id="9af69-140">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="9af69-141">Web ブラウザーを開き、`http://localhost:3000` を参照します。</span><span class="sxs-lookup"><span data-stu-id="9af69-141">Open a browser and browse to `http://localhost:3000`.</span></span>