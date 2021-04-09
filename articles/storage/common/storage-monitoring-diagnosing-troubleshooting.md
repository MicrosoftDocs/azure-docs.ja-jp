---
title: Azure Storage の監視、診断、およびトラブルシューティング | Microsoft Docs
description: ストレージ分析、クライアント側ログ、他のサード パーティのツールなどの機能を使って、Azure Storage 関連の問題を特定、診断、およびトラブルシューティングします。
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 5943a65d61dee4fcf610b8cc45139674362995cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600719"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Microsoft Azure Storage の監視、診断、およびトラブルシューティング
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>概要
クラウド環境でホストされる分散型アプリケーションの問題の診断およびトラブルシューティングは、従来の環境よりも複雑になる可能性があります。 アプリケーションは、PaaS や IaaS インフラストラクチャ、オンプレミス、モバイル デバイス、これらの環境を組み合わせたものにデプロイできます。 一般に、アプリケーションのネットワーク トラフィックは公衆ネットワークと専用ネットワークを経由する可能性があり、アプリケーションでは、Microsoft Azure Storage のテーブル、BLOB、キュー、ファイルのほか、リレーショナル データベースやドキュメント データベースといった他のデータ ストアなどの複数のストレージ技術を使用している可能性があります。

このようなアプリケーションを適切に管理するためには、アプリケーションをプロアクティブに監視し、アプリケーションおよびアプリケーションが依存する技術をあらゆる側面から診断およびトラブルシューティングする方法を理解する必要があります。 Azure Storage サービスのユーザーは、(応答時間が通常より長いなどの) 予期しない動作変化を捉えるために、アプリケーションで使用している Storage サービスを継続して監視し、ログを使用して詳細なデータを収集し、問題を徹底的に分析する必要があります。 監視とログの両方から得られた診断情報を基に、アプリケーションで発生した問題の根本原因を特定できます。 そして、問題をトラブルシューティングし、問題を解決するための適切な手順を決定できます。 Azure Storage は Azure の中核サービスであり、顧客が Azure インフラストラクチャにデプロイするソリューションのほとんどでは、Azure Storage が重要な役割を担っています。 Azure Storage には、クラウド ベース アプリケーションのストレージの問題を簡単に監視、診断、およびトラブルシューティングできる機能が組み込まれています。

* [はじめに]
  * [本書の構成]
* [Storage サービスの監視]
  * [サービス正常性の監視]
  * [容量監視]
  * [可用性監視]
  * [パフォーマンス監視]
* [ストレージ問題の診断]
  * [サービス正常性の問題]
  * [パフォーマンスの問題]
  * [エラーの診断]
  * [Storage エミュレーターの問題]
  * [ストレージ ログ ツール]
  * [ネットワーク ログ ツールの使用]
* [エンド ツー エンド トレース]
  * [ログ データの関連付け]
  * [クライアント要求 ID]
  * [サーバー要求 ID]
  * [タイムスタンプ]
