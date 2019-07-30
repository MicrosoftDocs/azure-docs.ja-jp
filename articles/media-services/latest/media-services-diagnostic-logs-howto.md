---
title: Azure Monitor を使用した Media Services 診断ログの監視 | Microsoft Docs
description: この記事では、Azure Monitor を使用して診断ログをルーティングおよび表示する方法を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 233b043ffdc295fe94ed2e3ba837d4229848df22
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795838"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Media Services 診断ログの監視

[Azure Monitor](../../azure-monitor/overview.md) により、アプリケーションの実行状況を理解する上で役立つメトリックと診断ログを監視できます。 この機能の詳細な説明と、Azure Media Services のメトリックと診断ログを使用する必要がある理由については、「[Media Services のメトリックと診断ログの監視](media-services-metrics-diagnostic-logs.md)」を参照してください。

この記事では、データをストレージ アカウントにルーティングし、そのデータを表示する方法を示します。 

## <a name="prerequisites"></a>前提条件

- [Media Services アカウントを作成する](create-account-cli-how-to.md)
- 「[Media Services のメトリックと診断ログの監視](media-services-metrics-diagnostic-logs.md)」を確認する。

## <a name="route-data-to-the-storage-account-using-the-portal"></a>ポータルを使用してストレージ アカウントにデータをルーティングする

1. Azure Portal (https://portal.azure.com ) にログインします。
1. Media Services アカウントに移動し、 **[監視]** の下の **[診断設定]** をクリックします。 サブスクリプションに含まれ Azure Monitor で監視データを生成するすべてのリソースの一覧が表示されます。 

    ![[診断設定] セクション](media/media-services-diagnostic-logs/logs01.png)

1. **[診断設定の追加]** をクリックします。

   リソースの診断設定では、"*どの*" 監視データを特定のリソースからルーティングして、"*どこ*" にその監視データを格納するかを定義します。

1. 表示されるセクションで、設定の **[名前]** を指定し、 **[ストレージ アカウントへのアーカイブ]** チェック ボックスをオンにします。

    ログの送信先のストレージ アカウントを選択し、 **[OK]** を押します。
1. **[ログ]** と **[メトリック]** の下のすべてのボックスをオンにします。 リソースの種類によっては、どちらかのオプションしかないこともあります。 これらのチェック ボックスは、そのリソースの種類で使用可能なログ データとメトリック データのカテゴリのうち、選んだアーカイブ先 (この例ではストレージ アカウント) に送られるものを制御します。

   ![[診断設定] セクション](media/media-services-diagnostic-logs/logs02.png)
1. **[リテンション期間 (日数)]** スライダーを 30 に設定します。 このスライダーは、監視データをストレージ アカウントに保持する日数を設定します。 Azure Monitor は、指定した日数より古いデータを自動的に削除します。 リテンション期間を 0 にすると、データは無期限に保存されます。
1. **[Save]** をクリックします。

リソースからの監視データが、ストレージ アカウントに送られるようになります。

## <a name="route-data-to-the-storage-account-using-the-cli"></a>CLI を使用してストレージ アカウントにデータをルーティングする

ストレージ アカウントでの診断ログの保存を有効にするには、次の `az monitor diagnostic-settings` CLI コマンドを実行します。 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

例:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>ポータルを使用してストレージ アカウントでデータを表示する

上記の手順に従って構成した場合、データがストレージ アカウントに送られるようになっています。

ストレージ アカウントにイベントが表示されるまで、最大で 5 分間待つ必要があります。

1. ポータルの左側のナビゲーション バーで探して、 **[ストレージ アカウント]** セクションに移動します。
1. 前のセクションで作成したストレージ アカウントをクリックします。
1. **[BLOB]** をクリックし、**insights-logs-keydeliveryrequests** というラベルのコンテナーをクリックします。 これは、ログが格納されているコンテナーです。 監視データは、まずリソース ID 別、次に日時別に分類されて、コンテナーに格納されます。
1. リソース ID、日付、時刻のコンテナーをクリックして、PT1H.json ファイルに移動します。 PT1H.json ファイルをクリックして、 **[ダウンロード]** をクリックします。

 ストレージ アカウントに保存されている JSON イベントを表示できるようになります。

### <a name="examples-of-pt1hjson"></a>PT1H.json の例

#### <a name="clear-key-delivery-log"></a>クリア キーの配信ログ

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine 暗号化キーの配信ログ

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="see-also"></a>関連項目

* [Azure Monitor のメトリック](../../azure-monitor/platform/data-platform.md)
* [Azure Monitor 診断ログ](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Azure リソースからログ データを収集して使用する方法](../../azure-monitor/platform/diagnostic-logs-overview.md)

## <a name="next-steps"></a>次の手順

[監視メトリック](media-services-metrics-howto.md)
