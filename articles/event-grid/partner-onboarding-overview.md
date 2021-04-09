---
title: パートナーのオンボードの概要 (Azure Event Grid)
description: Event Grid パートナーとしてオンボードする方法の概要について説明します。
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 2a2e33395cabd368d5d5d870dd0461e4cbd37e0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93081197"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>パートナーのオンボードの概要 (Azure Event Grid)

この記事では、Azure Event Grid パートナー リソースを非公開で使用する方法と、公開されたパートナー トピックの種類になる方法について説明します。

イベントの発行に関連付けられている Event Grid リソースの種類を Event Grid パートナーとして使用するために特別なアクセス許可は必要ありません。 パートナーになることを検討している場合は、実際、今日からそれらを使用して、ご自分の Azure サブスクリプションに非公開でイベントを発行したり、リソース モデルをテストしたりすることができます。

> [!NOTE]
> Azure portal を使用して Event Grid パートナーとしてオンボードする方法の詳細な手順については、[Event Grid パートナーとしてオンボードする方法 (Azure portal)](onboard-partner.md) に関するページを参照してください。 

## <a name="how-partner-events-work"></a>パートナー イベントのしくみ
パートナー イベント機能には既存のアーキテクチャが採用されています。これは Event Grid で Azure Storage や Azure IoT Hub などの Azure リソースからイベントを発行するために既に使用されているものであり、それらのツールは誰でも利用できるように公開されています。 これらのツールの使用は、既定で Azure サブスクリプションのみに限定されています。 イベントを公開するには、フォームに入力して、[Event Grid チームにお問い合わせください](mailto:gridpartner@microsoft.com)。

パートナー イベント機能を使用すると、Azure Event Grid にイベントを発行してマルチテナントで使用できます。

## <a name="onboarding-and-event-publishing-overview"></a>オンボードとイベント発行の概要

### <a name="partner-flow"></a>パートナー フロー

1. Azure テナントをまだお持ちでない場合は作成します。
1. Azure CLI を使用して、新しい Event Grid `partnerRegistration` を作成します。 このリソースには、表示名、説明、セットアップ URI などの情報が含まれます。

    ![パートナー トピックを作成する](./media/partner-onboarding-how-to/create-partner-registration.png)

