---
title: Data Factory で Resource Manager テンプレートを使用する
description: Azure Resource Manager テンプレートを作成および使用して Data Factory エンティティを作成する方法を説明します。
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 8ade57fc4cf96e3638fffc918899385d8935c95f
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782896"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>テンプレートを使用して Azure Data Factory のエンティティを作成する
> [!NOTE]
> この記事は、Data Factory のバージョン 1 に適用されます。 

## <a name="overview"></a>概要
データ統合のために Azure Data Factory を使用した場合、異なる環境間で同じパターンを何度も使用したり、同じソリューション内で同じタスクを繰り返し実装したりしていることに気づく場合があります。 テンプレートを使用すると、これらのシナリオを簡単に実装、管理することができます。 Azure Data Factory のテンプレートは、再利用や繰り返しを伴うシナリオに最適です。

世界中に 10 の製造プラントを持つ企業について考えてみましょう。 各プラントからのログは、個別の SQL Server データベースに格納されます。 企業は、アドホック分析用にクラウドに 1 つのデータ ウェアハウスを構築したいと考えています。 また、開発、テスト、および運用環境に同じロジックの異なる構成が必要でもあります。

この場合、同じ環境内でタスクを繰り返す必要がありますが、各製造プラントの 10 のデータ ファクトリ間で異なる値を使用する必要があります。 ここに **繰り返し** が伴います。 テンプレートでは、この一般的なフロー (つまり、各データ ファクトリに同じアクティビティを持つパイプライン) の抽象化が可能になり、各製造プラントに個別のパラメーター ファイルを使用できます。

また、企業が、異なる環境間でこれらの 10 のデータ ファクトリを複数回デプロイする必要がある場合、テンプレートでは、この **再利用性** を使用して、開発、テスト、および運用環境に個別のパラメーター ファイルを利用できます。

## <a name="templating-with-azure-resource-manager"></a>Azure Resource Manager でのテンプレート
[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/overview.md)は、Azure Data Factory でテンプレートを実現するための優れた方法です。 Resource Manager テンプレートでは、JSON ファイルを使用して Azure ソリューションのインフラストラクチャと構成を定義します。 Azure Resource Manager テンプレートはほぼすべての Azure サービスと連携するため、広範に使用して Azure アセットのあらゆるリソースを簡単に管理できます。 Resource Manager テンプレート全般については、「[Azure Resource Manager のテンプレートの作成](../../azure-resource-manager/templates/template-syntax.md)」を参照してください。

## <a name="tutorials"></a>チュートリアル
Resource Manager テンプレートを使用して Data Factory エンティティを作成する詳細な手順については、次のチュートリアルをご覧ください。

* [チュートリアル:Azure Resource Manager テンプレートを使用してデータをコピーするパイプラインを作成する](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [チュートリアル:Azure Resource Manager テンプレートを使用してデータを処理するパイプラインを作成する](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>GitHub の Data Factory テンプレート
GitHub の次の Azure クイック スタート テンプレートもご覧ください。

* [データ ファクトリを作成して Azure Blob Storage から Azure SQL Database にデータをコピーする](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Azure HDInsight クラスターで Hive アクティビティを伴うデータ ファクトリを作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [データ ファクトリを作成して Salesforce から Azure BLOB にデータをコピーする](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [複数のアクティビティを連結したデータ ファクトリ (FTP サーバーから Azure BLOB にデータをコピーし、Hive スクリプトをオンデマンド HDInsight クラスターで呼び出してデータを変換し、その結果を Azure SQL Database にコピーする) を作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

[Azure クイック スタート](https://azure.microsoft.com/documentation/templates/)の Azure Data Factory テンプレートを自由に共有してください。 このリポジトリで共有できるテンプレートを開発する際には、[投稿に関するガイド](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE)をご覧ください。

以降のセクションでは、Resource Manager テンプレートでの Data Factory リソースの定義方法について説明します。

## <a name="defining-data-factory-resources-in-templates"></a>テンプレートでの Data Factory リソースの定義
データ ファクトリを定義するための大まかなテンプレートは次のとおりです。

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>データ ファクトリの定義
次のサンプルに示されているように、Resource Manager テンプレートにデータ ファクトリを定義します。

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
dataFactoryName は以下のように "変数" で定義します。

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>リンクされたサービスの定義

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

デプロイする特定のリンクされたサービスの JSON プロパティについて詳しくは、[ストレージのリンクされたサービスに関する記事](data-factory-azure-blob-connector.md#azure-storage-linked-service)または「[コンピューティングのリンクされたサービス](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)」をご覧ください。 "dependsOn" パラメーターは、対応するデータ ファクトリの名前を指定します。 Azure Storage でのリンクされたサービスの定義の例を、次の JSON 定義に示します。

### <a name="define-datasets"></a>データセットの定義

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
デプロイする特定のデータセットの型の JSON プロパティについて詳しくは、[サポートされるデータ ストアに関する記事](data-factory-data-movement-activities.md#supported-data-stores-and-formats)をご覧ください。 "dependsOn" パラメーターは、対応するデータ ファクトリの名前と、ストレージのリンクされたサービスを指定します。 Azure Blob Storage のデータセット型の例を、次の JSON 定義に示します。

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>パイプラインの定義

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

デプロイする特定のパイプラインおよびアクティビティを定義するための JSON プロパティについて詳しくは、[パイプラインの定義に関する記事](data-factory-create-pipelines.md#pipeline-json)を参照してください。 "dependsOn" パラメーターは、データ ファクトリの名前と、対応するリンクされたサービスまたはデータセットを指定します。 Azure Blob Storage から Azure SQL Database にデータをコピーするパイプラインの例を次の JSON スニペットに示します。

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Data Factory テンプレートのパラメーター化
パラメーター化のベスト プラクティスについては、「[Azure Resource Manager テンプレートを作成するためのベスト プラクティス](../../azure-resource-manager/templates/template-best-practices.md)」を参照してください。 通常は、特に代わりに変数を使用できる場合には、パラメーターの使用を最小限にすることをお勧めします。 次のシナリオではパラメーターのみを指定します。

* 設定は環境によって異なります (例: 開発、テスト、運用)
* シークレット情報 (パスワードなど)

テンプレートを使用した Azure Data Factory エンティティのデプロイ時にシークレットを [Azure Key Vault](../../key-vault/general/overview.md) から取得する必要がある場合は、次の例で示されているように **Key Vault** と **シークレット名** を指定します。

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> 既存のデータ ファクトリのテンプレートのエクスポートはまだサポートされていません。現在開発中です。
>
>