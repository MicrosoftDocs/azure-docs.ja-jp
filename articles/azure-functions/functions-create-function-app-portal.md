---
title: Azure Portal で初めての関数を作成する
description: Azure Portal を使用して、サーバーレス実行のための最初の Azure Function を作成する方法について説明します。
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-csharp, mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 6ca187181d68d7924f97ebfac08e81dec77251e9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103492128"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Azure Portal で初めての関数を作成する

Azure Functions を使用すると、最初に仮想マシン (VM) を作成したり、Web アプリケーションを発行したりしなくても、サーバーレス環境でコードを実行できます。 この記事では、Azure Functions を使用して Azure portal で "hello world" の HTTP トリガー関数を作成する方法について説明します。

[!INCLUDE [functions-in-portal-editing-note](../../includes/functions-in-portal-editing-note.md)] 

代わりに、[関数をローカルで開発](functions-develop-local.md)し、Azure の関数アプリに公開することをお勧めします。  
次のいずれかのリンクを使用して、選択したローカル開発環境と言語で作業を開始します。

| Visual Studio Code | ターミナル/コマンド プロンプト | Visual Studio |
| --- | --- | --- |
|  &bull;&nbsp;[C# で作業を開始する](./create-first-function-vs-code-csharp.md)<br/>&bull;&nbsp;[Java で作業を開始する](./create-first-function-vs-code-java.md)<br/>&bull;&nbsp;[JavaScript で作業を開始する](./create-first-function-vs-code-node.md)<br/>&bull;&nbsp;[PowerShell で作業を開始する](./create-first-function-vs-code-powershell.md)<br/>&bull;&nbsp;[Python で作業を開始する](./create-first-function-vs-code-python.md) |&bull;&nbsp;[C# で作業を開始する](./create-first-function-cli-csharp.md)<br/>&bull;&nbsp;[Java で作業を開始する](./create-first-function-cli-java.md)<br/>&bull;&nbsp;[JavaScript で作業を開始する](./create-first-function-cli-node.md)<br/>&bull;&nbsp;[PowerShell で作業を開始する](./create-first-function-cli-powershell.md)<br/>&bull;&nbsp;[Python で作業を開始する](./create-first-function-cli-python.md) | [C# で作業を開始する](functions-create-your-first-function-visual-studio.md) |

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで [Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 関数アプリを使用すると、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

次に、新しい Function App で関数を作成します。

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP トリガー関数の作成

1. **[関数]** ウィンドウの左側のメニューで、 **[関数]** を選択し、上部のメニューから **[追加]** を選択します。 
 
1. **[関数の追加]** ウィンドウで、 **[Http トリガー]** テンプレートを選択します。

    ![HTTP トリガー関数の選択](./media/functions-create-first-azure-function/function-app-select-http-trigger.png)

1. **[テンプレートの詳細]** で、 **[新しい関数]** に `HttpExample` を使用し、 **[[承認レベル]](functions-bindings-http-webhook-trigger.md#authorization-keys)** ドロップダウン リストから **[匿名]** を選択し、 **[追加]** を選択します。

    Azure によって HTTP トリガー関数が作成されます。 ここで、HTTP 要求を送信することで、新しい関数を実行できます。

## <a name="test-the-function"></a>関数をテストする

1. 新しい HTTP トリガー関数で、左側のメニューから **[Code + Test]\(コード + テスト\)** を選択し、上部のメニューから **[関数の URL の取得]** を選択します。

    ![[関数の URL の取得] の選択](./media/functions-create-first-azure-function/function-app-select-get-function-url.png)

1. **[関数の URL の取得]** ダイアログ ボックスで、ドロップダウン リストから **[既定]** を選択し、 **[クリップボードにコピー]** アイコンを選択します。 

    ![Azure Portal からの関数 URL のコピー](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

1. 関数 URL をブラウザーのアドレス バーに貼り付けます。 この URL の末尾にクエリ文字列の値 `?name=<your_name>` を追加し、Enter キーを押して要求を実行します。 

    次の例は、ブラウザーでの応答を示しています。

    ![ブラウザーでの関数の応答。](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    要求 URL に [アクセス キー](functions-bindings-http-webhook-trigger.md#authorization-keys) (`?code=...`) が含まれていた場合は、関数を作成するときに **[匿名]** アクセス レベルではなく **[関数]** を選択することを示します。 この場合は、代わりに `&name=<your_name>` を追加する必要があります。

1. 関数が実行されると、ログにトレース情報が書き込まれます。 トレース出力を表示するには、ポータルの **[Code + Test]\(コード + テスト\)** ページに戻り、ページの下部にある **[ログ]** 矢印を展開します。

   ![Azure Portal の関数ログ ビューアー。](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
