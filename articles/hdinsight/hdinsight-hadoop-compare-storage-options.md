---
title: Azure HDInsight クラスターで使用するストレージ オプションを比較する
description: ストレージの種類と、それらが Azure HDInsight でどのように動作するかの概要を提供します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: b0d963e212e66bf96ec42ec2a5c0fd5005f7a889
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165891"
---
# <a name="compare-storage-options-for-use-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで使用するストレージ オプションを比較する

HDInsight クラスターを作成する際、次のいくつかの異なる Azure Storage サービスを選択できます。

* Azure Storage
* Azure Data Lake Storage Gen2
* Azure Data Lake Storage Gen1

この記事では、これらのストレージの種類とそれらの固有の機能の概要を提供します。

次の表は、HDInsight のさまざまなバージョンでサポートされている Azure Storage サービスをまとめたものです。

| ストレージ サービス | アカウントの種類 | 名前空間の種類 | サポートされているサービス | サポートされているパフォーマンス レベル | サポートされているアクセス層 | HDInsight のバージョン | クラスターの種類 |
|---|---|---|---|---|---|---|---|
|Azure Data Lake Storage Gen2| 汎用 v2 | 階層構造 (ファイルシステム) | Blob | Standard | ホット、クール、アーカイブ | 3.6 以降 | All |
|Azure Storage| 汎用 v2 | Object | Blob | Standard | ホット、クール、アーカイブ | 3.6 以降 | All |
|Azure Storage| 汎用 v1 | Object | Blob | Standard | 該当なし | All | All |
|Azure Storage| Blob Storage** | Object | ブロック BLOB | Standard | ホット、クール、アーカイブ | All | All |
|Azure Data Lake Storage Gen1| 該当なし | 階層構造 (ファイルシステム) | 該当なし | 該当なし | 該当なし | 3.6 のみ | HBase を除くすべて |

\* * HDInsight クラスターの場合、セカンダリ ストレージ アカウントのみが型 BlobStorage になることができます。

ストレージ アカウントの種類について詳しくは、「[Azure ストレージ アカウントの概要](../storage/common/storage-account-overview.md)」をご覧ください。

Azure Storage アクセス層の詳細については、「[Azure Blob Storage:Premium ストレージ層 (プレビュー)、ホット ストレージ層、クール ストレージ層、アーカイブ ストレージ層](../storage/blobs/storage-blob-storage-tiers.md)」を参照してください。

プライマリ ストレージと省略可能なセカンダリ ストレージ用のさまざまなサービスの組み合わせを使用してクラスターを作成することができます。 次の表に、HDInsight で現在サポートされているクラスター ストレージの構成をまとめています。

| HDInsight のバージョン | プライマリ ストレージ | セカンダリ ストレージ | サポートされています |
|---|---|---|---|
| 3.6 と 4.0 | General Purpose V1、General Purpose V2 | General Purpose V1、General Purpose V2、 BlobStorage (ブロック Blob) | はい |
| 3.6 と 4.0 | General Purpose V1、General Purpose V2 | Data Lake Storage Gen2 | いいえ |
| 3.6 と 4.0 | General Purpose V1、General Purpose V2 | Data Lake Storage Gen1 | はい |
| 3.6 と 4.0 | Data Lake Storage Gen2* | Data Lake Storage Gen2 | はい |
| 3.6 と 4.0 | Data Lake Storage Gen2* | General Purpose V1、General Purpose V2、 BlobStorage (ブロック Blob) | はい |
| 3.6 と 4.0 | Data Lake Storage Gen2 | Data Lake Storage Gen1 | いいえ |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen1 | はい |
| 3.6 | Data Lake Storage Gen1 | General Purpose V1、General Purpose V2、 BlobStorage (ブロック Blob) | はい |
| 3.6 | Data Lake Storage Gen1 | Data Lake Storage Gen2 | いいえ |
| 4.0 | Data Lake Storage Gen1 | 任意 | いいえ |

