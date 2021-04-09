---
title: 通知およびモデルのバックアップのために Azure Storage を統合する
titleSuffix: Azure Cognitive Services
description: Custom Vision モデルをトレーニングまたはエクスポートするときにプッシュ通知を受け取るために Azure Storage を統合する方法について説明します。 エクスポートしたモデルのバックアップを保存することもできます。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: fd4ad1443f466a78abfc569d5f52f6bdeff2d5be
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048886"
---
# <a name="integrate-azure-storage-for-notifications-and-backup"></a>通知とバックアップのために Azure Storage を統合する

Custom Vision プロジェクトを Azure Blob Storage キューと統合して、プロジェクトのトレーニングのプッシュ通知を取得したり、公開されたモデルのアクティビティ コピーやバック アップコピーをエクスポートしたりすることができます。 この機能は、長い操作が実行されているときに、サービスで結果をポーリングし続けなくて済むようにするのに役立ちます。 代わりに、ストレージ キュー通知をワークフローに統合できます。

このガイドでは、cURL でこれらの REST API を使用する方法について説明します。 Postman のような HTTP 要求サービスを使用して、要求を発行することもできます。

> [!NOTE]
> プッシュ通知は、**CreateProject** API のオプションの _notificationQueueUri_ パラメーターに依存します。また、モデルのバックアップでは、オプションの _exportModelContainerUri_ パラメーターも使用する必要があります。 このガイドでは、すべての機能を説明するために両方とも使用します。

## <a name="prerequisites"></a>前提条件

- Azure の Custom Vision リソース。 それがない場合は、Azure portal に移動し、[新しい Custom Vision リソースを作成してください](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true)。 現在、この機能では、Cognitive Services リソース (オール イン ワン キー) はサポートされていません。
- BLOB コンテナーがある Azure ストレージ アカウント。 このステップに関してヘルプが必要な場合は、[Azure Storage ラボの演習 1](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) に従ってください。
* [PowerShell バージョン 6.0 以降](/powershell/scripting/install/installing-powershell-core-on-windows)、または同様のコマンド ライン アプリケーション。

## <a name="set-up-azure-storage-integration"></a>Azure ストレージの統合をセットアップする

Azure portal で Custom Vision トレーニング リソースに移動し、 **[ID]** ページを選択して、システム割り当てマネージド ID を有効にします。

次に、Azure portal でストレージ リソースに移動します。 **[アクセス制御 (IAM)]** ページに移動し、各統合機能に対してロールの割り当てを追加します。
* モデルのバックアップ機能を使用する場合は、Custom Vision トレーニング リソースを選択して、**ストレージ BLOB データ共同作成者** ロールを割り当てます。 
* 次に、通知キュー機能を使用する場合は、Custom Vision トレーニング リソースを選択して、**ストレージ キュー データ共同作成者** を割り当てます。

> [!div class="mx-imgBorder"]
> ![ストレージ アカウントのロール割り当て追加ページ](./media/storage-integration/storage-access.png)

### <a name="get-integration-urls"></a>統合 URL を取得する

次に、Custom Vision リソースでこれらのエンドポイントにアクセスできる URL を取得します。

通知キューの統合 URL の場合は、ストレージ アカウントの **[キュー]** ページに移動し、新しいキューを追加して、その URL を一時的な場所に保存します。

> [!div class="mx-imgBorder"]
> ![Azure Storage キューのページ](./media/storage-integration/queue-url.png) 

モデル バックアップの統合 URL の場合は、ストレージ アカウントの **[コンテナー]** ページに移動し、新しいコンテナーを作成します。 次に、それを選択して、 **[プロパティ]** ページに移動します。 URL を一時的な場所にコピーします。
 
> [!div class="mx-imgBorder"]
> ![Azure ストレージ コンテナーのプロパティ ページ](./media/storage-integration/container-url.png) 


## <a name="integrate-custom-vision-project"></a>Custom Vision プロジェクトを統合する

統合 URL が用意できたので、Azure Storage の機能を統合する新しい Custom Vision プロジェクトを作成できます。 また、既存のプロジェクトを更新して、機能を追加することもできます。

