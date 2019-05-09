---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: b275a86f8fd35c43865fd920d1bfc9994a796a9c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59804465"
---
>[!NOTE]
>固定されないリソースの場合は、サポート チケットを開いてクォータの増加を要求してください。 上限を高くするために追加の Azure Media Services アカウントを作成することはしないでください。

| Resource | 既定の制限 | 
| --- | --- | 
| 1 つのサブスクリプション内の Azure Media Services アカウント | 25 (固定) |
| Media Services アカウントあたりのメディア占有ユニット数 |25 (S1)<br/>10 (S2、S3)<sup>1</sup> | 
| Media Services アカウントあたりの Job | 50,000<sup>2</sup> |
| ジョブあたりのチェーン タスク数 | 30 (固定) |
| Media Services アカウントあたりのアセット数 | 1,000,000|
| タスクあたりのアセット数 | 50 |
| ジョブあたりのアセット数 | 100 |
| 一度に 1 つの資産に関連付けられる一意のロケーター数 | 5<sup>4</sup> |
| Media Services アカウントあたりのライブ チャンネル数 |5|
| チャネルあたりの停止状態のプログラム数 |50|
| チャネルあたりの実行状態のプログラム数  |3|
| Media Services アカウントあたりの、停止または実行しているストリーミング エンドポイント数|2|
| ストリーミング エンドポイントあたりのストリーミング ユニット数  |10 |
| ストレージ アカウント | 1,000<sup>5</sup> (固定) |
| ポリシー | 1,000,000<sup>6</sup> |
| ファイル サイズ| シナリオによっては、Media Services での処理についてサポートされている最大ファイル サイズに制限があります。<sup>7</sup> |

<sup>1</sup> 種類を変更した場合 (S2 から S1 など)、最大予約ユニット数はリセットされます。

<sup>2</sup> この数には、キューに置かれたジョブ、終了したジョブ、アクティブなジョブ、および取り消されたジョブが含まれます。 削除されたジョブは含まれません。 **IJob.Delete** または **DELETE** HTTP 要求を使用して、古いジョブを削除できます。

2017 年 4 月 1 日以降、アカウント内の 90 日前より古いジョブ レコードすべてが、関連付けられているタスク レコードと共に自動的に削除されます。 レコードの合計数が最大クォータより小さい場合でも、自動的な削除が行われます。 ジョブとタスクの情報をアーカイブするには、[Media Services .NET SDK でのアセットの管理](../articles/media-services/previous/media-services-dotnet-manage-entities.md)に関する記事で説明されているコードを使用します。

<sup>3</sup> ジョブ エンティティの一覧を要求すると、要求ごとに最大 1,000 件のジョブが返されます。 送信したすべてのジョブを追跡するには、[OData システム クエリ オプション](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))に関する記事で説明されている top または skip クエリを使用します。

<sup>4</sup> ロケーターはユーザーごとのアクセス制御を管理するようには設計されていません。 個々のユーザーに異なるアクセス権限を付与するには、デジタル著作権管理 (DRM) ソリューションを使用します。 詳しくは、[Azure Media Services でのコンテンツの保護](../articles/media-services/previous/media-services-content-protection-overview.md)に関する記事をご覧ください。

<sup>5</sup> ストレージ アカウントは、同じ Azure サブスクリプションからのものである必要があります。

<sup>6</sup> さまざまな Media Services ポリシーには、1,000,000 ポリシーの制限があります。 たとえば、ロケーター ポリシーや ContentKeyAuthorizationPolicy です。 

>[!NOTE]
> 常に同じ日数およびアクセス許可を使用する場合は、同じポリシー ID を使用します。 説明と例については、[Media Services .NET SDK でのアセットの管理](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)に関する記事をご覧ください。

<sup>7</sup> サービスのメディア プロセッサのいずれかで処理するために Media Services でアセットにコンテンツをアップロードする場合は、サポートされている最大ファイル サイズに注意してください。 アセットには、Media Encoder Standard や Media Encoder Premium Workflow のようなエンコーダー、または Face Detector などの分析エンジンが含まれます。

Azure Blob Storage では現在、1 つの BLOB でサポートされる最大サイズは 5 TB です。 Media Services ではさらに、サービスで使用される VM サイズに基づく別の制限が適用されます。 以下の表では、メディア占有ユニット S1、S2、S3 での制限を示します。 ソース ファイルがこの表に定義されている上限を超える場合、エンコード ジョブは失敗します。 4K 解像度の長時間ソースをエンコードする場合、必要なパフォーマンスを確保するためには、S3 メディア占有ユニットを使用する必要があります。 S3 メディア占有ユニットの上限 260 GB を超える 4K コンテンツがある場合、必要なシナリオ実現に向けた状況の改善策については、amshelp@microsoft.com までお問い合わせください。

| メディア占有ユニットの種類 | 最大入力サイズ (GB)| 
| --- | --- | 
|S1 | 325|
|S2 | 640|
|S3 | 260|
