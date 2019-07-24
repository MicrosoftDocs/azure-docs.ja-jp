---
title: Azure Portal で初めての関数を作成する | Microsoft Docs
description: Azure Portal を使用して、サーバーレス実行のための最初の Azure 関数を作成する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 96cf87b9-8db6-41a8-863a-abb828e3d06d
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, devcenter, cc996988-fb4f-47
ms.openlocfilehash: 1b7efd4f36d5587667010d25f30a3e7945faee4f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "52997807"
---
# <a name="create-your-first-function-in-the-azure-portal"></a>Azure Portal で初めての関数を作成する

Azure Functions を使用すると、最初に VM を作成したり Web アプリケーションを発行したりしなくても、[サーバーレス](https://azure.microsoft.com/solutions/serverless/)環境でコードを実行できます。 このトピックでは、Functions を使用して Azure Portal で "hello world" 関数を作成する方法について説明します。

![Azure Portal での Function App の作成](./media/functions-create-first-azure-function/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> C# の開発者は、[初めての関数をポータルではなく Visual Studio 2017 で作成](functions-create-your-first-function-visual-studio.md)することをお勧めします。 

## <a name="log-in-to-azure"></a>Azure にログインする

Azure アカウントで Azure Portal (<https://portal.azure.com>) にサインインします。

## <a name="create-a-function-app"></a>Function App を作成する

関数の実行をホストするための Function App が存在する必要があります。 Function App を使用すると、リソースの管理、デプロイ、および共有を容易にするためのロジック ユニットとして関数をグループ化できます。 

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

次に、新しい Function App で関数を作成します。

## <a name="create-function"></a>HTTP によってトリガーされる関数の作成

1. 新しい関数アプリを展開し、**[関数]** の横にある **[+]** ボタン、**[ポータル内]**、**[続行]** の順に選択します。

    ![Functions のクイック スタート: プラットフォームを選択する。](./media/functions-create-first-azure-function/function-app-quickstart-choose-portal.png)

1. **[webhook + API]** を選択し、**[作成]** を選択します。

    ![Azure Portal での関数のクイック スタート。](./media/functions-create-first-azure-function/function-app-quickstart-node-webhook.png)

HTTP によってトリガーされる関数の言語固有のテンプレートを使用して、関数が作成されます。

ここで、HTTP 要求を送信することで、新しい関数を実行できます。

## <a name="test-the-function"></a>関数をテストする

1. 新しい関数で、右上の **[</> 関数の URL の取得]** をクリックし、**[既定値 (関数キー)]** を選択して、**[コピー]** をクリックします。 

    ![Azure Portal からの関数 URL のコピー](./media/functions-create-first-azure-function/function-app-develop-tab-testing.png)

2. 関数 URL をブラウザーのアドレス バーに貼り付けます。 この URL の末尾にクエリ文字列 `&name=<yourname>` を追加し、キーボードで`Enter` キーを押して要求を実行します。 ブラウザーに表示される関数によって返される応答が表示されます。  

    次の例は、ブラウザーでの応答を示しています。

    ![ブラウザーでの関数の応答。](./media/functions-create-first-azure-function/function-app-browser-testing.png)

    要求 URL には、既定では HTTP 経由で関数にアクセスするために必要なキーが含まれています。

3. 関数が実行されると、ログにトレース情報が書き込まれます。 前の実行からのトレース出力を表示するには、ポータルで関数に戻り、画面の下部にある矢印をクリックして **[ログ]** を展開します。

   ![Azure Portal の関数ログ ビューアー。](./media/functions-create-first-azure-function/function-view-logs.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次の手順

HTTP によってトリガーされる単純な関数を含む Function App を作成しました。  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

詳細は、「[Azure Functions の HTTP バインド](functions-bindings-http-webhook.md)」を参照してください。
