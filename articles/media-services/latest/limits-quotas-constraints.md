---
title: Azure Media Services v3 のクォータと制限 | Microsoft Docs
description: このトピックでは、Azure Media Services v3 のクォータと制限について説明します
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/16/2019
ms.author: juliako
ms.openlocfilehash: 709ed293dbb0550dc1bb43bf1e1e1cc50906cc31
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293442"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Azure Media Services v3 のクォータと制限

この記事では、Azure Media Services v3 のクォータと制限について説明します

| Resource | 既定の制限 | 
| --- | --- | 
| Azure Media Services アカウントあたりの資産 | 1,000,000|
| 動的マニフェスト フィルター|100|
| Job あたりの JobInput | 50 (固定)|
| Job あたりの JobOutput | 20 (固定) |
| Transform にある TransformOutputs | 20 (固定) |
| JobInput あたりのファイル|10 (固定)|
| ファイル サイズ| シナリオによっては、Media Services での処理についてサポートされている最大ファイル サイズに制限があります。 <sup>(1)</sup> |
| Media Services アカウントあたりの Job | 500,000 <sup>(2)</sup> (固定)|
| 変換の一覧表示|応答の改ページ処理 (ページあたり 1,000 変換)|
| ジョブの一覧表示|応答の改ページ処理 (ページあたり 500 ジョブ)|
| Media Services アカウントあたりのライブ イベント数 |5|
| 1 つのサブスクリプション内の Media Services アカウント | 25 (固定) |
| ライブ イベントあたりのライブ出力 |3 <sup>(3)</sup> |
| ライブ出力の最大期間 | 25 時間 |
| ストレージ アカウント | 100<sup>(4)</sup> (固定) |
| Media Services アカウントあたりのストリーミング エンドポイント (停止済みまたは実行中)|2 (固定)|
| ストリーミング ポリシー | 100 <sup>(5)</sup> |
| Media Services アカウントあたりの Transform | 100 (固定)|
| 1 つの資産に同時に関連付けられる一意のストリーミング ロケーター数 | 100<sup>(6)</sup> (固定) |
| コンテンツ キー ポリシーごとのオプション |30 | 
| アカウントあたりの Media Services キー配信サービスでの各 DRM 型の月毎のライセンス|1,000,000|

<sup>1</sup> Azure Blob Storage では現在、1 つの BLOB でサポートされる最大サイズは 5 TB です。 Media Services ではさらに、サービスで使用される VM サイズに基づく別の制限が適用されます。 サイズの上限は、アップロードするファイルのほかに、Media Services 処理 (エンコードまたは分析) の結果として生成されるファイルにも適用されます。 ソース ファイルが 260 GB を超える場合、Job は失敗する可能性があります。 

以下の表では、メディア占有ユニット S1、S2、S3 での制限を示します。 ソース ファイルがこの表に定義されている上限を超える場合、エンコード ジョブは失敗します。 4K 解像度の長時間ソースをエンコードする場合、必要なパフォーマンスを確保するためには、S3 メディア占有ユニットを使用する必要があります。 S3 メディア占有ユニットの上限 260 GB を超える 4K コンテンツがある場合、必要なシナリオ実現に向けた状況の改善策については、amshelp@microsoft.com までお問い合わせください。

|メディア占有ユニットの種類   |最大入力サイズ (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> この数には、キューに置かれた Job、終了した Job、アクティブな Job、および取り消された Job が含まれます。 削除された Job は含まれません。 

レコードの合計数が最大クォータより小さい場合でも、アカウント内の 90 日前より古いすべての Job レコードは自動的に削除されます。 

<sup>3</sup> ライブ出力は作成すると開始され、削除されると停止します。

<sup>4</sup> ストレージ アカウントは、同じ Azure サブスクリプションからのものである必要があります。

<sup>5</sup> カスタム [ストリーミング ポリシー](https://docs.microsoft.com/rest/api/media/streamingpolicies)を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いの StreamingLocator に対してそのセットを再利用する必要があります。 ストリーミング ロケーターごとに新しいストリーミング ポリシーを作成しないでください。

<sup>6</sup> ストリーミング ロケーターは、ユーザーごとのアクセス制御を管理するようには設計されていません。 個々のユーザーに異なるアクセス権限を付与するには、デジタル著作権管理 (DRM) ソリューションを使用します。

## <a name="support-ticket"></a>サポート チケット

固定されていないリソースについては、[サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてクォータの増加を依頼できます。 要求には、目的のクォータの変更、ユース ケース シナリオ、および必要なリージョンに関する詳細情報を含めてください。 <br/>上限を高くするために追加の Azure Media Services アカウントを作成することは**しないでください**。

## <a name="next-steps"></a>次の手順

[概要](media-services-overview.md)
