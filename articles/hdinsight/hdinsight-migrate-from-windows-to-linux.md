---
title: Windows ベースの HDInsight から Linux ベースの HDInsight への移行 - Azure
description: Windows ベースの HDInsight クラスターから Linux ベースの HDInsight クラスターへの移行方法について説明します。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: hrasheed
ms.openlocfilehash: ec40c587ea4c609b1f8201e9860eb0e98e06ec62
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448191"
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Windows ベースの HDInsight クラスターから Linux ベースのクラスターへの移行

このドキュメントでは、Windows および Linux 上の HDInsight の違いについて詳しく説明します。 また、既存のワークロードを Linux ベースのクラスターに移行する方法についてのガイダンスも提供します。

Windows ベースの HDInsight はクラウドで Apache Hadoop を使用する簡単な方法を提供しますが、Linux ベースのクラスターへの移行が必要になる場合もあります。 たとえば、ソリューションに必要な Linux ベースのツールとテクノロジを活用したい場合などです。 Hadoop エコシステムの多くの要素は Linux ベース システムで開発されており、Windows ベースの HDInsight で使用できない場合があります。 多くの書籍、ビデオ、およびその他のトレーニング資料では、Hadoop の操作時に Linux システムを使用することを前提としています。

> [!NOTE]  
> HDInsight クラスターは、クラスター内のノードのオペレーティング システムとして Ubuntu の長期サポート (LTS) を使用します。 HDInsight で使用可能な Ubuntu のバージョンの詳細や他のコンポーネントのバージョン情報については、[HDInsight コンポーネントのバージョンに関する記事](hdinsight-component-versioning.md)を参照してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migration-tasks"></a>移行タスク

移行の一般的なワークフローは次のとおりです。

![移行のワークフロー図](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. このドキュメントの各セクションを読んで、移行時に必要になる場合がある変更について理解します。

2. テスト/品質保証環境として、Linux ベースのクラスターを作成します。 Linux ベースのクラスターの作成の詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

3. 既存のジョブ、データ ソース、およびシンクを新しい環境にコピーします。

4. 検証テストを実行し、新しいクラスターで予期したとおりにジョブが動作していることを確認します。

すべて予期したとおりに動作していることを確認したら、移行のダウンタイムをスケジュールします。 このダウンタイム中に、次の操作を実行します。

1. クラスター ノードでローカルに格納されている一時的なデータをバックアップします。 たとえば、ヘッド ノードに直接データを格納している場合です。

2. Windows ベースのクラスターを削除します。

3. Windows ベースのクラスターで使用したものと同じ既定のデータ ストアを使用して、Linux ベースのクラスターを作成します。 これで、Linux ベースのクラスターで既存の運用データを引き続き使用できます。

4. バックアップしたすべての一時的なデータをインポートします。

5. 新しいクラスターを使用して、ジョブを開始または処理を続行します。

### <a name="copy-data-to-the-test-environment"></a>テスト環境にデータをコピーする

データやジョブをコピーする方法は多くありますが、このセクションで説明する最も簡単な方法では、ファイルを直接テスト クラスターに移動します。

#### <a name="hdfs-copy"></a>HDFS のコピー

以下の手順を使用して、運用環境クラスターからテスト クラスターにデータをコピーします。 この手順では、HDInsight に付属している `hdfs dfs` ユーティリティを使用します。

1. 既存のクラスターのストレージ アカウントと既定のコンテナー情報を見つけます。 次の例では、PowerShell を使用してこの情報を取得します。

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. HDInsight ドキュメントに記載された Linux ベース クラスターの作成手順に従ってテスト環境を作成します。 クラスターを作成する前に、 **[オプションの構成]** を選択します。

3. [オプションの構成] セクションで、**[リンクされたストレージ アカウント]** を選択します。

4. **[ストレージ キーを追加]** を選択し、メッセージが表示されたら、手順 1. で PowerShell スクリプトによって返されたストレージ アカウントを選択します。 各セクションで **[選択]** をクリックします。 最後に、クラスターを作成します。

5. クラスターが作成されたら、**SSH** を使用して接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

6. SSH セッションで次のコマンドを使用して、リンクされたストレージ アカウントから新しい既定のストレージ アカウントにファイルをコピーします。 CONTAINER を、PowerShell によって返されたコンテナーの情報に置き換えます。 __ACCOUNTNAME__ を、アカウント名に置き換えます。 データへのパスをデータ ファイルへのパスに置き換えます。

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]  
    > テスト環境にデータを格納するディレクトリ構造が存在しない場合は、次のコマンドを使用して作成できます。

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    `-p` スイッチで、パス内のすべてのディレクトリを作成できます。

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Azure Storage の BLOB 間の直接コピー

