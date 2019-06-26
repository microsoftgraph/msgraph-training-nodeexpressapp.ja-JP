<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="dd198-101">この演習では、Azure Active Directory 管理センターを使用して、新しい Azure AD web アプリケーション登録を作成します。</span><span class="sxs-lookup"><span data-stu-id="dd198-101">In this exercise, you will create a new Azure AD web application registration using the Azure Active Directory admin center.</span></span>

1. <span data-ttu-id="dd198-102">ブラウザーを開き、[Azure Active Directory 管理センター](https://aad.portal.azure.com)に移動します。</span><span class="sxs-lookup"><span data-stu-id="dd198-102">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com).</span></span> <span data-ttu-id="dd198-103">**個人用アカウント** (別名: Microsoft アカウント)、または**職場/学校アカウント**を使用してログインします。</span><span class="sxs-lookup"><span data-stu-id="dd198-103">Login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="dd198-104">左側のナビゲーションで [ **Azure Active Directory** ] を選択し、[**管理**] の下にある [**アプリの登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd198-104">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="dd198-105">アプリの登録のスクリーンショット</span><span class="sxs-lookup"><span data-stu-id="dd198-105">A screenshot of the App registrations</span></span> ](./images/aad-portal-app-registrations.png)

1. <span data-ttu-id="dd198-106">**[新規登録]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd198-106">Select **New registration**.</span></span> <span data-ttu-id="dd198-107">**[アプリケーションを登録]** ページで、次のように値を設定します。</span><span class="sxs-lookup"><span data-stu-id="dd198-107">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="dd198-108">`Node.js Graph Tutorial` に **[名前]** を設定します。</span><span class="sxs-lookup"><span data-stu-id="dd198-108">Set **Name** to `Node.js Graph Tutorial`.</span></span>
    - <span data-ttu-id="dd198-109">**[サポートされているアカウントの種類]** を **[任意の組織のディレクトリ内のアカウントと個人用の Microsoft アカウント]** に設定します。</span><span class="sxs-lookup"><span data-stu-id="dd198-109">Set **Supported account types** to **Accounts in any organizational directory and personal Microsoft accounts**.</span></span>
    - <span data-ttu-id="dd198-110">**[リダイレクト URI]** で、最初のドロップダウン リストを `Web` に設定し、それから `http://localhost:3000/auth/callback` に値を設定します。</span><span class="sxs-lookup"><span data-stu-id="dd198-110">Under **Redirect URI**, set the first drop-down to `Web` and set the value to `http://localhost:3000/auth/callback`.</span></span>

    ![[アプリケーションの登録] ページのスクリーンショット](./images/aad-register-an-app.png)

1. <span data-ttu-id="dd198-112">[**登録**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd198-112">Select **Register**.</span></span> <span data-ttu-id="dd198-113">**Node.js グラフのチュートリアル**ページで、**アプリケーション (クライアント) ID**の値をコピーして保存します。そのためには、次の手順で必要になります。</span><span class="sxs-lookup"><span data-stu-id="dd198-113">On the **Node.js Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![新しいアプリの登録のアプリケーション ID のスクリーンショット](./images/aad-application-id.png)

1. <span data-ttu-id="dd198-115">[**管理**] の下の [**認証**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd198-115">Select **Authentication** under **Manage**.</span></span> <span data-ttu-id="dd198-116">**[暗黙的な許可]** セクションを検索し、**[ID トークン]** を有効にします。</span><span class="sxs-lookup"><span data-stu-id="dd198-116">Locate the **Implicit grant** section and enable **ID tokens**.</span></span> <span data-ttu-id="dd198-117">**[保存]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd198-117">Select **Save**.</span></span>

    ![暗黙的な grant セクションのスクリーンショット](./images/aad-implicit-grant.png)

1. <span data-ttu-id="dd198-119">**[管理]** で **[証明書とシークレット]** を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd198-119">Select **Certificates & secrets** under **Manage**.</span></span> <span data-ttu-id="dd198-120">**[新しいクライアント シークレット]** ボタンを選択します。</span><span class="sxs-lookup"><span data-stu-id="dd198-120">Select the **New client secret** button.</span></span> <span data-ttu-id="dd198-121">[**説明**] に値を入力して、[**有効期限**] のオプションの1つを選択し、[**追加**] を選択します。</span><span class="sxs-lookup"><span data-stu-id="dd198-121">Enter a value in **Description** and select one of the options for **Expires** and select **Add**.</span></span>

    ![[クライアントシークレットの追加] ダイアログのスクリーンショット](./images/aad-new-client-secret.png)

1. <span data-ttu-id="dd198-123">クライアント シークレットの値をコピーしてから、このページから移動します。</span><span class="sxs-lookup"><span data-stu-id="dd198-123">Copy the client secret value before you leave this page.</span></span> <span data-ttu-id="dd198-124">次の手順で行います。</span><span class="sxs-lookup"><span data-stu-id="dd198-124">You will need it in the next step.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="dd198-125">このクライアント シークレットは今後表示されないため、今必ずコピーするようにしてください。</span><span class="sxs-lookup"><span data-stu-id="dd198-125">This client secret is never shown again, so make sure you copy it now.</span></span>

    ![新しく追加されたクライアントシークレットのスクリーンショット](./images/aad-copy-client-secret.png)
