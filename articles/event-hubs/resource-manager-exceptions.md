---
title: Azure Event Hubs - Resource Manager の例外 | Microsoft Docs
description: Azure Resource Manager によって表示される Azure Event Hubs の例外と推奨されるアクションの一覧。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: cec24696d0d49ba408860f6562c34dd14876c311
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91334210"
---
# <a name="azure-event-hubs---resource-manager-exceptions"></a>Azure Event Hubs - Resource Manager の例外
この記事では、Azure Resource Manager を使用して、テンプレートまたは直接呼び出しにより Azure Event Hubs とやり取りするときに生成される例外を示します。

> [!IMPORTANT]
> このドキュメントは頻繁に更新されます。 更新プログラムがないかどうか確認してください。

次のセクションでは、Azure Resource Manager によって表示されるさまざまな例外/エラーを示します。

## <a name="error-code-conflict"></a>エラー コード:Conflict

| エラー コード | エラー サブコード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Conflict | 40300 | The maximum number of resources of type EventHub has been reached or exceeded. (EventHub タイプのリソースの最大数に達したか、それを超えています。) Actual: #, Max allowed: # (実際: #、許可される最大数: #) | 名前空間は、格納できる Event Hubs 数の[クォータ](event-hubs-quotas.md)に達しました。 | 未使用または余分なイベント ハブを名前空間から削除するか、[専用クラスター](event-hubs-dedicated-overview.md)にアップグレードすることを検討してください。 |
| Conflict | なし | Disaster recovery (DR) config can't be deleted because replication is in progress. (レプリケーションが進行中のため、ディザスター リカバリー (DR) 構成を削除できません。) Fail over or break pairing before attempting to delete the DR Config. (フェールオーバーまたはペアリングを解除してから、DR 構成を削除します。) | [GeoDR レプリケーション](event-hubs-geo-dr.md)が進行中であるため、現時点では構成を削除できません。 | 構成削除のブロックを解除するには、レプリケーションが完了するまで待機するか、フェールオーバーをトリガーするか、GeoDR のペアリングを解除します。 |
| Conflict | なし | Namespace update failed with conflict in backend. (バックエンドで競合が発生したため、名前空間の更新に失敗しました。) | 現在、この名前空間で別の操作が実行されています。 | 現在の操作が完了するまで待ってから、再試行してください。 |

## <a name="error-code-429"></a>エラー コード:429

| エラー コード | エラー サブコード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | なし | Namespace provisioning in transition (名前空間のプロビジョニングが移行中です。) | 現在、この名前空間で別の操作が実行されています。 | 現在の操作が完了するまで待ってから、再試行してください。 |
| 429 | なし | Disaster recovery operation in progress. (ディザスター リカバリー操作が進行中です。) | 現在、この名前空間またはペアリングで [GeoDR](event-hubs-geo-dr.md) 操作が実行されています。 | 現在の GeoDR 操作が完了するまで待ち、その後再試行してください。 |

## <a name="error-code-badrequest"></a>エラー コード:BadRequest

