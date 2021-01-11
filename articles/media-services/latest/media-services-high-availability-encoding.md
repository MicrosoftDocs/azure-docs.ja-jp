---
title: Media Services とビデオ オン デマンド(VOD) を使用した高可用性
description: この記事では、VOD アプリケーションの高可用性を実現するために使用できる Azure サービスの概要について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: conceptual
ms.custom: ''
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c3aaba6939f9e5e3f5d7c169cd3a199cc93f527d
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89296777"
---
# <a name="high-availability-with-media-services-and-video-on-demand-vod"></a>Media Services とビデオ オン デマンド(VOD) を使用した高可用性

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="high-availability-for-vod"></a>VOD のための高可用性

Azure アーキテクチャ ドキュメントには [Geodes](/azure/architecture/patterns/geodes) という高可用性設計パターンがあります。 これには、スケーラビリティと回復性を提供するために、重複するリソースが異なる地理的リージョンにデプロイされる方法が記載されています。  Azure サービスを使用して、冗長性、正常性の監視、負荷分散、データのバックアップと回復など、高可用性設計のさまざまな考慮事項に対応するアーキテクチャを作成できます。  このようなアーキテクチャの 1 つを以下に示します。ここでは、ソリューションで使用される各サービスの詳細と、個々のサービスを使用して VOD アプリケーション用の高可用性アーキテクチャを作成する方法が記載されています。

### <a name="sample"></a>サンプル

Media Services とビデオ オン デマンド (VOD) を使用した高可用性について理解を深めるために使用できるサンプルがあります。 また、VOD シナリオでのサービスの使用方法についても詳しく説明します。  このサンプルは、現在の形式で実稼働環境で使用することは意図されていません。  サンプル コードと readme を慎重に確認し、特に実稼働アプリケーションに統合する前に[障害モード](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)に関するセクションを確認してください。  ビデオ オン デマンド (VOD) のための高可用性の運用実装では、Content Delivery Network (CDN) 戦略も慎重に確認する必要があります。  [GitHub のコード](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming)を確認してください。

## <a name="overview-of-services"></a>サービスの概要

このサンプル アーキテクチャで使用されるサービスは次のとおりです。