* [トラブルシューティング ガイダンス]
  * [メトリックが示す AverageE2ELatency が高く、AverageServerLatency が低い]
  * [メトリックでは AverageE2ELatency も AverageServerLatency も低いのにクライアントで大きな遅延が発生している]
  * [メトリックが高い AverageServerLatency を示す]
  * [キューのメッセージ配信で予期しない遅延が発生する]
  * [メトリックが PercentThrottlingError の増加を示す]
  * [メトリックが PercentTimeoutError の増加を示す]
  * [メトリックが PercentNetworkError の増加を示す]
  * [クライアントが HTTP 403 (許可されていません) のメッセージを受け取る]
  * [クライアントが HTTP 404 (見つからない) のメッセージを受け取る]
  * [クライアントが HTTP 409 (競合) のメッセージを受け取る]
  * [メトリックの示す PercentSuccess が低い、または分析ログ エントリの中にトランザクション ステータスが ClientOtherErrors の操作がある]
  * [ストレージ使用量の予期しない増加が容量メトリックに示される]
  * [開発またはテストでのストレージ エミュレーターの使用に起因する問題]
  * [Azure SDK for .NET のインストールで問題が発生する]
  * [Storage サービスで別の問題が発生する]
  * [Windows 仮想マシン上の VHD のトラブルシューティング](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)   
  * [Linux 仮想マシン上の VHD のトラブルシューティング](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
  * [Windows での Azure Files に関する問題のトラブルシューティング](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Linux での Azure Files に関する問題のトラブルシューティング](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [付録]
  * [付録 1: Fiddler を使用した HTTP および HTTPS トラフィックのキャプチャ]
  * [付録 2: Wireshark を使用したネットワーク トラフィックのキャプチャ]
  * [付録 4: Excel を使用したメトリックおよびログ データの表示]
  * [付録 5: Application Insights for Azure DevOps を使用した監視]

## <a name="introduction"></a><a name="introduction"></a>はじめに
このガイドでは、Azure Storage Analytics、Azure ストレージ クライアント ライブラリのクライアント側ログ、他のサード パーティのツールなどの機能を使用して、Azure Storage 関連の問題を識別、診断、およびトラブルシューティングする方法を説明します。

![クライアント アプリケーションと Azure ストレージ サービス間の情報の流れを示す図。][1]

このガイドの主な対象読者は、Azure Storage サービスを使用するオンライン サービスの開発者と、そのようなオンライン サービスを管理する IT プロフェッショナルです。 このガイドの目的を次に示します。

* Azure ストレージ アカウントの正常性およびパフォーマンスを維持できるようにする。
* アプリケーションの問題が Azure Storage に関するものかどうかを判断するために必要なプロセスおよびツールを示す。
* Azure Storage に関する問題を解決するための実用的なガイダンスを提供する。

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>本書の構成
セクション「[Storage サービスの監視]」では、Azure Storage Analytics のメトリック (ストレージ メトリック) を使用して Azure Storage サービスの正常性およびパフォーマンスを監視する方法を説明します。

セクション「[ストレージ問題の診断]」では、Azure Storage Analytics のログ (ストレージ ログ) を使用して問題を診断する方法を説明します。 Storage Client Library for .NET や Azure SDK for Java などのクライアント ライブラリの機能を使用して、クライアント側のログを有効にする方法についても説明します。

セクション「[エンド ツー エンド トレース]」では、さまざまなログ ファイルに含まれている情報とメトリック データを関連付ける方法を説明します。

セクション「[トラブルシューティング ガイダンス]」では、発生する可能性のあるストレージ関連の一般的な問題をトラブルシューティングするための手引きを示します。

「[付録]」には、ネットワーク パケット データを分析する Wireshark や Netmon、HTTP/HTTPS メッセージを分析する Fiddler などの他のツールの使用に関する情報を記載しています。

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Storage サービスの監視
Windows のパフォーマンス監視に詳しい人は、Storage メトリックのことを Windows パフォーマンス モニターのカウンターに相当する Azure Storage の機能だと考えることができます。 Storage メトリックでは、サービス可用性、サービスに対する要求の総数、サービスに対する要求の成功率などの、包括的なメトリック (Windows パフォーマンス モニターの用語で言えばカウンター) のセットを利用できます。 使用可能なメトリックの詳細なリストについては、「 [Storage Analytics Metrics のテーブル スキーマ](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)」を参照してください。 Storage サービスでメトリックを収集および集計する間隔は、1 時間または 1 分を指定できます。 メトリックを有効にしてストレージ アカウントを監視する方法の詳細については、 [ストレージ メトリックの有効化とメトリック データの表示](../blobs/monitor-blob-storage.md)に関するページをご覧ください。

[Azure Portal](https://portal.azure.com) に表示する時間単位のメトリックを選ぶことができます。また、時間単位メトリックが特定のしきい値を超えたときに必ず電子メールで管理者に通知するようにルールを構成することもできます。 詳しくは、「[アラート通知を受け取る](../../azure-monitor/alerts/alerts-overview.md)」をご覧ください。

[Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (プレビュー) を確認することをお勧めします。 これは、Azure Storage サービスのパフォーマンス、容量、可用性の統合されたビューが提供されることで、Azure Storage アカウントを包括的に監視できる Azure Monitor の機能です。 何も有効にしたり構成したりする必要はありません。これらのメトリックは、あらかじめ定義されている対話型のグラフやその他の含まれている視覚エフェクトからすぐに表示できます。

Storage サービスは、最大限メトリックを収集しますが、すべてのストレージ操作を記録するわけではありません。

Azure Portal では、ストレージ アカウントの可用性、要求の総数、平均待機時間などのメトリックを表示できます。 可用性が特定のレベルを下回った場合に管理者にアラートを送信する通知ルールもセットアップされています。 このデータからは、調査対象領域として、成功率が 100% より低いテーブル サービスが候補に挙げられます (詳細については、セクション「[メトリックの示す PercentSuccess が低い、または分析ログ エントリの中にトランザクション ステータスが ClientOtherErrors の操作がある]」を参照してください)。

次の方法により、Azure アプリケーションを継続して監視し、アプリケーションが正常であり、予期されるパフォーマンスが実現されていることを確認する必要があります。

* アプリケーションのメトリックの基準値を決定します。こうしておくと、現行データと比較して Azure Storage およびアプリケーションの動作の重大な変化を見つけることができます。 メトリックの基準値はアプリケーションごとに異なることが多いため、アプリケーションのパフォーマンス テストを実行する際に決定する必要があります。
* 分単位のメトリックを記録し、それらを使用して、エラーの数や要求レートが急増するなどの予期しないエラーや異常がないかどうか積極的に監視します。
* 時間単位のメトリックを記録し、それらを使用して、エラーの数や要求レートの平均などの平均値を監視します。
* Event Hub incoming message throughput for a namespace診断ツールを使用して潜在的な問題を調査します。後でセクション「[ストレージ問題の診断]」で説明します。

下の図のグラフは、時間単位メトリックでなされる平均値の算出によってアクティビティの急増が隠れてしまう状況を示しています。 時間単位メトリックには安定した要求レートが示されますが、分単位メトリックから、実際は変動していることが分かります。

![時間単位メトリックでなされる平均値の算出によってアクティビティの急増が隠れてしまう状況を示しているグラフ。][3]

このセクションの残りの部分では、監視する必要があるメトリックとその理由について説明します。

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>サービス正常性の監視
[Azure Portal](https://portal.azure.com) を使うことにより、世界中のすべての Azure リージョンの Storage サービス (およびその他の Azure サービス) の正常性を確認できます。 監視を行うことで、アプリケーションで使用しているリージョンの Storage サービスに制御不能な問題による影響が発生していないかどうか、素早く確認できます。

[Azure Portal](https://portal.azure.com) には、さまざまな Azure サービスに影響を与えるアクシデントについての通知も表示されます。
注:この情報は、以前は [Azure サービス ダッシュボード](https://status.azure.com)上で履歴データと共に入手できました。
Application Insights for Azure DevOps の詳細については、付録「[付録 5: Application Insights for Azure DevOps を使用した監視](#appendix-5)」を参照してください。

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>容量監視
ストレージ メトリックは、一般に保管データの大部分を BLOB が占めるため、BLOB サービスの容量メトリックのみを保管します (現時点では、ストレージ メトリックを使用してテーブルおよびキューの容量を監視することはできません)。 このデータは、Blob service の監視を有効にした場合に **$MetricsCapacityBlob** テーブルに格納されます。 ストレージ メトリックはこのデータを 1 日に 1 回記録します。**RowKey** の値を使用すれば、行に含まれているエンティティがユーザー データに関するものか (値 **data**)、それとも分析データに関するものか (値 **analytics**) 調べることができます。 格納された各エンティティには、ストレージの使用量 (**Capacity**: バイト単位)、現在のコンテナー数 (**ContainerCount**)、およびストレージ アカウントで使用されている BLOB の数 (**ObjectCount**) に関する情報が入っています。 **$MetricsCapacityBlob** テーブルに格納される容量メトリックの詳細については、「[Storage Analytics Metrics のテーブルのスキーマ](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)」をご覧ください。

> [!NOTE]
> ストレージ アカウントの容量制限に近づいていることを示す早期警告として、これらの値を監視する必要があります。 Azure Portal で、ストレージの合計使用量が指定のしきい値を超えた場合または下回った場合に通知するアラート ルールを追加できます。
>
>

BLOB などのさまざまなストレージ オブジェクトのサイズを推定する方法については、ブログ記事「 [Understanding Azure Storage Billing – Bandwidth, Transactions, and Capacity (Azure Storage の課金について - 帯域幅、トランザクション、および容量)](/archive/blogs/patrick_butler_monterde/azure-storage-understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity)」を参照してください。

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>可用性監視
時間単位メトリックまたは分単位メトリックのテーブルの **Availability** 列の値を監視して、ストレージ アカウントの Storage サービスの可用性を監視する必要があります。これらのテーブルは、 **$MetricsHourPrimaryTransactionsBlob**、 **$MetricsHourPrimaryTransactionsTable**、 **$MetricsHourPrimaryTransactionsQueue**、 **$MetricsMinutePrimaryTransactionsBlob**、 **$MetricsMinutePrimaryTransactionsTable**、 **$MetricsMinutePrimaryTransactionsQueue**、 **$MetricsCapacityBlob** です。 **Availability** 列には、その行が表しているサービスまたは API 操作の可用性を示すパーセント値が入っています (**RowKey** から、行に含まれているのがサービス全体のメトリックなのか、それとも特定の API 操作のメトリックなのかがわかります)。

100% より低い値は、一部のストレージ要求が失敗したことを示します。 失敗した理由は、エラーの種類別に要求数を示すメトリック データの他の列 (**ServerTimeoutError** など) を参照して調べることができます。 要求の負荷分散を向上させるためにサービスがパーティションを移動しているときの一時的なサーバー タイムアウトなどの理由で、**Availability** が一時的に 100% を切ることを予期しておく必要があります。また、このような偶発的な状況は、クライアント アプリケーションの再試行ロジックで対処する必要があります。 ストレージ メトリックが **Availability** の計算に含めるトランザクションの種類の一覧については、「[Storage Analytics によって記録される操作やステータス メッセージ](/rest/api/storageservices/Storage-Analytics-Logged-Operations-and-Status-Messages)」をご覧ください。

[Azure Portal](https://portal.azure.com) で、サービスの **Availability** が指定のしきい値を超えた場合または下回った場合に通知するアラート ルールを追加できます。

このガイドの「[トラブルシューティング ガイダンス]」セクションでは、Storage サービスの可用性に関する一般的な問題について説明します。

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>パフォーマンス監視
Storage サービスのパフォーマンスを監視するには、時間単位および分単位のメトリック テーブルにある次のメトリックを使用します。

* **AverageE2ELatency** 列と **AverageServerLatency** 列の値は、ストレージ サービスまたは API 操作タイプが要求の処理に要した時間の平均を示します。 **AverageE2ELatency** は、要求の処理にかかった時間に加えて要求の読み取りと応答の送信にかかった時間を含む、エンドツーエンドの待機時間の測定値です (そのため、要求が Storage サービスに達した後のネットワーク待機時間も含まれます)。一方、**AverageServerLatency** は、処理時間のみの測定値であるため、クライアントとの通信に関するネットワーク待機時間は含まれません。 これらの 2 つの値が大きく異なる場合がありますが、その理由については、このガイド中の後のセクション「[メトリックが示す AverageE2ELatency が高く、AverageServerLatency が低い]」をご覧ください。
* **TotalIngress** と **TotalEgress** 列の値は、Storage サービスまたは特定の API 操作タイプの送受信データの総量 (バイト単位) を示します。
* **TotalRequests** 列の値は、API 操作の Storage サービスが受信した要求の総数を示します。 **TotalRequests** は、Storage サービスが受信した要求の総数です。

一般には、調査が必要な問題が存在することを示すインジケーターとして、これらの値のいずれかに予期しない変化が生じていないか監視します。

[Azure Portal](https://portal.azure.com) で、このサービスのパフォーマンス メトリックのいずれかが指定のしきい値を超えた場合または下回った場合に通知するアラート ルールを追加できます。

このガイドの「[トラブルシューティング ガイダンス]」セクションでは、Storage サービスのパフォーマンスに関する一般的な問題について説明します。

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>ストレージ問題の診断
アプリケーションの問題を認識するようになる状況には次のようなものがあります。

* 大きな障害によってアプリケーションがクラッシュする、またはアプリケーションの実行が停止する。
* 前のセクション「[Storage サービスの監視]」で説明した基準値から、監視しているメトリックが大きく外れる。
* アプリケーションのユーザーから、特定の操作が予期したとおりに完了しなかった、または、特定の機能が正常に使用できないとの報告を受ける。
* アプリケーション内部でエラーが発生し、ログ ファイルに記録される、または、他の何らかの通知方式を介して明らかになる。

一般に、Azure Storage サービスに関する問題は、大まかに次の 4 つのカテゴリに分けられます。

* アプリケーションのパフォーマンスに問題があり、ユーザーから報告されるか、パフォーマンス メトリックの変化から明らかになる。
* 1 つ以上のリージョンの Azure Storage インフラストラクチャに問題がある。
* アプリケーションでエラーが発生し、ユーザーから報告されるか、監視対象のエラー数メトリックのいずれかが増加して明らかになる。
* 開発およびテストでローカルのストレージ エミュレーターを使用しているときに、ストレージ エミュレーターの使用に特に関係する問題が発生する。

次のセクションでは、これらの 4 つのカテゴリのそれぞれの問題を診断およびトラブルシューティングするときに従う必要のある手順を大まかに説明します。 後でこのガイドのセクション「[トラブルシューティング ガイダンス]」で、発生する可能性のある一般的な問題について詳しく説明します。

### <a name="service-health-issues"></a><a name="service-health-issues"></a>サービス正常性の問題
一般に、サービス正常性の問題は制御不能です。 [Azure Portal](https://portal.azure.com) では、Storage サービスを含む Azure サービスに現在発生している問題に関する情報が提供されます。 ストレージ アカウントの作成時に読み取りアクセス地理冗長ストレージを選択していた場合は、プライマリ ロケーションのデータが使用できなくなったときに、アプリケーションはセカンダリ ロケーションの読み取り専用コピーに一時的に切り替えることができます。 セカンダリから読み取るには、アプリケーションが、プライマリとセカンダリのストレージ ロケーションの間で切り替えを実行し、制限された機能モードで読み取り専用データを使用して動作できる必要があります。 Azure ストレージ クライアント ライブラリを使用して、プライマリ ストレージから読み取れなくなった場合にセカンダリ ストレージから読み取れるようにする再試行ポリシーを定義できます。 また、アプリケーションは、セカンダリ ロケーションのデータが実際に一貫性のあるデータかどうかを認識できる必要もあります。 詳細については、[Azure Storage の冗長オプションおよび読み取りアクセス地理冗長ストレージ](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/)に関するブログ記事を参照してください。

### <a name="performance-issues"></a><a name="performance-issues"></a>パフォーマンスの問題
アプリケーションのパフォーマンスは主観的であり、特にユーザーの見方によって変わることがあります。 そのため、パフォーマンスに問題がある可能性のある場所を特定できるように、メトリックの基準値を設けておくことが重要です。 クライアント アプリケーションの観点から見ると、Azure Storage サービスのパフォーマンスはさまざまな要因によって影響を受ける可能性があります。 このような要因には Storage サービス、クライアント、ネットワーク インフラストラクチャに関するものがあります。したがって、パフォーマンス問題の根本原因を特定するための戦略を用意しておくことが重要です。

メトリックを使用してパフォーマンス問題の原因と考えられる場所を特定したら、ログ ファイルを使用して詳細な情報を見つけ、問題をさらに診断してトラブルシューティングすることができます。

後でこのガイドのセクション「[トラブルシューティング ガイダンス]」で、発生する可能性のある一般的なパフォーマンス関連の問題について詳しく説明します。

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>エラーの診断
アプリケーションのユーザーから、クライアント アプリケーションの報告したエラーについて通知されることがあります。 ストレージ メトリックにも、Storage サービスのさまざまな種類のエラーの数が記録されます。たとえば、**NetworkError**、**ClientTimeoutError**、**AuthorizationError** などがあります。 ストレージ メトリックにはエラーの種類ごとの数しか記録されませんが、サーバー側のログ、クライアント側のログ、およびネットワーク ログを調べれば個々の要求に関する詳細を取得できます。 一般に、要求が失敗した理由は、Storage サービスから返される HTTP 状態コードに示されます。

> [!NOTE]
> たとえば、一時的なネットワークの状態によるエラーやアプリケーション エラーなど、断続的なエラーが表示される場合があります。
>
>

次のリソースは、ストレージ関連の状態コードおよびエラー コードの理解に役立ちます。

* [REST API の一般的なエラー コード](/rest/api/storageservices/Common-REST-API-Error-Codes)
* [BLOB サービスのエラー コード](/rest/api/storageservices/Blob-Service-Error-Codes)
* [Queue サービスのエラー コード](/rest/api/storageservices/Queue-Service-Error-Codes)
* [テーブル サービスのエラー コード](/rest/api/storageservices/Table-Service-Error-Codes)
* [File サービスのエラー コード](/rest/api/storageservices/File-Service-Error-Codes)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Storage エミュレーターの問題
Azure SDK には、開発用ワークステーションで実行できるストレージ エミュレーターが含まれています。 このエミュレーターは、Azure ストレージ サービスのほとんどの動作をシミュレートし、Azure サブスクリプションと Azure ストレージ アカウントがなくても Azure ストレージ サービスを使用するアプリケーションを実行できるため、開発やテストで役に立ちます。

このガイドの「[トラブルシューティング ガイダンス]」セクションでは、ストレージ エミュレーターの使用時に発生する一般的な問題について説明します。

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>ストレージ ログ ツール
ストレージ ログによって、Azure ストレージ アカウントのストレージ要求をサーバー側でログに記録できます。 サーバー側のログを有効にしてログ データにアクセスする方法の詳細については、 [ストレージ ログの有効化とログデータへのアクセス](./storage-analytics-logging.md)に関するページをご覧ください。

Storage Client Library for .NET では、アプリケーションで実行されたストレージ操作に関するクライアント側のログ データを収集できます。 詳細については、「 [.NET ストレージ クライアント ライブラリによるクライアント側のログ](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library)」を参照してください。

> [!NOTE]
> (SAS の許可エラーなどの) 状況によっては、サーバー側の ストレージ ログには要求データが見つからないエラーを、ユーザーから報告される可能性があります。 ストレージ クライアント ライブラリのログ機能を使用して問題の原因がクライアント側にあるか調べたり、ネットワーク監視ツールを使用してネットワークを調べたりできます。
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>ネットワーク ログ ツールの使用
クライアントとサーバーの間のトラフィックをキャプチャして、クライアントとサーバーの間で交換されたデータおよび基礎ネットワークの状態に関する詳細な情報を取得できます。 便利なネットワーク ログ ツールには次のようなものがあります。

* [Fiddler](https://www.telerik.com/fiddler) は、無料の Web デバッグ プロキシです。HTTP および HTTPS の要求および応答メッセージのヘッダーおよびペイロード データを調べることができます。 詳細については、「[付録 1: Fiddler を使用した HTTP および HTTPS トラフィックのキャプチャ](#appendix-1)」を参照してください。
* [Microsoft Network Monitor (Netmon)](https://download.cnet.com/s/network-monitor/) および [Wireshark](https://www.wireshark.org/) は、無料のネットワーク プロトコル アナライザーです。さまざまな種類のネットワーク プロトコルの詳細なパケット情報を表示できます。 Wireshark の詳細については、「[付録 2: Wireshark を使用したネットワーク トラフィックのキャプチャ](#appendix-2)」を参照してください。
* クライアント マシンが Azure Storage サービスにネットワーク経由で接続できることを確認するための基本的な接続テストを実行する目的で、クライアント側で標準的な **ping** ツールを使用することはできません。 ただし、[**tcping** ツール](https://www.elifulkerson.com/projects/tcping.php)を使って接続を確認することはできます。

多くの場合、問題の診断には ストレージ ログおよび ストレージ クライアント ライブラリのログ データで十分ですが、状況によっては、これらのネットワーク ログ ツールを使用して取得できる詳細な情報が必要になります。 たとえば、Fiddler を使用して HTTP および HTTPS メッセージを表示し、Storage サービスとの間で送受信されたヘッダーおよびペイロード データを確認して、クライアント アプリケーションがどのようにストレージ操作を再試行したのかを調べることができます。 Wireshark などのパケット レベルで動作するプロトコル アナライザーを使用して TCP データを表示し、失われたパケットや接続問題をトラブルシューティングすることができます。 

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>エンド ツー エンド トレース
各種ログ ファイルを使用したエンド ツー エンド トレースは、潜在的な問題を調査するための有用な手法です。 メトリック データからの日時情報を使用すると、問題のトラブルシューティングに役立つ詳細な情報についてログ ファイルを調べるときにどこから開始するとよいかが分かります。

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>ログ データの関連付け
クライアント アプリケーション、ネットワーク トレース、およびサーバー側ストレージ ログからのログを表示する際、各種ログ ファイルの要求を相関付ける機能が重要になります。 ログ ファイルには、相関 ID として役立つ多数の各種フィールドが含まれます。 クライアント要求 ID は、各種ログのエントリを相関付けるために使う最も役立つフィールドです。 ただし、サーバー要求 ID またはタイムスタンプのどちらかを使うと役立つ場合もあります。 以下のシナリオは、こうしたオプションの詳細を示しています。

### <a name="client-request-id"></a><a name="client-request-id"></a>クライアント要求 ID
ストレージ クライアント ライブラリは、要求ごとに固有のクライアント要求 ID を自動生成します。

* ストレージ クライアント ライブラリが作成するクライアント側のログの場合、クライアント要求 ID は、その要求に関連したすべてのログ エントリの **クライアント要求 ID** フィールドに表示されます。
* Fiddler などによってキャプチャされたネットワーク トレースの場合、クライアント要求 ID は、要求メッセージの **x-ms-client-request-id** HTTP ヘッダー値として表示されます。
* サーバー側の Storage Logging ログの場合、クライアント要求 ID は クライアント要求 ID 列に表示されます。

> [!NOTE]
> 複数の要求が同じクライアント要求 ID を共有する可能性があります。クライアントがこの値を割り当てることができるからです (一方、ストレージ クライアント ライブラリは新しい値を自動的に割り当てます)。 クライアントが再試行を行う際には、すべての試行で同じクライアント要求 ID が共有されます。 クライアントから送信されるバッチの場合、バッチのクライアント要求 ID は 1 つだけです。
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>サーバー要求 ID
Storage サービスにより、サーバー要求 ID が自動生成されます。

* サーバー側の Storage Logging ログの場合、サーバー要求 ID は、 **[要求 ID ヘッダー]** 列に表示されます。
* Fiddler などによってキャプチャされたネットワーク トレースの場合、サーバー要求 ID は、応答メッセージの **x-ms-request-id** HTTP ヘッダー値として表示されます。
* ストレージ クライアント ライブラリが作成するクライアント側のログの場合、サーバー要求 ID は、サーバー応答の詳細が示されているすべてのログ エントリの **[操作テキスト]** 列に表示されます。

> [!NOTE]
> Storage サービスは受け取るすべての要求に対して必ず固有のサーバー要求 ID を割り当て、クライアントが実行するすべての再試行操作およびバッチに含まれるすべての操作において、サーバー要求 ID が固有になるようにします。
>
>

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

次のコード サンプルは、カスタム クライアント要求 ID を使用する方法を示しています。 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_UseCustomRequestID":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

ストレージ クライアント ライブラリがクライアントで **StorageException** をスローする場合、**RequestInformation** プロパティには、**ServiceRequestID** プロパティが含まれる **RequestResult** オブジェクトが入ります。 また、**RequestResult** オブジェクトには **OperationContext** インスタンスからもアクセスできます。

以下のコード サンプルは、カスタムの **ClientRequestId** 値を設定する方法を示しています。この場合、**OperationContext** オブジェクトを、Storage サービスに対する要求に接続します。 また、応答メッセージから **ServerRequestId** 値を取得する方法も示しています。

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

---

### <a name="timestamps"></a><a name="timestamps"></a>タイムスタンプ
またタイムスタンプを使用して、関連するログ エントリを見つけることもできます。ただし、クライアントとサーバー間で存在する可能性があるクロック スキューに注意してください。 サーバー側の項目は、クライアントのタイムスタンプを基準として前後に 15 分ずつずらして一致項目を検索してください。 メトリックが含まれる BLOB 用の BLOB メタデータは、BLOB で格納されているメトリックの時間範囲を示しています。 この時間範囲は、同じ分数または時間で多くのメトリック BLOB が含まれる場合に役立ちます。

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>トラブルシューティング ガイダンス
このセクションは、Azure Storage サービスを使用するときにご使用のアプリケーションで生じる可能性がある一般的な問題のいくつかを診断したりトラブルシューティングしたりするために役立ちます。 以下のリストを使用して、特定の問題に関連する情報を見つけてください。

**トラブルシューティングの決定木**

---
問題は、Storage サービスのいずれかのパフォーマンスと関連がありますか。

* [メトリックが示す AverageE2ELatency が高く、AverageServerLatency が低い]
* [メトリックでは AverageE2ELatency も AverageServerLatency も低いのにクライアントで大きな遅延が発生している]
* [メトリックが高い AverageServerLatency を示す]
* [キューのメッセージ配信で予期しない遅延が発生する]

---
問題は、Storage サービスのいずれかの可用性と関連がありますか。

* [メトリックが PercentThrottlingError の増加を示す]
* [メトリックが PercentTimeoutError の増加を示す]
* [メトリックが PercentNetworkError の増加を示す]

---
 クライアント アプリケーションは、Storage サービスから HTTP 4XX (404 など) の応答を受信しましたか。

* [クライアントが HTTP 403 (許可されていません) のメッセージを受け取る]
* [クライアントが HTTP 404 (見つからない) のメッセージを受け取る]
* [クライアントが HTTP 409 (競合) のメッセージを受け取る]

---
[メトリックの示す PercentSuccess が低い、または分析ログ エントリの中にトランザクション ステータスが ClientOtherErrors の操作がある]

---
[ストレージ使用量の予期しない増加が容量メトリックに示される]

---
[多数の VHD が接続されている Virtual Machines が予期せず再起動する]

---
[開発またはテストでのストレージ エミュレーターの使用に起因する問題]

---
[Azure SDK for .NET のインストールで問題が発生する]

---
[Storage サービスで別の問題が発生する]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>メトリックが示す AverageE2ELatency が高く、AverageServerLatency が低い
[Azure Portal](https://portal.azure.com) 監視ツールに基づいて示されている以下の例の場合、**AverageE2ELatency** が **AverageServerLatency** よりもかなり高くなっています。

![AverageE2ELatency が AverageServerLatency よりもかなり高くなっている例を示す Azure portal からの図。][4]

Storage サービスは、正常な要求に対するメトリック **AverageE2ELatency** のみを計算します。**AverageServerLatency** と違い、クライアントでデータを送信し、Storage サービスから確認応答を受け取るまでにかかる時間が含まれます。 そのため、**AverageE2ELatency** と **AverageServerLatency** の差は、クライアント アプリケーションの応答速度が遅いこと、あるいはネットワークの状態に起因している可能性があります。

> [!NOTE]
> Storage Logging ログ データの個々のストレージ操作に関して、**E2ELatency** と **ServerLatency** を表示することもできます。
>
>

#### <a name="investigating-client-performance-issues"></a>クライアントのパフォーマンス上の問題に関する調査
クライアントの応答が低下する理由として、使用可能な接続またはスレッドの数が制限されていること、または CPU、メモリ、ネットワーク帯域幅などのリソースが不足していることが考えられます。 この問題は、クライアント コードをより効率的に変更する (たとえば、Storage サービスに対する非同期呼び出しを使用する)、または (コアおよびメモリを増加させた) より大きな仮想マシンを使用するように変更すると、解決できる場合があります。

Table サービスおよび Queue サービスの場合、Nagle アルゴリズムも **AverageE2ELatency** が **AverageServerLatency** と比較して高くなる原因となる可能性があります。詳しくは、「[Nagle's Algorithm is Not Friendly towards Small Requests](/archive/blogs/windowsazurestorage/nagles-algorithm-is-not-friendly-towards-small-requests)」(小さな要求に不親切な Nagle アルゴリズム) の投稿をご覧ください。 Nagle アルゴリズムは、**System.Net** 名前空間で **ServicePointManager** クラスを使用するとコード内で無効にすることができます。 この操作は、既に開かれている接続に対しては影響を及ぼさないため、アプリケーションの中で Table サービスまたは Queue サービスを呼び出す前に実行する必要があります。 以下の例は、worker ロールの **Application_Start** メソッドに関係する部分です。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_DisableNagle":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

---

クライアント側のログを調べて、クライアント アプリケーションが送信している要求数を確認したり、CPU、.NET ガベージ コレクション、ネットワーク使用率、メモリなどの一般的な .NET 関連のパフォーマンス ボトルネックがクライアントに存在しないかどうかを確かめたりしてください。 .NET クライアント アプリケーションのトラブルシューティングの開始点として、「 [Debugging, Tracing, and Profiling (デバッグ、トレース、およびプロファイリング)](/dotnet/framework/debug-trace-profile/)」を参照してください。

#### <a name="investigating-network-latency-issues"></a>ネットワーク待ち時間に関する問題の調査
一般的に、エンド ツー エンドの待ち時間が長くなるのは、ネットワークの一時的な状態に起因します。 Wireshark などのツールを使用して、ドロップされたパケットなど一時的および永続的なネットワークの問題を調査できます。

Wireshark を使用したネットワークの問題のトラブルシューティングの詳細については、「[付録 2: Wireshark を使用したネットワーク トラフィックのキャプチャ]」を参照してください。

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>メトリックでは AverageE2ELatency も AverageServerLatency も低いのにクライアントで大きな遅延が発生している
このシナリオの場合、最も可能性が高い原因は、Storage サービスに到達するストレージ要求の遅延です。 クライアントの要求が BLOB サービスにうまく送られない原因を調査する必要があります。

要求送信でクライアントに遅延が生じる理由の 1 つとして、使用可能な接続またはスレッドの数が制限されていることが考えられます。

クライアントが複数回再試行したかどうかを確認し、試行している場合は、その理由を調査する必要があります。 クライアントが複数回再試行しているかどうかを確認するには、以下を実行できます。

* Storage Analytics のログを調べます。 再試行が複数回発生している場合は、クライアント要求 ID が同じでサーバー要求 ID が異なる複数の操作があります。
* クライアントのログを調べます。 詳細ログに、再試行が発生したことが示されます。
* コードをデバッグし、要求に関連付けられた **OperationContext** オブジェクトのプロパティをチェックします。 操作が再試行されている場合、 **RequestResults** プロパティに複数の重複しないサーバー要求 ID が含まれます。 各要求の開始時刻と終了時刻をチェックすることもできます。 詳細については、「 [サーバー要求 ID]」セクションのコード サンプルを参照してください。

クライアントに問題がない場合、パケット損失などの考えられるネットワーク問題について調査してください。 Wireshark などのツールを使用して、ネットワーク問題を調査できます。

Wireshark を使用したネットワークの問題のトラブルシューティングの詳細については、「[付録 2: Wireshark を使用したネットワーク トラフィックのキャプチャ]」を参照してください。

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>メトリックが高い AverageServerLatency を示す
BLOB ダウンロード要求の **AverageServerLatency** が高い場合、Storage Logging ログを使用して、同じ BLOB (または BLOB セット) に対して反復されている要求がないかどうかを確かめます。 BLOB アップロード要求に関しては、クライアントが使用しているブロック サイズ (たとえば、サイズが 64 K を下回るブロックの場合、読み取りも 64 K 未満のチャンクでない限りはオーバーヘッドが生じる可能性があります)、および同じ BLOB に対して複数のクライアントがブロックを同時にアップロードしていないかどうかを調べてください。 要求数が急増したことを示す毎分のメトリックがないかどうかも調べる必要があります。このように急増すると、毎秒のスケーラビリティ ターゲットを超えることになります。「[メトリックが PercentTimeoutError の増加を示す]」もご覧ください。

同一の BLOB または BLOB セットに対して反復された要求が存在する場合に BLOB ダウンロード要求の **AverageServerLatency** が高いことが分かるときには、Azure キャッシュまたは Azure Content Delivery Network (CDN) を使用してこうした BLOB をキャッシュすることを考慮してください。 アップロード要求に関しては、ブロック サイズを大きくすることによってスループットを改善できます。 テーブルに対するクエリの場合、同じクエリ操作を実行し、データが頻繁に変更されないクライアントにクライアント側のキャッシュを実装することもできます。

また **AverageServerLatency** 値が高いという症状は、テーブルやクエリがうまく設計されていないために、スキャン操作が生じたり、前後にアンチ パターンが存在したりする場合に起こり得ます。 詳しくは、『[メトリックが PercentThrottlingError の増加を示す]』をご覧ください。

> [!NOTE]
> 総合的なパフォーマンス チェックリストについては、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../blobs/storage-performance-checklist.md)」を参照してください。
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>キューのメッセージ配信で予期しない遅延が発生する
アプリケーションがメッセージをキューに追加した時刻とキューからの読み取りが可能になる時刻の間に遅延が生じる場合、以下の手順で問題を診断する必要があります。

* アプリケーションにより、メッセージがキューに正常に追加されていることを確認します。 正常に完了する前にアプリケーションが **AddMessage** メソッドを再試行していないことを確かめてください。 ストレージ クライアント ライブラリ ログには、ストレージ操作の再試行の反復が表示されます。
* キューにメッセージを追加した worker ロールと、処理に遅延があるかどうかを表示するキューからのメッセージを読み取る worker ロール間でクロック スキューがないことを検証します。
* キューからのメッセージを読み取る worker ロールに障害がないことを確かめます。 キュー クライアントが **GetMessage** メソッドを呼び出した後、受信確認で応答できないと、メッセージは、**invisibilityTimeout** 期間の有効期限が切れるまで、キューで非表示のままになります。 有効期限が切れた時点で、メッセージは再び処理可能になります。
* キューの長さが時間の経過とともに大きくなっているかどうかを確認してください。 他のワーカーがキューに配置したすべてのメッセージを処理するのに十分な数のワーカーを使用できない場合に、大きくなる可能性があります。 また、要求の削除が失敗したかどうかを示すメトリック、およびメッセージのデキュー カウントを示すメトリックを調べてください。こうしたメトリックは、メッセージを削除する試行が何度も繰り返して失敗したことを示す場合があります。
* 通常よりも長期間に渡り、予想された **E2ELatency** 値および **ServerLatency** 値よりも高くなっているキュー操作がないかどうか、Storage Logging ログを調べます。

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>メトリックが PercentThrottlingError の増加を示す
Storage サービスのスケーラビリティ ターゲットを超えると、調整エラーが生じます。 Storage サービスは、1 つのクライアントやテナントがサービスを占有してしまうことがないよう、調整を行います。 詳細については、「[Standard Storage アカウントのスケーラビリティとパフォーマンスのターゲット](scalability-targets-standard-account.md)」を参照し、ストレージ アカウントのスケーラビリティ ターゲットと、ストレージ アカウントのパーティション用のパフォーマンス ターゲットの詳細を確認してください。

**PercentThrottlingError** メトリックが、調整エラーによって失敗した要求の割合が増加していることを示す場合、以下の 2 つのシナリオのいずれかについて調査する必要があります。

* [PercentThrottlingError の一時的増加]
* [PercentThrottlingError エラーの永続的増加]

**PercentThrottlingError** の増加は、多くの場合、ストレージ要求数が増加するのと同時に、またはアプリケーションの最初のロード テストのときに生じます。 また、ストレージ操作により、「503 サーバーはビジー状態」または「500 操作タイムアウト」HTTP ステータス メッセージがクライアントで示されることもあります。

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>PercentThrottlingError の一時的増加
アプリケーションにおいてアクティビティ量が多い時期と一致して **PercentThrottlingError** 値が急増している場合は、クライアントの再試行に備えて、指数 (非線形) バックオフ戦略を実装します。 バックオフの再試行により、パーティションにおける即座の負荷が減少し、アプリケーションがトラフィックの急増を平滑化しやすくなります。 ストレージ クライアント ライブラリを使って再試行ポリシーを実装する方法の詳細については、「[Microsoft.Azure.Storage.RetryPolicies 名前空間](/dotnet/api/microsoft.azure.storage.retrypolicies)」を参照してください。

> [!NOTE]
> アプリケーションのアクティビティ量が多い時期と一致せずに、**PercentThrottlingError** 値が急増することもあります。この原因として可能性が高いのは、Storage サービスが負荷分散を改善するためにパーティションを移動している場合です。
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>PercentThrottlingError エラーの永続的増加
**PercentThrottlingError** 値が継続的に高くなるのが、トランザクション量がずっと増加しているという状況の後に生じる場合、またはアプリケーションで最初のロード テストを実行するときに生じる場合には、アプリケーションがストレージ パーティションをどのように使用しているか、およびストレージ アカウントのスケーラビリティ ターゲットに近づいているかを評価する必要があります。 たとえば、(単一パーティションとしてカウントされる) キューで調整エラーが生じる場合、トランザクションを複数パーティションに分散するためにキューを追加することを考慮する必要があります。 テーブルで調整エラーが生じる場合、より広い範囲のパーティション キー値を使用して複数のパーティションにトランザクションを分散させるため、異なるパーティション スキームを使用することを考慮する必要があります。 この問題が生じる一般的な原因の 1 つは、日付をパーティション キーとして選択し、特定の日のすべてのデータを 1 つのパーティションに書き込むという、前後にあるアンチ パターンです。この場合、ロード中に、書き込みボトルネックとなるおそれがあります。 別のパーティション設計を考慮するか、Blob Storage を使用する方がよいソリューションとなるかどうかを考えてください。 また、トラフィックの急増が原因となって調整が生じているのか確認し、要求パターンを平滑化する方法を調べてください。

トランザクションを複数のパーティションに分散する場合であっても、ストレージ アカウントに設定されているスケーラビリティ限界に注意してください。 たとえば、10 個のキューを使用し、それぞれで毎秒最大で 2,000 メッセージ (各メッセージは 1 KB) を処理した場合、ストレージ アカウントの 1 秒あたり 20,000 メッセージという全体の限界に達します。 1 秒あたり 20,000 を超えるエンティティを処理する必要がある場合、複数のストレージ アカウントの使用を考慮しなければなりません。 また、要求とエンティティのサイズは、Storage サービスがクライアントを調整する時期に影響を与えることも考えておく必要があります。つまり、要求とエンティティが大きくなると、より早く調整される可能性があるのです。

クエリの設計が非効率的な場合も、テーブル パーティションのスケーラビリティ限界に達する原因となる可能性があります。 たとえば、1 つのパーティションのエンティティの 1% だけを選択するフィルターが指定されたクエリが、パーティション内のすべてのエンティティをスキャンする場合、各エンティティにアクセスする必要があります。 エンティティを読み取る操作それぞれが、パーティション内のトランザクション数合計に含まれるので、すぐにスケーラビリティ ターゲットに達してしまいます。

> [!NOTE]
> パフォーマンス テストを実行すると、アプリケーションにおける非効率的なクエリ設計が明らかになります。
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>メトリックが PercentTimeoutError の増加を示す
メトリックが、いずれかの Storage サービスの **PercentTimeoutError** が増加していることを示しています。 同時に、クライアントは、ストレージ操作により「500 操作タイムアウト」HTTP ステータス メッセージを大量に受け取ります。

> [!NOTE]
> パーティションを新しいサーバーに移動するときに、Storage サービスの負荷分散要求としてタイムアウト エラーが一時的に表示される場合もあります。
>
>

**PercentTimeoutError** メトリックは、**ClientTimeoutError**、**AnonymousClientTimeoutError**、**SASClientTimeoutError**、**ServerTimeoutError**、**AnonymousServerTimeoutError**、**SASServerTimeoutError** の各メトリックを統合したメトリックです。

サーバー タイムアウトは、サーバー上のエラーが原因で生じます。 クライアント タイムアウトは、サーバー上の操作がクライアントによって指定されたタイムアウトを超えたことが原因です。たとえば、ストレージ クライアント ライブラリを使用するクライアントは、**QueueRequestOptions** クラスの **ServerTimeout** プロパティを使用して操作のタイムアウトを設定できます。

サーバー タイムアウトは、Storage サービスに問題があることを示します。この場合、さらに調査が必要です。 サービスのスケーラビリティ限界に達したかどうか示すメトリック、および問題の原因となる可能性があるトラフィックの急増を示すメトリックを使用できます。 問題が断続的な場合、サービスにおける負荷分散アクティビティが原因となっている可能性があります。 問題が永続的で、サービスのスケーラビリティ限界にアプリケーションが達したことが原因ではない場合には、サポートに問題を知らせてください。 クライアントのタイムアウトの場合、クライアントでタイムアウト値が適切に設定されているかどうかを判断し、クライアントで設定されているタイムアウト値を変更するか、Storage サービスにおける操作のパフォーマンスを改善する方法を調べてください。改善策としては、たとえば、テーブル クエリを最適化したり、メッセージのサイズを減らしたりできます。

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>メトリックが PercentNetworkError の増加を示す
メトリックが、いずれかの Storage サービスの **PercentNetworkError** が増加していることを示しています。 **PercentNetworkError** メトリックは、**NetworkError**、**AnonymousNetworkError**、**SASNetworkError** の各メトリックの集計です。 このエラーは、クライアントがストレージ要求を出す際に Storage サービスによりネットワーク エラーが検出されると生じます。

多くの場合、このエラーの原因は、Storage サービスでタイムアウト期間が切れる前にクライアントが切断することです。 クライアントのコードを調べて、Storage サービスからクライアントが切断した理由とタイミングを把握してください。 また、Wireshark または Tcping を使用して、クライアントからネットワーク接続の問題を調査できます。 こうしたツールについては、 [付録]で取り上げられています。

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>クライアントが HTTP 403 (許可されていません) のメッセージを受け取る
クライアント アプリケーションが HTTP 403 (許可されていません) エラーをスローする場合、可能性の高い原因は、クライアントがストレージ要求を送信するときに期限切れの Shared Access Signature (SAS) を使用していることです (原因の他の可能性としては、クロック スキュー、無効なキー、空のヘッダーなどがあります)。 期限切れの SAS キーが原因の場合、サーバー側の Storage Logging ログ データのエントリが表示されません。 以下の表に、この問題が生じたときにストレージ クライアント ライブラリによって生成されるクライアント側のログのサンプルを示します。

| source | 詳細度 | 詳細度 | クライアント要求 ID | [操作テキスト] |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |場所 Primary、場所モード PrimaryOnly で操作を開始しています。 |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |[https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr=c&amp;si=mypolicy&amp;sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&amp;api-version=2014-02-14](<https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests#Synchronous_request>) に同期要求を開始しています。 |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |応答を待機しています。 |
| Microsoft.Azure.Storage |警告 |2 |85d077ab -… |応答の待機中に例外がスローされました。リモート サーバーがエラー(403) 禁止。 |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |応答を受け取りました。 状態コード = 403、要求 ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d、Content-MD5 = 、ETag = . |
| Microsoft.Azure.Storage |警告 |2 |85d077ab -… |操作中に例外がスローされました。リモート サーバーがエラー「(403) 許可されていません」を返しました。 |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |操作を再試行する必要があるかどうかを検査しています。 再試行回数 = 0、HTTP 状態コード = 403、例外 = リモート サーバーがエラー「(403) 許可されていません」を返しました。 |
| Microsoft.Azure.Storage |Information |3 |85d077ab -… |次の場所が、場所モードに基づいてプライマリに設定されています。 |
| Microsoft.Azure.Storage |エラー |1 |85d077ab -… |再試行ポリシーは再試行を許可しませんでした。 失敗しました。リモート サーバーがエラー(403) 禁止。 |

このシナリオでは、クライアントが SAS トークンをサーバーに送信する前にトークンが期限切れになった理由を調査しなければなりません。

* 一般に、クライアントがすぐに使用できるように SAS を作成する場合には開始時刻は設定しないでください。 現行時間を使用して SAS を生成するホストと、Storage サービスのクロックに小さな差異が存在すると、有効になっていない SAS を Storage サービスが受け取る可能性があります。
* SAS に非常に短い有効期限は設定しないでください。 やはり、SAS を生成するホストと Storage サービス間のクロックに小さい差異があると、予想されていたよりも明らかに早く SAS の有効期限が切れる恐れがあります。
* SAS キーのバージョン パラメーター (例: **sv=2015-04-05**) が、使用しているストレージ クライアント ライブラリのバージョンと一致するようにします。 常に最新バージョンの [ストレージ クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage/)を使用することをお勧めします。
* ストレージ アクセス キーを再生成すると、既存の SAS トークンが無効になる可能性があります。 この状況は、SAS トークンを生成する際にクライアント アプリケーションがキャッシュする有効期限を長く設定していると、問題が生じることがあります。

SAS トークンを生成するためのストレージ クライアント ライブラリを使用すると、有効なトークンの作成が容易です。 ただし、Storage REST API を使用し、手動で SAS トークンを構成している場合は、 [Shared Access Signature によるアクセスの委任](/rest/api/storageservices/delegate-access-with-shared-access-signature)に関するトピックをご覧ください。

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>クライアントが HTTP 404 (見つからない) のメッセージを受け取る
クライアント アプリケーションが HTTP 404 (未検出) メッセージをサーバーから受け取った場合は、クライアントが使用しようとしていたオブジェクト (エンティティ、テーブル、BLOB、コンテナー、キューなど) が Storage サービス内に存在しないことを意味します。 これには以下のようなさまざまな理由が考えられます。

* [クライアントまたは別のプロセスがそのオブジェクトを以前に削除した]
* [共有アクセス署名 (SAS) 認証の問題]
* [クライアント側の JavaScript コードにオブジェクトへのアクセス許可がない]
* [ネットワーク エラー]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>クライアントまたは別のプロセスがそのオブジェクトを以前に削除した
クライアントが Storage サービスにあるデータの読み取り、更新、または削除を行おうとした場合であれば、通常、サーバー側のログで、問題のオブジェクトを Storage サービスから削除した過去の操作を簡単に特定できます。 多くの場合、ログ データに別のユーザーまたはプロセスがオブジェクトを削除したことが示されています。 ストレージ ログのサーバー側のログでは、operation-type 列および requested-object-key 列からクライアントがオブジェクトを削除したことが分かります。

クライアントがオブジェクトを挿入しようとした場合であれば、新規オブジェクトを作成するのに HTTP 404 (未検出) 応答が返される理由がすぐにはわからない可能性があります。 しかし、クライアントが BLOB を作成する場合は BLOB コンテナーを検出できなければなりませんし、メッセージを作成する場合はキューを検出できなければなりません。また、行を追加する場合はテーブルを検出できなければなりません。

ストレージ クライアント ライブラリのクライアント側ログを使用して、クライアントが Storage サービスに特定の要求を送ったときの状況を詳しく調べることができます。

ストレージ クライアント ライブラリによって生成された以下のクライアント側のログには、作成する BLOB 用のコンテナーをクライアントが検出できないという問題が示されています。 このログには、以下のストレージ操作の詳細が示されています。

| 要求 ID | 操作 |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** メソッド。 この操作には、コンテナーの存在をチェックする **HEAD** 要求が含まれていることに注意してください。 |
| e2d06d78… |**CreateIfNotExists** 。 この操作には、コンテナーの存在をチェックする **HEAD** 要求が含まれていることに注意してください。 ここで、 **HEAD** から 404 メッセージが返されますが、処理を続行します。 |
| de8b1c3c-... |**UploadFromStream** メソッド。 ここで、 **PUT** 要求が 404 メッセージで失敗しています。 |

ログ エントリ:

| 要求 ID | [操作テキスト] |
| --- | --- |
| 07b26a5d-... |[https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt](`https://domemaildist.blob.core.windows.net/azuremmblobcontainer`) に同期要求を開始しています。 |
| 07b26a5d-... |StringToSign = HEAD............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |応答を待機しています。 |
| 07b26a5d-... |応答を受け取りました。 状態コード = 200、要求 ID = eeead849-...Content-MD5 = 、ETag = &quot;0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |応答ヘッダーは正常に処理されました。残りの操作を処理しています。 |
| 07b26a5d-... |応答の本文をダウンロードしています。 |
| 07b26a5d-... |操作は正常に完了しました。 |
| 07b26a5d-... |[https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt](`https://domemaildist.blob.core.windows.net/azuremmblobcontainer`) に同期要求を開始しています。 |
| 07b26a5d-... |StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12    GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| 07b26a5d-... |応答を待機しています。 |
| 07b26a5d-... |応答を受け取りました。 状態コード = 202、要求 ID = 6ab2a4cf-...、Content-MD5 = ETag = 。 |
| 07b26a5d-... |応答ヘッダーは正常に処理されました。残りの操作を処理しています。 |
| 07b26a5d-... |応答の本文をダウンロードしています。 |
| 07b26a5d-... |操作は正常に完了しました。 |
| e2d06d78-... |[https://domemaildist.blob.core.windows.net/azuremmblobcontainer](`https://domemaildist.blob.core.windows.net/azuremmblobcontainer`) に非同期要求を開始しています。</td> |
| e2d06d78-... |StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |応答を待機しています。 |
| de8b1c3c-... |[https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt](`https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt`) に同期要求を開始しています。 |
| de8b1c3c-... |StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |要求データを書き込む準備をしています。 |
| e2d06d78-... |応答の待機中に例外がスローされました。リモート サーバーがエラー「(404) 見つかりません」を返しました。 |
| e2d06d78-... |応答を受け取りました。 状態コード = 404、要求 ID = 353ae3bc-...、Content-MD5 =、ETag = 。 |
| e2d06d78-... |応答ヘッダーは正常に処理されました。残りの操作を処理しています。 |
| e2d06d78-... |応答の本文をダウンロードしています。 |
| e2d06d78-... |操作は正常に完了しました。 |
| e2d06d78-... |[https://domemaildist.blob.core.windows.net/azuremmblobcontainer](`https://domemaildist.blob.core.windows.net/azuremmblobcontainer`) に非同期要求を開始しています。 |
| e2d06d78-... |StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container. |
| e2d06d78-... |応答を待機しています。 |
| de8b1c3c-... |要求データを待機しています。 |
| de8b1c3c-... |応答を待機しています。 |
| e2d06d78-... |応答の待機中に例外がスローされました。リモート サーバーがエラー「(409) 競合」を返しました。 |
| e2d06d78-... |応答を受け取りました。 状態コード = 409、要求 ID = c27da20e-...、Content-MD5 = 、ETag = 。 |
| e2d06d78-... |エラー応答の本文をダウンロードしています。 |
| de8b1c3c-... |応答の待機中に例外がスローされました。リモート サーバーがエラー「(404) 見つかりません」を返しました。 |
| de8b1c3c-... |応答を受け取りました。 状態コード = 404、要求 ID = 0eaeab3e-...、Content-MD5 = 、ETag = 。 |
| de8b1c3c-... |操作中に例外がスローされました。リモート サーバーがエラー「(404) 見つかりません」を返しました。 |
| de8b1c3c-... |再試行ポリシーは再試行を許可しませんでした。 失敗しました。リモート サーバーがエラー「(404) 見つかりません」を返しました。 |
| e2d06d78-... |再試行ポリシーは再試行を許可しませんでした。 失敗しました。リモート サーバーがエラー「(409) 競合」を返しました。 |

この例では、クライアントが **CreateIfNotExists** メソッドからの要求 (要求 ID e2d06d78…) と **UploadFromStream** メソッドからの要求 (de8b1c3c-...) を交互に処理していることがログに示されています。このインターリーブは、クライアント アプリケーションがこれらのメソッドを非同期的に呼び出しているために生じます。 必ず、コンテナーを作成してからそのコンテナーの BLOB にデータをアップロードするように、クライアントの非同期コードを変更してください。 すべてのコンテナーをあらかじめ作成しておくことをお勧めします。

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>共有アクセス署名 (SAS) 認証の問題
クライアント アプリケーションが、操作のために必要なアクセス許可が含まれていない SAS キーを使用しようとすると、Storage サービスは HTTP 404 (未検出) メッセージをクライアントに返します。 同時に、メトリックの **SASAuthorizationError** に 0 以外の値が示されます。

以下の表に、ストレージ ログのログ ファイルのサーバー側ログ メッセージの例を示します。

| 名前 | 値 |
| --- | --- |
| 要求の開始時刻 | 2014-05-30T06:17:48.4473697Z |
| 操作の種類     | GetBlobProperties            |
| 要求の状態     | SASAuthorizationError        |
| HTTP 状態コード   | 404                            |
| 認証の種類| SAS                          |
| サービスの種類       | BLOB                         |
| 要求 URL         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| 要求 ID ヘッダー  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| クライアント要求 ID  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


クライアント アプリケーションが、アクセス許可を付与されていない操作を実行しようとした理由を調べてください。

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>クライアント側の JavaScript コードにオブジェクトへのアクセス許可がない
JavaScript クライアントを使用していて Storage サービスから HTTP 404 メッセージが返された場合は、以下の JavaScript エラーをブラウザーで確認する必要があります。

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> クライアント側の JavaScript の問題をトラブルシューティングするときには、Internet Explorer の F12 開発者ツールを使用して、ブラウザーと Storage サービスの間で交換されたメッセージをトレースできます。
>
>

これらのエラーは、Web ページがそのページのドメインとは異なるドメインの API を呼び出さないようにする [Same Origin Policy](https://www.w3.org/Security/wiki/Same_Origin_Policy) セキュリティ制限を Web ブラウザーで実装しているために発生します。

この JavaScript の問題を回避するには、クライアントがアクセスする Storage サービスにクロス オリジン リソース共有 (CORS) を構成します。 詳細については、 [Azure Storage サービスでのクロス オリジン リソース共有 (CORS) のサポート](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services)に関するページをご覧ください。

以下のコード サンプルは、Contoso ドメインで実行される JavaScript に対して Blob Storage サービスの BLOB へのアクセスを許可する BLOB サービスを構成する方法を示しています。

# <a name="net-v12"></a>[.NET v12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_ConfigureCORS":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnet11)

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

---

#### <a name="network-failure"></a><a name="network-failure"></a>ネットワーク エラー
状況によっては、ネットワーク パケットの紛失が原因で、Storage サービスが HTTP 404 メッセージをクライアントに返すことがあります。 たとえば、クライアント アプリケーションがエンティティを Table サービスから削除するときに、クライアントが、Table サービスからの "HTTP 404 (未検出)" 状態メッセージを報告するストレージ例外をスローすることがあります。 Table Storage サービスのテーブルを調べると、要求どおりにサービスがエンティティを削除したことがわかります。

クライアントにおける例外の詳細には、Table Service によって割り当てられた要求 ID (7e84f12d…) が含まれます。この情報を使用し、ログ ファイルの **request-id-header** 列で検索することによりサーバー側のストレージ ログにある要求の詳細を検索できます。 メトリックを使用して、このようなエラーが発生した時刻を特定し、エラーがメトリックに記録された時刻に基づいてログ ファイルを検索することもできます。 このログ エントリには、状態メッセージ "HTTP (404) Client Other Error" を出して削除が失敗したことが示されます。 同じログ エントリの **client-request-id** 列には、クライアントによって生成された要求 ID (813ea74f…) も含まれています。

また、サーバー側のログには、同じエンティティに対する同じクライアントの成功した削除操作を示す、同じ **client-request-id** 値 (813ea74f…) のエントリがもう 1 つ含まれています。 この成功した削除操作は、失敗した削除要求の直前に発生しています。

このシナリオの原因として最も可能性が高いのは、クライアントがエンティティの削除要求を Table サービスに送信してこれに成功したが、(おそらく、一時的なネットワークの問題が原因で) サーバーから受信確認を受け取らなかったというものです。 そのため、クライアントが自動的に操作を再試行しますが (同じ **client-request-id** を使用)、エンティティは既に削除されているので、この再試行は失敗します。

この問題が頻繁に発生する場合は、クライアントが Table サービスからの受信確認の受け取りに失敗している原因を調べてください。 問題が断続的に発生する場合は、クライアントで "HTTP (404) 未検出" エラーをトラップしてログに記録する一方でクライアントの実行を継続できるようにする必要があります。

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>クライアントが HTTP 409 (競合) のメッセージを受け取る
以下の表に、**DeleteIfExists** と、その直後の同じ BLOB コンテナー名を使用する **CreateIfNotExists** の 2 つのクライアント操作に関する、サーバー側のログから抜粋した内容を示します。 どちらのクライアント操作でも、2 つの要求がサーバーに送信されます (1 つ目がコンテナーの存在をチェックする **GetContainerProperties** 要求で、その次が **DeleteContainer** 要求または **CreateContainer** 要求です)。

| Timestamp | 操作 | 結果 | コンテナー名 | クライアント要求 ID |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-… |
| 05:10:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-… |
| 05:10:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-… |
| 05:10:14.2147723 |CreateContainer |409 |mmcont |bc881924-… |

クライアント アプリケーションのコードは同じ名前を使用して BLOB コンテナーを削除し、またすぐに作成します。**CreateIfNotExists** メソッド (クライアント要求 ID bc881924-…) は最終的に HTTP 409 (競合) エラーで失敗します。 クライアントが BLOB コンテナー、テーブル、またはキューを削除した後、その名前が再び使用できるようになるまでに少し時間がかかります。

削除/再作成のパターンをよく使用する場合、クライアント アプリケーションは、新規コンテナーの作成時に必ず一意のコンテナー名を使用する必要があります。

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>メトリックの示す PercentSuccess が低い、または分析ログ エントリの中にトランザクション ステータスが ClientOtherErrors の操作がある
**PercentSuccess** メトリックは、操作の HTTP 状態コードに基づいて、成功した操作の割合をキャプチャします。 2XX の状態コードを持つ操作は、成功としてカウントされます。一方、3XX、4XX、5XX の範囲の状態コードを持つ操作は失敗としてカウントされ、**PercentSucess** メトリック値を下げます。 サーバー側のストレージ ログ ファイルには、これらの操作が **ClientOtherErrors** のトランザクション状態で記録されます。

これらの操作は正常に実行されているため、可用性などの他のメトリックに影響することはないことに留意してください。 正常に実行されても失敗の HTTP 状態コードを返される可能性のある操作の例を以下に示します。

* **ResourceNotFound** (未検出 404)。存在しない BLOB への GET 要求など。
* **ResourceAlreadyExists** (競合 409)。リソースが既に存在している場合の **CreateIfNotExist** 操作など。
* **ConditionNotMet** (変更なし 304)。最後の操作より後にイメージが更新された場合にのみ、クライアントが **ETag** 値と HTTP **If-None-Match** ヘッダーを送信してそのイメージを要求するなどの条件付き操作。

「[REST API の一般的なエラー コード](/rest/api/storageservices/Common-REST-API-Error-Codes)」に、Storage サービスが返す一般的な REST API エラー コードの一覧を記載しています。

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>ストレージ使用量の予期しない増加が容量メトリックに示される
突然、ストレージ アカウントの使用量に予期しない変化が生じた場合は、まず、可用性メトリックを参照して理由を調べます。たとえば、領域を解放すると想定していたアプリケーション固有のクリーンアップ操作が、(領域の解放に使用する SAS トークンの期限が切れていたなどの理由で) 想定どおりに機能しなかったために削除要求の失敗の数が増加し、その結果、Blob Storage の使用量が増加した可能性があります。

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>開発またはテストでのストレージ エミュレーターの使用に起因する問題
通常、開発およびテストでは、Azure ストレージ アカウントを使用せずに済むように、ストレージ エミュレーターを使用します。 ストレージ エミュレーターを使用する場合に発生する可能性のある一般的な問題を以下に示します。

* [機能 "X" がストレージ エミュレーターで機能しない]
* [ストレージ エミュレーターを使用するとエラー "HTTP ヘッダーのいずれかの値の形式が正しくありません" が発生する]
* [ストレージ エミュレーターの実行に管理者権限が必要になる]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>機能 "X" がストレージ エミュレーターで機能しない
ストレージ エミュレーターは、ファイル サービスなどの Azure Storage サービスの機能をすべてサポートしているわけではありません。 詳細については、「 [開発とテストのための Azure のストレージ エミュレーター使用](storage-use-emulator.md)」を参照してください。

ストレージ エミュレーターでサポートされない機能については、クラウドの Azure Storage サービスを使用してください。

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>ストレージ エミュレーターを使用するとエラー "HTTP ヘッダーのいずれかの値の形式が正しくありません" が発生する
ストレージ クライアント ライブラリを使用するアプリケーションをローカル ストレージ エミュレーターでテストすると、 **CreateIfNotExists** などのメソッドの呼び出しがエラー メッセージ "HTTP ヘッダーのいずれかの値の形式が正しくありません" で失敗します。 これは、使用しているストレージ エミュレーターのバージョンが、使用しているストレージ クライアント ライブラリのバージョンをサポートしていないことを示しています。 ストレージ クライアント ライブラリは、生成するすべての要求にヘッダー **x-ms-version** を追加します。 ストレージ エミュレーターが **x-ms-version** ヘッダーの値を認識しない場合、要求を拒否します。

ストレージ クライアント ライブラリのログを使用して、送信された **x-ms-version ヘッダー** の値を確認できます。 Fiddler を使用してクライアント アプリケーションからの要求をトレースする場合も、 **x-ms-version ヘッダー** の値を確認できます。

通常、ストレージ エミュレーターを更新せずに、最新バージョンのストレージ クライアント ライブラリをインストールして使用すると、この状況が発生します。 最新バージョンのストレージ エミュレーターをインストールするか、エミュレーターではなくクラウド ストレージを開発およびテストに使用する必要があります。

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>ストレージ エミュレーターの実行に管理者権限が必要になる
ストレージ エミュレーターを実行する際に、管理者の資格情報を入力するように求められます。 これは、ストレージ エミュレーターを初めて初期化する場合にのみ発生します。 ストレージ エミュレーターを初期化した後は、再実行する際に管理者権限は必要ありません。

詳細については、「 [開発とテストのための Azure のストレージ エミュレーター使用](storage-use-emulator.md)」を参照してください。 Visual Studio でストレージ エミュレーターを初期化することもできますが、その場合も管理者権限が必要になります。

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Azure SDK for .NET のインストールで問題が発生する
SDK をインストールしようとすると、ローカル マシンへのストレージ エミュレーターのインストールのところで失敗します。 インストール ログには、以下のメッセージのいずれかが含まれています。

* CAQuietExec: エラー:SQL インスタンスにアクセスできません
* CAQuietExec: エラー:データベースを作成できません

原因は、既存の LocalDB インストール環境の問題にあります。 既定では、Azure Storage サービスをシミュレートするときに、ストレージ エミュレーターは LocalDB を使用してデータを保持します。 SDK のインストールを試行する前に、コマンド プロンプト ウィンドウで次のコマンドを使用することによって、LocalDB インスタンスをリセットできます。

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

**delete** コマンドは、ストレージ エミュレーターの既存のインストール環境から古いデータベース ファイルをすべて削除します。

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Storage サービスで別の問題が発生する
これまでのトラブルシューティング セクションに、ストレージ サービスで発生している問題が含まれていない場合は、以下のアプローチによって問題を診断およびトラブルシューティングする必要があります。

* メトリックを参照して、想定していた基準の動作から変化しているものがないか調べます。 メトリックから、問題が一時的か永続的か、また問題がどのストレージ操作に影響しているのかを判断できます。
* メトリック情報を使用してサーバー側のログ データを検索することで、発生している問題に関するより詳細な情報を得ることができます。 その情報を基に、問題をトラブルシューティングして解決できる可能性があります。
* サーバー側のログにある情報では問題をトラブルシューティングするのに不十分な場合、ストレージ クライアント ライブラリのクライアント側のログを使用してクライアント アプリケーションの動作を調べることができます。また、Fiddler、Wireshark などのツールを使用してネットワークを調べることもできます。

Fiddler の使用の詳細については、「[付録 1: Fiddler を使用した HTTP および HTTPS トラフィックのキャプチャ]」を参照してください。

Wireshark の使用の詳細については、「[付録 2: Wireshark を使用したネットワーク トラフィックのキャプチャ]」を参照してください。

## <a name="appendices"></a><a name="appendices"></a>付録
付録では、Azure Storage (およびその他のサービス) の問題を診断およびトラブルシューティングする際に役立つ可能性のあるいくつかのツールについて説明します。 これらのツールは Azure Storage には含まれていません。また、サードパーティの製品もあります。 そのため、これらの付録で説明するツールに、Microsoft Azure または Azure Storage のサポート契約は適用されません。したがって、評価プロセスの一環として、これらのツールのプロバイダーから入手可能なライセンスおよびサポート オプションを検討する必要があります。

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>付録 1: Fiddler を使用した HTTP および HTTPS トラフィックのキャプチャ
[Fiddler](https://www.telerik.com/fiddler) は、クライアント アプリケーションと、使用する Azure Storage サービスの間の HTTP および HTTPS トラフィックを分析するのに役立つツールです。

> [!NOTE]
> Fiddler は HTTPS トラフィックをデコードできます。Fiddler のドキュメントをよく読み、このツールがデコードする方法およびセキュリティ上の影響を理解してください。
>
>

この付録では、Fiddler をインストールしたローカル マシンと Azure Storage サービスの間のトラフィックをキャプチャするための Fiddler の構成方法について簡単に説明します。

Fiddler を起動すると、ローカル マシンの HTTP および HTTPS トラフィックのキャプチャが開始されます。 以下に、Fiddler の操作に役立ついくつかのコマンドを示します。

* トラフィックのキャプチャを停止および開始します。 メイン メニューで、 **[File]** 、 **[Capture Traffic]** の順にクリックし、キャプチャのオン/オフを切り替えます。
* キャプチャしたトラフィック データを保存します。 メイン メニューで、 **[File]** 、 **[Save]** 、 **[All Sessions]** の順にクリックします。これにより、セッション アーカイブ ファイルにトラフィックを保存できるようになります。 後で分析するためセッション アーカイブを再読み込みしたり、要請された場合にそのファイルを Microsoft サポートに送信したりできます。

Fiddler がキャプチャするトラフィックの量を制限するために、 **[Filters]** タブで構成するフィルターを使用することができます。以下のスクリーンショットには、 **contosoemaildist.table.core.windows.net** ストレージ エンドポイントに送信されるトラフィックのみをキャプチャするフィルターが示されています。

![contosoemaildist.table.core.windows.net ストレージ エンドポイントに送信されるトラフィックのみをキャプチャするフィルターが示されているスクリーンショット。][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>付録 2: Wireshark を使用したネットワーク トラフィックのキャプチャ
[Wireshark](https://www.wireshark.org/) は、さまざまなネットワーク プロトコルの詳細なパケット情報を表示できるネットワーク プロトコル アナライザーです。

以下の手順は、Wireshark をインストールしたローカル マシンから Azure ストレージ アカウントの Table サービスへのトラフィックに関する詳細なパケット情報をキャプチャする方法を示しています。

1. ローカル マシンで Wireshark を起動します。
2. **[Start]** セクションで、インターネットに接続されているローカル ネットワーク インターフェイスを選択します。
3. **[Capture Options]** をクリックします。
4. フィルターを **[Capture Filter]** テキストボックスに追加します。 たとえば、**host contosoemaildist.table.core.windows.net** では、**contosoemaildist** ストレージ アカウントの Table service エンドポイントとの間で送受信されるパケットのみをキャプチャするように Wireshark を構成します。 [キャプチャ フィルターの完全な一覧](https://wiki.wireshark.org/CaptureFilters)を確認してください。

   ![[Capture Filter] ボックスにフィルターを追加する方法を示すスクリーンショット。][6]
5. **[開始]** をクリックします。 これで、Wireshark は、ローカル マシンでクライアント アプリケーションを使用しているときに Table サービス エンドポイントとの間で送受信されるすべてのパケットをキャプチャします。
6. 終了したら、メイン メニューで **[Capture]** 、 **[Stop]** の順にクリックします。
7. キャプチャしたデータを Wireshark キャプチャ ファイルに保存するには、メイン メニューで **[File]** 、 **[Save]** の順にクリックします。

WireShark は、 **[packetlist]** ウィンドウに存在するエラーをすべて強調表示します。 **[Expert Info]** ウィンドウを使用して ( **[Analyze]** 、 **[Expert Info]** の順にクリック)、エラーや警告の概要を表示することもできます。

![エラーと警告の概要を表示できる [Expert Info] ウィンドウを示すスクリーンショット。][7]

アプリケーション レイヤーで見られる形で TCP データを表示するように選択することもできます。このためには、TCP データを右クリックしてから **[Follow TCP Stream]** を選択します。 これは、キャプチャ フィルターを使用せずにダンプをキャプチャした場合に便利です。 詳細については、[Follow TCP Stream](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html) に関する記事を参照してください。

![アプリケーション レイヤーで見られる形で TCP データを表示する方法を示すスクリーンショット。][8]

> [!NOTE]
> Wireshark の使用方法の詳細については、『[Wireshark Users Guide](https://www.wireshark.org/docs/wsug_html_chunked)』を参照してください。
>
>

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>付録 4: Excel を使用したメトリックおよびログ データの表示
さまざまなツールを使用して、Excel に簡単に読み込んで表示したり分析したりできる区切り形式で、ストレージ メトリック データを Azure Table Storage からダウンロードすることができます。 Azure Blob Storage のストレージ ログ データは、既に、Excel に読み込み可能な区切り形式になっています。 しかし、「[Storage Analytics のログの形式](/rest/api/storageservices/Storage-Analytics-Log-Format)」および「[Storage Analytics Metrics のテーブル スキーマ](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)」の情報にある該当する列見出しを追加する必要があるでしょう。

BLOB ストレージからダウンロードしたストレージ ログ データを Excel にインポートするには、以下の手順に従います。

* **[データ]** メニューで **[テキストから]** をクリックします。
* 表示するログ ファイルを参照し、 **[インポート]** をクリックします。
* **[テキスト ファイル ウィザード]** の手順 1 で **[カンマやタブなどの区切り文字によってフィールドごとに区切られたデータ]** を選択します。

**[テキスト ファイル ウィザード]** の手順 1 で、 **[セミコロン]** のみを区切り文字として選択し、 **[文字列の引用符]** として二重引用符を選択します。 **[完了]** をクリックし、ワークブックでデータを配置する場所を選択します。

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>付録 5: Application Insights for Azure DevOps を使用した監視
パフォーマンスと可用性の監視の一部として、Azure DevOps 用の Application Insights 機能を使用することもできます。 このツールは、以下を実行できます。

* Web サービスが使用可能および応答可能であることを確認できます。 アプリケーションが Web サイトであるか、Web サービスを使用するデバイス アプリである場合、世界中の複数の場所から数分ごとに URL をテストし、問題がある場合には通知することができます。
* Web サービスのパフォーマンス上の問題または例外をすばやく診断できます。 CPU やその他のリソースが限界まで使用されていることを検出したり、例外からスタック トレースを取得したり、ログ トレース全体を簡単に検索したりできます。 アプリのパフォーマンスの低下が許容できる限界を超えている場合には、Microsoft から電子メールを送信できます。 .NET と Java の両方の Web サービスを監視できます。

詳細については、「 [Application Insights とは何か?](../../azure-monitor/app/app-insights-overview.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

Azure Storage の分析については、次のリソースを参照してください。

* [Azure Portal でのストレージ アカウントの監視](./manage-storage-analytics-logs.md)
* [Storage Analytics](storage-analytics.md)
* [Storage Analytics のメトリック](storage-analytics-metrics.md)
* [Storage Analytics Metrics のテーブル スキーマ](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Storage Analytics のログ](storage-analytics-logging.md)
* [Storage Analytics のログの形式](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[はじめに]: #introduction
[本書の構成]: #how-this-guide-is-organized

[Storage サービスの監視]: #monitoring-your-storage-service
[サービス正常性の監視]: #monitoring-service-health
[容量監視]: #monitoring-capacity
[可用性監視]: #monitoring-availability
[パフォーマンス監視]: #monitoring-performance

[ストレージ問題の診断]: #diagnosing-storage-issues
[サービス正常性の問題]: #service-health-issues
[パフォーマンスの問題]: #performance-issues
[エラーの診断]: #diagnosing-errors
[Storage エミュレーターの問題]: #storage-emulator-issues
[ストレージ ログ ツール]: #storage-logging-tools
[ネットワーク ログ ツールの使用]: #using-network-logging-tools

[エンド ツー エンド トレース]: #end-to-end-tracing
[ログ データの関連付け]: #correlating-log-data
[クライアント要求 ID]: #client-request-id
[サーバー要求 ID]: #server-request-id
[タイムスタンプ]: #timestamps

[トラブルシューティング ガイダンス]: #troubleshooting-guidance
[メトリックが示す AverageE2ELatency が高く、AverageServerLatency が低い]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[メトリックでは AverageE2ELatency も AverageServerLatency も低いのにクライアントで大きな遅延が発生している]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[メトリックが高い AverageServerLatency を示す]: #metrics-show-high-AverageServerLatency
[キューのメッセージ配信で予期しない遅延が発生する]: #you-are-experiencing-unexpected-delays-in-message-delivery

[メトリックが PercentThrottlingError の増加を示す]: #metrics-show-an-increase-in-PercentThrottlingError
[PercentThrottlingError の一時的増加]: #transient-increase-in-PercentThrottlingError
[PercentThrottlingError エラーの永続的増加]: #permanent-increase-in-PercentThrottlingError
[メトリックが PercentTimeoutError の増加を示す]: #metrics-show-an-increase-in-PercentTimeoutError
[メトリックが PercentNetworkError の増加を示す]: #metrics-show-an-increase-in-PercentNetworkError

[クライアントが HTTP 403 (許可されていません) のメッセージを受け取る]: #the-client-is-receiving-403-messages
[クライアントが HTTP 404 (見つからない) のメッセージを受け取る]: #the-client-is-receiving-404-messages
[クライアントまたは別のプロセスがそのオブジェクトを以前に削除した]: #client-previously-deleted-the-object
[共有アクセス署名 (SAS) 認証の問題]: #SAS-authorization-issue
[クライアント側の JavaScript コードにオブジェクトへのアクセス許可がない]: #JavaScript-code-does-not-have-permission
[ネットワーク エラー]: #network-failure
[クライアントが HTTP 409 (競合) のメッセージを受け取る]: #the-client-is-receiving-409-messages

[メトリックの示す PercentSuccess が低い、または分析ログ エントリの中にトランザクション ステータスが ClientOtherErrors の操作がある]: #metrics-show-low-percent-success
[ストレージ使用量の予期しない増加が容量メトリックに示される]: #capacity-metrics-show-an-unexpected-increase
[開発またはテストでのストレージ エミュレーターの使用に起因する問題]: #your-issue-arises-from-using-the-storage-emulator
[機能 "X" がストレージ エミュレーターで機能しない]: #feature-X-is-not-working
[ストレージ エミュレーターを使用するとエラー "HTTP ヘッダーのいずれかの値の形式が正しくありません" が発生する]: #error-HTTP-header-not-correct-format
[ストレージ エミュレーターの実行に管理者権限が必要になる]: #storage-emulator-requires-administrative-privileges
[Azure SDK for .NET のインストールで問題が発生する]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Storage サービスで別の問題が発生する]: #you-have-a-different-issue-with-a-storage-service

[付録]: #appendices
[付録 1: Fiddler を使用した HTTP および HTTPS トラフィックのキャプチャ]: #appendix-1
[付録 2: Wireshark を使用したネットワーク トラフィックのキャプチャ]: #appendix-2
[付録 4: Excel を使用したメトリックおよびログ データの表示]: #appendix-4
[付録 5: Application Insights for Azure DevOps を使用した監視]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png