*=すべてがクラスター アクセスに同じマネージド ID を使用するように構成されている限り、これは 1 つ以上の Data Lake Storage Gen2 アカウントの可能性があります。

## <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Azure HDInsight で Azure Data Lake Storage Gen2 と Apache Hadoop を使用する

Azure Data Lake Storage Gen2 は、Azure Data Lake Storage Gen1 のコア機能を取得し、それらを Azure Blob Storage に統合します。 これらの機能には、Hadoop と互換性のあるファイル システム、Azure Active Directory (Azure AD)、および POSIX ベースのアクセス制御リスト (ACL) などがあります。 この組み合わせにより、Azure Data Lake Storage Gen1 のパフォーマンスを利用しつつ、Blob Storage の階層化とデータのライフ サイクル管理も使用することができます。

Azure Data Lake Storage Gen2 の詳細については、「[Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)」を参照してください。

### <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2 のコア機能

* **Hadoop と互換性のあるアクセス:** Azure Data Lake Storage Gen2 では、Hadoop 分散ファイル システム (HDFS) を利用する場合と同様に、データの管理およびアクセスが可能です。 Azure BLOB ファイル システム (ABFS) ドライバーは、Azure HDInsight や Azure Databricks を含むすべての Apache Hadoop 環境内で使用できます。 Data Lake Storage Gen2 に格納されているデータにアクセスするには、ABFS を使用します。

* **POSIX アクセス許可のスーパーセット**:Data Lake Gen2 のセキュリティ モデルは、ACL および POSIX のアクセス許可に加え、Data Lake Storage Gen2 固有の追加設定をサポートしています。 設定は、管理ツールまたはフレームワーク (Apache Hive や Apache Spark など) を通じて構成できます。

* **コスト効率**:Data Lake Storage Gen2 は、低コストのストレージ容量とトランザクションを備えています。 Azure Blob Storage のライフサイクルなどの機能は、データのライフサイクルの進展に応じて請求金額を調整することで、コストの削減に役立ちます。

* **BLOB ストレージ ツール、フレームワーク、およびアプリとの互換性**:Data Lake Storage Gen2 では、BLOB ストレージ用の多様なツール、フレームワーク、およびアプリケーションを継続的に利用できます。

* **最適化されたドライバー**:ABFS ドライバーは、ビッグ データ分析のために特別に最適化されています。 対応する REST API は、分散ファイル システム (DFS) エンドポイント dfs.core.windows.net を介して表示されます。

### <a name="whats-new-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen2 の新機能

#### <a name="managed-identities-for-secure-file-access"></a>セキュア ファイル アクセスのためのマネージド ID

Azure HDInsight は、マネージド ID を使用して、Azure Data Lake Storage Gen2 内のファイルへのクラスター アクセスをセキュリティで保護します。 マネージド ID は、Azure サービスに自動的に管理される一連の資格情報を提供する Azure Active Directory の機能です。 これらの資格情報は、Active Directory 認証をサポートする任意のサービスを認証するために使用できます。 マネージド ID の使用には、コードまたは構成ファイルに資格情報を格納する必要はありません。