| アイコン | 名前 | 説明 |
| :--: | ---- | ----------- |
|![image](media/media-services-high-availability-encoding/azure-media-services.svg)| Media Services アカウント | **説明:**<br>Media Services アカウントは、Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを行うための出発点です。 これは Azure Storage アカウント リソースに関連付けられています。 このアカウントおよび関連するすべてのストレージは、同じ Azure サブスクリプションに存在する必要があります。<br><br>**VOD での使用:**<br>これらはビデオ資産とオーディオ資産のエンコードと配信に使用するサービスです。  高可用性を実現するには、異なるリージョンにそれぞれ 2 つ以上の Media Services アカウントを設定します。 [Azure Media Services](media-services-overview.md) の詳細を確認してください。 |
|![image](media/media-services-high-availability-encoding/storage-account.svg)| ストレージ アカウント | **説明:**<br>Azure ストレージ アカウントには、すべての Azure Storage データ オブジェクト (BLOB、ファイル、キュー、テーブル、およびディスク) が含まれます。 世界中のどこからでも、HTTP または HTTPS 経由でデータにアクセスできます。<br><br>各リージョンの各 Media Services アカウントには、同じリージョンにストレージ アカウントがあります。<br><br>**VOD での使用:**<br>VOD の処理とストリーミングのための入力データと出力データを格納できます。 [Azure Storage](../../storage/common/storage-introduction.md) の詳細を確認してください。 |
|![image](media/media-services-high-availability-encoding/storage-account-queue.svg)| Azure Storage キュー | **説明:**<br>Azure キュー ストレージは、HTTP または HTTPS を使用した認証された呼び出しを介して世界中のどこからでもアクセスできる大量のメッセージを格納するためのサービスです。<br><br>**VOD での使用:**<br>キューを使用してメッセージを送受信し、異なるモジュール間でアクティビティを調整できます。 このサンプルでは Azure Storage キューを使用していますが、Azure には、ユーザーのニーズをよりよく満たす可能性がある Service Bus や Service Fabric リライアブル キューなどの他の種類のキューが用意されています。 [Azure Queue](../../storage/queues/storage-queues-introduction.md) の詳細を確認してください。 |
|![image](media/media-services-high-availability-encoding/azure-cosmos-db.svg)| Azure Cosmos DB  | **説明:**<br>Azure Cosmos DB は Microsoft のグローバル分散マルチモデル データベース サービスです。これにより、世界中の任意の数の Azure リージョン全体で、スループットとストレージを個別にスケーリングできるようになります。<br><br>**VOD での使用:**<br>テーブルを使用して、ジョブ出力状態レコードを格納し、各 Media Services インスタンスの正常性状態を追跡できます。 Media Services API の各呼び出しの状態を追跡し、記録することもできます。 [Azure Cosmos DB](../../cosmos-db/introduction.md) の詳細を確認してください。  |
|![image](media/media-services-high-availability-encoding/managed-identity.svg)| マネージド ID | **説明:**<br>マネージド ID は、Azure AD で自動マネージド ID を提供する Azure AD の機能です。 これを使用すれば、コードに資格情報を格納しなくても、Key Vault を含む、Azure AD の認証をサポートする様々なサービスに対して認証を行うことができます。<br><br>**VOD での使用:**<br>Azure Functions はマネージド ID を使用して、Key Vault に接続するために Media Services インスタンスに対して認証できます。 [マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認してください。 |
|![image](media/media-services-high-availability-encoding/key-vault.svg)| Key Vault | **説明:**<br>Azure Key Vault を使用すると、トークン、パスワード、証明書、API キー、その他のシークレットを安全に格納し、それらへのアクセスを厳密に制御できます。 これはキー管理ソリューションとしても使用できます。 Azure Key Vault により、データの暗号化に使用される暗号化キーの作成と制御が簡単になります。 これは、Azure および内部の接続されているリソースで使用するためのパブリックおよびプライベートのトランスポート層セキュリティ/Secure Sockets Layer (TLS/SSL) 証明書を容易にプロビジョニング、管理、デプロイできます。 シークレットとキーは、ソフトウェアまたは FIPS 140-2 レベル 2 検証済み HSM で保護できます。<br><br>**VOD での使用:**<br>Key Vault を使用して、アプリケーションのサービス プリンシパルのアクセス ポリシーを設定できます。  ストレージ アカウントに接続文字列を格納するためにこれを使用できます。 Key Vault を使用して、ストレージ アカウントと Cosmos DB に接続文字列を格納します。 また、Key Vault を使用して、クラスター構成全体を格納することもできます。 Media Service インスタンスごとに、サブスクリプション ID、リソース グループ名、アカウント名を格納できます。 詳細については、サンプルでの使用方法を参照してください。 [Key Vault](../../key-vault/general/overview.md) の詳細を確認してください。 |
|![image](media/media-services-high-availability-encoding/function-app.svg)| Azure Functions | **説明:**<br>Azure Functions では、アプリケーションのインフラストラクチャについて気にすることなく、小さなコード ("関数") を実行することができます。 [Azure Functions](../../azure-functions/functions-overview.md) の詳細を確認してください。<br><br>**VOD での使用:**<br>Azure Functions を使用して、VOD アプリケーションのモジュールを保管/ホストすることができます。  VOD アプリケーション用のモジュールには、次のものが含まれます。<br><br>**ジョブ スケジュール モジュール**<br>ジョブ スケジュール モジュールは、新しいジョブを Media Services クラスター (異なるリージョンの 2 つ以上のインスタンス) に送信するためのものです。 各 Media Services インスタンスの正常性状態を追跡し、次の正常なインスタンスに新しいジョブを送信します。<br><br>**ジョブ状態モジュール**<br>ジョブ状態モジュールは、Azure Event Grid サービスからのジョブ出力状態イベントをリッスンします。 イベントをイベント ストアに格納して、残りのモジュールによる Media Services API の呼び出し回数を最小限に抑えます。<br><br>**インスタンス正常性モジュール**<br>このモジュールは送信されたジョブを追跡し、各 Media Services インスタンスの正常性状態を判断します。 完了したジョブ、失敗したジョブ、完了していないジョブを追跡します。<br><br>**プロビジョニング モード**<br>このモジュールは、処理済み資産をプロビジョニングします。 資産データをすべての Media Services インスタンスにコピーし、一部の Media Services インスタンスが使用できない場合でも資産をストリーミングできるように Azure Front Door サービスを設定します。 また、ストリーミング ロケーターも設定します。<br><br>**ジョブ検証モジュール**<br>このモジュールは、送信された各ジョブを追跡し、失敗したジョブを再送信し、ジョブが正常に終了するとジョブ データのクリーンアップを実行します。  |
|![image](media/media-services-high-availability-encoding/application-service.svg)| App Service (およびプラン)  | **説明:**<br>Azure App Service は、Web アプリケーション、REST API、およびモバイル バックエンドをホストするための HTTP ベースのサービスです。 .NET、.NET Core、Java、Ruby、Node.js、PHP、または Python がサポートされています。 アプリケーションの実行とスケーリングは、Windows ベースの環境と Linux ベースの環境の両方で行うことができます。<br><br>**VOD での使用:**<br>各モジュールは、App Service によってホストされます。 [App Service](../../app-service/overview.md) の詳細を確認してください。 |
|![image](media/media-services-high-availability-encoding/azure-front-door.svg)| Azure Front Door | **説明:**<br>Azure Front Door を使用すると、最大限のパフォーマンスのための最適化と高可用性のための即時グローバル フェールオーバーによって、Web トラフィックのグローバル ルーティングを定義、管理、監視することができます。<br><br>**VOD での使用:**<br>Azure Front Door はストリーミング エンドポイントにトラフィックをルーティングするために使用できます。 [Azure Front Door](../../frontdoor/front-door-overview.md) の詳細を確認してください。  |
|![image](media/media-services-high-availability-encoding/event-grid-subscription.svg)| Azure Event Grid | **説明:**<br>イベントベースのアーキテクチャ用に作成された Event Grid には、Storage Blob やリソース グループなどの、Azure サービスから送信されるイベントに対するサポートが組み込まれています。 また、カスタム トピック イベントもサポートされています。 フィルターを使用することで、特定のイベントをさまざまなエンドポイントにルーティングしたり、複数のエンドポイントにマルチキャストしたり、イベントを確実に配信したりできます。 これは、すべてのリージョンの複数の障害ドメインに、および可用性ゾーンをまたいでネイティブに分散させることによって可用性を最大化します。<br><br>**VOD での使用:**<br>Event Grid を使用すると、すべてのアプリケーション イベントを追跡し、ジョブの状態を永続化するためにそれらを保存できます。 [Azure Event Grid](../../event-grid/overview.md) の詳細を確認してください。 |
|![image](media/media-services-high-availability-encoding/application-insights.svg)| Application Insights | **説明:** <br>Application Insights は Azure Monitor の機能であり、開発者や DevOps プロフェッショナル向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 このサービスを使用して、実行中のアプリケーションを監視することができます。 これにより、パフォーマンスの異常が検出されます。また、問題を診断したり、ユーザーがアプリを使用して実行している操作を把握したりするための分析ツールが組み込まれています。 Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。<br><br>**VOD での使用:**<br>すべてのログは、Application Insights に送信できます。 正常に作成されたジョブ メッセージを検索することで、各ジョブが処理したインスタンスを確認することができます。 これには、一意識別子とインスタンス名の情報を含む、送信されたすべてのジョブ メタデータを含めることができます。 [Application Insights](../../azure-monitor/app/app-insights-overview.md) の詳細 |
## <a name="architecture"></a>アーキテクチャ

この概要図は、高可用性と Media Services の利用を開始するために用意されているサンプルのアーキテクチャを示しています。

[ ![ビデオ オン デマンド (VOD) の高レベルのアーキテクチャの図 ](media/media-services-high-availability-encoding/high-availability-architecture.svg) ](media/media-services-high-availability-encoding/high-availability-architecture.svg#lightbox)

## <a name="best-practices"></a>ベスト プラクティス

### <a name="regions"></a>リージョン

* 2 つ (以上) の Azure Media Services アカウントを[作成](https://review.docs.microsoft.com/azure/media-services/latest/create-account-cli-how-to)します。 2 つのアカウントは所属するリージョンが異なっている必要があります。 詳細については、「[Azure Media Services サービスがデプロイされているリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)」を参照してください。
* ジョブを送信する予定のリージョンにメディアをアップロードします。 エンコードを開始する方法の詳細については、「[HTTPS URL からジョブの入力を作成する](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-http-how-to)」または「[ローカル ファイルからジョブの入力を作成する](https://review.docs.microsoft.com/azure/media-services/latest/job-input-from-local-file-how-to)」を参照してください。
* その後、[ジョブ](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)を別のリージョンに再送信する必要がある場合、`JobInputHttp` を使用するか、`Copy-Blob` を使用してソース アセット コンテナーから代替リージョンのアセット コンテナーにデータをコピーします。

### <a name="monitoring"></a>監視

* Azure Event Grid を介して各アカウントで `JobStateChange` メッセージをサブスクライブします。
    * Azure portal または CLI を使用して[イベントに登録する](https://review.docs.microsoft.com/azure/media-services/latest/reacting-to-media-services-events) (Event Grid Management SDK を使用して行うこともできます)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) を使用する (ネイティブで Media Services イベントをサポートしています)。
    * Azure Functions を介して Event Grid イベントを使用することもできます。

    詳細情報:

    * [オーディオ分析サンプル](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)を参照すると、Azure Event Grid メッセージが何らかの理由で遅延した場合のフォールバックを追加するなど、Azure Event Grid でジョブを監視する方法がわかります。
    * [Media Services 用の Azure Event Grid スキーマ](https://review.docs.microsoft.com/azure/media-services/latest/media-services-event-schemas)をご覧ください。

* [ジョブ](https://review.docs.microsoft.com/azure/media-services/latest/transforms-jobs-concept)を作成する場合:
    * 現在使用しているアカウントの一覧からアカウントをランダムに選択します (通常、この一覧には両方のアカウントが含まれますが、問題が検出された場合は、アカウントを 1 つだけ含めることができます)。 一覧が空の場合は、オペレーターが調査できるようにアラートを生成します。
    * 実行中の各ジョブと、使用されているリージョン/アカウントを追跡するレコードを作成します。
* ジョブがスケジュールされた状態に到達したことを示す通知を `JobStateChange` ハンドラーが受け取ったら、ジョブがスケジュールの状態になった時刻と、使用されているリージョンとアカウントを記録します。
* ジョブが処理中の状態に達したという通知を `JobStateChange` ハンドラーが受け取ったら、ジョブのレコードに処理中とマークし、ジョブが処理中の状態になった時刻を記録します。
* `JobStateChange` ハンドラーは、ジョブが最終的な状態 (完了/エラー/キャンセル) に達したという通知を受け取ると、ジョブのレコードを適切にマークします。
* ジョブのレコードを定期的に確認する別のプロセスを用意します
    * 特定のリージョンについて、スケジュールされた状態のジョブのうち、適切な時間内に処理中の状態に進まないものがある場合は、現在使用されているアカウントの一覧からそのリージョンを削除します。 ビジネスの要件によっては、このようなジョブをすぐに取り消して、他のリージョンに再送信することもできます。 また、次の状態に移行するための時間を増やすこともできます。
    * リージョンがアカウントの一覧から削除された場合、一覧に戻す前に回復させるか観察してください。 一定の時間が経過したらアカウントを一覧に戻すか、Azure Media Services に影響を与える可能性のある停止期間がないかオペレーターが Azure の通信を観察することによって、リージョンの正常性は、リージョンの既存ジョブから監視できます (キャンセルされておらず、再送信されていない場合)。

## <a name="next-steps"></a>次のステップ

* [コード サンプル](/samples/browse/?products=azure-media-services)を確認する
