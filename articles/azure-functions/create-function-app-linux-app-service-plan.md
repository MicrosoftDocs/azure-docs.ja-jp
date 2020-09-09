---
title: Linux 上で Azure portal から Function Appを作成する
description: Azure Portal を使用して、サーバーレス実行のための最初の Azure 関数を作成する方法について説明します。
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: 5aae60900a61c28f6c53f89b8e273daccb047eef
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83116212"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan"></a>Azure App Service プランで Linux 上に Function App を作成する

Azure Functions を使うと、既定の Azure App Service コンテナー内の Linux で関数をホストできます。 この記事では、[Azure portal](https://portal.azure.com) を使用して、Linux でホストされた Function App を作成し、[App Service プラン](functions-scale.md#app-service-plan)内で実行する方法について説明します。 [独自のカスタム コンテナーを利用する](functions-create-function-linux-custom-image.md)こともできます。

![Azure Portal での Function App の作成](./media/create-function-app-linux-app-service-plan/function-app-in-portal-editor.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで Azure Portal (<https://portal.azure.com>) にサインインします。

## <a name="create-a-function-app"></a>Function App を作成する

Linux での関数の実行をホストするための Function App が必要です。 Function App は、関数コードの実行環境を提供します。 これにより、リソースの管理、デプロイ、スケーリング、および共有を容易にするための論理ユニットとして関数をグループ化できます。 この記事では、Function App を作成するときに App Service プランを作成します。

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。

1. **[新規]** ページで、 **[計算]** 、 >  **[関数アプリ]** の順に選択します。

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-flow.png" alt-text="Azure portal での関数アプリの作成":::

1. **[基本]** ページで、下の表で指定されている関数アプリの設定を使用します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **サブスクリプション** | 該当するサブスクリプション | この新しい Function App が作成されるサブスクリプション。 |
    | **[リソース グループ](../azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Function App を作成するための新しいリソース グループの名前。 |
    | **関数アプリ名** | グローバルに一意の名前 | 新しい Function App を識別する名前。 有効な文字は、`a-z` (大文字と小文字の区別をしない)、`0-9`、および `-`です。  |
    |**発行**| **コード** (既定) | コード ファイルまたは Docker コンテナーの発行オプション。 |
    | **ランタイム スタック** | 優先言語 | お気に入りの関数プログラミング言語をサポートするランタイムを選択します。 C# 関数および F# 関数の場合は、 **[.NET Core]** を選択します。 |
    |**Version**| バージョン番号 | インストールされているランタイムのバージョンを選択します。  |
    |**リージョン**| 優先リージョン | ユーザーに近い[リージョン](https://azure.microsoft.com/regions/)、または関数がアクセスする他のサービスの近くのリージョンを選択します。 |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-basics-linux.png" alt-text="[基本] ページ":::

1. **[次へ :ホスティング]** を選択します。 **[ホスティング]** ページで、次の設定を入力します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **[ストレージ アカウント](../storage/common/storage-account-create.md)** |  グローバルに一意の名前 |  Function App で使用されるストレージ アカウントを作成します。 ストレージ アカウント名は、3 文字から 24 文字までの長さにし、数字と小文字のみを使用する必要があります。 既存のアカウントを使用することもできますが、[ストレージ アカウントの要件](../azure-functions/functions-scale.md#storage-account-requirements)を満たしている必要があります。 |
    |**オペレーティング システム**| **Linux** | オペレーティング システムは、ランタイム スタックの選択に基づいてあらかじめ選択されますが、必要に応じて設定を変更できます。 |
    | **[プラン](../azure-functions/functions-scale.md)** | **従量課金プラン (サーバーレス)** | Function App にどのようにリソースが割り当てられるかを定義するホスティング プラン。 既定の **[従量課金プラン]** では、リソースは関数の必要に応じて動的に追加されます。 この[サーバーレス](https://azure.microsoft.com/overview/serverless-computing/)のホスティングでは、関数が実行された時間にのみ課金されます。 App Service プランで実行する場合は、[関数アプリのスケーリング](../azure-functions/functions-scale.md)を管理する必要があります。  |

    :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-hosting-linux.png" alt-text="ホスティング ページ":::

1. **[次へ :監視]** を選択します。 **[監視]** ページで、次の設定を入力します。

    | 設定      | 推奨値  | 説明 |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../azure-functions/functions-monitoring.md)** | **可** (既定) | 最も近いサポートされているリージョン内に同じ*アプリ名*の Application Insights リソースを作成します。 この設定を展開するか、 **[新規作成]** を選択することによって、Application Insights 名を変更するか、データを格納する [Azure 地理的環境](https://azure.microsoft.com/global-infrastructure/geographies/)内の別のリージョンを選択することができます。 |

   :::image type="content" source="./media/create-function-app-linux-app-service-plan/function-app-create-monitoring-linux.png" alt-text="[監視] ページ":::

1. **[確認および作成]** を選択して、アプリ構成の選択内容を確認します。

1. **[確認および作成]** ページで設定を確認して、 **[作成]** を選択し、関数アプリをプロビジョニングしてデプロイします。

1. ポータルの右上隅の **[通知]** アイコンを選択し、"**デプロイメントに成功しました**" というメッセージが表示されるまで待ちます。

1. **[リソースに移動]** を選択して、新しい Function App を確認します。 また、 **[ダッシュボードにピン留めする]** を選択することもできます。 ピン留めすると、ダッシュボードからこの関数アプリ リソースに戻るのが容易になります。

    ![デプロイの通知](./media/create-function-app-linux-app-service-plan/function-app-create-notification2.png)

    Function App を使用できるようになった後でも、完全に初期化されるまでに数分かかることがあります。

次に、新しい Function App で関数を作成します。

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP トリガー関数の作成

このセクションでは、ポータルの新しい Function App で関数を作成する方法について説明します。

> [!NOTE]
> Azure Functions を試す場合、ポータル開発の経験があれば役立ちます。 ほとんどのシナリオでは、[Visual Studio Code](functions-create-first-function-vs-code.md#create-an-azure-functions-project)または [Azure Functions Core Tools](functions-run-local.md#create-a-local-functions-project)を使用して、ローカルで関数を開発し、プロジェクトを Function App に公開することを検討してください。  

1. **[関数]** ウィンドウの左側のメニューで、 **[関数]** を選択し、上部のメニューから **[追加]** を選択します。 
 
1. **[新規関数]** ウィンドウで **[Http トリガー]** を選択します。

    ![HTTP トリガー関数の選択](./media/create-function-app-linux-app-service-plan/function-app-select-http-trigger.png)

1. **[新規関数]** ウィンドウで、 **[新規関数]** の既定の名前を受け入れるか、新しい名前を入力します。 

1. **[承認レベル]** ドロップダウン リストから **[匿名]** を選択し、 **[関数の作成]** を選択します。

    Azure によって HTTP トリガー関数が作成されます。 ここで、HTTP 要求を送信することで、新しい関数を実行できます。

## <a name="test-the-function"></a>関数をテストする

1. 新しい HTTP トリガー関数で、左側のメニューから **[Code + Test]\(コード + テスト\)** を選択し、上部のメニューから **[関数の URL の取得]** を選択します。

    ![[関数の URL の取得] の選択](./media/create-function-app-linux-app-service-plan/function-app-select-get-function-url.png)

1. **[関数の URL の取得]** ダイアログ ボックスで、ドロップダウン リストから **[既定]** を選択し、 **[クリップボードにコピー]** アイコンを選択します。 

    ![Azure Portal からの関数 URL のコピー](./media/create-function-app-linux-app-service-plan/function-app-develop-tab-testing.png)

1. 関数 URL をブラウザーのアドレス バーに貼り付けます。 この URL の末尾にクエリ文字列の値 `?name=<your_name>` を追加し、Enter キーを押して要求を実行します。 

    次の例は、ブラウザーでの応答を示しています。

    ![ブラウザーでの関数の応答。](./media/create-function-app-linux-app-service-plan/function-app-browser-testing.png)

    要求 URL には、既定では HTTP 経由で関数にアクセスするために必要なキーが含まれています。

1. 関数が実行されると、ログにトレース情報が書き込まれます。 トレース出力を表示するには、ポータルの **[Code + Test]\(コード + テスト\)** ページに戻り、ページの下部にある **[ログ]** 矢印を展開します。

   ![Azure Portal の関数ログ ビューアー。](./media/create-function-app-linux-app-service-plan/function-view-logs.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

単純な HTTP トリガー関数を含む関数アプリを作成しました。  

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

詳細は、「[Azure Functions の HTTP バインド](functions-bindings-http-webhook.md)」を参照してください。
