---
title: ストレージ:Azure HDInsight へのオンプレミスの Apache Hadoop の 移行
description: オンプレミスの Apache Hadoop クラスターを Azure HDInsight に移行することについてのストレージのベスト プラクティスについて説明します。
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/10/2019
ms.openlocfilehash: 89a2a659e195afcae336c930a101ed0c0b470cb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715413"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight"></a>オンプレミスの Apache Hadoop クラスターの Azure HDInsight への移行

この記事では、Azure HDInsight システムのデータ ストレージのための推奨事項について説明します。 これは、オンプレミスの Apache Hadoop システムを Azure HDInsight に移行する際に役立つベスト プラクティスを紹介するシリーズの一部です。

## <a name="choose-right-storage-system-for-hdinsight-clusters"></a>HDInsight クラスターのための適切なストレージ システムを選択する

オンプレミスの Apache Hadoop ファイル システム (HDFS) のディレクトリ構造は、Azure Blob Storage または Azure Data Lake Storage 内に再作成できます。 計算に使用される HDInsight クラスターを安全に削除できます。このとき、ユーザー データは失われません。 どちらのサービスも、HDInsight クラスターの既定のファイル システムと追加のファイル システムの両方として使用できます。 HDInsight クラスターとストレージ アカウントは、同じリージョンに置く必要があります。

### <a name="azure-storage"></a>Azure Storage

HDInsight クラスターは Azure Storage 内の blob コンテナーを既定のファイル システムまたは別のファイル システムのいずれかとして使用できます。  Standard レベルのストレージ アカウントは HDInsight クラスターで使用するようにサポートされています。 Premier レベルはサポートされていません。 既定の BLOB コンテナーには、ジョブ履歴やログなどのクラスター固有の情報が格納されます。  1 つの BLOB コンテナーを複数のクラスターの既定のファイル システムとして共有することはサポートされていません。

作成プロセスで定義されたストレージ アカウントとそのそれぞれのキーは、クラスター ノードの `%HADOOP_HOME%/conf/core-site.xml` に格納されます。 これらは Ambari UI の HDFS の構成で「Custom core site (カスタム コア サイト)」セクションからもアクセスできます。 ストレージ アカウント キーは既定で暗号化されており、カスタムの復号スクリプトを使用してキーを復号化してから Hadoop デーモンに渡されます。 Hive、MapReduce、Hadoop ストリーミング、Pig などを含むジョブを利用して、ストレージ アカウントの説明とそのメタデータを伝達できます。

Azure Storage は、geo レプリケーションすることが可能です。 geo レプリケーションにより、災害発生時には別の拠点でデータを回復でき、データの冗長性が高まりますが、geo レプリケーションした別拠点へのフェールオーバーはパフォーマンスに大きな影響を与え、追加コストが発生する可能性もあります。 geo レプリケーションを利用するときは、追加コストがかかっても保護する価値のあるデータかどうかを十分に考慮してください。

次の形式の 1 つを使用して、Azure Storage に格納されているデータにアクセスできます。

|データ アクセス形式 |説明 |
|---|---|
|`wasb:///`|暗号化されていない通信を使用して既定のストレージにアクセスします。|
|`wasbs:///`|暗号化された通信を使用して既定のストレージにアクセスします。|
|`wasb://<container-name>@<account-name>.blob.core.windows.net/`|既定以外のストレージ アカウントを使用して通信するときに使用します。 |

[Standard ストレージ アカウントのスケーラビリティ ターゲット](../../storage/common/scalability-targets-standard-account.md)に関する記事には、Azure Storage アカウントの現在の制限が記載されています。 アプリケーションで必要とされるスケーラビリティが、単一ストレージ アカウントあたりのスケーラビリティ ターゲットを超えている場合、複数のストレージ アカウントを使用し、それらのストレージ アカウント間でデータが分割されるようにアプリケーションを構築できます。