詳細については、[Azure リソースのマネージド ID](../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

#### <a name="azure-blob-file-system-driver"></a>Azure BLOB ファイル システム ドライバー

Apache Hadoop アプリケーションは、ローカル ディスク ストレージからネイティブにデータを読み書きすることを想定しています。 ABFS などの Hadoop ファイル システム ドライバーは、通常の Hadoop ファイル システム操作をエミュレートすることによって、Hadoop アプリケーションがクラウド ストレージで動作できるようにします。 このドライバーは、アプリケーションから受信されたコマンドを実際のクラウド ストレージ プラットフォームで認識される操作に変換します。

以前は、Hadoop ファイル システム ドライバーは、すべてのファイルシステム操作をクライアント側での Azure Storage REST API 呼び出しに変換してから、REST API を呼び出していました。 しかし、このクライアント側の変換では、ファイルの名前変更のような 1 つのファイルシステム操作に対して複数の REST API が呼び出されていました。 ABFS では、Hadoop ファイル システムのロジックの一部をクライアント側からサーバー側に移行しています。 Azure Data Lake Storage Gen2 API は、Blob API と並列実行されます。 この移行により、Hadoop ファイルシステムの一般的な操作が 1 つの REST API 呼び出しで実行できるようになったため、パフォーマンスが向上します。

詳細については、「[The Azure Blob Filesystem driver (ABFS):A dedicated Azure Storage driver for Hadoop](../storage/blobs/data-lake-storage-abfs-driver.md)」 (Azure BLOB ファイルシステム ドライバー (ABFS): Hadoop 専用の Azure Storage ドライバー) を参照してください。

#### <a name="uri-scheme-for-azure-data-lake-storage-gen-2"></a>Azure Data Lake Storage Gen 2 の URI スキーム 

Azure Data Lake Storage Gen2 は、HDInsight から Azure Storage 内のファイルにアクセスするための新しい URI スキームを使用します。

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

この URI スキームは、SSL で暗号化されたアクセス (`abfss://` プレフィックス) と暗号化なしのアクセス (`abfs://` プレフィックス) に対応しています。 同じ Azure リージョン内のデータにアクセスする場合でも、可能な限り `abfss` を使用してください。

`<FILE_SYSTEM_NAME>` には、Data Lake Storage Gen2 ファイル システムのパスを指定します。

`<ACCOUNT_NAME>` には、Azure Storage アカウント名を指定します。 完全修飾ドメイン名 (FQDN) を指定する必要があります。

`<PATH>` は、ファイルまたはディレクトリの HDFS パス名です。

`<FILE_SYSTEM_NAME>` と `<ACCOUNT_NAME>` の値が指定されていない場合、既定のファイルシステムが使用されます。 既定のファイル システム上にあるファイルに関しては、相対パスか絶対パスを使用します。 たとえば、HDInsight クラスターに付属している `hadoop-mapreduce-examples.jar` ファイルは、次のいずれかのパスを使用して参照できます。

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!Note]
> HDInsight バージョン 2.1 クラスターと 1.6 クラスターでは、ファイル名は `hadoop-examples.jar` です。 HDInsight の外部からファイルを操作する場合、ほとんどのユーティリティで ABFS 形式が認識されず、代わりに `example/jars/hadoop-mapreduce-examples.jar` などの基本的なパス形式が要求されます。

詳細については、「[Use the Azure Data Lake Storage Gen2 URI](../storage/blobs/data-lake-storage-introduction-abfs-uri.md)」 (Azure Data Lake Storage Gen2 の URI を使用する) を参照してください。

## <a name="azure-storage"></a>Azure Storage

Azure Storage は、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。 HDInsight は、クラスターの既定のファイル システムとして Azure Storage 内の BLOB コンテナーを使用できます。 HDInsight のすべてのコンポーネントは、BLOB として格納された構造化データまたは非構造化データを HDFS インターフェイスを介して直接操作できます。

Microsoft では、既定のクラスター ストレージとビジネス データに別々のストレージ コンテナーを使用して、HDInsight のログと一時ファイルをご自身のビジネス データから切り離すことをお勧めしています。 また、アプリケーション ログとシステム ログが含まれている既定の BLOB コンテナーは、ストレージ コストを削減するために、それぞれのログを使用した後に削除することもお勧めしています。 コンテナーを削除する前に、ログを取り出してください。

**[選択されたネットワーク]** で **[ファイアウォールと仮想ネットワーク]** に関する制限を使用してストレージ アカウントをセキュリティで保護する場合、 **[Allow trusted Microsoft services]\(信頼された Microsoft サービスを許可)** の例外を有効にして、HDInsight ストレージ アカウントにアクセスできるようにしてください。

### <a name="hdinsight-storage-architecture"></a>HDInsight のストレージ アーキテクチャ

次の図は、Azure Storage のHDInsight アーキテクチャを示しています。

