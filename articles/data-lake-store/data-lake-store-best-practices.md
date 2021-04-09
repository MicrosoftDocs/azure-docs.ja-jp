---
title: Azure Data Lake Storage Gen1 の使用に関するベスト プラクティス | Microsoft Docs
description: データ インジェスト、データのセキュリティ、および Azure Data Lake Storage Gen1 (以前は Azure Data Lake Store と呼ばれていました) の使用に関連するパフォーマンスに関するベスト プラクティスについて説明します
services: data-lake-store
documentationcenter: ''
author: sachinsbigdata
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: sachins
ms.openlocfilehash: 9a5c5f9a4033b70a664071d6077a69f38c905093
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452222"
---
# <a name="best-practices-for-using-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 の使用に関するベスト プラクティス

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

この記事では、Azure Data Lake Storage Gen1 の操作に関するベスト プラクティスと考慮事項について説明します。 この記事では、Data Lake Storage Gen1 のセキュリティ、パフォーマンス、回復性、監視に関連する情報を取り上げます。 Data Lake Storage Gen1 が登場するまで、Azure HDInsight などのサービスで大規模なビッグデータを取り扱うことは大変な作業でした。 ペタバイト クラスのストレージとそのスケールでの最適なパフォーマンスを達成できるように、複数の Blob Storage アカウント間でデータをシャードする必要がありました。 Data Lake Storage Gen1 では、サイズやパフォーマンスに関するほとんどのハード制限が取り除かれています。 ただし、Data Lake Storage Gen1 で最適なパフォーマンスを得るための考慮事項がまだいくつか残っています。この記事ではそれについて取り上げます。

## <a name="security-considerations"></a>セキュリティに関する考慮事項

Azure Data Lake Storage Gen1 には、POSIX アクセス制御と Azure Active Directory (Azure AD) のユーザー、グループ、およびサービス プリンシパルの詳細な監査が用意されています。 これらのアクセス制御は既存のファイルやフォルダーに設定できます。 アクセス制御は、新しいファイルやフォルダーに適用できる既定値を作成するためにも使用できます。 既存のフォルダーや子オブジェクトにアクセス許可が設定されると、そのアクセス許可は各オブジェクトに再帰的に伝達される必要があります。 ファイルの数が多い場合、アクセス許可の伝達に時間がかかることがあります。 1 秒に 30 ～ 50 オブジェクトが処理されます。 このため、フォルダー構造とユーザー グループは適切に計画する必要があります。 計画しないと、データを操作するときに予期しない遅延や問題が発生することがあります。