| エラー コード | エラー サブコード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| BadRequest | 40000 | PartitionCount can't be changed for an event hub. (イベント ハブの PartitionCount を変更することはできません。) | Basic または Standard レベルの Azure Event Hubs では、パーティションの変更はサポートされていません。 | Basic または Standard レベルの名前空間で必要な数のパーティションを使用した新しいイベント ハブを作成します。 パーティション スケールアウトは[専用クラスター](event-hubs-dedicated-overview.md)に対してサポートされています。 |
| BadRequest | 40000 | The value '#' for MessageRetentionInDays isn't valid for the Basic tier. (MessageRetentionInDays の値 '#' は、Basic レベルでは無効です。) the value can't exceed '1' day(s). (値は '1' 日を超えることはできません。) | Basic レベルの Event Hubs 名前空間でサポートされるメッセージの保有期間は、最大 1 日です。 | メッセージの保有期間が 1 日以上必要な場合は、[標準 Event Hubs 名前空間](event-hubs-create.md)を作成します。 | 
| BadRequest | なし | The specified name isn't available. (指定された名前は使用できません。) | 名前空間名は一意である必要がありますが、指定された名前は既に取得されています。 | 指定した名前の既存の名前空間の所有者である場合は、それを削除することで、データが失われます。 次に、同じ名前でもう一度やり直してください。 名前空間を削除することが安全でない (または所有者ではない) 場合は、別の名前空間名を選択します。 |
| BadRequest | なし | The specified subscription has reached its quota of namespaces. (指定されたサブスクリプションは、名前空間のクォータに達しました。) | サブスクリプションは、保持できる名前空間の数の[クォータ](event-hubs-quotas.md)に達しました。 | このサブスクリプションで使用されていない名前空間を削除するか、別のサブスクリプションを作成するか、[専用クラスター](event-hubs-dedicated-overview.md)にアップグレードすることを検討してください。 |
| BadRequest | なし | Can't update a namespace that is secondary (セカンダリ名前空間は更新できません) | [GeoDR ペアリング](event-hubs-geo-dr.md)のセカンダリ名前空間であるため、名前空間を更新できません。 | 必要に応じて、代わりにこのペアリングでプライマリ名前空間に変更を加えます。 それ以外の場合は、GeoDR のペアリングを解除して変更を行います。 |
| BadRequest | なし | Can't set Auto-Inflate in basic SKU (Basic SKU では自動インフレを設定できません) | Basic レベルの Event Hubs 名前空間で自動インフレを有効にすることはできません。 | 名前空間で[自動インフレ](event-hubs-auto-inflate.md)を有効にするには、Standard レベルであることを確認します。 |
| BadRequest | なし | There isn't enough capacity to create the namespace. (名前空間を作成するのに十分な容量がありません。) Contact your Event Hubs administrator. (Event Hubs 管理者に連絡してください。) | 選択したリージョンは容量の上限に達しているため、名前空間を追加で作成することはできません。 | 名前空間を格納する別のリージョンを選択します。 |
| BadRequest | なし | The operation can't be done on entity type 'ConsumerGroup' because the namespace 'namespace name' is using 'Basic' tier. (名前空間 'namespace name' が 'Basic' レベルを使用しているため、エンティティ型 'ConsumerGroup' に対して操作を実行できません。)  | Basic レベルの Event Hubs 名前空間には、1 つのコンシューマー グループ (既定) というクォータ (event-hubs-quotas.md) があります。 コンシューマー グループの作成はサポートされていません。 | 既定のコンシューマー グループ ($Default) を引き続き使用するか、さらに必要な場合は、代わりに Standard レベルの Event Hubs 名前空間を使用することを検討してください。 | 
| BadRequest | なし | The namespace 'namespace name' doesn't exist. (名前空間 '<名前空間名>' は存在しません。) | 指定された名前空間が見つかりませんでした。 | 名前空間の名前が正しいことと、サブスクリプションに存在することを確認してください。 存在しない場合は、[Event Hubs 名前空間を作成します](event-hubs-create.md)。 | 
| BadRequest | なし | The location property of the resource doesn't match its containing Namespace. (名前空間と含まれているリソースのロケーション プロパティが一致しません。) | イベント ハブが名前空間のリージョンと一致しなかったため、特定のリージョンでイベント ハブの作成に失敗しました。 | 名前空間と同じリージョンにイベント ハブを作成してみてください。 | 

## <a name="error-code-internal-server-error"></a>エラー コード:内部サーバー エラー

| エラー コード | エラー サブコード | エラー メッセージ | 説明 | 推奨 |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 内部サーバー エラー | なし | 内部サーバー エラー。 | Event Hubs サービスで内部エラーが発生しました。 | 失敗した操作を再試行してください。 操作が引き続き失敗する場合は、サポートにお問い合わせください。 |