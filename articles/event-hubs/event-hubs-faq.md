---
title: "Azure Event Hubs の FAQ | Microsoft Docs"
description: "Azure Event Hubs のよく寄せられる質問 (FAQ)"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: bfa10984-eb22-4671-861a-f377a90d9372
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2017
ms.author: sethm;jotaub;shvija
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 7bae4ae6d41e6dc6515a3fcdf574ffd193ae1aa3
ms.lasthandoff: 03/14/2017

---

# <a name="event-hubs-frequently-asked-questions"></a>Event Hubs のよく寄せられる質問

## <a name="general"></a>全般

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Event Hubs の Basic レベルと Standard レベルは何が違いますか。
Azure Event Hubs の Standard レベルは、Basic レベルでは使用できない機能を提供します。 Standard レベルには次の機能が含まれています。
* 長いイベント リテンション期間
* 追加の仲介型接続。提供される数を超える場合は超過料金が適用されます
* 複数のコンシューマー グループ
* [アーカイブ](https://docs.microsoft.com/azure/event-hubs/event-hubs-archive-overview)

専用の Event Hubs を含む価格レベルの詳細については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

### <a name="what-are-event-hubs-throughput-units"></a>Event Hubs のスループット単位とは何ですか。
Event Hubs のスループット単位は、Azure Portal または Event Hubs リソース マネージャー テンプレートによってユーザーが明示的に選択します。 スループット単位はイベント ハブ名前空間のすべての Event Hubs に適用され、それぞれのスループット単位はその名前空間に次の使用許可を与えます。

* 最大 1 MB/秒の受信イベント (Event Hub に送信されるイベント)。ただし、1 秒あたり 1000 個未満の受信イベント、管理操作、または制御 API 呼び出し。
* 最大 2 MB/秒の送信イベント (Event Hub から使用されるイベント)。
* 最大 84 GB のイベント ストレージ (既定の 24 時間の保持期間に対応できる十分なストレージ)。

Event Hubs のスループット単位は、特定の時間に選択された単位の最大数に基づいて時間単位で課金されます。

### <a name="how-are-event-hubs-throughput-unit-limits-enforced"></a>Event Hubs のスループット単位制限はどのように適用されますか。
名前空間内のすべての Event Hubs の合計受信スループットまたは合計受信イベント レートがスループット単位の上限の総計を超過した場合は、送信側が調整され、受信クォータを超えたことを示すエラーを受信します。

名前空間内のすべての Event Hubs の合計送信スループットまたは合計受信イベント レートがスループット単位の上限の総計を超過した場合は、送信側が調整され、受信クォータを超えたことを示すエラーを受信します。 送信側でイベントの使用速度が低下しないようにするため、また受信側で Event Hubs にイベントを送信できなくなることを避けるために、受信クォータと送信クォータは別々に適用されます。

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-selected"></a>選択できるスループット単位の数に制限はありますか。
名前空間ごとに 20 というスループット単位の既定のクォータがあります。 サポート チケットを送信することで、スループット単位の大きなクォータを要求できます。 20 というスループット単位の制限を超える 20 ～ 100 スループット単位のバンドルを使用できます。 20 を超えるスループット単位を使用すると、サポート チケットの送信なしでスループット単位の数を変更する機能は無効になることに注意してください

### <a name="can-i-use-a-single-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>単一の AMQP 接続を使用して、複数の Event Hubs から送受信を実行できますか。
はい。すべての Event Hubs が同じ名前空間にある限り可能です。

### <a name="what-is-the-maximum-retention-period-for-events"></a>イベントの最大保有期間は何日ですか。
Event Hubs Standard レベルでは、現在最大 7 日間のリテンション期間をサポートしています。 Event Hubs は永続的なデータ ストアになることを目的としていません。 24 時間を超える保持期間の目的は、同じシステムでイベント ストリームを再生すると便利なシナリオ (たとえば既存データで新しい機械学習モデルのトレーニングや検証を行うこと) に対応することです。 7 日間を超えるメッセージのリテンション期間が必要な場合は、Event Hub で [Archive](https://docs.microsoft.com/azure/event-hubs/event-hubs-archive-overview) を有効にすると Event Hub から任意のストレージにデータをプルできます。 Archive を有効にすると、購入済みのスループット単位に基づく料金が発生します。

### <a name="where-is-azure-event-hubs-available"></a>Azure Event Hubs はどこで利用できますか。
Azure Event Hubs は、サポートされているすべての Azure リージョンで利用できます。 一覧については、「[Azure リージョン](https://azure.microsoft.com/regions/)」ページを参照してください。  

## <a name="best-practices"></a>ベスト プラクティス

### <a name="how-many-partitions-do-i-need"></a>パーティションはいくつ必要ですか。
セットアップの終了後は、Event Hub のパーティション数を変更できないことに注意してください。 作業を開始する前に、その点を考慮して、必要なパーティションの数を検討する必要があります。 

Event Hubs は、コンシューマー グループ&1; つにつきパーティション リーダーを&1; つ許可するように設計されています。 ほとんどのユース ケースでは、既定の設定の&4; つのパーティションで十分です。 イベント処理のスケール設定を予定している場合は、パーティションを追加したほうが良い場合があります。 パーティションには特定のスループット制限はありませんが、名前空間の総スループットは、スループット単位の数によって制限されます。 名前空間内のスループット単位の数を増やすときは、独自の最大スループットを実現するために、同時読み取りを許可するための追加のパーティションが必要になる場合があります。

ただし、アプリケーションで特定のパーティションに対してアフィニティが設定されているモデルがある場合は、パーティションの数を増やすことによる利点がない場合があります。 詳細については、[可用性と一貫性](event-hubs-availability-and-consistency.md)に関するページを参照してください。

## <a name="pricing"></a>価格

### <a name="where-can-i-find-more-pricing-information"></a>価格についての詳細情報はどこにありますか。
Event Hubs 料金の詳細については、「 [Event Hubs 料金](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>Event Hubs のイベントを 24 時間以上保持する場合に料金はかかりますか。
Event Hubs Standard レベルでは、24 時間を超えて最大 7 日間メッセージを保持できます。 保存されたイベントの合計数が選択したスループット単位数のストレージの上限 (スループット単位あたり&84; GB) を超える場合、上限を超えるサイズには公開された Azure Blob ストレージ レートの料金が発生します。 各スループット単位のストレージの上限は、スループット単位が受信の上限まで使用された場合でも、24 時間の保持期間に対するすべてのストレージ コストをカバーします。

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Event Hubs のストレージ サイズはどのように計算され、課金されますか。
保存されたすべてのイベントの合計サイズは、すべての Event Hubs のイベント ヘッダーまたはディスク ストレージ構造の内部オーバーヘッドを含めて、1 日中測定されます。 1 日の終わりに、ピーク ストレージ サイズが計算されます。 1 日あたりのストレージの上限は、その日に選択されたスループット単位の最小数に基づいて計算されます (それぞれのスループット単位には 84 GB の上限が与えらえます)。 合計サイズが計算された&1; 日あたりのストレージの上限を超過した場合は、超過したストレージが、Azure Blob ストレージ レート ( **Locally Redundant Storage (LRS)** ) を使用して課金されます。

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Event Hubs のイングレス イベントはどのように計算されますか。
Event Hubs に送信されたイベントは、それぞれが課金対象メッセージとしてカウントされます。 1 つの *イングレス イベント* は 64 KB 以下の単位のデータと定義されます。 サイズが 64 KB 以下のイベントは、1 つの課金対象イベントとみなされます。 イベントが 64 KB よりも大きい場合、課金対象イベントの数はイベント サイズに従って計算され、64 KB の倍数になります。 たとえば、Event Hub に送信された 8 KB のイベントは 1 つのイベントとして課金されますが、Event Hub に送信された 96 KB のメッセージは 2 つのイベントとして課金されます。

Event Hub や管理操作、チェックポイントなどの制御呼び出しで使用されるイベントは、課金対象受信イベントとしてはカウントされませんが、スループット単位の上限まで蓄積されます。

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>仲介型接続料金は Event Hubs に適用されますか。
接続料金は AMQP プロトコルが使用されている場合にのみ適用されます。 送信側システムまたはデバイスの数に関係なく、HTTP を使用したベントの送信には接続料金は発生しません。 AMQP を使用する場合 (たとえば、イベント ストリーミングの効率を高めたり、IoT のコマンドと制御のシナリオで双方向通信を可能にする場合) は、[Event Hubs の価格情報](https://azure.microsoft.com/pricing/details/event-hubs/)に関するページで各サービスレベルに含まれる接続数に関する詳細を参照してください。

### <a name="how-is-event-hubs-archive-billed"></a>Event Hubs Archive はどのように課金されますか。
Archive が有効になるのは、名前空間内のいずれかの Event Hub で Archive 機能が有効になっている場合です。 Archive は購入済みのスループット単位ごとに時間単位で課金されます。 スループット単位数が増減すると、Event Hubs Archive の課金についても、全体の時間の増分にスループット単位数の変化が反映されます。
Event Hubs Archive の課金の詳細については、[Event Hubs の料金情報](https://azure.microsoft.com/pricing/details/event-hubs/)に関するページをご覧ください。

### <a name="will-i-be-billed-for-the-storage-account-i-select-for-event-hubs-archive"></a>Event Hubs Archive 用に選択したストレージ アカウントに対しては課金されますか。
Event Hub で有効にされた場合、Archive はお客様の指定したストレージ アカウントを使用します。 お客様のストレージ アカウントであるため、このアカウントに関する変更はお客様の Azure サブスクリプションに課金されます。

## <a name="quotas"></a>Quotas (クォータ)

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Event Hubs に関連付けられているクォータはありますか。
Event Hubs のすべてのクォータの一覧については、[クォータ](event-hubs-quotas.md)に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Event Hubs によって生成される例外とその推奨されるアクションをいくつか教えてください。
発生する可能性がある Event Hubs 例外の一覧については、[例外の概要](event-hubs-messaging-exceptions.md)に関するページを参照してください。

### <a name="diagnostic-logs"></a>診断ログ
Event Hubs では、アーカイブ エラー ログと運用ログの&2; 種類の[診断ログ](event-hubs-diagnostic-logs.md)がサポートされており、両方とも JSON で表されます。この診断ログは Azure Portal で有効にできます。

### <a name="support-and-sla"></a>サポートと SLA
Event Hubs のテクニカル サポートは、 [コミュニティ フォーラム](https://social.msdn.microsoft.com/forums/azure/home)を通して利用できます。 課金とサブスクリプション管理のサポートは無料で提供されます。

SLA の詳細については、「 [サービス レベル アグリーメント](https://azure.microsoft.com/support/legal/sla/) 」ページを参照してください。

## <a name="next-steps"></a>次のステップ
Event Hubs の詳細については、次のリンク先を参照してください:

* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hub を作成する](event-hubs-create.md)