1. イベントを発行する各リージョンに、1 つ以上のパートナー名前空間を作成します。 Event Grid サービスによって、発行エンドポイント (`https://contoso.westus-1.eventgrid.azure.net/api/events` など) とアクセス キーがプロビジョニングされます。

    ![パートナー名前空間を作成する](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. パートナー トピックを必要とする顧客を御社のシステムに登録する方法を用意します。
1. Event Grid チームに連絡して、パートナー トピックの種類を公開にするように伝えてください。

### <a name="customer-flow"></a>顧客フロー

1. 顧客は Azure portal にアクセスし、パートナー トピックの作成を希望する Azure サブスクリプション ID とリソース グループをメモします。
1. 顧客は御社のシステムを使用してパートナー トピックを依頼します。 御社はそれに応じてパートナー名前空間へのイベント トンネルを作成します。
1. Event Grid によって、顧客の Azure サブスクリプションとリソース グループ内に **保留中** のパートナー トピックが作成されます。

    ![イベント チャネルを作成する](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. 顧客は、Azure portal を使用してパートナー トピックをアクティブ化します。 これで、御社のサービスから顧客の Azure サブスクリプションまでイベントをフローさせることができるようになります。

    ![パートナー トピックをアクティブ化する](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>リソース モデル
次のリソース モデルは、パートナー イベントを対象としています。

### <a name="partner-registrations"></a>パートナー登録
* リソース: `partnerRegistrations`
* 使用元:パートナー
* 説明:サービスとしてのソフトウェア (SaaS) パートナーのグローバル メタデータ (名前、表示名、説明、セットアップ URI など) を取り込みます。
    
    パートナー登録の作成または更新は、パートナーのセルフサービス操作です。 このセルフサービス機能により、パートナーはエンドツーエンドの完全なフローを構築およびテストできます。
    
    顧客は、Microsoft が承認したパートナー登録のみを見つけることができます。
* スコープ:パートナーの Azure サブスクリプションで作成されます。 メタデータは、公開されると、顧客に表示されます。

### <a name="partner-namespaces"></a>パートナー名前空間
* リソース: `partnerNamespaces`
* 使用元:パートナー
* 説明:顧客イベントを発行するためのリージョンのリソースを提供します。 各パートナー名前空間には、発行エンドポイントと認証キーがあります。 名前空間は、パートナーが特定の顧客用にパートナー トピックを依頼し、アクティブな顧客を一覧表示するためにも使用されます。
* スコープ:パートナーのサブスクリプションに存在します。

### <a name="event-channel"></a>イベント チャネル
* リソース: `partnerNamespaces/eventChannels`
* 使用元:パートナー
* 説明:イベント チャネルは、顧客のパートナー トピックのミラーです。 イベント チャネルを作成し、メタデータで顧客の Azure サブスクリプションとリソース グループを指定することで、顧客用のパートナー トピックを作成するように Event Grid に知らせます。 Event Grid から Azure Resource Manager 呼び出しが発行され、対応するパートナー トピックが顧客のサブスクリプションに作成されます。 パートナー トピックは、保留中の状態で作成されます。 各イベント チャネルとパートナー トピックの間には 1 対 1 のリンクがあります。
* スコープ:パートナーのサブスクリプションに存在します。

### <a name="partner-topics"></a>パートナー トピック
* リソース: `partnerTopics`
* 使用元:顧客
* 説明:パートナー トピックは、Event Grid のカスタム トピックやシステム トピックに似ています。 各パートナー トピックは、特定のソース (たとえば、`Contoso:myaccount`) と特定のパートナー トピックの種類 (たとえば、Contoso) に関連付けられています。 顧客は、パートナー トピックに対してイベント サブスクリプションを作成し、イベントをさまざまなイベント ハンドラーにルーティングします。

    顧客がこのリソースを直接作成することはできません。 パートナー トピックを作成する唯一の方法は、イベント チャネルを作成するパートナー操作を使用することです。
* スコープ:顧客のサブスクリプションに存在します。

### <a name="partner-topic-types"></a>パートナー トピックの種類
* リソース: `partnerTopicTypes`
* 使用元:顧客
* 説明:パートナー トピックの種類は、テナント全体のリソースの種類であり、これによって、顧客は承認されたパートナー トピックの種類の一覧を見つけることができます。 URL は、 https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes) のようになります。
* スコープ:グローバル

## <a name="publish-events-to-event-grid"></a>Event Grid にイベントを発行する
Azure リージョンにパートナー名前空間を作成すると、リージョン エンドポイントと対応する認証キーが得られます。 その名前空間内のすべての顧客イベント チャネル向けに、イベントのバッチをこのエンドポイントに発行します。 イベントのソース フィールドに基づいて、Azure Event Grid によって各イベントが対応するパートナー トピックにマップされます。

### <a name="event-schema-cloudevents-v10"></a>イベント スキーマ:CloudEvents v1.0
CloudEvents 1.0 スキーマを使用して Azure Event Grid にイベントを発行します。 Event Grid は、構造化モードとバッチ モードの両方をサポートしています。 CloudEvents 1.0 は、パートナー名前空間でサポートされている唯一のイベント スキーマです。

### <a name="example-flow"></a>フローの例

1.  発行サービスによって、`https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` への HTTP POST が行われます。
1.  この要求には、認証用のキーを含む aeg-sas-key という名前のヘッダー値を設定します。 このキーは、パートナー名前空間の作成時にプロビジョニングされます。 たとえば、有効なヘッダー値は aeg-sas-key:VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg== です。
1.  Content-Type ヘッダーを "application/cloudevents-batch+json; charset=UTF-8a" に設定します。
1.  発行 URL に対して、そのリージョンに対応するイベントのバッチを使用して HTTP POST クエリを実行します。 次に例を示します。

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

パートナー名前空間エンドポイントに投稿すると、応答が返されます。 応答は、標準 HTTP 応答コードです。 いくつかの一般的な応答を次に示します。

| 結果                             | Response              |
|------------------------------------|-----------------------|
| Success                            | 200 OK                |
| イベント データの形式が正しくない    | 400 Bad Request       |
| 無効なアクセス キー                 | 401 権限がありません      |
| エンドポイントが正しくない                 | 404 見つかりません         |
| 配列またはイベントが、サイズ制限を超えています | 413 ペイロードが大きすぎます |

## <a name="references"></a>References

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM テンプレート](/azure/templates/microsoft.eventgrid/allversions)
  * [ARM テンプレート スキーマ](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API](/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [CLI 拡張機能](/cli/azure/ext/eventgrid/)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>次のステップ
- [パートナー トピックの概要](partner-events-overview.md)
- [パートナー トピックのオンボード フォーム](https://aka.ms/gridpartnerform)
- [Auth0 パートナー トピック](auth0-overview.md)
- [Auth0 パートナー トピックの使用方法](auth0-how-to.md)