![Hadoop クラスターが HDFS API を使用して、BLOB ストレージの構造化データおよび非構造化データへのアクセスと保存を実行する方法を示す図](./media/hdinsight-hadoop-compare-storage-options/HDI.WASB.Arch.png "HDInsight ストレージのアーキテクチャ")

HDInsight では、それぞれのコンピューティング ノードにローカルに割り当てられている分散ファイル システムにアクセスします。 このファイル システムには、完全修飾 URI を使用してアクセスできます。次に例を示します。

    hdfs://<namenodehost>/<path>

HDInsight を介して、Azure Storage 内のデータにアクセスすることもできます。 構文は次のとおりです。

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

HDInsight クラスターで Azure Storage アカウントを使用するときには、次の原則を考慮してください。

* **クラスターに接続されているストレージ アカウント内のコンテナー:** アカウントの名前とキーは作成中にクラスターと関連付けられるため、これらのコンテナー内の BLOB に対するフル アクセス許可が与えられます。

* **クラスターに接続されて*いない*ストレージ アカウント内のパブリック コンテナーまたはパブリック BLOB:** コンテナー内の BLOB に対する読み取り専用のアクセス許可が与えられます。
  
  > [!NOTE]  
  > パブリック コンテナーの場合、そのコンテナー内に配置されているすべての BLOB のリストとコンテナー メタデータを取得できます。 パブリック BLOB の場合、正確な URL がわかっているときのみ、その BLOB にアクセスできます。 詳細については、「 [コンテナーと BLOB への匿名読み取りアクセスを管理する](../storage/blobs/storage-manage-access-to-resources.md)」を参照してください。

* **クラスターに接続されて*いない*ストレージ アカウント内のプライベート コンテナー:** WebHCat ジョブを送信するときにストレージ アカウントを定義しない限り、コンテナー内の BLOB にはアクセスできません。 

作成プロセスで定義されたストレージ アカウントとそのキーは、クラスター ノードの %HADOOP_HOME%/conf/core-site.xml に格納されます。 既定では、HDInsight は core-site.xml ファイルに定義されたストレージ アカウントを使用します。 この設定は、[Apache Ambari](./hdinsight-hadoop-manage-ambari.md) を使用して変更できます。

Apache Hive、MapReduce、Apache Hadoop ストリーミング、Apache Pig など、複数の WebHCat ジョブを利用して、ストレージ アカウントの説明とそのメタデータを伝達できます。 (現在、ストレージ アカウントについては Pig が対応していますが、メタデータについては対応していません)。詳細については、「[代替のストレージ アカウントとメタストアでの HDInsight クラスターの使用](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)」を参照してください。

BLOB は、構造化データと非構造化データに使用できます。 BLOB コンテナーには、"キーと値のペア" としてデータが格納されます。ディレクトリ階層はありません。 ただし、キー名にスラッシュ (/) を含めると、ファイルがディレクトリ階層に保存されているように見せかけることができます。 たとえば、BLOB のキーが `input/log1.txt` であるとします。 実際には `input` というディレクトリは存在しませんが、キー名のスラッシュにより、キーがファイル パスのように見えます。

### <a id="benefits"></a>Azure Storage の利点
コンピューティング クラスターとストレージ リソースを同じ場所に併置しないと、パフォーマンスの低下が懸念されます。 こうした懸念は、Azure リージョン内のストレージ アカウント リソースの近くにコンピューティング クラスターを作成することで軽減されます。 このリージョンでは、コンピューティング ノードは Azure Storage 内の高速ネットワークを介してデータに効率的にアクセスできます。

HDFS ではなく、Azure Storage にデータを格納すると、いくつかの利点があります。

* **データの再使用と共有:** HDFS のデータはコンピューティング クラスター内に配置されます。 HDFS API を使用してデータを操作できるのは、コンピューティング クラスターへのアクセスが許可されているアプリケーションだけです。 対照的に、Azure Storage 内のデータには、HDFS API または Blob Storage REST API のどちらかを使用してアクセスできます。 これにより、さまざまなアプリケーション (その他の HDInsight クラスターを含む) やツールを使用してデータの生成と利用ができます。

