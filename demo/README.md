# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="1016e-101">完了したプロジェクトを実行する方法</span><span class="sxs-lookup"><span data-stu-id="1016e-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1016e-102">前提条件</span><span class="sxs-lookup"><span data-stu-id="1016e-102">Prerequisites</span></span>

<span data-ttu-id="1016e-103">このフォルダーで完了したプロジェクトを実行するには、次のものが必要です。</span><span class="sxs-lookup"><span data-stu-id="1016e-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="1016e-104">[Node.js](https://nodejs.org) 開発用コンピューターにインストールされます。</span><span class="sxs-lookup"><span data-stu-id="1016e-104">[Node.js](https://nodejs.org) installed on your development machine.</span></span> <span data-ttu-id="1016e-105">Node.js がない場合は、「ダウンロードオプション」の「前へ」のリンクを参照してください。</span><span class="sxs-lookup"><span data-stu-id="1016e-105">If you do not have Node.js, visit the previous link for download options.</span></span> <span data-ttu-id="1016e-106">( **注:** このチュートリアルは、ノードバージョン12.6.1 を使用して作成されています。</span><span class="sxs-lookup"><span data-stu-id="1016e-106">( **Note:** This tutorial was written with Node version 12.6.1.</span></span> <span data-ttu-id="1016e-107">このガイドの手順は、他のバージョンでは動作しますが、テストされていません。)</span><span class="sxs-lookup"><span data-stu-id="1016e-107">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="1016e-108">Outlook.com 上のメールボックスを持つ個人の Microsoft アカウント、または Microsoft 職場または学校のアカウントのいずれか。</span><span class="sxs-lookup"><span data-stu-id="1016e-108">Either a personal Microsoft account with a mailbox on Outlook.com, or a Microsoft work or school account.</span></span>

<span data-ttu-id="1016e-109">Microsoft アカウントを持っていない場合は、無料のアカウントを取得するためのオプションがいくつかあります。</span><span class="sxs-lookup"><span data-stu-id="1016e-109">If you don't have a Microsoft account, there are a couple of options to get a free account:</span></span>