[Azure Storage Analytics](../../storage/common/storage-analytics.md) はすべてのストレージ サービスのためのメトリックを提供し、Azure portal はメトリックを収集してグラフで視覚化するように構成できます。 ストレージ リソース メトリックのしきい値に達したときに通知するアラートを作成できます。

Azure Storage では、[BLOB オブジェクトの論理的な削除](../../storage/blobs/soft-delete-blob-overview.md)が提供されており、アプリケーションまたは他のストレージ アカウント ユーザーによって誤って変更または削除された場合に、データを復旧できます。

[blob スナップショット](/rest/api/storageservices/creating-a-snapshot-of-a-blob)を作成することができます。 スナップショットは、ある時点で作成された読み取り専用の BLOB で、BLOB をバックアップする手段を提供します。 作成されたスナップショットは、読み取り、コピー、削除はできますが、変更はできません。

> [!Note]
> 「wasbs」証明書がない以前のバージョンのオンプレミスの Hadoop ディストリビューションでは、証明書を Java トラストストアにインポートする必要があります。

以下の方法を使用して、証明書を Java トラストストアにインポートできます。

Azure Blob の TLS/SSL 証明書をファイルにダウンロードします

```bash
echo -n | openssl s_client -connect <storage-account>.blob.core.windows.net:443 | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > Azure_Storage.cer
```

上記のファイルをすべてのノードの Java トラストストアにインポートします

```bash
keytool -import -trustcacerts -keystore /path/to/jre/lib/security/cacerts -storepass changeit -noprompt -alias blobtrust -file Azure_Storage.cer
```

追加された証明書がトラストストアにあることを確認します

```bash
keytool -list -v -keystore /path/to/jre/lib/security/cacerts
```

詳細については、次の記事を参照してください。

- [Azure HDInsight クラスターで Azure Storage を使用する](../hdinsight-hadoop-use-blob-storage.md)
- [Standard ストレージ アカウントのスケーラビリティ ターゲット](../../storage/common/scalability-targets-standard-account.md)
- [Blob ストレージのスケーラビリティおよびパフォーマンス ターゲット](../../storage/blobs/scalability-targets.md)
- [Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](../../storage/blobs/storage-performance-checklist.md)
- [Microsoft Azure Storage の監視、診断、およびトラブルシューティング](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md)
- [Azure Portal でのストレージ アカウントの監視](../../storage/common/manage-storage-analytics-logs.md)

### <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

Azure Data Lake Storage Gen1 には、HDFS と POSIX スタイルのアクセス制御モデルが実装されます。 これにより、きめ細やかなアクセス制御のための Azure AD との最高クラスの統合が提供されます。 格納できるデータのサイズや、大量の並行分析を実行するための機能の制限はありません。

詳細については、次の記事を参照してください。

