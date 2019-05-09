---
title: Linux 上で Azure portal から関数アプリを作成する | Microsoft Docs
description: Azure Portal を使用して、サーバーレス実行のための最初の Azure 関数を作成する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: glenga
ms.custom: ''
ms.openlocfilehash: cc99bc4345c388f22e72957590f3917a85e214e0
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339813"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Azure App Service プランで Linux 上に関数アプリを作成する

Azure Functions を使うと、既定の Azure App Service コンテナー内の Linux で関数をホストできます。 この記事では、[Azure portal](https://portal.azure.com) を使用して、Linux でホストされた関数アプリを作成し、[App Service プラン](functions-scale.md#app-service-plan)内で実行する方法について説明します。 [独自のカスタム コンテナーを利用する](functions-create-function-linux-custom-image.md)こともできます。

![Azure Portal での Function App の作成](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで Azure Portal (<https://portal.azure.com>) にサインインします。

## <a name="create-a-function-app"></a>Function App を作成する

Linux での関数の実行をホストするための関数アプリが必要です。 関数アプリは、関数コードの実行環境を提供します。 Function App を使用すると、リソースの管理、デプロイ、共有を容易にするためのロジック ユニットとして関数をグループ化できます。 この記事では、関数アプリを作成するときに App Service プランを作成します。

1. Azure portal の左上にある **[リソースの作成]** ボタンを選択し、**[コンピューティング]** > **[Function App]** を選択します。

    ![Azure ポータルでの関数アプリの作成](./media/create-function-app-linux-app-service-plan/function-app-create-flow.png)

2. 図の下にある表に指定されている関数アプリの設定を使用します。

    ![新しい関数アプリの設定を定義する](./media/create-function-app-linux-app-service-plan/function-app-create-flow2.png)

    | Setting      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a-z`、`0-9`、および `-` です。  | 
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 | 
    | **[リソース グループ](../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Function App を作成するための新しいリソース グループの名前。 |
    | **OS** | Linux | Linux 上で関数アプリを実行する |
    | **[発行]** | コード | **ランタイム スタック**の既定の Linux コンテナーが使用されます。 用意する必要があるのは関数アプリ プロジェクトのコードのみです。 もう 1 つの選択肢は、カスタムの [Docker イメージ](functions-create-function-linux-custom-image.md)を公開することです。 |
    | **[ホスティング プラン](functions-scale.md)** | App Service プラン | Function App にどのようにリソースが割り当てられるかを定義するホスティング プラン。 App Service プランで実行する場合は、[関数アプリのスケーリング](functions-scale.md)を制御できます。  |
    | **App Service プラン/場所** | プランの作成 | **[新規作成]** を選択し、**[App Service プラン]** の名前を指定します。 最寄りの[リージョン](https://azure.microsoft.com/regions/)または関数がアクセスする他のサービスの近くのリージョン内の **[場所]** を選択します。 目的の **[[価格レベル]](https://azure.microsoft.com/pricing/details/app-service/linux/)** を選択します。 <br/>同じ App Service プランで Linux と Windows の両方の関数アプリを実行することはできません。 |
    | **ランタイム スタック** | 優先言語 | お気に入りの関数プログラミング言語をサポートするランタイムを選択します。 C# および F# 関数用の **[.NET]** を選択します。 [Python のサポート](functions-reference-python.md)は現時点でプレビュー段階です |
    | **[Storage](../storage/common/storage-quickstart-create-account.md)** |  グローバルに一意の名前 |  関数アプリで使用されるストレージ アカウントを作成します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用できます。 既存のアカウントを使用することもできますが、[ストレージ アカウントの要件](functions-scale.md#storage-account-requirements)を満たしている必要があります。 |
    | **[Application Insights](functions-monitoring.md)** | Enabled | Application Insights は、既定で無効です。 今すぐ Application Insights 統合を有効にして、App Service プランの場所に近いホスティング場所を選択することをお勧めします。 これを後で行う場合は、「[Azure 関数を監視する](functions-monitoring.md)」を参照してください。  |

3. **[作成]** を選択して、Function App をプロビジョニングし、デプロイします。

4. ポータルの右上隅の通知アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

    ![新しい関数アプリの設定を定義する](./media/create-function-app-linux-app-service-plan/function-app-create-notification.png)

5. **[リソースに移動]** を選択して、新しい関数アプリを確認します。

> [!TIP]
> 目的の関数アプリがポータルに見つからない場合は、[Azure Portal のお気に入りに Function Apps を追加](functions-how-to-use-azure-function-app-settings.md#favorite)してみてください。

次に、新しい Function App で関数を作成します。 関数アプリを使用できるようになった後でも、完全に初期化されるまでに数分かかることがあります。

## <a name="create-function"></a>HTTP によってトリガーされる関数の作成

このセクションでは、ポータルの新しい関数アプリで関数を作成する方法について説明します。

> [!NOTE]
> Azure Functions を試す場合、ポータル開発の経験があれば役立ちます。 ほとんどのシナリオでは、[Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project) または [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project) を使用して、ローカルで関数を開発し、プロジェクトを関数アプリに公開することを検討してください。  

1. 新しい関数アプリで、**[概要]** タブを選択し、読み込みが完了したら **[+ 新しい関数]** を選択します。

    ![[概要] タブから新しい関数を作成する](./media/create-function-app-linux-app-service-plan/overview-create-function.png)

1. **[クイック スタート]** タブで **[ポータル内]** を選択し、**[続行]** を選択します。

    ![関数の開発プラットフォームを選択します。](./media/create-function-app-linux-app-service-plan/function-app-quickstart-choose-portal.png)

1. **[webhook + API]** を選択し、**[作成]** を選択します。

    ![Azure Portal での関数のクイック スタート。](./media/create-function-app-linux-app-service-plan/function-app-quickstart-node-webhook.png)

HTTP によってトリガーされる関数の言語固有のテンプレートを使用して、関数が作成されます。

ここで、HTTP 要求を送信することで、新しい関数を実行できます。

## <a name="test-the-function"></a>関数をテストする

1. 新しい関数で、右上の **[</> 関数の URL の取得]** をクリックし、**[既定値 (関数キー)]** を選択して、**[コピー]** をクリックします。 

    ![Azure Portal からの関数 URL のコピー](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

2. 関数 URL をブラウザーのアドレス バーに貼り付けます。 この URL の末尾にクエリ文字列 `&name=<yourname>` を追加し、キーボードで`Enter` キーを押して要求を実行します。 ブラウザーに表示される関数によって返される応答が表示されます。  

    次の例は、ブラウザーでの応答を示しています。

    ![ブラウザーでの関数の応答。](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    要求 URL には、既定では HTTP 経由で関数にアクセスするために必要なキーが含まれています。

3. 関数が実行されると、ログにトレース情報が書き込まれます。 前の実行からのトレース出力を表示するには、ポータルで関数に戻り、画面の下部にある矢印をクリックして **[ログ]** を展開します。

   ![Azure Portal の関数ログ ビューアー。](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次の手順

HTTP によってトリガーされる単純な関数を含む Function App を作成しました。  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

詳細は、「[Azure Functions の HTTP バインド](functions-bindings-http-webhook.md)」を参照してください。
