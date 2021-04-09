---
title: Azure Communication Services の利用可能なリージョンとデータ所在地
description: Azure Communication Services でのデータ所在地およびプライバシーに関連した問題について説明します
author: chpalm
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 933b5605cf38be90d419673a94e23e4c36f0ef36
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103495710"
---
# <a name="region-availability-and-data-residency"></a>利用可能なリージョンとデータの保存場所

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure Communication Services は、お客様が自身のプライバシーおよび個人データの要件を満たせるよう支援することを約束します。 アプリケーションの使用者と直接的な関係がある、Communication Services を使用する開発者は、場合によってはデータの管理者になります。 Azure Communication Services が代理でこの保存データを格納および暗号化しているため、Microsoft がこのデータの処理担当になる可能性が高くなります。 このページには、このサービスがデータを保持するしくみと、このデータを識別、エクスポート、削除する方法がまとめられています。

## <a name="data-residency"></a>データの保存場所

Communication Services リソースを作成する際は、(Azure データ センターではなく) **地域** を指定します。 Communication Services によって格納されたすべての保存データは、Communication Services によって内部で選択されたデータ センター内のその地域で保持されます。 データは他の地域で転送または処理される可能性はあります。 これらのグローバル エンドポイントは、高パフォーマンスかつ低遅延のエクスペリエンスを場所に関係なくエンドユーザーに提供するために必要です。

## <a name="data-residency-and-events"></a>データ所在地とイベント

Azure Communication Services で構成された Event Grid システムのトピックはすべて、グローバルな場所に作成されます。 信頼できる配信をサポートするために、グローバル Event Grid システムのトピックは、イベント データを任意の Microsoft データセンターに格納することができます。 Azure Communication Services を使用して Event Grid を構成すると、自分の管理下にある Azure リソースである Event Grid にイベント データが配信されます。 Azure Event Grid を使用するように Azure Communication Services を構成することはできますが、Event Grid リソースとその中に格納されているデータの管理は、ご自身で行う必要があります。

## <a name="relating-humans-to-azure-communication-services-identities"></a>Azure Communication Services の ID に人間を関連付ける

アプリケーションでは、人間のユーザーと Communication Services の ID の関係を管理します。 人間のユーザーのデータを削除する場合は、そのユーザーに関連付けられている Communication Services の ID すべてを含む、データを削除する必要があります。

Communication Services のデータには 2 つのカテゴリがあります。
- **API データ。** このデータは、Communication Services API シリーズによって作成および管理されます。典型的な例として、チャット API を介して管理されるチャット メッセージがあります。
- **Azure Monitor ログ。** このデータは、このサービスによって作成され、Azure Monitor データ プラットフォームで管理されます。 このデータには、Communication Services の使用状況を把握するのに役立つテレメトリとメトリックが含まれます。 これは、Communication Services API シリーズでは管理されません。

## <a name="api-data"></a>API データ

### <a name="identities"></a>Identities

Azure Communication Services は、ID のディレクトリを保持しており、それらを削除するには [DeleteIdentity](/rest/api/communication/communicationidentity/delete) API を使用します。 ID を削除すると、関連付けられているすべてのアクセス トークンが取り消され、そのチャット メッセージが削除されます。 ID の削除方法の詳細については、[このページを参照](../quickstarts/access-tokens.md)してください。

- DeleteIdentity

### <a name="azure-resource-manager"></a>Azure Resource Manager

Communication Services と共に Azure portal または Azure Resource Manager API シリーズを使用すると、個人データを作成できます。 [Azure Resource Manager システムで個人データを管理する方法を確認するには、このページを使用してください。](../../azure-resource-manager/management/resource-manager-personal-data.md)

### <a name="telephone-number-management"></a>電話番号の管理

Azure Communication Services では、Communication Services リソースに関連付けられた電話番号のディレクトリが保持されます。 次の[電話番号の管理 API](/rest/api/communication/phonenumberadministration) を使用して、電話番号を取得して削除します。

- `Get All Phone Numbers`
- `Release Phone Number`

### <a name="chat"></a>チャット

チャットのスレッドとメッセージは、明示的に削除されるまで保持されます。 完全にアイドル状態のスレッドは 30 日後に自動的に削除されます。 [チャット API シリーズ](/rest/api/communication/chat/chatthread)を使用すると、メッセージの取得、一覧表示、更新、削除を実行できます。

- `Get Thread`
- `Get Message`
- `Delete Thread`
- `Delete Message`

### <a name="sms"></a>SMS

送受信された SMS メッセージは、サービスによって短時間で処理され、保持されません。

### <a name="pstn-voice-calling"></a>PSTN 音声通話

音声およびビデオによる通信はサービスによって短時間で処理され、データは Azure Monitor ログ以外のリソースに保持されません。

### <a name="internet-voice-and-video-calling"></a>インターネットによる音声およびビデオ通話

音声およびビデオによる通信はサービスによって短時間で処理され、データは Azure Monitor ログ以外のリソースに保持されません。

## <a name="azure-monitor-and-log-analytics"></a>Azure Monitor とログ分析

Azure Communication Services は、サービスの動作の正常性と使用状況を把握するためにログ データを Azure Monitor に送ります。 これらのログには、フィールド データとして Communication Services の ID や電話番号が含まれているものもあります。 個人データである可能性のあるデータを削除するには、[Azure Monitor に対してこちらの手順を使用](../../azure-monitor/logs/personal-data-mgmt.md)してください。 また、[Azure Monitor で既定の保持期間](../../azure-monitor/logs/manage-cost-storage.md)を構成することもできます。

## <a name="additional-resources"></a>その他のリソース

- [GDPR および CCPA のための Azure データ主体要求](/microsoft-365/compliance/gdpr-dsr-azure)
- [Microsoft セキュリティ センター](https://www.microsoft.com/trust-center/privacy/data-location)
- [Azure の対話型マップ - 顧客データの場所](https://azuredatacentermap.azurewebsites.net/)