### <a name="create-new-project"></a>新しいプロジェクトの作成

[CreateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae) API を呼び出すときに、オプションのパラメーター _exportModelContainerUri_ と _notificationQueueUri_ を追加します。 前のセクションで取得した URL の値を割り当てます。 

```curl
curl -v -X POST "{endpoint}/customvision/v3.3/Training/projects?exportModelContainerUri={inputUri}&notificationQueueUri={inputUri}&name={inputName}"
-H "Training-key: {subscription key}"
```

応答 `200/OK` を受け取ったら、URL が正常に設定されたことを意味します。 JSON の応答にも URL の値が表示されます。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

### <a name="update-existing-project"></a>既存のプロジェクトを更新する

Azure Storage 機能の統合を使用して既存のプロジェクトを更新するには、更新するプロジェクトの ID を使用して、[UpdateProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb1) API を呼び出します。 

```curl
curl -v -X PATCH "{endpoint}/customvision/v3.3/Training/projects/{projectId}"
-H "Content-Type: application/json"
-H "Training-key: {subscription key}"

--data-ascii "{body}" 
```

要求の本文 (`body`) に次の JSON 形式を設定し、_exportModelContainerUri_ と _notificationQueueUri_ に適切な値を設定します。

```json
{
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "imageProcessingSettings": {
      "augmentationMethods": {}
},
"exportModelContainerUri": {url}
"notificationQueueUri": {url}

  },
  "status": "Succeeded"
}
```

応答 `200/OK` を受け取ったら、URL が正常に設定されたことを意味します。

## <a name="verify-the-connection"></a>接続を確認する 

前のセクションの API 呼び出しにより、Azure ストレージ アカウントで新しい情報が既にトリガーされています。 

指定したコンテナーの **CustomVision-TestPermission** フォルダー内に、テスト BLOB があるはずです。 この BLOB は一時的にのみ存在します。

通知キューには、次の形式のテスト通知が表示されます。
 
```json
{
"version": "1.0" ,
"type": "ConnectionTest",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000"
    }
}
```

## <a name="get-event-notifications"></a>イベント通知を取得する

準備ができたら、プロジェクトで [TrainProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API を呼び出して、通常のトレーニング操作を行います。

トレーニングが完了すると、ストレージの通知キューで通知を受け取ります。

```json
{
"version": "1.0" ,
"type": "Training",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "trainingStatus": "TrainingCompleted"
    }
}
```

`"trainingStatus"` フィールドは、`"TrainingCompleted"` または `"TrainingFailed"` のいずれかになります。 `"iterationId"` フィールドは、トレーニング済みのモデルの ID です。

## <a name="get-model-export-backups"></a>モデルのエクスポートのバックアップを取得する

準備ができたら、[ExportIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddece) API を呼び出して、トレーニング済みのモデルを指定したプラットフォームにエクスポートします。

指定したストレージ コンテナーに、エクスポートしたモデルのバックアップ コピーが表示されます。 BLOB 名の形式は次のとおりです。

```
{projectId} - {iterationId}.{platformType}
```

また、エクスポートが完了すると、キューで通知を受け取ります。 

```json
{
"version": "1.0" ,
"type": "Export",
"Content":
    {
    "projectId": "00000000-0000-0000-0000-000000000000",
    "iterationId": "00000000-0000-0000-0000-000000000000",
    "exportStatus": "ExportCompleted",
    "modelUri": {url}
    }
}
```

`"exportStatus"` フィールドは、`"ExportCompleted"` または `"ExportFailed"` のいずれかになります。 最初にキュー通知を統合した場合は、コンテナーに格納されたバックアップ モデルの URL が`"modelUri"` フィールドに含まれます。 そうでない場合は、Custom Vision モデルの BLOB の SAS URL が `"modelUri"` フィールドに表示されます。

## <a name="next-steps"></a>次の手順

このガイドでは、Custom Vision リソース間でプロジェクトをコピーして移動する方法について説明しました。 次に、API リファレンス ドキュメントを参照して、Custom Vision を使用して他にできることを確認してください。
* [REST API リファレンス ドキュメント (トレーニング)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)
* [REST API リファレンス ドキュメント (予測)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3)