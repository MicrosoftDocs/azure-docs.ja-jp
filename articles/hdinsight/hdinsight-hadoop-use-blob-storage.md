---
title: HDFS と互換性のある Azure Storage のデータのクエリ - Azure HDInsight
description: Azure Storage と Azure Data Lake Storage からのデータに対してクエリを実行し、分析結果を格納する方法について説明します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/23/2019
ms.openlocfilehash: e9ecc34566e6e534b7489c934c0d5fa3b34e219b
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104492"
---
# <a name="use-azure-storage-with-azure-hdinsight-clusters"></a>Azure HDInsight クラスターで Azure Storage を使用する

HDInsight クラスターでデータを分析するには、そのデータを [Azure Storage](../storage/common/storage-introduction.md)、[Azure Data Lake Storage Gen 1](../data-lake-store/data-lake-store-overview.md)/[Azure Data Lake Storage Gen 2](../storage/blobs/data-lake-storage-introduction.md) のいずれか、またはその組み合わせに格納できます。 いずれのストレージ オプションでも、計算に使用される HDInsight クラスターを安全に削除できます。このとき、ユーザー データは失われません。

Apache Hadoop は、既定のファイル システムの概念をサポートしています。 既定のファイル システムは、既定のスキームとオーソリティを意味します。 これは相対パスの解決に使用することもできます。 HDInsight クラスターの作成プロセス中に、Azure Storage 内の BLOB コンテナーを既定のファイル システムとして指定できます。または、HDInsight 3.6 では、Azure Storage または Azure Data Lake Storage Gen 1/Azure Data Lake Store Gen 2 のどちらかを既定のファイル システムとして選択できます (いくつかの例外があります)。 Data Lake Storage Gen 1 を既定のストレージとリンクされたストレージの両方として使用することのサポート可能性については、「[ HDInsight クラスターの利用可能性](./hdinsight-hadoop-use-data-lake-store.md#availability-for-hdinsight-clusters)」を参照してください。

この記事では、HDInsight クラスターでの Azure Storage の動作について説明します。 HDInsight クラスターでの Data Lake Storage Gen 1 の動作については、「[Azure HDInsight クラスターで Azure Data Lake Storage を使用する](hdinsight-hadoop-use-data-lake-store.md)」を参照してください。 HDInsight クラスターの作成について詳しくは、[HDInsight での Apache Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

Azure Storage は、堅牢な汎用ストレージ ソリューションであり、HDInsight とシームレスに統合されます。 HDInsight は、クラスターの既定のファイル システムとして Azure Storage 内の BLOB コンテナーを使用できます。 HDInsight のすべてのコンポーネントは、Hadoop 分散ファイル システム (HDFS) のインターフェイスを利用して、BLOB として格納された構造化データまたは非構造化データを直接操作できます。

> [!WARNING]  
> ストレージ アカウントの種類 **BlobStorage** は、HDInsight クラスターのセカンダリ ストレージとしてのみ使用できます。

| Storage アカウントの種類 | サポートされているサービス | サポートされているパフォーマンス レベル | サポートされているアクセス層 |
|----------------------|--------------------|-----------------------------|------------------------|
| StorageV2 (汎用 v2)  | BLOB     | Standard                    | ホット、クール、アーカイブ\*   |
| Storage (汎用 V1)   | BLOB     | Standard                    | 該当なし                    |
| BlobStorage                    | BLOB     | Standard                    | ホット、クール、アーカイブ\*   |

ビジネス データの格納には、既定の BLOB コンテナーを使用しないことをお勧めします。 ストレージ コストを削減するために、既定の BLOB コンテナーの使用後、コンテナーを毎回削除することをお勧めします。 既定のコンテナーには、アプリケーション ログとシステム ログが格納されます。 コンテナーを削除する前に、ログを取り出してください。

1 つの BLOB コンテナーを複数のクラスターの既定のファイル システムとして共有することはサポートされていません。

> [!NOTE]  
> アーカイブ アクセス層はオフライン層であり、取得の際に数時間の待ち時間があるので、HDInsight での使用は推奨されません。 詳しくは、「[アーカイブ アクセス層](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier)」をご覧ください。

**[選択されたネットワーク]** で **[ファイアウォールと仮想ネットワーク]** に関する制限を使用してストレージ アカウントをセキュリティで保護する場合、 **[信頼された Microsoft サービスによる ... を許可します]** の例外を有効にして、HDInsight ストレージ アカウントにアクセスできるようにしてください。

## <a name="hdinsight-storage-architecture"></a>HDInsight のストレージ アーキテクチャ
次の図は、Azure Storage を使う HDInsight のストレージ アーキテクチャを示しています。

![Hadoop クラスターでは、HDFS API を使用してデータへのアクセスが行われ、BLOB ストレージに保存されます](./media/hdinsight-hadoop-use-blob-storage/storage-architecture.png "HDInsight のストレージ アーキテクチャ")

HDInsight では、それぞれのコンピューティング ノードにローカルに割り当てられている分散ファイル システムにアクセスします。 このファイル システムには、完全修飾 URI を使用してアクセスできます。次に例を示します。

    hdfs://<namenodehost>/<path>

さらに、HDInsight では、Azure Storage に格納されたデータにアクセスすることもできます。 の構文は次のとおりです。

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

HDInsight クラスターで Azure ストレージ アカウントを使用するときの考慮事項を次に示します。

* **クラスターに接続されているストレージ アカウント内のコンテナー:** アカウントの名前とキーは作成中にクラスターと関連付けられるため、これらのコンテナー内の BLOB に対するフル アクセス許可が与えられます。

* **クラスターに接続されていないストレージ アカウント内のパブリック コンテナーまたはパブリック BLOB:** コンテナー内の BLOB に対する読み取り専用のアクセス許可が与えられます。
  
> [!NOTE]  
> パブリック コンテナーの場合、そのコンテナー内に配置されているすべての BLOB のリストとコンテナー メタデータを取得できます。 パブリック BLOB の場合、正確な URL がわかっているときのみ、その BLOB にアクセスできます。 詳細については、[コンテナーと BLOB に対するアクセスの管理](../storage/blobs/storage-manage-access-to-resources.md)に関するページを参照してください。

* **クラスターに接続されていないストレージ アカウント内のプライベート コンテナー:** WebHCat ジョブを送信するときにストレージ アカウントを定義しない限り、コンテナー内の BLOB にはアクセスできません。 詳しくは、この記事の後半で説明します。

作成プロセスで定義されたストレージ アカウントとそれぞれのキーは、クラスター ノードの `%HADOOP_HOME%/conf/core-site.xml` に格納されます。 HDInsight の既定の動作では、core-site.xml ファイルに定義されたストレージ アカウントが使用されます。 この設定は、[Apache Ambari](./hdinsight-hadoop-manage-ambari.md) を使用して変更できます。

Apache Hive、MapReduce、Apache Hadoop ストリーミング、Apache Pig など、複数の WebHCat ジョブを利用して、ストレージ アカウントの説明とそのメタデータを伝達できます。 (現在、ストレージ アカウントについては Pig が対応していますが、メタデータについては対応していません)。詳細については、「 [Using an HDInsight Cluster with Alternate Storage Accounts and Metastores (代替のストレージ アカウントおよびメタストアでの HDInsight クラスターの使用)](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)」を参照してください。

BLOB は、構造化データと非構造化データに使用できます。 BLOB コンテナーには、"キーと値のペア" としてデータが格納されます。ディレクトリ階層はありません。 ただし、キー名でスラッシュ (/) を使用すれば、ファイルがディレクトリ階層に保存されているように見せかけることができます。 たとえば、BLOB のキー名を「 *input/log1.txt*」とします。 この場合、*input* ディレクトリは実際に存在しませんが、キー名でスラッシュが使用されているのでファイル パスのように見えます。

## <a id="benefits"></a>Azure Storage の利点

コンピューティング クラスターとストレージ リソースを同じ場所で併置しないとなるとパフォーマンスの低下が懸念されますが、これは、Azure リージョン内のストレージ アカウント リソースの近くにコンピューティング クラスターを作成することで軽減されます。高速ネットワークが整備されているので、コンピューティング ノードは Azure Storage 内のデータに効率的にアクセスできます。

HDFS ではなく、Azure Storage にデータを格納することにはいくつかの利点があります。

* **データの再使用と共有:** HDFS のデータはコンピューティング クラスター内に配置されます。 HDFS API を使用してデータを操作できるのは、コンピューティング クラスターへのアクセスが許可されているアプリケーションだけです。 Azure ストレージ内のデータには、HDFS API または [Blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API) のどちらかを使用してアクセスできます。 したがって、さまざまなアプリケーション (その他の HDInsight クラスターを含む) やツールを使用してデータの生成と利用ができます。

* **データのアーカイブ:** Azure Storage にデータを格納した場合、計算で使用する HDInsight クラスターを削除してもユーザー データは失われません。

* **データ ストレージ コスト:** コンピューティング クラスターは Azure Storage よりコストがかかるため、DFS に長期間データを格納すると、Azure Storage にデータを格納した場合よりコストが高くなります。 さらに、コンピューティング クラスターを生成するたびにデータを読み込む必要がないので、データの読み込みコストも節約できます。

* **柔軟なスケールアウト:** HDFS は大規模なファイル システムを提供しますが、規模を拡張するにはクラスターに対して作成するノードの数を増やさなければならないので、作業が複雑になります。 一方、Azure Storage には柔軟なスケール機能がもともと備わっています。

* **geo レプリケーション:** Azure Storage は、別の拠点に geo レプリケートできます。 災害発生時には別の拠点でデータを回復でき、データの冗長性が高まりますが、geo レプリケートした別拠点へのフェールオーバーはパフォーマンスに大きな影響を与え、追加コストが発生する可能性もあります。 geo レプリケーションを利用するときは、追加コストがかかっても保護する価値のあるデータかどうかを十分に考慮してください。

MapReduce の一部のジョブやパッケージでは中間結果が生成されますが、Azure Storage には保存したくない場合もあります。 このような場合、中間結果データをローカルの HDFS に保存できます。 実際、HDInsight では、Hive ジョブやその他のプロセスで生成される中間結果の一部が DFS に格納されます。

> [!NOTE]  
> ほとんどの HDFS コマンド (`ls`、`copyFromLocal`、`mkdir` など) は通常と同じように機能します。 ただし、`fschk` や `dfsadmin` など、HDFS ネイティブ実装 (DFS) に固有のコマンドについては、Azure Storage 上で実行した場合に動作が異なります。

## <a name="address-files-in-azure-storage"></a>Azure Storage 内のファイルの指定

HDInsight から Azure Storage 内のファイルにアクセスするための URI スキームは次のとおりです。

```config
wasb://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>
```

この URI スキームは、暗号化なしのアクセス (*wasb*: プレフィックス) と SSL で暗号化されたアクセス (*wasbs*) に対応しています。 同じ Azure リージョン内のデータにアクセスする場合でも、できる限り *wasbs* を使用することをお勧めします。

`<BlobStorageContainerName>` では、Azure Storage 内の BLOB コンテナーの名前を指定します。
`<StorageAccountName>`には、Azure Storage アカウント名を指定します。 完全修飾ドメイン名 (FQDN) を指定する必要があります。

`<BlobStorageContainerName>` と `<StorageAccountName>` のいずれの値も指定されていない場合、既定のファイルシステムが使用されます。 既定のファイル システム上にあるファイルに関しては、相対パスか絶対パスを使用できます。 たとえば、HDInsight クラスターに付属している *hadoop-mapreduce-examples.jar* ファイルは、次のどちらかを使用して確認できます。

```config
wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
wasb:///example/jars/hadoop-mapreduce-examples.jar
/example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> HDInsight バージョン 2.1 クラスターと 1.6 クラスターでは、ファイル名は `hadoop-examples.jar` です。

PATH は、ファイルまたはディレクトリの HDFS パス名です。 Azure Storage のコンテナーにはキーと値のペアが格納されるため、階層ファイル システムは存在しません。 ただし、BLOB キー内でスラッシュ (/) を使用すると、ディレクトリの区切りと見なされます。 たとえば、 *hadoop-mapreduce-examples.jar* に対応する BLOB 名は、次のとおりです。

```bash
example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> HDInsight の外部から BLOB を操作する場合、ほとんどのユーティリティで WASB 形式が認識されず、代わりに `example/jars/hadoop-mapreduce-examples.jar` などの基本的なパス形式が要求されます。

##  <a name="blob-containers"></a>BLOB コンテナー

BLOB を使用するには、まず、[Azure ストレージ アカウント](../storage/common/storage-create-storage-account.md)を作成します。 その一環として、ストレージ アカウントを作成する Azure リージョンを指定します。 クラスターとストレージ アカウントは、同じリージョンに置く必要があります。 Hive メタストア SQL Server データベースと Apache Oozie メタストア SQL Server データベースも、同じリージョンに配置する必要があります。

作成される各 BLOB は、どこにあるとしても、Azure ストレージ アカウント内のコンテナーに属します。 このコンテナーは、HDInsight の外部で作成された既存の BLOB ストレージ コンテナーか、HDInsight クラスター用に作成されたコンテナーであってもかまいません。

既定の BLOB コンテナーには、ジョブ履歴やログなどのクラスター固有の情報が格納されます。 既定の BLOB コンテナーと複数の HDInsight クラスターを共有しないでください。 ジョブ履歴が破損する場合があります。 各クラスターで別のコンテナーを使用し、既定のストレージ アカウントではなく、関連するすべてのクラスターのデプロイメントで指定された、リンクされているストレージ アカウントに共有データを格納することをお勧めします。 リンクされているストレージ アカウントの構成の詳細については、[HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。 ただし、元の HDInsight クラスターを削除した後でも既定のストレージ コンテナーを再利用できます。 HBase クラスターでは、削除された HBase クラスターで使用される既定の BLOB コンテナーを使用して、新しい HBase クラスターを作成することで、HBase テーブルのスキーマとデータを実際に保持できます。

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

## <a name="interacting-with-azure-storage"></a>Azure マシンとの対話

Microsoft では、Azure Storage を操作する次のツールを提供しています。

| ツール | Linux | OS X | Windows |
| --- |:---:|:---:|:---:|
| [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) |✔ |✔ |✔ |
| [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md) |✔ |✔ |✔ |
| [Azure PowerShell](../storage/blobs/storage-quickstart-blobs-powershell.md) | | |✔ |
| [AzCopy](../storage/common/storage-use-azcopy-v10.md) |✔ | |✔ |

## <a name="use-additional-storage-accounts"></a>追加ストレージ アカウントの使用

HDInsight クラスターを作成しているときに、そのクラスターに関連付ける Azure ストレージ アカウントを指定します。 作成プロセス時またはクラスターが作成された後に、このストレージ アカウントに加えて、同じ Azure サブスクリプションか、別の Azure サブスクリプションに属するストレージ アカウントをさらに追加することもできます。 ストレージ アカウントをさらに追加する手順については、[HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページをご覧ください。

> [!WARNING]  
> HDInsight クラスター以外の場所で追加のストレージ アカウントを使用することはできません。

## <a name="next-steps"></a>次の手順

この記事では、HDInsight で HDFS と互換性のある Azure Storage を使う方法について説明しました。 これにより、収集したデータを長期にわたって格納できるスケーラブルなソリューションを構築できます。さらに HDInsight を使用すると、格納されている構造化データと非構造化データから有益な情報を得ることができます。

詳細については、次を参照してください。

* [Azure HDInsight の概要](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Azure Data Lake Storage の概要](../data-lake-store/data-lake-store-get-started-portal.md)
* [HDInsight へのデータのアップロード](hdinsight-upload-data.md)
* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight での Apache Pig の使用](hadoop/hdinsight-use-pig.md)
* [Azure Storage の Shared Access Signature を使用した HDInsight でのデータへのアクセスの制限](hdinsight-storage-sharedaccesssignature-permissions.md)
* [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](hdinsight-hadoop-use-data-lake-storage-gen2.md)