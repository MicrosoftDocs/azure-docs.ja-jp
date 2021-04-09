---
title: インクルード ファイル
description: インクルード ファイル
services: data-factory
author: memildin
ms.service: data-factory
ms.topic: include
ms.date: 03/10/2020
ms.author: memildin
ms.custom: include file
ms.openlocfilehash: 55390a3eb2a074729b4a0868416a95e208325b76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91400933"
---
```json
{
  "VendorName": "Microsoft",
  "AlertType": "SUSPECT_SVCHOST",
  "StartTimeUtc": "2016-12-20T13:38:00.000Z",
  "EndTimeUtc": "2019-12-20T13:40:01.733Z",
  "ProcessingEndTime": "2019-09-16T12:10:19.5673533Z",
  "TimeGenerated": "2016-12-20T13:38:03.000Z",
  "IsIncident": false,
  "Severity": "High",
  "Status": "New",
  "ProductName": "Azure Security Center",
  "SystemAlertId": "2342409243234234_F2BFED55-5997-4FEA-95BD-BB7C6DDCD061",
  "CompromisedEntity": "WebSrv1",
  "Intent": "Execution",
  "AlertDisplayName": "Suspicious process detected",
  "Description": "Suspicious process named 'SVCHOST.EXE' was running from path: %{Process Path}",
  "RemediationSteps": ["contact your security information team"],
  "ExtendedProperties": {
    "Process Path": "c:\\temp\\svchost.exe",
    "Account": "Contoso\\administrator",
    "PID": 944,
    "ActionTaken": "Detected"
  },
  "Entities": [],
  "ResourceIdentifiers": [
        {
            Type: "AzureResource",
            AzureResourceId: "/subscriptions/86057C9F-3CDD-484E-83B1-7BF1C17A9FF8/resourceGroups/backend-srv/providers/Microsoft.Compute/WebSrv1"
        },
        {
            Type: "LogAnalytics",
            WorkspaceId: "077BA6B7-8759-4F41-9F97-017EB7D3E0A8",
            WorkspaceSubscriptionId: "86057C9F-3CDD-484E-83B1-7BF1C17A9FF8",
            WorkspaceResourceGroup: "omsrg",
            AgentId: "5A651129-98E6-4E6C-B2CE-AB89BD815616",
        }
  ]
}
```

### <a name="the-data-model-of-the-schema"></a>スキーマのデータ モデル

|フィールド|データ型|説明|
|----|----|----|
|**AlertDisplayName**|String|アラートの表示名。|
|**AlertType**|String|アラートの種類。 同じ種類のアラートの値は同じである必要があります。 このフィールドは、アラート インスタンスではなく、アラートの種類を表すキー付き文字列です。 同じ検出ロジック/分析からのすべてのアラート インスタンスは、アラートの種類の値が同じである必要があります。|
|**CompromisedEntity**|String|このアラートに最も関連したリソースの表示名。|
|**説明**|String|アラートの説明。|
|**EndTimeUtc**|DateTime|アラートに含まれる最後のイベントまたはアクティビティの時刻。  フィールドは、UTC タイムゾーン情報を含む ISO8601 形式に準拠した文字列である必要があります。|
|**エンティティ**|IEnumerable (IEntity)|アラートに関連するエンティティのリスト。 このリストには、さまざまな種類のエンティティを混在させることができます。 エンティティの種類には、Entities セクションで定義されているどの種類でも指定できます。 以下のリストに含まれていないエンティティも送信できますが、処理される保証はありません (新しい種類のエンティティでのアラートの検証は失敗しません)。|
|**ExtendedProperties**|ディクショナリ (文字列,文字列)|プロバイダーは (必要に応じて)、ここにカスタムフィールドを含めることができます。|
|**インテント**|列挙型|アラートの背後にある強制終了チェーンに関連するインテント。 サポートされている値のリストと、Azure Security Center のサポートされる強制終了チェーン インテントの説明については、「[意図](../articles/security-center/alerts-reference.md#intentions)」を参照してください。<br/>このフィールドには複数の値が含まれる場合があります (コンマで区切られます)。|
|**IsIncident**|Bool|このフィールドは、アラートがインシデントである (複数のアラートの複合グループ化) か、単一のアラートであるかを決定します。 フィールドの既定値は "false" (単一のアラートであることを意味します) です。|
|**ProcessingEndTime**|DateTime|アラートを保持している元の製品で、エンド ユーザーがアラートにアクセスできた時間。|
|**ProductName**|String|このアラートを発行した製品の名前 (Azure Security Center、Azure ATP、Microsoft Defender ATP、MCAS など)。|
|**RemediationSteps**|リスト<String>|アラートを修復するために実行する手動のアクション項目。|
|**ResourceIdentifiers**|リスト (リソース識別子)|適切な製品露出グループ (テナント、ワークスペース、サブスクリプションなど) にアラートを送信するために使用できる、このアラートのリソース識別子。 アラートごとに異なる種類の識別子を複数指定できます。|
|**Severity**|列挙型|プロバイダーから報告されたアラートの重要度。 有効値は次のとおりです。情報、低、中、および高。|
|**StartTimeUtc**|DateTime|アラートに含まれる最初のイベントまたはアクティビティの時刻。 フィールドは、UTC タイムゾーン情報を含む ISO8601 形式に準拠した文字列である必要があります。|
|**状態**|列挙型|アラートのライフサイクル ステータス。<br/>次の状態がサポートされています。新規、解決済み、無視、不明。<br/>サポートされているオプション以外の値が指定されているアラートには、"不明" の状態が割り当てられます。<br/>値が指定されていないアラートには、"新規" 状態が割り当てられます。|
|**SystemAlertId**|String|アラート識別子。|
|**TimeGenerated**|DateTime|アラート プロバイダーがアラートを生成した時刻。 内部アラート プロバイダーによって報告されていない場合は、製品は、処理するために製品が受信した時刻を割り当てるように選択できます。  フィールドは、UTC タイムゾーン情報を含む ISO8601 形式に準拠した文字列である必要があります。|
|**VendorName**|String|アラートを発生させたベンダーの名前。|
|||
