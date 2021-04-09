---
title: Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Data Factory を作成する
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して、サンプルの Azure Data Factory パイプラインを作成します。
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: c579b6d723533e751e08a80a578195c03e945607
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783338"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>クイック スタート:ARM テンプレートを使用して Azure Data Factory を作成する

> [!div class="op_single_selector" title1="使用している Data Factory サービスのバージョンを選択してください:"]
> * [Version 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [現在のバージョン](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Data Factory を作成する方法を説明します。 このデータ ファクトリに作成されたパイプラインは、データを Azure BLOB ストレージ内のあるフォルダーから別のフォルダーに **コピー** します。 Azure Data Factory を使用してデータを **変換** する方法のチュートリアルについては、[Spark を使用したデータ変換のチュートリアル](transform-data-using-spark.md)を参照してください。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> この記事では、Data Factory サービスの概要については詳しく取り上げません。 Azure Data Factory サービスの概要については、「[Azure Data Factory の概要](introduction.md)」をご覧ください。

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

### <a name="azure-subscription"></a>Azure サブスクリプション

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

### <a name="create-a-file"></a>ファイルを作成する

**メモ帳** などのテキスト エディターを開き、次の内容を含む **emp.txt** という名前のファイルを作成します。

```emp.txt
John, Doe
Jane, Doe
```

**C:\ADFv2QuickStartPSH** フォルダーにファイルを保存します (フォルダーがまだ存在しない場合は作成します)。

## <a name="review-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/)からのものです。

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

テンプレート内には次の Azure リソースが定義されています。

- [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): ストレージ アカウントを定義します。
- [Microsoft.DataFactory/factories](/azure/templates/microsoft.datafactory/factories):Azure Data Factory を作成します。
- [Microsoft.DataFactory/factories/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices):Azure Data Factory のリンクされたサービスを作成します。
- [Microsoft.DataFactory/factories/datasets](/azure/templates/microsoft.datafactory/factories/datasets):Azure Data Factory データセットを作成します。
- [Microsoft.DataFactory/factories/pipelines](/azure/templates/microsoft.datafactory/factories/pipelines):Azure Data Factory パイプラインを作成します。

その他の Azure Data Factory テンプレートのサンプルについては、[クイックスタート テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)をご覧ください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 テンプレートによって、Azure Data Factory アカウント、ストレージ アカウント、および BLOB コンテナーが作成されます。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. 次の値を選択または入力します。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="ADF ARM テンプレートをデプロイする":::

    特に明記されていない場合は、既定値を使用して Azure Data Factory リソースを作成してください。

    - **サブスクリプション**:Azure サブスクリプションを選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、リソース グループの一意の名前を入力し、 **[OK]** を選択します。
    - **[リージョン]** :場所を選択します。  たとえば、*East US* などとします。
    - **データ ファクトリ名**: 既定値を使用します。
    - **[場所]** :既定値を使用します。
    - **[ストレージ アカウント名]** : 既定値を使用します。
    - **BLOB コンテナー**: 既定値を使用します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. **[リソース グループに移動]** を選択します。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="リソース グループ":::

2.  ご自分の Azure Data Factory が作成されていることを確認します。
    1. その Azure Data Factory 名の形式は、datafactory\<uniqueid\> です。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="データ ファクトリのサンプル":::

2. ご自分のストレージ アカウントが作成されていることを確認します。
    1. そのストレージ アカウント名の形式は、storage\<uniqueid\> です。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="ストレージ アカウント":::

3. 作成されたストレージ アカウントの名前を選択して、 **[コンテナー]** を選択します。
    1. **[コンテナー]** ページで、作成した BLOB コンテナーを選択します。
        1. その BLOB コンテナー名の形式は、blob\<uniqueid\> です。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="BLOB コンテナー":::

### <a name="upload-a-file"></a>ファイルをアップロードする

1. **[コンテナー]** ページで、 **[アップロード]** を選択します。

2. 右側のペインで、 **[ファイル]** ボックスを選択してから、先ほど作成した **emp .txt** ファイルを参照して選択します。

3. **[詳細設定]** の見出しを展開します。

4. **[アップロード先のフォルダー]** ボックスに「*input*」と入力します。

5. **[アップロード]** ボタンを選択します。 一覧に **emp.txt** ファイルとアップロードの状態が表示されます。

6. **[閉じる]** アイコン (**X**) を選択して、 **[BLOB のアップロード]** ページを閉じます。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="ファイルを入力フォルダーにアップロードする":::

コンテナー ページは開いたままにしておいてください。そうすることで、このクイックスタートの最後に出力を確認できるからです。

### <a name="start-trigger"></a>トリガーを開始する

1. **[データ ファクトリ]** ページに移動し、作成したデータ ファクトリを選択します。 

2. **[作成と監視]** タイルを選択します。 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="作成と監視":::

2. **[作成]** タブ:::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false":::を選択します。

3. 作成したパイプライン (ArmtemplateSampleCopyPipeline) を選択します。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="ARM テンプレート パイプライン":::

4. **[トリガーの追加]**  >  **[Trigger Now]\(今すぐトリガー\)** の順に選択します。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="トリガー":::

5. **[パイプラインの実行]** の下の右側のウィンドウで、 **[OK]** を選択します。

### <a name="monitor-the-pipeline"></a>パイプラインの監視

1. **[監視]** タブを選択します。:::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false":::

2. パイプラインの実行に関連付けられたアクティビティの実行が表示されます。 このクイック スタートでは、パイプラインに、コピーという種類のアクティビティが 1 つのみ含まれます。 そのため、そのアクティビティの実行が表示されます。

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="正常に実行":::

### <a name="verify-the-output-file"></a>出力ファイルを検証する

パイプラインでは、BLOB コンテナー内に出力フォルダーが自動的に作成されます。 そのうえで、input フォルダーから output フォルダーに emp.txt ファイルをコピーします。 

1. Azure portal の **[コンテナー]** ページで、 **[最新の情報に更新]** を選択して出力フォルダーを表示します。 

2. フォルダー一覧で、 **[output]** を選択します。

3. **emp.txt** が output フォルダーにコピーされていることを確認します。 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="出力":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したリソースは、2 とおりの方法でクリーンアップすることができます。 [Azure リソース グループを削除](../azure-resource-manager/management/delete-resource-group.md)します。この場合、そのリソース グループに含まれているすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、このチュートリアルで作成したデータ ファクトリだけを削除してください。

リソース グループを削除すると、その中のデータ ファクトリも含めて、すべてのリソースが削除されます。 次のコマンドを実行して、リソース グループ全体を削除します。 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

リソース グループ全体ではなく、データ ファクトリだけを削除する場合は、次のコマンドを実行します。 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して Azure Data Factory を作成し、デプロイを検証しました。 Azure Data Factory と Azure Resource Manager の詳細については、引き続き以下の記事を参照してください。

- [Azure Data Factory のドキュメント](index.yml)
- [Azure Resource Manager](../azure-resource-manager/management/overview.md) の詳細を確認する
- その他の [Azure Data Factory ARM テンプレートを取得する](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)