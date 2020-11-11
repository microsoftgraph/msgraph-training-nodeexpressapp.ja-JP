<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="7b0dc-101">このセクションでは、ユーザーの予定表にイベントを作成する機能を追加します。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-101">In this section you will add the ability to create events on the user's calendar.</span></span>

## <a name="create-a-new-event-form"></a><span data-ttu-id="7b0dc-102">新しいイベントフォームを作成する</span><span class="sxs-lookup"><span data-stu-id="7b0dc-102">Create a new event form</span></span>

1. <span data-ttu-id="7b0dc-103">**Newevent** という名前の新しいファイルを作成し、次のコードを追加し **ます。**</span><span class="sxs-lookup"><span data-stu-id="7b0dc-103">Create a new file in the **./views** directory named **newevent.hbs** and add the following code.</span></span>

    :::code language="html" source="../demo/graph-tutorial/views/newevent.hbs" id="NewEventFormSnippet":::

1. <span data-ttu-id="7b0dc-104">行の前に、次のコードを **/routes/calendar.js** ファイルに追加します。 `module.exports = router;`</span><span class="sxs-lookup"><span data-stu-id="7b0dc-104">Add the following code to the **./routes/calendar.js** file before the `module.exports = router;` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="GetEventFormSnippet":::

<span data-ttu-id="7b0dc-105">これにより、ユーザー入力用のフォームが実装され、レンダリングされます。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-105">This implements a form for user input and renders it.</span></span>

## <a name="create-the-event"></a><span data-ttu-id="7b0dc-106">イベントを作成する</span><span class="sxs-lookup"><span data-stu-id="7b0dc-106">Create the event</span></span>

1. <span data-ttu-id="7b0dc-107">**/graph.js** を開き、に次の関数を追加 `module.exports` します。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-107">Open **./graph.js** and add the following function inside `module.exports`.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/graph.js" id="CreateEventSnippet":::

    <span data-ttu-id="7b0dc-108">このコードでは、フォームフィールドを使用して Graph イベントオブジェクトを作成し、エンドポイントに POST 要求を送信して、 `/me/events` ユーザーの既定の予定表にイベントを作成します。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-108">This code uses the form fields to create a Graph event object, then sends a POST request to the `/me/events` endpoint to create the event on the user's default calendar.</span></span>

1. <span data-ttu-id="7b0dc-109">行の前に、次のコードを **/routes/calendar.js** ファイルに追加します。 `module.exports = router;`</span><span class="sxs-lookup"><span data-stu-id="7b0dc-109">Add the following code to the **./routes/calendar.js** file before the `module.exports = router;` line.</span></span>

    :::code language="javascript" source="../demo/graph-tutorial/routes/calendar.js" id="PostEventFormSnippet":::

    <span data-ttu-id="7b0dc-110">このコードは、フォーム入力を検証して、その後、 `graph.createEvent` イベントを作成するために呼び出します。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-110">This code validates and sanitized the form input, then calls `graph.createEvent` to create the event.</span></span> <span data-ttu-id="7b0dc-111">呼び出しが完了すると、[予定表] ビューにリダイレクトされます。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-111">It redirects back to the calendar view after the call completes.</span></span>

1. <span data-ttu-id="7b0dc-112">変更内容を保存し、アプリを再起動します。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-112">Save your changes and restart the app.</span></span> <span data-ttu-id="7b0dc-113">[ **予定表** ] ナビゲーション項目をクリックし、[ **イベントの作成** ] ボタンをクリックします。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-113">Click the **Calendar** nav item, then click the **Create event** button.</span></span> <span data-ttu-id="7b0dc-114">値を入力し、[ **作成** ] をクリックします。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-114">Fill in the values and click **Create**.</span></span> <span data-ttu-id="7b0dc-115">新しいイベントが作成されると、アプリは [予定表] ビューに戻ります。</span><span class="sxs-lookup"><span data-stu-id="7b0dc-115">The app returns to the calendar view once the new event is created.</span></span>

    ![新しいイベントフォームのスクリーンショット](images/create-event-01.png)
