---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: d6ff823951e6474a35a514036f02c0aabb17bf01
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997705"
---
> [!NOTE]
> 固定されないリソースの場合は、サポート チケットを開いてクォータの増加を要求してください。 上限を高くするために追加の Azure Media Services アカウントを作成することはしないでください。

### <a name="account-limits"></a>アカウントの制限

| リソース | 既定の制限 | 
| --- | --- | 
| 1 つのサブスクリプション内の Media Services アカウント | 25 (固定) |

### <a name="asset-limits"></a>アセットの制限

| リソース | 既定の制限 | 
| --- | --- | 
| Media Services アカウントあたりのアセット数 | 1,000,000|

### <a name="storage-media-limits"></a>ストレージ (メディア) の制限

| リソース | 既定の制限 | 
| --- | --- | 
| ファイル サイズ| シナリオによっては、Media Services での処理についてサポートされている最大ファイル サイズに制限があります。 <sup>(1)</sup> |
| ストレージ アカウント | 100<sup>(2)</sup> (固定) |

<sup>1</sup> Azure Blob Storage では現在、1 つの BLOB でサポートされる最大サイズは 5 TB です。 Media Services ではさらに、サービスで使用される VM サイズに基づく別の制限が適用されます。 サイズの上限は、アップロードするファイルのほかに、Media Services 処理 (エンコードまたは分析) の結果として生成されるファイルにも適用されます。 ソース ファイルが 260 GB を超える場合、Job は失敗する可能性があります。 

以下の表では、メディア占有ユニット S1、S2、S3 での制限を示します。 ソース ファイルがこの表に定義されている上限を超える場合、エンコード ジョブは失敗します。 4K 解像度の長時間ソースをエンコードする場合、必要なパフォーマンスを確保するためには、S3 メディア占有ユニットを使用する必要があります。 S3 メディア占有ユニットに関する 260 GB の制限を超える 4K コンテンツがある場合は、サポート チケットを開きます。

|メディア占有ユニットの種類|最大入力サイズ (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> ストレージ アカウントは、同じ Azure サブスクリプションからのものである必要があります。

### <a name="jobs-encoding--analyzing-limits"></a>Job (エンコードおよび分析) の制限

| リソース | 既定の制限 | 
| --- | --- | 
| Media Services アカウントあたりの Job | 500,000 <sup>(3)</sup> (固定)|
| Job ごとの Job 入力 | 50 (固定)|
| Job ごとの Job 出力 | 20 (固定) |
| Media Services アカウントあたりの Transform | 100 (固定)|
| Transform にある Transform の出力 | 20 (固定) |
| Job 入力ごとのファイル|10 (固定)|

<sup>3</sup> この数には、キューに置かれた Job、終了した Job、アクティブな Job、および取り消された Job が含まれます。 削除された Job は含まれません。 

レコードの合計数が最大クォータより小さい場合でも、アカウント内の 90 日前より古いすべての Job レコードは自動的に削除されます。 

### <a name="live-streaming-limits"></a>ライブ ストリーミングの制限

| リソース | 既定の制限 | 
| --- | --- | 
| Media Services アカウントあたりのライブ イベント数 <sup>(4)</sup> |5|
| ライブ イベントあたりのライブ出力 |3 <sup>(5)</sup> |
| ライブ出力の最大期間 | [DVR ウィンドウのサイズ](../articles/media-services/latest/live-event-cloud-dvr.md) |

<sup>4</sup> ライブ イベントの制限の詳細については、[ライブ イベントの種類の比較と制限](../articles/media-services/latest/live-event-types-comparison.md)に関する記事を参照してください。

<sup>5</sup> ライブ出力は作成すると開始され、削除されると停止します。

### <a name="packaging--delivery-limits"></a>パッケージおよび配信の制限

| リソース | 既定の制限 | 
| --- | --- | 
| Media Services アカウントあたりのストリーミング エンドポイント (停止済みまたは実行中)|2 (固定)|
| 動的マニフェスト フィルター|100|
| ストリーミング ポリシー | 100 <sup>(6)</sup> |
| 1 つの資産に同時に関連付けられる一意のストリーミング ロケーター数 | 100<sup>(7)</sup> (固定) |

<sup>6</sup> カスタム [ストリーミング ポリシー](https://docs.microsoft.com/rest/api/media/streamingpolicies)を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いの StreamingLocator に対してそのセットを再利用する必要があります。 ストリーミング ロケーターごとに新しいストリーミング ポリシーを作成しないでください。

<sup>7</sup> ストリーミング ロケーターは、ユーザーごとのアクセス制御を管理するようには設計されていません。 個々のユーザーに異なるアクセス権限を付与するには、デジタル著作権管理 (DRM) ソリューションを使用します。

### <a name="protection-limits"></a>保護の制限

| リソース | 既定の制限 | 
| --- | --- | 
| コンテンツ キー ポリシーごとのオプション |30 | 
| アカウントあたりの Media Services キー配信サービスでの各 DRM 型の月毎のライセンス|1,000,000|

### <a name="support-ticket"></a>サポート チケット

固定されていないリソースについては、[サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてクォータの増加を依頼できます。 要求には、目的のクォータの変更、ユース ケース シナリオ、および必要なリージョンに関する詳細情報を含めてください。 <br/>上限を高くするために追加の Azure Media Services アカウントを作成することは**しないでください**。