- [Azure portal を使用して、Data Lake Storage Gen1 を使用する HDInsight クラスターを作成する](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)
- [Azure HDInsight クラスターで Data Lake Storage Gen1 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

### <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 は、最新のストレージ製品です。 これによって、Azure Blob Storage に直接統合された Hadoop 互換ファイル システム エンドポイントに、Azure Data Lake Storage Gen1 の第 1 世代からのコア機能が統合されます。 この機能強化は、通常はオンプレミスのファイル システムにのみ関連付けされている信頼性とパフォーマンスに、オブジェクト ストレージのスケールとコスト上のメリットを結合したものです。

Azure Data Lake Storage Gen 2 は、[Azure Blob Storage](../../storage/blobs/storage-blobs-introduction.md) に基づいて構築され、ファイル システムとオブジェクト ストレージの両方のパラダイムを使用して、データと連携させることができます。 ファイル システム セマンティクス、ファイル レベルのセキュリティおよびスケールなど、[Azure Data Lake Storage Gen1](../../data-lake-store/index.yml) に由来する機能は、[Azure Blob ストレージ](../../storage/blobs/storage-blobs-introduction.md)の低コストの階層型記憶域、高可用性/ディザスター リカバリー機能、および SDK/ツール エコシステムと組み合わされています。 Data Lake Storage Gen2 では、すべてのオブジェクト ストレージの品質を備えたまま、分析ワークロード用に最適化されたファイル システム インターフェイスの利点が追加されています。

Data Lake Storage Gen2 の基本的な機能は、パフォーマンスの高いデータ アクセスのために、オブジェクト/ファイルをディレクトリの階層に編成した Blob ストレージ サービスに、[階層構造の名前空間](../../storage/blobs/data-lake-storage-namespace.md)を追加することです。  階層構造により、ディレクトリの名前変更や削除などの操作は、ディレクトリ名のプレフィックスを共有するすべてのオブジェクトを列挙して処理するのではなく、ディレクトリ上の単一のアトミック メタデータの操作になります。

以前は、パフォーマンス、管理、およびセキュリティの領域では、クラウドベース分析は妥協する必要がありました。 Azure Data Lake Storage (ADLS) Gen2 の主要機能は次のとおりです。

- **Hadoop と互換性のあるアクセス**:Azure Data Lake Storage Gen2 では、[Hadoop 分散ファイル システム (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) を利用する場合と同様に、データの管理およびアクセスを可能にします。 新しい [ABFS ドライバー](../../storage/blobs/data-lake-storage-abfs-driver.md)は、[Azure HDInsight](../index.yml) 内に含まれているすべての Apache Hadoop 環境内で使用できます。 このドライバーにより、Data Lake Storage Gen2 に格納されているデータにアクセスできます。

- **POSIX アクセス許可のスーパーセット**:Data Lake Gen2 のセキュリティ モデルは、ACL および POSIX のアクセス許可に加え、Data Lake Storage Gen2 固有の追加設定を完全サポートしています。 設定は、管理ツールを通じて構成することもできますし、Hive や Spark などのフレームワークを通じて構成することもできます。

- **コスト効率**:Data Lake Storage Gen2 は、低コストのストレージ容量とトランザクションを備えています。 ライフサイクル全体を通してデータが移行されるにつれて、[Azure Blob ストレージ ライフサイクル](../../storage/blobs/storage-lifecycle-management-concepts.md)などの組み込み機能を使用してコストを最小限に抑えるために課金レートが変化します。

- **BLOB ストレージ ツール、フレームワーク、およびアプリの操作**:Data Lake Storage Gen2 では、BLOB ストレージ用に今日存在するさまざまなツール、フレームワーク、およびアプリケーションを継続的に利用できます。

- **最適化されたドライバー**:Azure Blob ファイル システム ドライバー (ABFS) は、ビッグ データ分析用に [特別に最適化されています](../../storage/blobs/data-lake-storage-abfs-driver.md)。 該当する REST API は、dfs エンドポイント、dfs.core.windows.net を介して表示されます。

次の形式の 1 つを使用して、ADLS Gen2 に格納されているデータにアクセスできます。
- `abfs:///`:クラスターの既定の Data Lake Storage にアクセスします。
- `abfs://file_system@account_name.dfs.core.windows.net`:既定以外の Data Lake Storage と通信するときに使用します。

詳細については、次の記事を参照してください。

- [Azure Data Lake Storage Gen2 の概要](../../storage/blobs/data-lake-storage-introduction.md)
- [Azure BLOB ファイル システム ドライバー (ABFS.md)](../../storage/blobs/data-lake-storage-abfs-driver.md)
- [Azure HDInsight クラスターで Azure Data Lake Storage Gen2 を使用する](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

## <a name="secure-azure-storage-keys-within-on-premises-hadoop-cluster-configuration"></a>オンプレミスの Hadoop クラスター構成内の Azure Storage キーをセキュリティ保護する

Hadoop 構成ファイルに追加された Azure Storage キーは、オンプレミスの HDFS と Azure Blob Storage 間に接続を確立します。 これらのキーは、Hadoop の資格情報プロバイダー フレームワークを使用して暗号化することで保護できます。 暗号化されると、それらを安全に格納してアクセスできます。

**資格情報をプロビジョニングするには:**

```bash
hadoop credential create fs.azure.account.key.account.blob.core.windows.net -value <storage key> -provider jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks/file
```

**上記のプロバイダー パスを core-site.xml またはカスタムのコアサイトの下の Ambari 構成に追加するには:**

```xml
<property>
    <name>hadoop.security.credential.provider.path</name>
        <value>
        jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks
        </value>
    <description>Path to interrogate for protected credentials.</description>
</property>
```

> [!Note]
> プロバイダー パス プロパティは、core-site.xml のクラスター レベルでキーを格納する代わりに、次のように distcp コマンド ラインに追加することもできます。

```bash
hadoop distcp -D hadoop.security.credential.provider.path=jceks://hdfs@headnode.xx.internal.cloudapp.net/path/to/jceks /user/user1/ wasb:<//yourcontainer@youraccount.blob.core.windows.net/>user1
```

## <a name="restrict-azure-storage-data-access-using-sas"></a>SAS を使用して Azure Storage データ アクセスを制限する

HDInsight には、既定の状態で、クラスターに関連付けられた Azure Storage アカウントのデータへのフル アクセス権があります。 blob コンテナーの Shared Access Signatures (SAS) を使用して、データへの読み取り専用アクセスをユーザーに提供するなど、データへのアクセスを制限できます。

### <a name="using-the-sas-token-created-with-python"></a>Python で作成された SAS トークンを使用する

1. [SASToken.py](https://github.com/Azure-Samples/hdinsight-dotnet-python-azure-storage-shared-access-signature/blob/master/Python/SASToken.py) ファイルを開き、次の値を変更します。

    |トークンのプロパティ|説明|
    |---|---|
    |policy_name|作成する保存済みのポリシーに使用する名前。|
    |storage_account_name|ご利用のストレージ アカウントの名前。|
    |storage_account_key|ストレージ アカウントのキー。|
    |storage_container_name|アクセスを制限するストレージ アカウントのコンテナー。|
    |example_file_path|コンテナーにアップロードされるファイルのパス。|

2. SASToken.py ファイルには、`ContainerPermissions.READ + ContainerPermissions.LIST` のアクセス許可が付いており、ユース ケースに基づいて調整できます。

3. スクリプトを次のように実行します: `python SASToken.py`

4. スクリプトが完了すると、次のテキストのような SAS トークンが表示されます。`sr=c&si=policyname&sig=dOAi8CXuz5Fm15EjRUu5dHlOzYNtcK3Afp1xqxniEps%3D&sv=2014-02-14`

5. Shared Access Signature によるコンテナーへのアクセスを制限するために、Ambari HDFS Configs Advanced Custom core-site Add プロパティの下のクラスターのコアサイト構成にカスタム エントリを追加します。

6. **[キー]** と **[値]** フィールドに、次の値を使用します。

    **[キー]** :`fs.azure.sas.YOURCONTAINER.YOURACCOUNT.blob.core.windows.net` **[値]** :上記の手順 4 で Python アプリケーションから返された SAS キー。

7. **[追加]** ボタンをクリックしてこのキーと値を保存し、 **[保存]** ボタンをクリックして構成の変更を保存します。 プロンプトが表示されたら、変更の説明 (「SAS ストレージ アクセスの追加」など) を追加し、 **[保存]** をクリックします。

8. Ambari Web UI で、左側の一覧から [HDFS] を選択して、右側の [サービス アクション] ドロップダウン リストから **[すべて再起動]** を選択します。 メッセージが表示されたら、 **[Confirm Restart All]\(すべての再起動の確認\)** を選択します。

9. この手順を MapReduce2 と YARN に対して繰り返します。

Azure での SAS トークンの使用に関して、3 つの重要なことを覚えておく必要があります。

1. 「READ + LIST」アクセス許可を持つ SAS トークンが作成されると、その SAS トークンを使用して Blob コンテナーにアクセスするユーザーは、データを「書き込みおよび削除」できません。 その SAS トークンを使用して Blob コンテナーにアクセスし、書き込みまたは削除の操作を試行すると、`"This request is not authorized to perform this operation"` のようなメッセージが表示されます。

2. SAS トークンが、`READ + LIST + WRITE` のアクセス許可で生成された場合 (`DELETE` のみ制限する)、`hadoop fs -put` のようなコマンドは、最初に `\_COPYING\_` ファイルに記述し、後でファイルの名前を変更しようとします。 この HDFS 操作は、WASB の `copy+delete` にマップします。 `DELETE` アクセス許可が提供されなかったため、「プット」は失敗します。 `\_COPYING\_` 操作は、いくつかの同時実行制御を提供する Hadoop です。 現在、「書き込み」操作に影響を与えずに、「削除」操作だけを制限する方法はありません。

3. 残念ながら、Hadoop の資格情報プロバイダーおよび復号化キー プロバイダー (ShellDecryptionKeyProvider) は現在、SAS トークンと一緒に動作しないため、現時点では可視性から保護できません。

「[Azure Storage の Shared Access Signature を使用して HDInsight でのデータへのアクセスを制限する](../hdinsight-storage-sharedaccesssignature-permissions.md)」をご覧ください。

## <a name="use-data-encryption-and-replication"></a>データの暗号化およびレプリケーションを使用する

Azure Storage に書き込まれるすべてのデータは、[Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md) を使用して自動的に暗号化されます。 Azure Storage アカウント内のデータは、高可用性のため常にレプリケートされます。  ストレージ アカウントを作成するときは、次のレプリケーション オプションのいずれかを選択できます。

- [ローカル冗長ストレージ (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage)
- [ゾーン冗長ストレージ (ZRS)](../../storage/common/storage-redundancy.md#zone-redundant-storage)
- [geo 冗長ストレージ (GRS)](../../storage/common/storage-redundancy.md#geo-redundant-storage)
- [読み取りアクセス geo 冗長ストレージ (RA-GRS)](../../storage/common/storage-redundancy.md)

Azure Storage によってローカル冗長ストレージ (LRS) が提供されますが、ディザスター リカバリー プランのニーズに合った頻度で、別のリージョン内の別の Azure Storage アカウントに重要なデータをコピーする必要もあります。 [ADLCopy](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)、[DistCp](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)、[Azure PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md)、[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md) など、データをコピーするさまざまな方法があります。  また、誤って削除されないように、Azure Storage アカウントのアクセス ポリシーを適用することもお勧めします。

詳細については、次の記事を参照してください。

- [Azure Storage のレプリケーション](../../storage/common/storage-redundancy.md)
- [Azure Data Lake Storage Gen1 (ADLS) 向けのディザスター ガイダンス](../../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

## <a name="attach-additional-azure-storage-accounts-to-cluster"></a>クラスターに Azure Storage アカウントを追加する

HDInsight の作成プロセス時に、既定のファイル システムとして Azure Storage アカウント、Azure Data Lake Storage Gen1、または Azure Data Lake Storage Gen2 が選択されます。 クラスター作成プロセス時またはクラスターが作成された後に、この既定のストレージ アカウントに加えて、同じ Azure サブスクリプションか、別の Azure サブスクリプションからストレージ アカウントをさらに追加することもできます。

次のいずれかの方法でストレージ アカウントを追加できます。
- Ambari HDFS Config Advanced Custom core-site Add the storage Account Name を指定し、サービスの再起動を指定します
- ストレージ アカウントの名前とキーを渡して[スクリプト アクション](../hdinsight-hadoop-add-storage.md)を使用する

> [!Note]
> 有効なユース ケースでは、[Azure サポート](https://azure.microsoft.com/support/faq/)への要求を使用して Azure ストレージの制限を増やすことができます。

詳細については、「[HDInsight にストレージ アカウントを追加する](../hdinsight-hadoop-add-storage.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このシリーズの次の記事をお読みください。[オンプレミスから Azure HDInsight Hadoop への移行のためのデータ移行のベスト プラクティス](apache-hadoop-on-premises-migration-best-practices-data-migration.md)。