* **データのアーカイブ:** Azure Storage にデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。

* **データ ストレージ コスト:** コンピューティング クラスターは Azure Storage よりコストがかかるため、DFS に長期間データを格納すると、Azure Storage にデータを格納した場合よりコストが高くなります。 また、コンピューティング クラスターを生成するたびにデータを再読み込みする必要がないため、データの読み込みコストも節約されます。

* **柔軟なスケールアウト:** HDFS は大規模なファイル システムを提供しますが、規模を拡張するにはクラスターに対して作成するノードの数を増やさなければならないので、作業が複雑になります。 一方、Azure Storage には柔軟なスケール機能がもともと備わっています。

* **geo レプリケーション:** Azure Storage は、別の拠点に geo レプリケートできます。 geo レプリケーションにより、災害発生時には別の拠点でデータを回復でき、データの冗長性が高まりますが、geo レプリケーションした別拠点へのフェールオーバーはパフォーマンスに大きな影響を与え、追加コストが発生する可能性もあります。 このため、geo レプリケーションを利用するときは、追加コストがかかっても保護する価値のあるデータかどうかを十分に考慮してください。

MapReduce の一部のジョブやパッケージでは中間結果が生成されますが、Azure Storage には保存したくない場合もあります。 このような場合、中間結果データをローカルの HDFS に保存できます。 HDInsight では、Hive ジョブやその他のプロセスで生成される中間結果の一部が DFS に格納されます。

> [!NOTE]  
> ほとんどの HDFS コマンド (`ls`、`copyFromLocal`、`mkdir` など) は、Azure Storage で想定どおりに機能します。 ただし、`fschk` や `dfsadmin` など、HDFS ネイティブ実装 (DFS) に固有のコマンドについては、Azure Storage 上で実行した場合に動作が異なります。

## <a name="overview-of-azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1 の概要

Azure Data Lake Storage Gen1 は、ビッグ データの分析ワークロードに対応するエンタープライズ レベルのハイパースケール リポジトリです。 Azure Data Lake を使用すると、運用分析や調査分析を目的として任意のサイズ、種類、および取り込み速度のデータを 1 か所でキャプチャすることができます。

Data Lake Storage Gen1 には、WebHDFS と互換性のある REST API を使用して Hadoop (HDInsight クラスターで使用可能) からアクセスします。 Data Lake Storage Gen1 は、格納されたデータに対する分析を可能にするように設計されており、データ分析シナリオ用にパフォーマンスがチューニングされます。 これには、企業における実際のユース ケースで不可欠な機能が既定で組み込まれています。 これらの機能には、セキュリティ、管理の容易性、スケーラビリティ、信頼性、および可能性が含まれます。

Azure Data Lake Storage Gen1 の詳細については、「[Azure Data Lake Storage Gen1 の概要](../data-lake-store/data-lake-store-overview.md)」の詳しい説明を参照してください。

Data Lake Storage Gen1 の主要な機能を以下に示します。

### <a name="compatibility-with-hadoop"></a>Hadoop との互換性

Data Lake Storage Gen1 は、HDFS と互換性のある Apache Hadoop ファイル システムであり、Hadoop エコシステムと連動します。  WebHDFS API を使用する既存の HDInsight アプリケーションまたはサービスは、Data Lake Storage Gen1 と簡単に統合することができます。 Data Lake Storage Gen1 では、アプリケーション向けの WebHDFS と互換性のある REST インターフェイスも公開されています。

Data Lake Storage Gen1 に格納されたデータは、MapReduce または Hive などの Hadoop 分析フレームワークを使用して簡単に分析することができます。 Data Lake Storage Gen1 に格納されたデータに直接アクセスするように Azure HDInsight クラスターをプロビジョニングおよび構成することができます。

### <a name="unlimited-storage-petabyte-files"></a>無制限のストレージ、ペタバイト ファイル