`Start-AzStorageBlobCopy` Azure PowerShell コマンドレットを使用して、HDInsight 外のストレージ アカウント間で BLOB をコピーすることもできます。 詳細については、「Azure Storage での Azure PowerShell の使用」の「Azure BLOB の管理方法」セクションを参照してください。

## <a name="client-side-technologies"></a>クライアント側のテクノロジ

[Azure PowerShell コマンドレット](/powershell/azureps-cmdlets-docs)、[Azure クラシック CLI](../cli-install-nodejs.md)、[.NET SDK for Apache Hadoop](https://hadoopsdk.codeplex.com/) などのクライアント側のテクノロジでは、Linux ベースのクラスターを引き続き使用できます。 これらのテクノロジは、両方のクラスター OS の種類で同じ REST API を使用します。

## <a name="server-side-technologies"></a>サーバー側のテクノロジ

次の表では、Windows 固有のサーバー側コンポーネントの移行に関するガイダンスを示します。

| 使用するテクノロジ | 実行するアクション |
| --- | --- |
| **PowerShell** (クラスターの作成時に使用されるスクリプトを含む、サーバー側スクリプト) |Bash スクリプトを書き直します。 スクリプト アクションについては、「[Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」および「[HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」をご覧ください。 |
| **Azure クラシック CLI** (サーバー側スクリプト) |Azure クラシック CLI は Linux で使用可能ですが、HDInsight クラスター ヘッド ノードにはプレインストールされません。 Azure クラシック CLI のインストールの詳細については、[Azure クラシック CLI の概要](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)に関するページをご覧ください。 |
| **.NET コンポーネント** |.Net は、[Mono](https://mono-project.com) を使用した Linux ベースの HDInsight でサポートされています。 詳細については、「[Migrate .NET solutions to Linux-based HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)」 (Linux ベースの HDInsight に .NET ソリューションを移行する) を参照してください。 |
| **Win32 コンポーネントまたはその他の Windows 専用のテクノロジ** |ガイダンスはコンポーネントやテクノロジによって異なります。 Linux と互換性があるバージョンを確認できます。 ない場合は、代わりの解決策を見つけるか、このコンポーネントを再生成する必要があります。 |

> [!IMPORTANT]  
> HDInsight 管理 SDK は Mono と完全な互換性はありません。 HDInsight クラスターにデプロイされているソリューションに組み込んで使用しないでください。

## <a name="cluster-creation"></a>クラスターの作成

このセクションでは、クラスターの作成における違いについて説明します。

### <a name="ssh-user"></a>SSH ユーザー

Linux ベースの HDInsight クラスターでは **Secure Shell (SSH)** プロトコルを使用して、クラスター ノードにリモート アクセスできます。 Windows ベースのクラスターのリモート デスクトップとは異なり、ほとんどの SSH クライアントはグラフィカル ユーザー エクスペリエンスを提供しません。 代わりに、SSH クライアントは、クラスター上でコマンドを実行できるコマンド ラインを提供します。 一部のクライアント ([MobaXterm](https://mobaxterm.mobatek.net/)など) は、リモート コマンドラインに加え、グラフィカル ファイル システム ブラウザーを提供します。

クラスターの作成時に、SSH ユーザーと、認証用の**パスワード**または**公開キー証明書**を指定する必要があります。

パスワードを使用するよりも安全であるため、公開キー証明書を使用することをお勧めします。 証明書認証は署名済みの公開キーと秘密キーのペアを生成してから、クラスターの作成時に公開キーを指定することで機能します。 SSH を使用してサーバーに接続するときに、クライアントの秘密キーで接続を認証します。

詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

### <a name="cluster-customization"></a>クラスターのカスタマイズ

**スクリプト アクション** は、Bash スクリプトに記述する必要があります。 Linux ベースのクラスターでは、クラスターのインストール中または作成後にスクリプト アクションを使用できます。 詳細については、「[Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」および「[HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」をご覧ください。

他のカスタマイズ機能として **ブートストラップ**があります。 Windows クラスターの場合、この機能により、Hive で使用する追加ライブラリの場所を指定できます。 クラスターの作成後にこれらのライブラリは自動的に Hive クエリで使用できるようになるため、 `ADD JAR`を使用する必要はありません。

Linux ベースのクラスターのブートストラップ機能では、この機能は提供されません。 代わりに、「[HDInsight クラスター作成時の Apache Hive ライブラリの追加](hdinsight-hadoop-add-hive-libraries.md)」に記載されているスクリプト アクションを使用します。

### <a name="virtual-networks"></a>仮想ネットワーク

Windows ベースの HDInsight クラスターは従来の仮想ネットワークでのみ動作しますが、Linux ベースの HDInsight クラスターにはリソース マネージャーの仮想ネットワークが必要になります。 Linux ベースの HDInsight クラスターを接続する必要がある従来の Virtual Network にリソースがある場合は、「 [従来の VNet を新しい VNet に接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)」を参照してください。

構成要件の詳細については、[Virtual Network を使用した HDInsight の機能の拡張](hdinsight-extend-hadoop-virtual-network.md)に関するドキュメントをご覧ください。

## <a name="management-and-monitoring"></a>管理と監視

ジョブ履歴 UI や Yarn UI など、Windows ベースの HDInsight で使用した可能性のある Web UI の多くは、Apache Ambari で使用できます。 また、Ambari Hive View で、Web ブラウザーを使用して Hive クエリを実行することができます。 Ambari Web UI は、 https://CLUSTERNAME.azurehdinsight.net の Linux ベースのクラスターで利用できます。

Ambari の操作の詳細については、次のドキュメントを参照してください。

* [Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari のアラート

Ambari には、クラスターに関する潜在的な問題を通知できるアラート システムがあります。 Ambari Web UI ではアラートは赤または黄色のエントリとして表示されますが、REST API を使用して取得することもできます。

> [!IMPORTANT]  
> Ambari アラートは問題が*ある可能性のある*ことを示すものであり、問題が*ある*ことを示すものではありません。 たとえば、HiveServer2 に通常どおりアクセスできる場合でも、アクセスできないことを示すアラートを受け取ることがあります。
>
> 多くのアラートがサービスに対する一定間隔のクエリとして実装され、特定の期間内での応答を予期します。 したがって、アラートは必ずしもサービスが停止していることを意味するわけでなく、単に予期した期間内に結果が返されなかったことを意味します。

## <a name="file-system-locations"></a>ファイル システムの場所

Linux クラスターのファイル システムは、Windows ベースの HDInsight クラスターとは異なる方法で配置されます。 一般的に使用されるファイルを検索するには、以下の表を使用します。

| 検索対象 | 配置場所 |
| --- | --- |
| 構成 |`/etc` たとえば、 `/etc/hadoop/conf/core-site.xml` |
| ログ ファイル |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`。ここには 2 つのディレクトリがあります。つまり、現在の HDP バージョンと `current` です。 `current`ディレクトリには、バージョン番号ディレクトリにあるファイルとディレクトリへのシンボリック リンクが含まれています。 バージョン番号は HDP バージョンの更新時に変更されるため、`current` ディレクトリは HDP ファイルにアクセスするための便利な方法として提供されます。 |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

通常、ファイル名がわかっている場合は、SSH セッションから次のコマンドを使用してファイル パスを検索することができます。

    find / -name FILENAME 2>/dev/null

ファイル名にワイルドカードを使用することもできます。 たとえば、`find / -name *streaming*.jar 2>/dev/null` の場合、ファイル名の一部として 'streaming' という単語を含む jar ファイルへのパスが返されます。

## <a name="apache-hive-apache-pig-and-mapreduce"></a>Apache Hive、Apache Pig、および MapReduce

Linux ベースのクラスターでは、Pig と MapReduce のワークロードは似ています。 ただし、Linux ベースの HDInsight クラスターは、Hadoop、Hive 、Pig の新しいバージョンを使用して作成できます。 これらのバージョンの違いにより、既存のソリューションの機能が変わる場合があります。 HDInsight に付属するコンポーネントのバージョンの詳細については、「[HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md)」を参照してください。

Linux ベースの HDInsight では、リモート デスクトップ機能は提供されません。 代わりに、SSH を使用して、クラスターのヘッド ノードにリモート接続できます。 詳細については、以下のドキュメントをご覧ください。

* [SSH での Apache Hive の使用](hdinsight-hadoop-use-hive-ssh.md)
* [SSH での Apache Pig の使用](hadoop/apache-hadoop-use-pig-ssh.md)
* [SSH での MapReduce の使用](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]  
> 外部の Hive メタストアを使用する場合は、Linux ベースの HDInsight で使用する前にメタストアをバックアップする必要があります。 Linux ベースの HDInsight は、新しいバージョンの Hive で利用できますが、以前のバージョンで作成されたメタストアと互換性がない場合があります。

次のグラフでは、Hive ワークロードの移行に関するガイダンスを示します。

| Windows ベースの場合 | Linux ベースの場合 |
| --- | --- |
| **Hive エディター** |[Ambari の Apache Hive ビュー](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` を使用して Tez を有効にします。 |Apache Tez は Linux ベースのクラスターの既定の実行エンジンであるため、set ステートメントは不要になります。 |
| C# ユーザー定義関数 | Linux ベースの HDInsight での C# コンポーネントの検証の詳細については、「[Migrate .NET solutions to Linux-based HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)」 (Linux ベースの HDInsight に .NET ソリューションを移行する) を参照してください。 |
| サーバーで、Hive ジョブの一部として呼び出される CMD ファイルまたはスクリプトを使用します。 |Bash スクリプトを使用します。 |
| `hive` コマンドを使用します。 |[Apache Hive Beeline](hadoop/apache-hadoop-use-hive-beeline.md) または [SSH セッションから Apache Hive](hdinsight-hadoop-use-hive-ssh.md) を使用します |

### <a name="pig"></a>Pig

| Windows ベースの場合 | Linux ベースの場合 |
| --- | --- |
| C# ユーザー定義関数 | Linux ベースの HDInsight での C# コンポーネントの検証の詳細については、「[Migrate .NET solutions to Linux-based HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)」 (Linux ベースの HDInsight に .NET ソリューションを移行する) を参照してください。 |
| Pig ジョブの一部として呼び出されるサーバー上の CMD ファイルまたはスクリプト |Bash スクリプトを使用します。 |

### <a name="mapreduce"></a>MapReduce

| Windows ベースの場合 | Linux ベースの場合 |
| --- | --- |
| C# マッパーとレジューサのコンポーネント | Linux ベースの HDInsight での C# コンポーネントの検証の詳細については、「[Migrate .NET solutions to Linux-based HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)」 (Linux ベースの HDInsight に .NET ソリューションを移行する) を参照してください。 |
| サーバーで、Hive ジョブの一部として呼び出される CMD ファイルまたはスクリプトを使用します。 |Bash スクリプトを使用します。 |

## <a name="apache-oozie"></a>Apache Oozie

> [!IMPORTANT]  
> 外部の Oozie メタストアを使用する場合は、Linux ベースの HDInsight で使用する前にメタストアをバックアップする必要があります。 Linux ベースの HDInsight は、新しいバージョンの Oozie で利用できますが、以前のバージョンで作成されたメタストアと互換性がない場合があります。

Oozie ワークフローでは、シェルの操作が可能です。 シェルの操作では、オペレーティング システムの既定のシェルを使用して、コマンド ライン コマンドを実行します。 Windows シェルを使用する Oozie ワークフローがある場合は、Linux シェル環境 (Bash) を使用するワークフローを書き換える必要があります。 Oozie でのシェルの操作の使用の詳細については、「[Oozie shell action extension](https://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html)」 (Oozie シェル操作の拡張) を参照してください。

C# アプリケーションを使用するワークフローを使用している場合は、Linux 環境でこれらのアプリケーションを検証します。 詳細については、「[Migrate .NET solutions to Linux-based HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md)」 (Linux ベースの HDInsight に .NET ソリューションを移行する) を参照してください。

## <a name="storm"></a>Storm

| Windows ベースの場合 | Linux ベースの場合 |
| --- | --- |
| Storm ダッシュボード |Storm ダッシュ ボードは使用できません。 トポロジの送信方法については、「[Linux ベースの HDInsight での Apache Storm トポロジのデプロイと管理](storm/apache-storm-deploy-monitor-topology-linux.md)」を参照してください |
| Storm UI |Storm UI は、 https://CLUSTERNAME.azurehdinsight.net/stormui は利用できます。 |
| Visual Studio を使用して、C# またはハイブリッド トポロジを作成、デプロイ、および管理します。 |HDInsight 上の Linux ベースの Storm で、C# (SCP.NET) またはハイブリッド トポロジの作成、デプロイおよび管理に Visual Studio を使用できます。 2016年 10 月 28 日より後に作成されたクラスターでのみ使用できます。 |

## <a name="apache-hbase"></a>Apache HBase

Linux ベースのクラスターでは、HBase の znode の親は `/hbase-unsecure`です。 この値を、ネイティブの HBase Java API を使用するすべての Java クライアント アプリケーションの構成で設定する必要があります。

この値を設定するクライアント例については、[Java ベースの Apache HBase アプリケーションの構築](hbase/apache-hbase-build-java-maven-linux.md)に関するページを参照してください。

## <a name="spark"></a>Spark

プレビュー期間中は、Windows クラスターで Spark クラスターを使用できました。 Spark GA は、Linux ベースのクラスターでのみ使用できます。 プレビュー版の Windows ベースの Spark クラスターからリリース版の Linux ベースの Spark クラスターへの移行パスはありません。

## <a name="known-issues"></a>既知の問題

### <a name="azure-data-factory-custom-net-activities"></a>Azure Data Factory カスタム .NET アクティビティ

現在、Azure Data Factory カスタム .NET アクティビティは Linux ベースの HDInsight クラスターでサポートされていません。 代わりに、以下のいずれかの方法を使用して、ADF パイプラインの一部としてカスタム アクティビティを実装する必要があります。

* Azure Batch プールで .NET アクティビティを実行します。 「 [Azure Data Factory パイプラインでカスタム アクティビティを使用する](../data-factory/transform-data-using-dotnet-custom-activity.md)
* MapReduce アクティビティとしてアクティビティを実装します。 詳細については、「[Data Factory から MapReduce プログラムを起動する](../data-factory/transform-data-using-hadoop-map-reduce.md)」をご覧ください。

### <a name="line-endings"></a>行の終わり

通常、Windows ベース システムの行の終わりには CRLF を使用しますが、Linux ベース システムでは LF を使用します。 LF を使用するには、既存のデータ プロデューサーとデータ コンシューマーの変更が必要になる場合があります。

たとえば、Azure PowerShell を使用して Windows ベース クラスター上の HDInsight に対してクエリを実行すると、CRLF のデータが返されます。 Linux ベースのクラスターで同じクエリを実行すると、LF が返されます。 Linux ベースのクラスターに移行する前に、行の終わりによってソリューションで問題が発生しないかどうかを調べます。

クラスター ノードで実行するスクリプトの行の終わりに必ず LF を使用します。 CRLF を使用すると、Linux ベースのクラスターでスクリプトを実行するときにエラーが発生する可能性があります。

スクリプトに CR 文字が埋め込まれた文字列が含まれていないことがわかっている場合は、以下のいずれかの方法を使用して行の終わりを一括変更することができます。

* **クラスターにアップロードする前**:次の PowerShell ステートメントを使用して、クラスターにスクリプトをアップロードする前に、行の終わりを CRLF から LF に変更します。

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **クラスターにアップロードした後**:SSH セッションから Linux ベースのクラスターに次のコマンドを使用して、スクリプトを変更します。

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>次の手順

* [Linux ベースの HDInsight クラスターを作成する方法を確認する](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH を使用して HDInsight に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Apache Ambari を使用して Linux ベースのクラスターを管理する](hdinsight-hadoop-manage-ambari.md)
