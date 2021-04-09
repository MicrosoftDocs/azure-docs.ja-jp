---
title: include file description: include file services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 02/01/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

次の表に、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) に固有のクォータと制限を示します。 Event Hubs の価格については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

### <a name="common-limits-for-all-tiers"></a>すべてのレベルに共通の制限
次の制限は、すべてのレベルで共通です。 

| 制限 |  Notes | 値 |
| --- |  --- | --- |
| サブスクリプションあたりの Event Hubs 名前空間の数 |- |100 |
| 名前空間あたりのイベント ハブの数 | 以後、新しいイベント ハブの作成要求は拒否されます。 |10 |
| イベント ハブ名のサイズ |- | 256 文字 |
| コンシューマー グループ名のサイズ |- | 256 文字 |
| コンシューマー グループあたりの非エポック受信者の数 |- |5 |
| 名前空間ごとの承認規則の数 | 上限を超えると承認規則の作成要求が拒否されます。|12 |
| GetRuntimeInformation メソッドの呼び出し数 |  - | 1 秒あたり 50 | 
| 仮想ネットワーク (VNet) の数 | - | 128 | 
| IP 構成規則の数 | - | 128 | 


### <a name="basic-vs-standard-tiers"></a>Basic レベルと Standard レベル
次の表では、Basic と Standard のレベルで異なる可能性がある制限を示しています。 

| 制限 | Notes | Basic | Standard |
|---|---|--|---|
| Event Hubs 発行の最大サイズ| &nbsp; | 256 KB | 1 MB |
| イベント ハブあたりのコンシューマー グループの数 | &nbsp; |1 |20 |
| 名前空間あたりの AMQP 接続の数 | 追加の接続に関する後続の要求は拒否され、呼び出し元コードが例外を受け取ります。 |100 |5,000|
| イベント データの最大リテンション期間 | &nbsp; |1 日 |1 ～ 7 日間 |
| 最大スループット ユニット |この制限を超えると、データが調整され、[サーバー ビジー例外](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)が生成されます。 Standard レベルに対してより多くのスループット ユニットを要求するには、[サポート リクエスト](../articles/azure-portal/supportability/how-to-create-azure-support-request.md)を申請します。 [追加スループット ユニット](../articles/event-hubs/event-hubs-auto-inflate.md)は、20 単位で購入できます。 |20 | 20 | 
| イベント ハブあたりのパーティションの数 | |32 | 32 | 

> [!NOTE]
>
> イベントは、個別に発行することもバッチ処理することもできます。 
> 発行の制限 (SKU に基づく) は、1 つのイベントであるかバッチであるかに関係なく適用されます。 最大しきい値を超えるイベントの発行は拒否されます。

### <a name="dedicated-tier-vs-standard-tier"></a>Dedicated レベルと Standard レベル
Event Hubs Dedicated オファリングは固定の月額料金で課金され、最低の使用量は 4 時間です。 Dedicated レベルの機能は Standard プランとすべて同じですが、要求の厳しいワークロードを実行するお客様向けにエンタープライズ スケールの容量と制限で提供されます。 

Azure portal を使用して専用 Event Hubs クラスターを作成する方法については、この[ドキュメント](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md)を参照してください。

| 機能 | Standard | 専用 |
| --- |:---|:---|
| 帯域幅 | 20 TU (最大 40 TU) | 20 CU |
| 名前空間 |  1 | CU あたり 50 |
| Event Hubs |  名前空間あたり 10 | 名前空間あたり 1,000 |
| イングレス イベント | 100 万イベントごとの課金 | Included |
| メッセージ サイズ | 100 万バイト | 100 万バイト |
| [メジャー グループ] | イベント ハブあたり 32 | イベント ハブあたり 1,024<br/>CU あたり 2,000 |
| コンシューマー グループ | イベント ハブあたり 20 | CU あたりの制限なし、イベント ハブあたり 1,000 |
| 仲介型接続 | 1,000 (付属)、最大 5,000 | 100,000 (付属、最大) |
| メッセージのリテンション期間 | 7 日、TU あたり 84 GB を含む | 90 日、CU あたり 10 TB を含む |
| キャプチャ | 1 時間ごとの課金 | Included |


### <a name="schema-registry-limitations"></a>スキーマ レジストリに関する制限

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Standard と Dedicated のレベルで同じ制限 
| 機能 | 制限 | 
|---|---|
| スキーマ グループ名の最大長 | 50 |  
| スキーマ名の最大長 | 100 |    
| スキーマあたりのサイズ (バイト) | 1 MB |   
| スキーマ グループあたりのプロパティの数 | 1024 |
| グループ プロパティ キーあたりのサイズ (バイト) | 256 | 
| グループ プロパティ値あたりのサイズ (バイト) | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Standard と Dedicated のレベルで異なる制限 

| 制限 | Standard | 専用 | 
|---|---|--|
| スキーマ レジストリ (名前空間) のサイズ (MB) | 25 |  1024 |
| スキーマ レジストリまたは名前空間内のスキーマ グループの数 | 1 (既定のグループは除く) | 1000 |
| スキーマ グループ全体におけるスキーマ バージョンの数 | 25 | 10000 |