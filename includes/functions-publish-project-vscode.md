---
title: インクルード ファイル
description: インクルード ファイル
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3cfa36331f8f4ad45f3bf8ff32eee7d89c7d8852
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608261"
---
## <a name="publish-the-project-to-azure"></a>Azure にプロジェクトを発行する

Visual Studio Code を使用すると、関数プロジェクトを Azure に直接発行できます。 このプロセスでは、Azure サブスクリプションに関数アプリと関連リソースを作成します。 関数アプリは、関数の実行コンテキストを提供します。 プロジェクトがパッケージ化され、Azure サブスクリプション内の新しい関数アプリにデプロイされます。

既定で、Visual Studio では関数アプリの作成に必要なすべての Azure リソースが作成されます。 これらのリソースの名前は、選択した関数アプリの名前に基づきます。 作成したリソースを完全に管理する必要がある場合は、代わりに[高度なオプションを使用して発行する](../articles/azure-functions/functions-develop-vs-code.md#enabled-publishing-with-advanced-create-options)ことができます。

このセクションでは、Azure で新しい関数アプリを作成することを想定しています。

> [!IMPORTANT]
> 既存の関数アプリに発行すると、Azure のそのアプリのコンテンツが上書きされます。

1. Visual Studio Code で、F1 キーを押してコマンド パレットを開きます。 コマンド パレットで、`Azure Functions: Deploy to function app...` を検索して選択します。

1. サインインしていない場合、**Azure にサインイン**するよう求められます。 **無料の Azure アカウントを作成**することもできます。 ブラウザーから正常にサインインしたら、Visual Studio Code に戻ります。 

1. 複数のサブスクリプションがある場合、関数アプリ用の **[サブスクリプションを選択]** してから、 **[+ Create New Function App in Azure]\(+ Azure で新しい Function App を作成\)** を選択します。

1. 関数アプリを識別するグローバルに一意の名前を入力し、Enter キーを押します。 関数アプリ名の有効な文字は、`a-z`、`0-9`、`-` です。

    Enter キーを押すと、次の Azure リソースがサブスクリプションに作成されます。

    * **[リソース グループ](../articles/azure-resource-manager/resource-group-overview.md)** :作成された Azure リソースがすべて含まれます。 名前は関数アプリの名前に基づきます。
    * **[ストレージ アカウント](../articles/storage/common/storage-quickstart-create-account.md)** :関数アプリの名前に基づいた一意の名前で、Standard Storage アカウントが作成されます。
    * **[ホスティング プラン](../articles/azure-functions/functions-scale.md)** :サーバーレス関数アプリをホストするために、従量課金プランが米国西部リージョンに作成されます。
    * **関数アプリ**:プロジェクトはこの新しい関数アプリにデプロイされ、そこで実行されます。

    関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 この通知の **[View Output]\(出力の表示\)** を選択すると、作成済みの Azure リソースなど、作成とデプロイの結果が表示されます。

1. **[Azure: Functions]** 領域に戻り、サブスクリプションの下にある新しい関数アプリを展開します。 **[Functions]** を展開し、 **[HttpTrigger]** を右クリックして **[Copy function URL]\(関数 URL のコピー\)** を選択します。

    ![新しい HTTP トリガーの関数 URL をコピーします](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
