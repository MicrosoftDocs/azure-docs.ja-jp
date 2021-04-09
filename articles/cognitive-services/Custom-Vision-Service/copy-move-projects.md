---
title: Custom Vision プロジェクトをコピーして移動する
titleSuffix: Azure Cognitive Services
description: ExportProject API と ImportProject API を使用して Custom Vision プロジェクトをコピーして移動する方法について説明します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: how-to
ms.date: 09/08/2020
ms.author: pafarley
ms.openlocfilehash: 7d58a8239c728f70efe3584c2649e196dffd791f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501092"
---
# <a name="copy-and-move-your-custom-vision-projects"></a>Custom Vision プロジェクトをコピーして移動する

Custom Vision プロジェクトを作成してトレーニングした後、プロジェクトを別のリソースにコピーしたい場合があります。 たとえば、プロジェクトを開発環境から運用環境に移動することや、データのセキュリティを強化するために別の Azure リージョンのアカウントにプロジェクトをバックアップすることが必要になる場合があります。

**[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** および **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** API を使用すると、一方の Custom Vision アカウントから他方にプロジェクトをコピーできるため、このシナリオを実現できます。 このガイドでは、cURL でこれらの REST API を使用する方法について説明します。 Postman のような HTTP 要求サービスを使用して、要求を発行することもできます。

## <a name="business-scenarios"></a>ビジネス シナリオ

アプリまたはビジネスが Custom Vision プロジェクトの使用に依存している場合、別のリージョン内の別の Custom Vision アカウントにモデルをコピーすることをお勧めします。 地域的な障害が発生しても、コピー先のリージョン内のプロジェクトにアクセスできます。

##  <a name="prerequisites"></a>前提条件

- 2 つの Azure Custom Vision リソース。 これらがない場合、Azure portal に移動し、[新しい Custom Vision リソースを作成してください](https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision?azure-portal=true)。
- Custom Vision リソースのトレーニング キーとエンドポイント URL。 これらの値は Azure portal 上のリソースの **[概要]** タブにあります。
- 作成済みの Custom Vision プロジェクト。 これを行う手順については、「[分類器の構築](./getting-started-build-a-classifier.md)」を参照してください。
* [PowerShell バージョン 6.0 以降](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)、または同様のコマンドライン ユーティリティ。

## <a name="process-overview"></a>プロセスの概要

プロジェクトをコピーするプロセスは、次の手順で構成されます。

1. 最初に、コピーするソース アカウントのプロジェクトの ID を取得します。
1. 次に、このプロジェクト ID とソース アカウントのトレーニング キーを使用して、**ExportProject** API を呼び出します。 一時トークン文字列を受け取ります。
1. 次に、このトークン文字列とターゲット アカウントのトレーニング キーを使用して、**ImportProject** API を呼び出します。 プロジェクトがターゲット アカウントの下に一覧表示されます。

## <a name="get-the-project-id"></a>プロジェクト ID を取得する

最初に **[GetProjects](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddead)** を呼び出して、既存の Custom Vision プロジェクトとその ID の一覧を表示します。 ソース アカウントのトレーニング キーとエンドポイントを使用します。

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects"
-H "Training-key: {training key}"
```

プロジェクトとそのメタデータの一覧が本文に含まれる `200\OK` 応答が返されます。 `"id"` 値は、次の手順でコピーする文字列です。

```json
[
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
      }
    },
    "created": "string",
    "lastModified": "string",
    "thumbnailUri": "string",
    "drModeEnabled": true,
    "status": "Succeeded"
  }
]
```

## <a name="export-the-project"></a>プロジェクトをエクスポートする

プロジェクト ID、ソース トレーニング キー、エンドポイントを使用して、 **[ExportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)** を呼び出します。

```curl
curl -v -X GET "{endpoint}/customvision/v3.3/Training/projects/{projectId}/export"
-H "Training-key: {training key}"
```

エクスポートされたプロジェクトに関するメタデータと参照文字列 `"token"` を含む `200/OK` 応答が返されます。 トークンの値をコピーします。

```json
{
  "iterationCount": 0,
  "imageCount": 0,
  "tagCount": 0,
  "regionCount": 0,
  "estimatedImportTimeInMS": 0,
  "token": "string"
}
```

## <a name="import-the-project"></a>プロジェクトをインポートする

参照トークンと共に、ターゲット トレーニング キーとエンドポイントを使用して、 **[ImportProject](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee3)** を呼び出します。 新しいアカウントでプロジェクトに名前を付けることもできます。

```curl
curl -v -G -X POST "{endpoint}/customvision/v3.3/Training/projects/import"
--data-urlencode "token={token}" --data-urlencode "name={name}"
-H "Training-key: {training key}" -H "Content-Length: 0"
```

新しくインポートしたプロジェクトに関するメタデータを含む `200/OK` 応答が返されます。

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
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="next-steps"></a>次の手順

このガイドでは、Custom Vision リソース間でプロジェクトをコピーして移動する方法について説明しました。 次に、API リファレンス ドキュメントを参照して、Custom Vision を使用して他にできることを確認してください。
* [REST API リファレンス ドキュメント](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb3)