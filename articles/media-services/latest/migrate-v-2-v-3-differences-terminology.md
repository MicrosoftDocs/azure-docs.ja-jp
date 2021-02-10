---
title: Media Services v3 の用語とエンティティの変更
description: この記事では、Azure Media Services v2 と v3 の間の用語の違いについて説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: a2348e0578b60c59fd7205037bd42d7bb1e84fae
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98953701"
---
# <a name="terminology-and-entity-changes-between-media-services-v2-and-v3"></a>Media Services v2 と v3 の間の用語とエンティティの変更

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-2.svg)

この記事では、Azure Media Services v2 と v3 の間の用語の違いについて説明します。

## <a name="naming-conventions"></a>名前付け規則

Media Services V3 リソースに適用される名前付け規則をご確認ください。 [BLOB の名前付け](assets-concept.md#naming)についてもご確認ください。

## <a name="terminology-changes"></a>用語の変更

- "*ロケーター*" は "*ストリーミング ロケーター*" と呼ばれるようになりました。
- "*チャネル*" は "*ライブ イベント*" と呼ばれるようになりました。
- "*プログラム*" は "*ライブ出力*" と呼ばれるようになりました。
- "*タスク*" はジョブの一部である *JobOutput* と呼ばれるようになりました。

## <a name="entity-changes"></a>エンティティの変更
| **V2 エンティティ**<!-- row --> | **V3 エンティティ** | **ガイダンス** | **V3 からアクセス可能** | **V3 で更新** |
|--|--|--|--|--|
| `AccessPolicy`<!-- row --> | <!-- empty --> |  エンティティ `AccessPolicies` は V3 に存在しません。 | いいえ | いいえ |
| `Asset`<!-- row --> | `Asset` | <!-- empty --> | はい | Yes |
| `AssetDeliveryPolicy`<!-- row --> | `StreamingPolicy` | <!-- empty --> | はい | いいえ |
| `AssetFile`<!-- row --> | <!-- empty --> |エンティティ `AssetFiles` は V3 に存在しません。 ただし、アップロードしたファイル (ストレージ BLOB) は引き続きファイルと見なされます。<br/><br/> 代わりに Azure Storage API を使用して、コンテナー内の BLOB を列挙します。 ジョブを使用してファイルに変換を適用するには、2 つの方法があります。<br/><br/>ストレージに既にアップロードされたファイル:URI には、ストレージ アカウント内の資産に対して実行されるジョブの資産 ID が含まれます。<br/><br/>変換およびジョブ プロセス中にアップロードされるファイル:資産がストレージに作成され、SAS URL が返され、ファイルがストレージにアップロードされた後、変換がファイルに適用されます。 | いいえ | いいえ |
| `Channel`<!-- row --> | `LiveEvent` | ライブイベントは、v2 API のチャネルを置き換えます。 ほとんどの機能を引き継いでおり、ライブ文字起こし、スタンバイ モード、RTMPS 取り込みのサポートなど、より多くの新機能を備えています。 <br/><br/>[シナリオ ベースのライブ ストリーミングでのライブ イベント](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)に関する記事を参照してください。 | いいえ | いいえ |
| `ContentKey`<!-- row --> | <!-- empty --> | `ContentKeys` はエンティティではなくなり、現在はストリーミング ロケーターのプロパティです。<br/><br/>  V3 では、コンテンツ キー データは `StreamingLocator` (出力暗号化の場合) または資産自体 (クライアント側のストレージ暗号化の場合) に関連付けられています。 | はい | いいえ |
| `ContentKeyAuthorizationPolicy`<!-- row --> | `ContentKeyPolicy` | <!-- empty --> | はい | いいえ |
| `ContentKeyAuthorizationPolicyOption` <!-- row --> | <!-- empty --> |  `ContentKeyPolicyOptions` は `ContentKeyPolicy` に含まれています。 | はい | いいえ |
| `IngestManifest`<!-- row --> | <!-- empty --> | エンティティ `IngestManifests` は V3 に存在しません。 V3 でのファイルのアップロードには、Azure storage API を使用します。 資産が最初に作成された後、ファイルは関連するストレージ コンテナーにアップロードされます。 代わりに使用できる Azure Storage コンテナーにデータを取得するには、さまざまな方法があります。 `JobInputHttp` では、必要に応じて、指定された URL からジョブ入力をダウンロードすることもできます。 | いいえ | いいえ |
| `IngestManifestAsset`<!-- row --> | <!-- empty --> | 代わりに使用できる Azure Storage コンテナーにデータを取得するには、さまざまな方法があります。 `JobInputHttp` では、必要に応じて、指定された URL からジョブ入力をダウンロードすることもできます。 | いいえ | いいえ |
| `IngestManifestFile`<!-- row --> | <!-- empty --> | 代わりに使用できる Azure Storage コンテナーにデータを取得するには、さまざまな方法があります。 `JobInputHttp` では、必要に応じて、指定された URL からジョブ入力をダウンロードすることもできます。 | いいえ | いいえ |
| `Job`<!-- row --> | `Job` | `Job` を作成する前に、`Transform` を作成します。 | いいえ | いいえ |
| `JobTemplate`<!-- row --> | `Transform` | 代わりに`Transform`を使います。 変換はジョブとは別のエンティティであり、再利用可能です。 | いいえ | いいえ |
| `Locator`<!-- row --> | `StreamingLocator` | <!--empty --> | はい | いいえ |
| `MediaProcessor`<!-- row --> | <!-- empty --> | 使用する `MediaProcessor` を名前で検索するのではなく、変換を定義するときに必要なプリセットを使用します。 使用されるプリセットによって、ジョブ システムで使用されるメディア プロセッサが決まります。 [シナリオベースのエンコード](migrate-v-2-v-3-migration-scenario-based-encoding.md)に関する記事のエンコードのトピックを参照してください。 <!--Probably needs a link to its own article so customers know Indexerv1 maps to AudioAnalyzerPreset in basic mode, etc.--> | No | 該当しない (V2 では読み取り専用) |
| `NotificationEndPoint`<!-- row --> | <!--empty --> | V3 の通知は Azure Event Grid によって処理されます。 `NotificationEndpoint` は、受信する通知の種類の構成をカプセル化する Event Grid サブスクリプション登録によって置き換えられました (これは v2 では、ジョブの `JobNotificationSubscription`、タスクの `TaskNotificationSubscription`、テレメトリ `ComponentMonitoringSetting` によって処理されていました)。 ｖ2 テレメトリは、規模がさらに大きい Azure エコシステムの強化に合わせて、Azure Event Grid と Azure Monitor の間で分割されました。 | いいえ | いいえ |
| `Program`<!-- row --> | `LiveOutput` | v3 API では「プログラム」が「ライブ出力」に置き換えられるようになりました。  | いいえ | いいえ |
| `StreamingEndpoint`<!-- row --> | `StreamingEndpoint` | ストリーミング エンドポイントは、基本的には同じままです。 これらは、配信元から直接または CDN を介してライブとオンデマンドの両方のストリーミングで、HLS と DASH のコンテンツの動的なパッケージ化、暗号化、配信のために使用されます。 新機能には、より優れた Azure Monitor の統合とグラフ作成のためのサポートが含まれます。 |  Yes | Yes |
| `Task`<!-- row --> | `JobOutput` | `JobOutput` に置き換えられました (これは API 内の個別のエンティティではなくなりました)。  [シナリオベースのエンコード](migrate-v-2-v-3-migration-scenario-based-encoding.md)に関する記事のエンコードのトピックを参照してください。 | いいえ | いいえ |
| `TaskTemplate`<!-- row --> | `TransformOutput` | `TransformOutput` に置き換えられました (これは API 内の個別のエンティティではなくなりました)。 [シナリオベースのエンコード](migrate-v-2-v-3-migration-scenario-based-encoding.md)に関する記事のエンコードのトピックを参照してください。 | いいえ | いいえ |
| `Inputs`<!-- row --> | `Inputs` | 入力と出力がジョブ レベルになりました。 [シナリオベースのエンコード](migrate-v-2-v-3-migration-scenario-based-encoding.md)に関する記事のエンコードのトピックを参照してください。 | いいえ | いいえ |
| `Outputs`<!-- row --> | `Outputs` | 入力と出力がジョブ レベルになりました。  V3 では、メタデータの形式が XML から JSON に変更されました。  ライブ出力は作成すると開始され、削除されると停止します。 [シナリオベースのエンコード](migrate-v-2-v-3-migration-scenario-based-encoding.md)に関する記事のエンコードのトピックを参照してください。 | いいえ | いいえ |


| **その他の変更点** | **V2**  | **V3** |
|---|---|---|
| **ストレージ** <!--new row --> |||
| 記憶域 <!--new row --> | | V3 SDK が Storage SDK から分離されたため、使用する Storage SDK のバージョンをより詳細に制御し、バージョン管理の問題を回避できるようになりました。                      |
| **[エンコード]** <!--new row --> |||
| エンコード ビットレート <!--new row --> | kbps で測定されるビットレート。例:128 (kbps)| 1 秒あたりのビット数。例:128000 (ビット/秒)|
| エンコード DRM FairPlay <!--new row --> | Media Services V2 では、初期化ベクター (IV) を指定できます。 | Media Services V3 では、FairPlay IV を指定することはできません。|
| Premium エンコーダー <!--new row --> | Premium エンコーダーと従来のインデクサー| [Premium Encoder](https://docs.microsoft.com/azure/media-services/previous/media-services-encode-asset) と従来の [Media Analytics プロセッサ](https://docs.microsoft.com/azure/media-services/previous/legacy-components)(Azure Media Services Indexer 2 プレビュー、Face Redactor など) は V3 を使用してアクセスできません。 Standard エンコーダーへのオーディオ チャネル マッピングのサポートを追加しました。  [Media Services Encoding Swagger ドキュメントの Audio](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01/Encoding.json) を参照してください。  | [シナリオベースのエンコード](migrate-v-2-v-3-migration-scenario-based-encoding.md)に関する記事のエンコードのトピックを参照してください。 |
| **変換およびジョブ** <!--new row -->|||
| ジョブ ベースの処理用 HTTPS <!--new row --> |<!-- empty -->| ファイル ベースのジョブ処理では、入力として HTTPS URL を使用できます。 Azure にコンテンツをあらかじめ格納しておく必要がなく、資産を作成する必要もありません。 |
| ジョブの ARM テンプレート <!--new row --> | ARM テンプレートは V2 には存在しませんでした。 | 変換を使用して、再利用可能な構成を構築し、Azure Resource Manager テンプレートを作成し、複数の顧客またはテナント間の処理の設定を分離することができます。 |
| **ライブ イベント** <!--new row --> |||
| ストリーミング エンドポイント <!--new row --> | ストリーミング エンドポイントは、コンテンツをクライアント プレーヤー アプリケーションや、再配布のためのコンテンツ配信ネットワーク (CDN) に直接配信するストリーミング サービスを表します。 | ストリーミング エンドポイントは、基本的には同じままです。 これらは、配信元から直接または CDN を介してライブとオンデマンドの両方のストリーミングで、HLS と DASH のコンテンツの動的なパッケージ化、暗号化、配信のために使用されます。  新機能には、より優れた Azure Monitor の統合とグラフ作成のためのサポートが含まれます。 |
| ライブ イベント チャネル <!--new row --> | チャネルは、ライブ ストリーミング コンテンツの処理を担います。 チャネルは入力エンドポイントであり、その取り込み URL をライブ トランスコーダーに対して指定します。 チャネルは、ライブ トランスコーダーからライブ入力ストリームを受け取り、1 つまたは複数のストリーミング エンドポイントを介してストリーミングできる状態にします。 また、ストリームはあらかじめプレビューし、確認したうえで処理、配信しますが、チャネルはその際に使用するプレビュー エンドポイント (プレビュー URL) も提供します| ライブイベントは、v2 API のチャネルを置き換えます。 ほとんどの機能を引き継いでおり、ライブ文字起こし、スタンバイ モード、RTMPS 取り込みのサポートなど、より多くの新機能を備えています。 |
| ライブ イベント プログラム <!--new row --> | ライブ ストリームでのセグメントの公開と保存は、プログラムを使用して制御します。 プログラムはチャネルによって管理されます。 チャネルとプログラムの関係は、従来のメディアと似ています。チャネルが絶えずコンテンツのストリームを配信するのに対し、プログラムは、そのチャネル上で決まった時間に生じるイベントです。 プログラムの録画されたコンテンツの保持時間は、`ArchiveWindowLength` プロパティで設定できます。 この値は、最小 5 分から最大 25 時間までの範囲で設定できます。| v3 API では「プログラム」が「ライブ出力」に置き換えられるようになりました。 |
| ライブ イベントの長さ <!--new row --> |<!-- empty -->| Media Services を使用してシングル ビットレートのコントリビューション フィードをマルチ ビットレートの出力ストリームにコード変換すると、ライブ イベントを 24 時間 365 日ストリーミングできます。|
| ライブ イベントの待機時間 <!--new row --> |<!-- empty -->| ライブ イベントでの新しい低遅延ライブ ストリーミング サポート。 |
| ライブ イベント プレビュー <!--new row --> |<!-- empty -->| ライブ イベント プレビューは、ダイナミック パッケージと動的暗号化をサポートしています。 そのため、プレビューだけでなく、DASH および HLS パッケージに対するコンテンツ保護が可能です。 |
| ライブ イベントの RTMPS <!--new row --> |<!-- empty-->| 安定性の向上およびソース エンコーダー サポートの強化による RTMPS サポートの強化。 |
| ライブ イベントの RTMPS のセキュアな取り込み <!--new row --> | | ライブ イベントを作成すると、4 つの取り込み URL を取得します。 4 つの取り込み URL はほとんど同じで、ストリーミング トークン `AppId` は同じですが、ポート番号の部分のみが異なります。 URL のうち 2 つは RTMPS のプライマリとバックアップです。| 
| ライブ イベントの文字起こし <!--new row --> |<!-- empty--> | Azure Media Services は、さまざまなプロトコルでビデオやオーディオを配信し、テキストも配信します。 MPEG-DASH や HLS/CMAF を使用してライブ ストリームを発行し、その後、ビデオやオーディオと一緒に発行するときには、Microsoft のサービスにより、IMSC 1.1 互換 TTML で文字起こしされ、テキストが配信されます。|
| ライブ イベントのスタンバイ モード <!--new row --> | V2 にはスタンバイ モードがありませんでした。 | スタンドバイ モードは、ライブ イベントのホット プールを管理するのに役立つ新しい v3 機能です。 お客様は、ライブイベントを実行中の状態に移行する前に、より低いコストでスタンバイ モードで開始できるようになりました。 これにより、チャネルの開始時間が短縮され、すばやく起動するためにホット プールを運用するコストが削減されます。 |
| ライブ イベントの課金 <!--new row --> | <!-- empty-->| ライブ イベントの課金はライブ チャンネルの測定に基づいています。 |
| ライブ出力 <!--new row --> | プログラムは、作成後に開始する必要がありました。 | ライブ出力は作成すると開始され、削除されると停止します。 |

## <a name="next-steps"></a>次のステップ

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