あるフォルダーに 100,000 の子オブジェクトがあるとします。 1 秒に下限の 30 オブジェクトが処理されると仮定すると、全フォルダーのアクセス許可の更新には 1 時間かかります。 Data Lake Storage Gen1 の ACL の詳細は、「[Azure Data Lake Storage Gen1 のアクセス制御](data-lake-store-access-control.md)」で確認できます。 ACL を再帰的に割り当てるパフォーマンスを改善するには、Azure Data Lake のコマンドライン ツールを使用します。 ツールは複数のスレッドと再帰的なナビゲーション ロジックを作成し、数百万のファイルに ACL を短時間で適用します。 ツールは Linux と Windows で使用できます。このツールの[ドキュメント](https://github.com/Azure/data-lake-adlstool)や[ダウンロード](https://aka.ms/adlstool-download)は GitHub に用意されています。 以上と同じパフォーマンス改善が、Data Lake Storage Gen1 [.NET](data-lake-store-data-operations-net-sdk.md) と [Java](data-lake-store-get-started-java-sdk.md) SDK で記述された独自のツールで可能です。

### <a name="use-security-groups-versus-individual-users"></a>セキュリティ グループの使用と個々のユーザーの違い

Data Lake Storage Gen1 でビッグデータを取り扱うときは、ほとんどの場合、サービス プリンシパルを使用して、Azure HDInsight などのサービスによるデータの操作を許可します。 ただし、個々のユーザーがデータにアクセスする必要がある場合もあります。 このようなケースでは、個々のユーザーをフォルダーやファイルに割り当てる代わりに、Azure Active Directory の[セキュリティ グループ](data-lake-store-secure-data.md#create-security-groups-in-azure-active-directory)を使用する必要があります。

一度セキュリティ グループにアクセス許可が割り当てられると、Data Lake Storage Gen1 を更新することなくグループへのユーザーの追加と削除を行うことができます。 また、これによって [32 のアクセスとデフォルトの ACL](../azure-resource-manager/management/azure-subscription-service-limits.md#data-lake-storage-limits) の制限を超えることがありません。この ACL には、あらゆるファイルとフォルダーに常に関連付けられる 4 つの POSIX スタイル ACL ([所有ユーザー](data-lake-store-access-control.md#the-owning-user)、[所有グループ](data-lake-store-access-control.md#the-owning-group)、[マスク](data-lake-store-access-control.md#the-mask)、その他) が含まれます。

### <a name="security-for-groups"></a>グループのセキュリティ

前に説明したように、ユーザーが Data Lake Storage Gen1 にアクセスする必要がある場合は、Azure Active Directory のセキュリティ グループを使用することをおすすめします。 まず、アカウントのルートに **ReadOnlyUsers**、**WriteAccessUsers**、**FullAccessUsers** のいずれかのグループを設定し、主なサブフォルダーに別のグループを設定することをおすすめします。 後でその他のユーザーのグループを追加する可能性があるものの、まだ特定されていない場合は、特定のフォルダーへのアクセス権限が付与されたダミーのセキュリティ グループを作成することをおすすめします。 セキュリティ グループを使用すると、後で何千ものファイルに新しいアクセス許可を割り当てるときに、処理時間がそれほどかからなくて済みます。

### <a name="security-for-service-principals"></a>サービス プリンシパルのセキュリティ

Azure Active Directory のサービス プリンシパルは、一般的に Azure HDInsight などのサービスで、Data Lake Storage Gen1 のデータにアクセスするために使用されます。 複数のワークロードのアクセス要件によっては、組織内外のセキュリティを確保するための考慮事項がいくつか存在する場合があります。 多くのお客様にとって、Data Lake Storage Gen1 アカウントのルートのフル アクセスが付与された Azure Active Directory のサービス プリンシパルが 1 つあれば十分です。 お客様によっては、1 つのクラスターはデータへのフル アクセス、もう 1 つのクラスターには読み取りアクセスのみなど、複数のクラスターに別個のサービス プリンシパルを要求する場合があります。 セキュリティ グループについては、Data Lake Storage Gen1 が作成されたときに、予想される各シナリオ (読み取り、書き込み、フル) に対応するサービス プリンシパルの作成を検討することをおすすめします。

### <a name="enable-the-data-lake-storage-gen1-firewall-with-azure-service-access"></a>Azure のサービス アクセスで Data Lake Storage Gen1 のファイアウォールを有効にする

Data Lake Storage Gen1 には、ファイアウォールを有効にして、アクセスを Azure のサービスに限定するオプションが用意されていおり、外部からの侵入による攻撃ベクトルを小さくするのにおすすめです。 Data Lake Storage Gen1 アカウントのファイアウォールは、Azure Portal の **[ファイアウォール]**  >  **[ファイアウォールを有効にする] を [オン]**  >  **[Allow access to Azure services]\(Azure のサービスへのアクセスを許可する\)** のオプションで有効にできます。

![Data Lake Storage Gen1 のファイアウォール設定](./media/data-lake-store-best-practices/data-lake-store-firewall-setting.png "Data Lake Storage Gen1 のファイアウォール設定")

ファイアウォールを有効にすると、HDInsight、Data Factory、Azure Synapse Analytics などの Azure のサービスのみが Data Lake Storage Gen1 にアクセスできます。 Azure で使用される内部ネットワーク アドレス変換により、Data Lake Storage Gen1 のファイアウォールでは IP により特定のサービスを制限することができません。これは、Azure 外のエンドポイント (オンプレミスなど) を制限することのみを意図しています。

## <a name="performance-and-scale-considerations"></a>パフォーマンスとスケールに関する考慮事項

Data Lake Storage Gen1 の最も強力な機能の 1 つは、データ スループットに対するハード制限の撤廃です。 制限を撤廃することで、お客様はデータをシャードすることなく、データのサイズやそれに伴うパフォーマンス要件を拡大できます。 Data Lake Storage Gen1 のパフォーマンスを最適化するための最も重要な考慮事項の 1 つは、並列処理が指定されたときに最高のパフォーマンスを発揮することです。

### <a name="improve-throughput-with-parallelism"></a>並列処理のスループットを向上させる

最適な読み取り/書き込みスループットのために、1 つのコアに 8 ～ 12 のスレッドを指定することを検討してください。 これは、単一のスレッドで読み取り/書き込みがブロックされることによるもので、スレッドを増やすことで仮想マシンでより多くのコンカレンシーを許可できます。 レベルが正常で並列処理を増やすことができるように、仮想マシンの CPU 使用率を監視してください。

### <a name="avoid-small-file-sizes"></a>小さなファイル サイズを避ける

Data Lake Storage Gen1 での POSIX のアクセス許可と監査のオーバーヘッドは、多数の小さなファイルを処理するときに顕著になります。 ベスト プラクティスとして、Data Lake Storage Gen1 では多数の小さなファイルを書き込むのではなく、データをより大きなファイルにまとめてください。 小さなファイル サイズを避けることで、次のような利点があります。

* ファイルで行われる認証チェックの回数が減る
* ファイルを開くときの接続回数が減る
* コピー/レプリケーションが速くなる
* Data Lake Storage Gen1 の POSIX のアクセス許可を更新するときの処理対象ファイルが減る

データを使用しているサービスやワークロードに応じて考慮すべきファイルの適切なサイズは 256 MB 以上です。 Data Lake Storage Gen1 にランディングするときにファイル サイズをまとめることができない場合、それらのファイルをより大きなファイルに結合する圧縮ジョブを別個に指定できます。 ファイル サイズや Data Lake Storage Gen1 でのデータの整理に関する情報や推奨事項については、「[データセットの構成](data-lake-store-performance-tuning-guidance.md#structure-your-data-set)」をご覧ください。

### <a name="large-file-sizes-and-potential-performance-impact"></a>より大きなファイル サイズとパフォーマンスに対する潜在的な影響

Data Lake Storage Gen1 は最大でペタバイト単位の大きなファイルに対応していますが、パフォーマンスを最適に保つために、データを読み取るプロセスによっては、平均で 2 GB を超えないことが理想です。 たとえば、**Distcp** を使用してストレージ アカウント間などでデータをコピーするとき、マップのタスクを決定する最も小さな細分性のレベルがファイルになります。 そのため、1 TB のファイルを 10 個コピーする場合、最大で 10 のマッパーが割り当てられます。 また、マッパーが割り当てられたファイルが大量にある場合、最初はそれらのマッパーが並列で実行され、大きなファイルを移動します。 ただし、そのジョブが段階的に縮小し、少数のマッパーのみが割り当てられたままになると、大きなファイルに単一のマッパーが割り当てられてスタックしてしまいます。 マイクロソフトは将来の Hadoop のバージョンでこの問題に対処するように、Distcp に改善要求を提出しました。

もう 1 つの例は、Data Lake Storage Gen1 で Azure Data Lake Analytics を使用する場合です。 抽出で行われる処理によっては、分割できない一部のファイル (XML、JSON) が 2 GB を超えると、パフォーマンスに影響が出る場合があります。 抽出で分割できるファイル (CSV など) の場合は、大きなファイルをおすすめします。

### <a name="capacity-plan-for-your-workload"></a>ワークロードの容量計画

Azure Data Lake Storage Gen1 では、Blob ストレージ アカウントで指定されている入出力スロットリングのハード制限が撤廃されています。 ただし、ソフト制限は考慮する必要があります。 既定のイングレス/エグレスのスロットリング制限はほとんどのシナリオのニーズを満たします。 ワークロードで制限の引き上げが必要な場合は、Microsoft サポートにお問い合わせください。 また、運用環境で入出力のスロットリング制限に引っかかることがないように、概念実証の段階で制限を確認してください。 引っかかった場合、マイクロソフトのエンジニアリング チームが手動で引き上げるのを待つ必要があります。 入出力のスロットリングが発生した場合、Azure Data Lake Storage Gen1 はエラー コード 429 を返し、適切な指数バックオフ ポリシーで再試行されるのが理想です。

### <a name="optimize-writes-with-the-data-lake-storage-gen1-driver-buffer"></a>Data Lake Storage Gen1 のドライバー バッファーで "書き込み" を最適化する

Hadoop から Data Lake Storage Gen1 に書き込むときのパフォーマンスを最適化して IOPS を削減するには、できるだけ書き込み操作を Data Lake Storage Gen1 ドライバーのバッファー サイズに近付けて実行してください。 Apache Storm や Spark のストリーミング ワークロードを使用してストリーミングするときなど、フラッシュする前にバッファー サイズを超えないようにしてください。 HDInsight/Hadoop から Data Lake Storage Gen1 に書き込む際には、Data Lake Storage Gen1 に 4 MB バッファーのドライバーがあることを知っておくことが重要です。 多くのファイル システム ドライバーと同様に、このバッファーは 4 MB のサイズに達する前に手動でフラッシュできます。 できない場合、次回の書き込みがバッファーの最大サイズを超えると、即座にストレージにフラッシュされます。 回数や期間でポリシーを同期/フラッシュするときは、できるだけバッファーのサイズを超過または過度に下回ることは避ける必要があります。

## <a name="resiliency-considerations"></a>回復性に関する考慮事項

Data Lake Storage Gen1 などのクラウド サービスでシステムを構築するときは、可用性の要件と、サービスで中断が発生したときの対応について考慮する必要があります。 ある問題は特定のインスタンスに限定またはリージョン全域にわたる可能性があるため、両方について計画しておくことが重要です。 ワークロードの **目標復旧時間** と **目標復旧ポイント** の SLA によっては、高可用性とディザスター リカバリーのために多少は積極的な戦略を選択することをおすすめします。

### <a name="high-availability-and-disaster-recovery"></a>高可用性とディザスター リカバリー

高可用性 (HA) とディザスター リカバリー (DR) は結合して取り扱うこともありますが、それぞれの戦略は特にデータに関してはわずかに異なります。 Data Lake Storage Gen1 は局所的なハードウェア障害に備えて、既に内部的に 3 倍のレプリケーションを処理しています。 ただし、複数のリージョンにわたるレプリケーションは組み込まれていないため、自身で管理する必要があります。 HA の計画をビルドするとき、サービスの中断が発生した場合に備えて、ワークロードはローカルまたは新しいリージョンに別途レプリケートしたインスタンスに切り替えることで、できるだけ早く最新のデータにアクセスする必要があります。

DR 戦略では、あるリージョンで壊滅的な障害が万が一に発生した場合に備えて、別のリージョンにデータをレプリケートすることも重要です。 このデータは、最初はレプリケートされた HA データと同じであった可能性があります。 ただし、エッジ ケースに対応する要件も考慮する必要があります。たとえば、データの破損では、定期的にスナップショットを作成してフォールバックする必要があります。 データの重要度とサイズによっては、リスクの許容範囲に応じて、1 時間、6 時間、24 時間の期間のデルタ スナップショットをローカル/セカンダリ ストアにローリングすることを検討してください。

Data Lake Storage Gen1 のデータの回復性のために、HA/DR の要件を満たす頻度 (理想は 1 時間ごと) でデータを別のリージョンに geo レプリケートすることをおすすめします。 この頻度でのレプリケーションにより、メイン システムとスループットのニーズが競合する大量のデータ移動を最小限に抑え、目標復旧ポイント (RPO) を改善します。 さらに、Data Lake Storage Gen1 を使用するアプリケーションがトリガーや失敗した試行の長さを通じてセカンダリ アカウントに自動的にフェールオーバーするか、少なくとも管理者に通知を送信して手動で介入してもらう方法を考慮してください。 フェールオーバーには、サービスがオンラインに戻ってくるのを待つことと比較して、デメリットが存在します。 データのレプリケーションが完了していない場合、フェールオーバーによりデータの損失、不一致、複雑なデータのマージが発生する可能性があります。

以下に、Data Lake Storage Gen1 のアカウント間でレプリケーションを整理するために推奨される主な 3 つのオプションと、それらの主な違いを示します。

|  |Distcp  |Azure Data Factory  |AdlCopy  |
|---------|---------|---------|---------|
|**スケールの上限**     | ワーカー ノードによる制限あり        | 最大クラウド データ移動単位による上限あり        | 分析単位による制限あり        |
|**デルタのコピーをサポートする**     |   はい      | いいえ         | いいえ         |
|**オーケストレーションが組み込まれている**     |  いいえ (Oozie Airflow または cron ジョブを使用)       | はい        | いいえ (Azure Automation または Windows タスク スケジューラを使用)         |
|**サポートされるファイル形式**     | ADL、HDFS、WASB、S3、GS、CFS        |多数あり、[コネクタ](../data-factory/connector-azure-blob-storage.md)を参照。         | ADL から ADL、WASB から ADN (同じリージョンのみ)        |
|**OS のサポート**     |Hadoop を実行しているすべての OS         | 該当なし          | Windows 10         |

### <a name="use-distcp-for-data-movement-between-two-locations"></a>Distcp を使用して 2 つの場所の間でデータを移動する

Distcp (distributed copy の省略形) は Hadoop に付属の Linux のコマンドライン ツールで、2 つの場所の間でデータを分散移動します。 2 つの場所として、Data Lake Storage Gen1、HDFS、WASB、S3 を指定できます。 このツールは Hadoop クラスター (HDInsight など) で MapReduce ジョブを使用してすべてのノードでスケールアウトします。 Distcp は、特別なネットワーク圧縮アプライアンスを使用せずにビッグデータを移動できる、最速の方法と見なされています。 また、Distcp には 2 つの場所間のデルタのみを更新する、自動再試行を処理する、計算の動的な拡大縮小を実行するオプションが用意されています。 この手法は、単一のディレクトリにサイズの大きなファイルが大量にあり、変更があったデータのみを上書きコピーする、Hive/Spark のテーブルなどをレプリケートする場合に非常に効率的です。 これらの理由から、Distcp はビッグ データ ストア間でデータをコピーする最もおすすめのツールです。

コピー ジョブは、Apache Oozie ワークフローで頻度やデータ トリガージョブを使用するほか、Linux の cron トリガーを使用してトリガーできます。 負荷の大きなレプリケーション ジョブでは、コピー ジョブに特化してチューニングおよび拡大できる、別個の HDInsight Hadoop クラスターをスピンアップすることをおすすめします。 これにより、コピー ジョブが重要なジョブを妨害することがなくなります。 十分な間隔を空けた頻度でレプリケーションを実行すると、クラスターが各ジョブ間で分解されることもあります。 セカンダリ リージョンにフェールオーバーされる場合、Data Lake Storage Gen1 のプライマリ アカウントが復旧した後に新しいデータが再度レプリケートされるように、セカンダリ リージョンでもう 1 つのクラスターもスピンアップされていることを確認してください。 Distcp の使用例については、「[Distcp を使用して Azure Storage BLOB と Data Lake Storage Gen1 の間でデータをコピーする](data-lake-store-copy-data-wasb-distcp.md)」をご覧ください。

### <a name="use-azure-data-factory-to-schedule-copy-jobs"></a>Azure Data Factory を使用してコピー ジョブのスケジュールを設定する

Azure Data Factory は **コピー アクティビティ** を使用してコピー ジョブのスケジュールを設定できるほか、**コピー ウィザード** で頻度を設定することもできます。 Azure Data Factory にはクラウド データ移動単位 (DMU) の制限があり、最終的に大規模なデータ ワークロードのスループット/計算に上限を設定します。 さらに、Azure Data Factory は現在、Data Lake Storage Gen1 アカウント間のデルタの更新は提供しておらず、Hive のテーブルなどのフォルダーをレプリケートするには、完全なコピーが必要になります。 Data Factory のコピーに関する詳細については、[コピー アクティビティのチューニング](../data-factory/copy-activity-performance.md)に関するガイドをご覧ください。

### <a name="adlcopy"></a>AdlCopy

AdlCopy は、同じリージョン内の 2 つの Data Lake Storage Gen1 間でデータのコピーを許可する、Windows のコマンドライン ツールです。 AdlCopy ツールには、スタンドアロンのオプションまたは Azure Data Lake Analytics アカウントを使用してコピー ジョブを実行するオプションが用意されています。 元々は堅牢なレプリケーションではなく、オンデマンドのコピー用にビルドされたものですが、同じリージョン内で Data Lake Storage Gen1 アカウント間での分散コピーを行うもう 1 つのオプションを提供します。 信頼性確保のため、すべての運用環境のワークロードで Data Lake Analytics のプレミアム オプションを使用することをおすすめします。 スタンドアロンのバージョンは、ビジー状態の応答を返す場合があり、スケールおよび監視機能が制限されています。

Distcp と同様に、AdlCopy は Azure Automation や Windows タスク スケジューラなどでオーケストレーションされている必要があります。 Data Factory と同様に、AdlCopy は更新されたファイルのみのコピーはサポートされていませんが、再コピーや既存のファイルの上書きには対応しています。 AdlCopy の使用に関する詳細や例については、「[Azure Storage BLOB から Data Lake Storage Gen1 へのデータのコピー](data-lake-store-copy-data-azure-storage-blob.md)」をご覧ください。

## <a name="monitoring-considerations"></a>監視に関する考慮事項

Data Lake Storage Gen1 には詳細な診断ログと監査機能が用意されています。 Data Lake Storage Gen1 には、Data Lake Storage Gen1 アカウントの下および Azure Monitor の中に、Azure Portal の基本の指標がいくつか用意されています。 Data Lake Storage Gen1 の可用性は、Azure Portal に表示されます。 ただし、この指標は 7 分ごとに更新され、公開済みの API ではクエリを実行できません。 Data Lake Storage Gen1 アカウントの最新の可用性を取得するには、独自の統合テストを実行して可用性を検証する必要があります。 記憶域使用率の合計、読み取り/書き込み要求、イングレス/エグレスなどの他の指標では、最新の情報に更新されるまで、最大で 24 時間かかる場合があります。 そのため、Hadoop のコマンドライン ツールを使用またはログ情報を集計することを通じて、より最新の指標を手動で計算する必要があります。 最新の記憶域使用率を取得する最も早い方法は、この HDFS コマンドを Hadoop クラスター ノード (ヘッド ノードなど) で実行する方法です。

```console
hdfs dfs -du -s -h adl://<adlsg1_account_name>.azuredatalakestore.net:443/
```

### <a name="export-data-lake-storage-gen1-diagnostics"></a>Data Lake Storage Gen1 の診断をエクスポートする

Data Lake Storage Gen1 から検索可能なログへのログを取得する最も早い方法の 1 つは、Data Lake Storage Gen1 アカウントの **[診断]** ブレードで、**Log Analytics** へのログ配布を有効にする方法です。 これにより、時間およびコンテンツのフィルターと 15 分間隔でトリガーされるアラート オプション (電子メール/Webhook) を使用して、受信したログにすぐにアクセスできます。 手順については、「[Azure Data Lake Storage Gen1 の診断ログへのアクセス](data-lake-store-diagnostic-logs.md)」をご覧ください。

よりリアルタイムにアラートを設定する、およびログをランディングさせる場所をより詳しく指定するには、Azure EventHub にログをエクスポートすることを検討してください。Azure EventHub では、個々のコンテンツまたは期間を区切って分析し、リアルタイムの通知をキューに送信できます。 これで、[Logic App](../connectors/connectors-create-api-azure-event-hubs.md) などの別個のアプリケーションがアラートを適切なチャネルに伝達し、NewRelic、Datadog、AppDynamics などの監視ツールに指標を送信できます。 または、ElasticSearch などのサードパーティ製のツールを使用している場合は、ログを Blob Storage にエクスポートし、[Azure Logstash プラグイン](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-input-azureblob)を使用して Elasticsearch、Kibana、Logstash (ELK) スタック内のデータを消費できます。

### <a name="turn-on-debug-level-logging-in-hdinsight"></a>HDInsight でデバッグ レベルのログ記録を有効する

Data Lake Storage Gen1 のログ配布が有効になっていない場合、Azure HDInsight には log4j を介して [Data Lake Storage Gen1 のクライアント側のログ記録](data-lake-store-performance-tuning-mapreduce.md)を有効にできます。 **[Ambari]**  >  **[YARN]**  >  **[Config]\(構成\)**  >  **[Advanced yarn-log4j configurations]\(高度な yarn-log4j 構成\)** で次のプロパティを設定する必要があります。

`log4j.logger.com.microsoft.azure.datalake.store=DEBUG`

このプロパティを設定してノードを再起動すると、Data Lake Storage Gen1 の診断がノード上の YARN ログ (/tmp/\<user\>/yarn.log) に書き込まれ、エラーやスロットリング (HTTP 429 エラー コード) などの重要な詳細を監視できます。 この同じ情報は、Azure Monitor ログや、Data Lake Storage Gen1 アカウントの [[診断]](data-lake-store-diagnostic-logs.md) ブレードの他のログ配布先で監視できます。 少なくともクライアント側のログ記録を有効にするか、Data Lake Storage Gen1 でログ配布のオプションを活用して、運用を可視化し、デバッグを簡単にすることをおすすめします。

### <a name="run-synthetic-transactions"></a>代理トランザクションを実行する

現在、Azure Portal の Data Lake Storage Gen1 のサービスの可用性に関する指標には、7 分ごとに最新の情報に更新されます。 また、公開済みの API を使用してクエリを実行することはできません。 このため、Data Lake Storage Gen1 への代理トランザクションを実行し、最大で分単位の可用性を提供できる、基本アプリケーションをビルドすることをおすすめします。 例として、Data Lake Storage Gen1 に対して読み取り、作成、更新を実行し、監視ソリューションに結果を送信する WebJob、Logic App、Azure Function App を作成することをおすすめします。 操作は一時フォルダーで実行し、テスト後に削除できます。要件によって、テストは 30 ～ 60 秒ごとに実行してください。

## <a name="directory-layout-considerations"></a>ディレクトリのレイアウトに関する考慮事項

データを Data Lake にランディングするときは、セキュリティ、パーティション分割、プロセスが効果的に活用されるように、データの構造を事前に計画することが重要です。 次の推奨事項の多くは、Azure Data Lake Storage Gen1、Blob Storage、HDFS を問わず使用できます。 すべてのワークロードのデータの消費方法に関する要件は異なりますが、以下に IoT およびバッチのシナリオを操作するときの共通のレイアウトをいくつか紹介します。

### <a name="iot-structure"></a>IoT の構造

IoT のワークロードでは、データ ストアにランディングできるデータは、製品、端末、組織、お客様と多岐にわたります。 ダウンストリーム コンシューマーのために、組織のディレクトリのレイアウト、セキュリティ、データの効率的な処理について、事前に計画することが重要です。 考慮する一般的なテンプレートは、次のようなレイアウトになります。

```console
{Region}/{SubjectMatter(s)}/{yyyy}/{mm}/{dd}/{hh}/
```

たとえば、英国の飛行機のエンジンのランディング テレメトリは次のような構造になります。

```console
UK/Planes/BA1293/Engine1/2017/08/11/12/
```

フォルダー構造の最後に日付を追加することには、重要な意味があります。 特定のリージョンや主題をユーザー/グループにロック ダウンする場合、POSIX のアクセス許可で簡単に行うことができます。 それ以外で、特定のセキュリティ グループによる閲覧を英国のデータや特定の飛行機に限定する必要があった場合、日付構造を前すると、毎時のフォルダーの下の多数のフォルダーに、別個のアクセス許可が必要です。 さらに、日付構造を前にすると、時間が経過するごとにフォルダーの数が指数関数的に増加します。

### <a name="batch-jobs-structure"></a>バッチ ジョブの構造

バッチ処理で一般的に使用される基本的な方法は、"in" フォルダーにデータをランディングする方法です。 次に、データが処理されると、ダウンストリームのプロセスが消費できるように、新しいデータが "out" フォルダーに入ります。 このディレクトリ構造は、個々のファイルで処理が必要なプロセスで、大規模なデータセット上で膨大な並列処理が必要とされない可能性があるジョブで見られます。 前述の推奨される IoT の構造と同様に、優れたディレクトリ構造にはリージョンや主題 (例: 組織、製品/製造業者) など、親レベルのフォルダーがあります。 この構造により、組織全体でデータの安全を確保し、ワークロードのデータをより管理しやすくなります。 さらに、プロセスでより優れた組織、フィルター検索、セキュリティ、自動化を実現するために、構造で日付と時間を考慮します。 日付構造の細分性のレベルは、データがアップロードまたは処理される間隔 (毎時、毎日、毎月など) によって決まります。

ファイルのプロセスがデータの破損や予期しない形式により失敗することがあります。 このようなケースでは、 **/bad** フォルダーにファイルを移動してさらに調査すると便利な場合があります。 また、バッチ ジョブはこれらの "*不良*" ファイルのレポート作成や通知を管理し、手動で介入できるようにします。 次のテンプレート構造を考慮してください。

```console
{Region}/{SubjectMatter(s)}/In/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Out/{yyyy}/{mm}/{dd}/{hh}/
{Region}/{SubjectMatter(s)}/Bad/{yyyy}/{mm}/{dd}/{hh}/
```

たとえば、ある北米のマーケティング企業は、更新されたお客様情報の抽出データをクライアントから毎日受け取ります。 処理される前と後では、次のスニペットのようになります。

```console
NA/Extracts/ACMEPaperCo/In/2017/08/14/updates_08142017.csv
NA/Extracts/ACMEPaperCo/Out/2017/08/14/processed_updates_08142017.csv
```

Hive や従来の SQL データベースなどのデータベースで直接処理される一般的なバッチ データの場合、出力は既に Hive テーブルまたは外部データベースの別のフォルダーに入るように設定されているため、 **/in** フォルダーや **/out** フォルダーは不要です。 たとえば、お客様からの毎日の抽出データはそれぞれのフォルダーにランディングし、Azure Data Factory、Apache Oozie、Apache Airflow などによるオーケストレーションは Hive や Spark の毎日のジョブをトリガーし、データを処理して Hive のテーブルに書き込みます。

## <a name="next-steps"></a>次のステップ

* [Azure Data Lake Storage Gen1 の概要](data-lake-store-overview.md)
* [Azure Data Lake Storage Gen1 のアクセス制御](data-lake-store-access-control.md)
* [Azure Data Lake Storage Gen1 のセキュリティ](data-lake-store-security-overview.md)
* [Azure Data Lake Storage Gen1 のパフォーマンス チューニング](data-lake-store-performance-tuning-guidance.md)
* [Azure Data Lake Storage Gen1 で HDInsight Spark を使用するためのパフォーマンス チューニング ガイダンス](data-lake-store-performance-tuning-spark.md)
* [Azure Data Lake Storage Gen1 で HDInsight Hive を使用するためのパフォーマンス チューニング ガイダンス](data-lake-store-performance-tuning-hive.md)
* [Data Lake Storage Gen1 で HDInsight クラスターを作成する](data-lake-store-hdinsight-hadoop-use-portal.md)
