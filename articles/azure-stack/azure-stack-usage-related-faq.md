---
title: "Usage API のよくあるご質問 (FAQ) | Microsoft Docs"
description: "Azure Stack の測定、Azure Usage API との比較、使用時間と報告時間、エラー コードの一覧。"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: alfredop
ms.openlocfilehash: 166147c8cb4949be1b23e0a06868e66c8a5844f4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Azure Stack Usage API のよくあるご質問 
この記事では、Azure Stack Usage API についてよくあるご質問 (FAQ) とその回答を紹介します。

## <a name="what-meter-ids-can-i-see"></a>どのような測定 ID を表示できますか。
現在、ネットワーク、ストレージ、およびコンピューティング リソース プロバイダーの利用状況が報告されます。

| **リソース プロバイダー** | **測定 ID** | **測定名** | **単位** | **追加情報** |
| --- | --- | --- | --- | --- | 
| **ネットワーク** |F271A8A388C44D93956A063E1D2FA80B |静的 IP アドレスの使用状況 |IP アドレス|使用中の IP アドレス数 | 
| |9E2739BA86744796B465F64674B822BA |動的 IP アドレスの使用状況 |IP アドレス|使用中の IP アドレス数 | 
| **Storage** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*時 |テーブルによって使用された合計容量 |
| | B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*時 |ページ BLOB によって使用された合計容量 |
| | B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*時 |キューによって使用された合計容量 |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*時 |ブロック BLOB によって使用された合計容量 |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |10,000 秒間の要求数 |Table service 要求 (10,000 秒間) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |GB 単位の受信データ |GB 単位の Table service データ受信 |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |GB 単位の送信 |GB 単位の Table service データ送信 |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |10,000 秒間の要求数 |Blob service 要求 (10,000 秒間) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |GB 単位の受信データ |GB 単位の Blob service データ受信 |
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |GB 単位の送信 |GB 単位の Blob service データ送信 |
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |10,000 秒間の要求数 |Queue サービス要求 (10,000 秒間) |
| | E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |GB 単位の受信データ |GB 単位の Queue サービス データ受信 | 
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |GB 単位の送信 |GB 単位の Queue サービス データ送信 |
| **Compute** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |ベース VM サイズ時間 |仮想コア分 | 仮想コア数 x VM が実行した分数 |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Windows VM サイズ時間 |仮想コア分 | 仮想コア数 x VM が実行した分数 |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |VM サイズ時間 |VM 時間 |ベース VM と Windows VM の両方をキャプチャ。 仮想コアの調整なし |
| **Key Vault** | EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Key Vault トランザクション | 10,000 秒間の要求数| Key Vault データ プレーンによって受信された REST API 要求数 |

## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Azure Stack Usage API は [Azure Usage API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (現在パブリック プレビュー中) と比較してどうですか。
* Tenant Usage API は、Azure API と一貫性がありますが、唯一の例外として、現在 Azure Stack では *showDetails* フラグがサポートされていません。
* Provider Usage API は、Azure Stack にのみ適用されます。
* 現在、Azure で使用可能な [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) は Azure Stack で使用できません。

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>使用時間と報告時間の違いは何ですか。
使用状況データ レポートには、2 つの主要な時間値があります。

* **報告時間**。 使用状況システムに使用状況イベントが入力された時間
* **使用時間**。 Azure Stack リソースが使用された時間

特定の使用状況イベントに対して、使用時間と報告時間の値に不一致が見られることがあります。 遅延はすべての環境で数時間に達することもあります。

現在、*報告時間によってのみ*クエリを実行できます。

## <a name="what-do-these-usage-api-error-codes-mean"></a>これらの Usage API エラー コードの意味は何ですか。
| **HTTP 状態コード** | **エラー コード** | **説明** |
| --- | --- | --- |
| 400/無効な要求 |*NoApiVersion* |*api-version* クエリ パラメーターがありません。 |
| 400/無効な要求 |*InvalidProperty* |プロパティが見つからないか、無効な値が指定されています。 応答本文のエラー コード内のメッセージに、見つからないプロパティが示されています。 |
| 400/無効な要求 |*RequestEndTimeIsInFuture* |*ReportedEndTime* の値は将来の値です。 この引数では将来の値を使用できません。 |
| 400/無効な要求 |*SubscriberIdIsNotDirectTenant* |プロバイダー API 呼び出しで、呼び出し元の有効なテナントではないサブスクリプション ID が使用されました。 |
| 400/無効な要求 |*SubscriptionIdMissingInRequest* |呼び出し元のサブスクリプション ID がありません。 |
| 400/無効な要求 |*InvalidAggregationGranularity* |無効な集計単位が要求されました。 有効な値は、日単位と時間単位です。 |
| 503 |*ServiceUnavailable* |サービスがビジー状態か、呼び出しが調整されているため、再試行可能エラーが発生しました。 |

## <a name="next-steps"></a>次のステップ
[Azure Stack でのお客様への請求と配賦](azure-stack-billing-and-chargeback.md)

[プロバイダー リソース使用量 API](azure-stack-provider-resource-api.md)

[テナント リソース使用量 API](azure-stack-tenant-resource-usage-api.md)

