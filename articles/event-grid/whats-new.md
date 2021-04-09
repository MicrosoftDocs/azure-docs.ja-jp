---
title: 新機能 リリース ノート - Azure Event Grid
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Event Grid の新着情報について説明します。
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: 7528cef7007704bbf434f16622707da8920eec2b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350009"
---
# <a name="whats-new-in-azure-event-grid"></a>Azure Event Grid の新機能

>URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` をコピーして、お使いの ![RSS フィード リーダー アイコン](./media/whats-new/feed-icon-16x16.png) フィード リーダーに貼り付け、更新内容を確認するためにこのページに再度アクセスするタイミングに関する通知を受け取るようにしてください。

Azure Event Grid は随時改善されています。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能
- 変更の計画

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- 一般提供 (GA) の新しいサービス API バージョン 2020-06-01 に対するサポートが追加されました。
- GA になった新機能は次のとおりです。
    - [入力マッピング](input-mappings.md)
    - [カスタムの入力スキーマ](input-mappings.md)
    - [クラウド イベント V10 スキーマ](cloud-event-schema.md)
    - [宛先としての Service Bus トピック](handler-service-bus.md)
    - [宛先としての Azure 関数](handler-functions.md)
    - [Webhook のバッチ処理](./edge/delivery-output-batching.md)
    - [Webhook のセキュリティ保護 (Azure Active Directory のサポート)](secure-webhook-delivery.md)
    - [IP フィルター](configure-firewall.md)
    - [Private Link サービスのサポート](configure-private-endpoints.md)
    - [イベント配信スキーマ](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview (2020-05)
- このリリースには、品質向上のための追加のバグ修正が含まれています。
- バージョン 5.3.1-preview と同様に、このリリースは 2020-04-01-Preview API バージョンに対応します。これには、次の新機能が含まれています。 
    - [ドメインおよびトピックにイベントを発行するときの IP フィルターのサポート](configure-firewall.md)
    - [パートナー トピック](./partner-events-overview.md)
    - [Azure portal での追跡対象リソースとしてのシステム トピック](system-topics.md)
    - [マネージド サービス ID を使用したイベント配信](managed-service-identity.md) 
    - [Private Link サービスのサポート](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview (2020-04)
- このリリースには、品質向上のためのさまざまなバグ修正が含まれています。
- バージョン 5.3.0-preview と同様に、このリリースは 2020-04-01-Preview API バージョンに対応します。これには、次の新機能が含まれます。 
    - [ドメインおよびトピックにイベントを発行するときの IP フィルターのサポート](configure-firewall.md)
    - [パートナー トピック](./partner-events-overview.md)
    - [Azure portal での追跡対象リソースとしてのシステム トピック](system-topics.md)
    - [マネージド サービス ID を使用したイベント配信](managed-service-identity.md) 
    - [Private Link サービスのサポート](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview (2020-03)
- バージョン 5.2.0-preview で既に追加されている機能に加えて、新機能が導入されました。 
- バージョン 5.2.0-preview と同様に、このリリースは 2020-04-01-Preview API バージョンに対応します。
- 次の新機能に対するサポートが追加されました。 
    - [ドメインおよびトピックにイベントを発行するときの IP フィルターのサポート](configure-firewall.md)
    - [パートナー トピック](./partner-events-overview.md)
    - [Azure portal での追跡対象リソースとしてのシステム トピック](system-topics.md)
    - [マネージド サービス ID を使用したイベント配信](managed-service-identity.md) 
    - [Private Link サービスのサポート](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview (2020-01)
- このリリースは 2020-04-01-Preview API バージョンに対応します。
- 次の新機能に対するサポートが追加されました。
    - [ドメインおよびトピックにイベントを発行するときの IP フィルターのサポート](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- このリリースは、`2019-06-01` API バージョンに対応します。
- 次の新機能に対するサポートが追加されました。
    * [ドメイン](event-domains.md)
    * リソース リスト操作に対する改ページ位置の自動修正と検索フィルター。 例については、「[トピック - サブスクリプション別の一覧表示](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)」を参照してください。
    * [宛先としての Service Bus キュー](handler-service-bus.md)
    * [高度なフィルター処理](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview (2019-03)
- このリリースは 2019-02-01-preview API バージョンに対応します。
- 次の新機能に対するサポートが追加されました。
    * リソース リスト操作に対する改ページ位置の自動修正と検索フィルター。 例については、「[トピック - サブスクリプション別の一覧表示](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription)」を参照してください。
    * [ドメイン トピックの手動での作成/削除](how-to-event-domains.md)
    * [宛先としての Service Bus キュー](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- このリリースは、`2019-01-01` の安定 API バージョンに対応します。
- イベント サブスクリプションに関連する次の機能の一般提供 (GA) をサポートしています。
    * [配信不能メッセージの宛先](manage-event-delivery.md)
    * [宛先としての Azure Storage キュー](handler-storage-queues.md)
    * [Azure Relay - 宛先としてのハイブリッド接続](handler-relay-hybrid-connections.md)
    * [手動によるハンドシェイク検証](webhook-event-delivery.md)
    * [再試行ポリシーのサポート](delivery-and-retry.md)
- まだプレビュー段階にある機能 ([Event Grid ドメイン](event-domains.md)、[高度なフィルターのサポート](event-filtering.md#advanced-filtering)など) にも、SDK の 3.0.1-preview バージョンを使用してアクセスできます。

## <a name="301-preview-2018-10"></a>3.0.1-preview (2018-10)
- [Newtonsoft NuGet パッケージの 10.0.3 バージョン](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3)に依存します。

## <a name="300-preview-2018-10"></a>3.0.0-preview (2018-10)
- このリリースは、2018-09-15-preview API バージョンで導入された新機能のプレビュー SDK です。 - このリリースには、以下のサポートが含まれます。
    - [ドメインとドメイン トピック](event-domains.md)
    - [イベント サブスクリプションの有効期限](concepts.md#event-subscription-expiration)の導入
    - イベント サブスクリプションの[高度なフィルター処理](event-filtering.md#advanced-filtering)の導入
    - `2018-01-01` API バージョンを対象とした安定バージョンの SDK は、引き続きバージョン1.3.0 として存在します。

## <a name="next-steps"></a>次のステップ