Data Lake Storage Gen1 では無制限のストレージが提供されます。そのため、分析を目的としてさまざまなデータを格納するのに適しています。 Data Lake に格納できるアカウント サイズ、ファイル サイズ、またはデータ量に関する制限は設定されていません。 対応可能な個々のファイルのサイズはキロバイトからペタバイトの範囲にわたり、任意の種類のデータを Data Lake Storage Gen1 に自由に格納することができます。 データは複数のコピーを作成して格納されるため、障害が発生しても保護されます。Data Lake でのデータの格納期間に制限はありません。

### <a name="performance-tuning-for-big-data-analytics"></a>ビッグ データを分析するためのパフォーマンス チューニング

Data Lake Storage Gen1 は、大量のデータのクエリと分析のために非常に高いスループットを必要とする、大規模な分析システムを実行するために構築されています。 Data Lake では、ファイル内のデータを複数の異なる記憶域サーバーに分散します。 このセットアップにより、データ分析時にファイルを並列に読み取る場合の読み取りスループットが向上します。

### <a name="readiness-for-enterprise-highly-available-and-secure"></a>エンタープライズへの対応:高可用性およびセキュリティ

Data Lake Storage Gen1 では、業界標準の可用性と信頼性が提供されます。 データ資産は、冗長なコピーを作成して格納されるため、予期せぬ障害が発生しても保護されます。 企業では、実際のソリューションで既存のデータ プラットフォームの重要な部分として Data Lake Storage Gen1 を使用できます。