- <span data-ttu-id="1016e-110">[新しい個人用 Microsoft アカウントにサインアップ](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1)することができます。</span><span class="sxs-lookup"><span data-stu-id="1016e-110">You can [sign up for a new personal Microsoft account](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=12&ct=1454618383&rver=6.4.6456.0&wp=MBI_SSL_SHARED&wreply=https://mail.live.com/default.aspx&id=64855&cbcxt=mai&bk=1454618383&uiflavor=web&uaid=b213a65b4fdc484382b6622b3ecaa547&mkt=E-US&lc=1033&lic=1).</span></span>
- <span data-ttu-id="1016e-111">[Office 365 開発者プログラムにサインアップ](https://developer.microsoft.com/office/dev-program)して、無料の office 365 サブスクリプションを取得することができます。</span><span class="sxs-lookup"><span data-stu-id="1016e-111">You can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="1016e-112">Web アプリケーションを Azure Active Directory 管理センターに登録する</span><span class="sxs-lookup"><span data-stu-id="1016e-112">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="1016e-113">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動します。</span><span class="sxs-lookup"><span data-stu-id="1016e-113">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="1016e-114">**個人用アカウント** (別名: Microsoft アカウント)、または **職場/学校アカウント** を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="1016e-114">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="1016e-115">左側のナビゲーションで **[Azure Active Directory]** を選択し、それから **[管理]** で **[アプリの登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1016e-115">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="1016e-116">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="1016e-116">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="1016e-117">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1016e-117">Select **New registration**.</span></span> <span data-ttu-id="1016e-118">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="1016e-118">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="1016e-119">`Node.js Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="1016e-119">Set **Name** to `Node.js Graph Tutorial`.</span></span>
    - <span data-ttu-id="1016e-120">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="1016e-120">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="1016e-121">**[リダイレクト URI]** で、最初のドロップダウン リストを `Web` に設定し、それから `http://localhost:3000/auth/callback` に値を設定します。</span><span class="sxs-lookup"><span data-stu-id="1016e-121">Under **Redirect URI** , set the first drop-down to `Web` and set the value to `http://localhost:3000/auth/callback`.</span></span>

    ![[アプリケーションを登録する] ページのスクリーンショット](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="1016e-123">**[登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1016e-123">Choose **Register**.</span></span> <span data-ttu-id="1016e-124">[ **Node.js Graph のチュートリアル** ] ページで、 **アプリケーション (クライアント) ID** の値をコピーして保存します。次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="1016e-124">On the **Node.js Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリ登録のアプリケーション ID のスクリーンショット](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="1016e-126">**[管理]** で **[証明書とシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1016e-126">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="1016e-127">**[新しいクライアント シークレット]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="1016e-127">Select the **New client secret** button.</span></span> <span data-ttu-id="1016e-128">**[説明]** に値を入力して、 **[有効期限]** のオプションのいずれかを選び、 **[追加]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="1016e-128">Enter a value in **Description** and select one of the options for **Expires** and choose **Add**.</span></span>

    ![[クライアントシークレットの追加] ダイアログのスクリーンショット](/tutorial/images/aad-new-client-secret.png)

1. <span data-ttu-id="1016e-130">このページを離れる前に、クライアント シークレットの値をコピーします。</span><span class="sxs-lookup"><span data-stu-id="1016e-130">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="1016e-131">次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="1016e-131">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="1016e-132">このクライアント シークレットは今後表示されないため、この段階で必ずコピーするようにしてください。</span><span class="sxs-lookup"><span data-stu-id="1016e-132">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![新規追加されたクライアント シークレットのスクリーンショット](/tutorial/images/aad-copy-client-secret.png)

## <a name="configure-the-sample"></a><span data-ttu-id="1016e-134">サンプルを構成する</span><span class="sxs-lookup"><span data-stu-id="1016e-134">Configure the sample</span></span>

1. <span data-ttu-id="1016e-135">ファイルの名前 `example.env` をに変更 `.env` します。</span><span class="sxs-lookup"><span data-stu-id="1016e-135">Rename the `example.env` file to `.env`.</span></span>
1. <span data-ttu-id="1016e-136">ファイルを編集し、 `.env` 次のように変更します。</span><span class="sxs-lookup"><span data-stu-id="1016e-136">Edit the `.env` file and make the following changes.</span></span>
    1. <span data-ttu-id="1016e-137">を `YOUR_CLIENT_SECRET_HERE` アプリ登録ポータルで取得した **アプリケーション Id** に置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1016e-137">Replace `YOUR_CLIENT_SECRET_HERE` with the **Application Id** you got from the App Registration Portal.</span></span>
    1. <span data-ttu-id="1016e-138">を `YOUR_APP_PASSWORD_HERE` アプリ登録ポータルから取得したパスワードに置き換えます。</span><span class="sxs-lookup"><span data-stu-id="1016e-138">Replace `YOUR_APP_PASSWORD_HERE` with the password you got from the App Registration Portal.</span></span>
1. <span data-ttu-id="1016e-139">コマンドラインインターフェイス (CLI) で、このディレクトリに移動し、次のコマンドを実行して要件をインストールします。</span><span class="sxs-lookup"><span data-stu-id="1016e-139">In your command-line interface (CLI), navigate to this directory and run the following command to install requirements.</span></span>

    ```Shell
    npm install
    ```

## <a name="run-the-sample"></a><span data-ttu-id="1016e-140">サンプルを実行する</span><span class="sxs-lookup"><span data-stu-id="1016e-140">Run the sample</span></span>

1. <span data-ttu-id="1016e-141">CLI で次のコマンドを実行して、アプリケーションを起動します。</span><span class="sxs-lookup"><span data-stu-id="1016e-141">Run the following command in your CLI to start the application.</span></span>

    ```Shell
    npm start
    ```

1. <span data-ttu-id="1016e-142">Web ブラウザーを開き、`http://localhost:3000` を参照します。</span><span class="sxs-lookup"><span data-stu-id="1016e-142">Open a browser and browse to `http://localhost:3000`.</span></span>
