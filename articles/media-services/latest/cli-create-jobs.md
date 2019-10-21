---
title: Azure CLI のサンプル スクリプト - ジョブの作成と送信 | Microsoft Docs
description: このトピックの Azure CLI スクリプトは、HTTPs URL を使用して、単純なエンコード変換にジョブを送信する方法を示しています。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/01/2019
ms.author: juliako
ms.openlocfilehash: 08caf4b78b1042d5753dba2bc73e93fa3a250a17
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122391"
---
# <a name="cli-example-create-and-submit-a-job"></a>CLI の例:ジョブの作成と送信

Media Services v3 では、ビデオを処理するジョブを送信するときに、入力ビデオを検索する場所を Media Services に指示する必要があります。 選択肢の 1 つは、(この記事で示すように) HTTPS URL をジョブの入力として指定することです。 

## <a name="prerequisites"></a>前提条件 

[Media Services アカウントを作成する](create-account-cli-how-to.md)

[!INCLUDE [media-services-cli-instructions.md](../../../includes/media-services-cli-instructions.md)]

## <a name="example-script"></a>サンプル スクリプト

`az ams job start` を実行すると、ジョブの出力にラベルを設定できます。 ラベルは、後でこの出力アセットの目的を識別するために使用できます。 

- ラベルに値を割り当てる場合は、"--output-assets" を "assetname=label" に設定します。
- ラベルに値を割り当てない場合は、"--output-assets" を "assetname=" に設定します。
  `output-assets` に "=" を追加することに注意してください。 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

次のような応答画面が表示されます。

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

## <a name="next-steps"></a>次の手順

[az ams job (CLI)](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