Data Lake Storage Gen1 では、格納されたデータに対してエンタープライズ レベルのセキュリティも提供されます。 詳細については、[Azure Data Lake Storage Gen1 内のデータのセキュリティ保護](#DataLakeStoreSecurity)に関するページを参照してください。

### <a name="flexible-data-structures"></a>柔軟なデータ構造

Data Lake Storage Gen1 では、任意のデータをネイティブ形式でそのまま格納することができ、事前の変換は不要です。 Data Lake Storage Gen1 では、データが読み込まれる前にスキーマを定義する必要はありません。 分析時にデータを解釈してスキーマを定義するかどうかは個々の分析フレームワークに任されます。 任意のサイズおよび形式のファイルを格納できるため、Data Lake Storage Gen1 では構造化データ、半構造化データ、および非構造化データを処理できます。

Data Lake Storage Gen1 のデータ コンテナーは、本質的にはフォルダーとファイルです。 格納されたデータを SDK、Azure Portal、および Azure PowerShell を使用して操作します。 これらのインターフェイスと適切なコンテナーを使用してストアにデータを配置する限り、あらゆる種類のデータを格納できます。 Data Lake Storage Gen1 では、格納されているデータの種類に基づくデータの特別な処理は行われません。

## <a name="DataLakeStoreSecurity"></a>Data Lake Storage Gen1 のデータのセキュリティ
Data Lake Storage Gen1 では、Azure Active Directory を使用して認証を行い、アクセス制御リスト (ACL) を使用してデータへのアクセスを管理します。

| **機能** | **説明** |
| --- | --- |
| Authentication |Data Lake Storage Gen1 では、Azure Active Directory (Azure AD) と統合することで、Data Lake Storage Gen1 に格納されたすべてのデータの ID およびアクセスの管理を行います。 この統合により、Data Lake Storage Gen1 では、Azure AD のすべての機能の利点が得られます。 これらの機能には、多要素認証、条件付きアクセス、ロール ベースのアクセス制御、アプリケーション使用状況の監視、セキュリティの監視とアラート通知などが含まれます。 Data Lake Storage Gen1 では、REST インターフェイスでの認証に対応する OAuth 2.0 プロトコルがサポートされます。 「[Azure Data Lake Storage Gen1 での Azure Active Directory を使用した認証](../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)」を参照してください。|
| アクセス制御 |Data Lake Storage Gen1 では、WebHDFS プロトコルで公開された POSIX 形式のアクセス許可をサポートすることにより、アクセス制御が提供されます。 ルート フォルダー、サブフォルダー、個々のファイルで ACL を有効にすることができます。 Data Lake Storage Gen1 のコンテキストにおける ACL のしくみの詳細については、「[Data Lake Storage Gen1 でのアクセス制御](../data-lake-store/data-lake-store-access-control.md)」を参照してください。 |
| 暗号化 |Data Lake Storage Gen1 では、アカウントに格納されているデータを暗号化することもできます。 暗号化設定は、Data Lake Storage Gen1 アカウントの作成時に指定します。 データを暗号化するかどうかを選択できます。 詳細については、[Data Lake Storage Gen1 での暗号化](../data-lake-store/data-lake-store-encryption.md)に関するページを参照してください。 暗号化関連の構成を提供する方法については、「[Azure Portal で Azure Data Lake Storage Gen1 の使用を開始する](../data-lake-store/data-lake-store-get-started-portal.md)」を参照してください。 |

Data Lake Storage Gen1 内のデータのセキュリティ保護の詳細については、「[Azure Data Lake Storage Gen1 に格納されているデータのセキュリティ保護](../data-lake-store/data-lake-store-secure-data.md)」を参照してください。

## <a name="applications-that-are-compatible-with-data-lake-storage-gen1"></a>Data Lake Storage Gen1 と互換性のあるアプリケーション
Data Lake Storage Gen1 は、Hadoop エコシステム内のほとんどのオープンソース コンポーネントと互換性があります。 他の Azure サービスとの連携性にも優れています。  Data Lake Storage Gen1 をオープンソース コンポーネントとその他の Azure サービスの両方と共に使用する方法の詳細については、下のリンクに従ってください。

* Data Lake Storage Gen1 と相互運用可能なオープンソース アプリケーションの一覧については、「[Azure Data Lake Storage Gen1 と互換性のあるオープン ソースのビッグ データ アプリケーション](../data-lake-store/data-lake-store-compatible-oss-other-applications.md)」を参照してください。
* Data Lake Storage Gen1 を他の Azure サービスと共に使用して広範なシナリオを有効にする方法については、「[Azure Data Lake Storage Gen1 と他の Azure サービスを統合する](../data-lake-store/data-lake-store-integrate-with-other-services.md)」を参照してください。
* データの取り込み、処理、ダウンロード、視覚化などのシナリオにおける Data Lake Storage Gen1 の使い方については、「[Data Lake Storage Gen1 を使用してビッグ データの要件に対応する](../data-lake-store/data-lake-store-data-scenarios.md)」を参照してください。

## <a name="data-lake-storage-gen1-file-system-adl"></a>Data Lake Storage Gen1 ファイル システム (adl://)
Hadoop 環境 (HDInsight クラスターで使用可能) では、新しいファイル システムである AzureDataLakeFilesystem (adl://) 経由で Data Lake Storage Gen1 にアクセスできます。 adl:// を使用するアプリケーションとサービスのパフォーマンスは、WebHDFS で現在まだサポートされていない方法で最適化できます。 結果として、Data Lake Storage Gen1 を使用すると、推奨される adl:// を使用することで最適なパフォーマンスを利用することも、引き続き WebHDFS API を直接使用することにより既存のコードを維持することもできるという柔軟性が得られます。 Azure HDInsight は、AzureDataLakeFilesystem をフルに活用して Data Lake Storage Gen1 で最適なパフォーマンスを実現します。

Data Lake Storage Gen1 内のデータには、以下を使用してアクセスすることができます。

`adl://<data_lake_storage_gen1_name>.azuredatalakestore.net`

Data Lake Storage Gen1 内のデータへのアクセス方法の詳細については、「[保存データに対して実行できるアクション](../data-lake-store/data-lake-store-get-started-portal.md#properties)」を参照してください。



## <a name="next-steps"></a>次の手順

* [Azure Data Lake Storage Gen2 の概要](../storage/blobs/data-lake-storage-introduction.md)
* [Azure ストレージの概要](../storage/common/storage-introduction.md)
