---
title: "Windows ベースの HDInsight から Linux ベースの HDInsight への移行 | Microsoft Docs"
description: "Windows ベースの HDInsight クラスターから Linux ベースの HDInsight クラスターへの移行方法について説明します。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/13/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 9a2e9b7809b00ae2b60e152bc240ab2ef775c7bf
ms.lasthandoff: 03/25/2017


---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Windows ベースの HDInsight クラスターから Linux ベースのクラスターへの移行
Windows ベースの HDInsight はクラウドで Hadoop を使用する簡単な方法を提供しますが、Linux ベースのクラスターへの移行が必要になる場合もあります。 たとえば、ソリューションに必要な Linux ベースのツールとテクノロジを活用したい場合などです。 Hadoop エコシステムの多くの要素は Linux ベース システムで開発されており、Windows ベースの HDInsight で使用できない場合があります。 さらに、多くの書籍、ビデオ、およびその他のトレーニング資料では、Hadoop の操作時に Linux システムを使用することを前提としています。

このドキュメントでは、Windows と Linux での HDInsight の違いについて詳しく説明し、Linux ベースのクラスターに既存のワークロードを移行する方法に関するガイダンスを示します。

> [!NOTE]
> HDInsight クラスターは、クラスター内のノードのオペレーティング システムとして Ubuntu の長期サポート (LTS) を使用します。 HDInsight で使用可能な Ubuntu のバージョンの詳細や他のコンポーネントのバージョン情報については、[HDInsight コンポーネントのバージョンに関する記事](hdinsight-component-versioning.md)を参照してください。

## <a name="migration-tasks"></a>移行タスク
移行の一般的なワークフローは次のとおりです。

![移行のワークフロー図](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. このドキュメントの各セクションを読んで、既存のワークフローやジョブなどを Linux ベースのクラスターに移行するときに必要になる場合がある変更について理解します。
2. テスト/品質保証環境として、Linux ベースのクラスターを作成します。 Linux ベースのクラスターの作成の詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
3. 既存のジョブ、データ ソース、およびシンクを新しい環境にコピーします。
4. 検証テストを実行し、新しいクラスターで予期したとおりにジョブが動作していることを確認します。

すべて予期したとおりに動作していることを確認したら、移行のダウンタイムをスケジュールします。 このダウンタイム中に、次の操作を実行します。

1. クラスター ノードでローカルに格納されている一時的なデータをバックアップします。 たとえば、ヘッド ノードに直接データを格納している場合です。
2. Windows ベースのクラスターを削除します。
3. Windows ベースのクラスターで使用したものと同じ既定のデータ ストアを使用して、Linux ベースのクラスターを作成します。 これで、新しいクラスターで既存の運用データを引き続き使用できます。
4. バックアップしたすべての一時的なデータをインポートします。
5. 新しいクラスターを使用して、ジョブを開始または処理を続行します。

### <a name="copy-data-to-the-test-environment"></a>テスト環境にデータをコピーする
データやジョブをコピーする方法は多くありますが、このセクションで説明する最も簡単な方法では、ファイルを直接テスト クラスターに移動します。

#### <a name="hdfs-dfs-copy"></a>HDFS DFS コピー

以下の手順を使用して、運用環境クラスターからテスト クラスターにデータをコピーします。 この手順では、HDInsight に付属している `hdfs dfs` ユーティリティを使用します。

1. 既存のクラスターのストレージ アカウントと既定のコンテナー情報を見つけます。 次の例では、PowerShell を使用してこの情報を取得します。

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. HDInsight ドキュメントに記載された Linux ベース クラスターの作成手順に従ってテスト環境を作成します。 クラスターを作成する前に、 **[オプションの構成]**を選択します。
3. [オプションの構成] ブレードで、 **[リンクされたストレージ アカウント]**を選択します。
4. **[ストレージ キーを追加]** を選択し、メッセージが表示されたら、手順 1. で PowerShell スクリプトによって返されたストレージ アカウントを選択します。 各ブレードで **[選択]** をクリックします。 最後に、クラスターを作成します。
5. クラスターが作成されたら、**SSH** を使用して接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

6. SSH セッションで次のコマンドを使用して、リンクされたストレージ アカウントから新しい既定のストレージ アカウントにファイルをコピーします。 CONTAINER を、PowerShell によって返されたコンテナーの情報に置き換えます。 __ACCOUNTNAME__ を、アカウント名に置き換えます。 データへのパスをデータ ファイルへのパスに置き換えます。

        hdfs dfs -cp wasbs://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location

    > [!NOTE]
    > テスト環境にデータを格納するディレクトリ構造が存在しない場合は、次のコマンドを使用して作成できます。

        hdfs dfs -mkdir -p /new/path/to/create

    `-p` スイッチで、パス内のすべてのディレクトリを作成できます。

#### <a name="direct-copy-between-azure-storage-blobs"></a>Azure Storage BLOB 間で直接コピーする
`Start-AzureStorageBlobCopy` Azure PowerShell コマンドレットを使用して、HDInsight 外のストレージ アカウント間で BLOB をコピーすることもできます。 詳細については、「Azure Storage での Azure PowerShell の使用」の「Azure BLOB の管理方法」セクションを参照してください。

## <a name="client-side-technologies"></a>クライアント側のテクノロジ
通常、[Azure PowerShell コマンドレット](/powershell/azureps-cmdlets-docs)、[Azure CLI](../cli-install-nodejs.md)、[.NET SDK for Hadoop](https://hadoopsdk.codeplex.com/) などのクライアント側のテクノロジは、Linux ベースのクラスターでも同じように引き続き機能します。これは、依存する REST API が両方のクラスターの OS タイプで同じであるためです。

## <a name="server-side-technologies"></a>サーバー側のテクノロジ
次の表では、Windows 固有のサーバー側コンポーネントの移行に関するガイダンスを示します。

| 使用するテクノロジ | 実行するアクション |
| --- | --- |
| **PowerShell** (クラスターの作成時に使用されるスクリプトを含む、サーバー側スクリプト) |Bash スクリプトを書き直します。 スクリプト アクションについては、「[Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」および「[HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」をご覧ください。 |
| **Azure CLI** (サーバー側スクリプト) |Azure CLI は Linux で使用可能ですが、HDInsight クラスター ヘッド ノードにはプレインストールされません。 サーバー側スクリプトで必要な場合は、Linux ベースのプラットフォームへのインストールについて、「 [Azure CLI のインストール](../cli-install-nodejs.md) 」を参照してください。 |
| **.NET コンポーネント** |.Net は、Linux ベースの HDInsight クラスターのすべての種類で全面的にサポートされているわけではありません。 2016 年 10 月 28 日以降に作成された HDInsight クラスター上の Linux ベース Storm は、SCP.NET フレームワークを使用して C# Storm トポロジをサポートします。 .NET のその他のサポートは将来の更新時に追加される予定です。 |
| **Win32 コンポーネントまたはその他の Windows 専用のテクノロジ** |ガイダンスはコンポーネントやテクノロジによって異なります。 Linux と互換性のあるバージョンを見つけることができる場合や、代替ソリューションを見つけるかこのコンポーネントを書き換える必要がある場合があります。 |

## <a name="cluster-creation"></a>クラスターの作成
このセクションでは、クラスターの作成における違いについて説明します。

### <a name="ssh-user"></a>SSH ユーザー
Linux ベースの HDInsight クラスターでは **Secure Shell (SSH)** プロトコルを使用して、クラスター ノードにリモート アクセスできます。 Windows ベース クラスター用のリモート デスクトップとは異なり、ほとんどの SSH クライアントはグラフィカル ユーザー エクスペリエンスを提供しませんが、代わりに、クラスターでコマンドを実行できるコマンドラインを提供します。 一部のクライアント ([MobaXterm](http://mobaxterm.mobatek.net/)など) は、リモート コマンドラインに加え、グラフィカル ファイル システム ブラウザーを提供します。

クラスターの作成時に、SSH ユーザーと、認証用の**パスワード**または**公開キー証明書**を指定する必要があります。

パスワードを使用するよりも安全であるため、公開キー証明書を使用することをお勧めします。 証明書認証は署名済みの公開キーと秘密キーのペアを生成してから、クラスターの作成時に公開キーを指定することで機能します。 SSH を使用してサーバーに接続するときに、クライアントの秘密キーで接続を認証します。

詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

### <a name="cluster-customization"></a>クラスターのカスタマイズ
**スクリプト アクション** は、Bash スクリプトに記述する必要があります。 スクリプト アクションはクラスターの作成時に使用できますが、Linux ベースのクラスターの場合、クラスターの稼働後にカスタマイズを実行する場合にも使用できます。 詳細については、「[Script Action を使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」および「[HDInsight での Script Action 開発](hdinsight-hadoop-script-actions-linux.md)」をご覧ください。

他のカスタマイズ機能として **ブートストラップ**があります。 Windows クラスターの場合、この機能により、Hive で使用する追加ライブラリの場所を指定できます。 クラスターの作成後にこれらのライブラリは自動的に Hive クエリで使用できるようになるため、 `ADD JAR`を使用する必要はありません。

Linux ベースのクラスターのブートストラップ機能では、この機能は提供されません。 代わりに、「 [HDInsight クラスター作成時の Hive ライブラリの追加](hdinsight-hadoop-add-hive-libraries.md)」に記載されているスクリプト アクションを使用します。

### <a name="virtual-networks"></a>Virtual Networks
Windows ベースの HDInsight クラスターは従来の仮想ネットワークでのみ動作しますが、Linux ベースの HDInsight クラスターにはリソース マネージャーの仮想ネットワークが必要になります。 Linux ベースの HDInsight クラスターを接続する必要がある従来の Virtual Network にリソースがある場合は、「 [従来の VNet を新しい VNet に接続する](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md)」を参照してください。

HDInsight で Azure Virtual Networks を使用する場合の構成要件の詳細については、「 [Azure Virtual Network を使用した HDInsight 機能の拡張](hdinsight-extend-hadoop-virtual-network.md)」を参照してください。

## <a name="management-and-monitoring"></a>管理と監視
ジョブ履歴 UI や Yarn UI など、Windows ベースの HDInsight で使用した可能性のある Web UI の多くは、Ambari で使用できます。 また、Ambari Hive View で、Web ブラウザーを使用して Hive クエリを実行することができます。 Ambari Web UI は、https://CLUSTERNAME.azurehdinsight.net の Linux ベースの HDInsight クラスターで利用できます。

Ambari の操作の詳細については、次のドキュメントを参照してください。

* [Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Ambari のアラート
Ambari には、クラスターに関する潜在的な問題を通知できるアラート システムがあります。 Ambari Web UI ではアラートは赤または黄色のエントリとして表示されますが、REST API を使用して取得することもできます。

> [!IMPORTANT]
> Ambari アラートは問題が*ある可能性のある*ことを示すものであり、問題が*ある*ことを示すものではありません。 たとえば、HiveServer2 に通常どおりアクセスできる場合でも、アクセスできないことを示すアラートを受け取ることがあります。
>
> 多くのアラートがサービスに対する一定間隔のクエリとして実装され、特定の期間内での応答を予期します。 したがって、アラートは必ずしもサービスが停止していることを意味するわけでなく、単に予期した期間内に結果が返されなかったことを意味します。

通常は、アラートが長期間発生していたか、アクションを実行する前に報告されていたユーザーの問題を反映しているのかを評価する必要があります。

## <a name="file-system-locations"></a>ファイル システムの場所
Linux クラスターのファイル システムは、Windows ベースの HDInsight クラスターとは異なる方法で配置されます。 一般的に使用されるファイルを検索するには、以下の表を使用します。

| 検索対象 | 配置場所 |
| --- | --- |
| 構成 |`/etc`」を参照してください。 たとえば、 `/etc/hadoop/conf/core-site.xml` |
| ログ ファイル |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`。ここには 2 つのディレクトリがあります。つまり、現在の HDP バージョンと `current` です。 `current`ディレクトリには、バージョン番号ディレクトリにあるファイルとディレクトリへのシンボリック リンクが含まれています。 バージョン番号は HDP バージョンの更新時に変更されるため、`current` ディレクトリは HDP ファイルにアクセスするための便利な方法として提供されます。 |
| hadoop-streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

通常、ファイル名がわかっている場合は、SSH セッションから次のコマンドを使用してファイル パスを検索することができます。

    find / -name FILENAME 2>/dev/null

ファイル名にワイルドカードを使用することもできます。 たとえば、`find / -name *streaming*.jar 2>/dev/null` の場合、ファイル名の一部として 'streaming' という単語を含む jar ファイルへのパスが返されます。

## <a name="hive-pig-and-mapreduce"></a>Hive、Pig、および MapReduce

Linux ベースのクラスターの Pig と MapReduce は非常に似ています。 唯一の違いは、クラスターのヘッド ノードへの接続方法です。 詳細については、以下のドキュメントをご覧ください。

* [SSH での Pig の使用](hdinsight-hadoop-use-pig-ssh.md)
* [SSH での MapReduce の使用](hdinsight-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive
次のグラフでは、Hive ワークロードの移行に関するガイダンスを示します。

| Windows ベースの場合 | Linux ベースの場合 |
| --- | --- |
| **Hive エディター** |[Ambari の Hive ビュー](hdinsight-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` を使用して Tez を有効にします。 |Tez は Linux ベースのクラスターの既定の実行エンジンであるため、set ステートメントは不要になります。 |
| サーバーで、Hive ジョブの一部として呼び出される CMD ファイルまたはスクリプトを使用します。 |Bash スクリプトを使用します。 |
| `hive` コマンドを使用します。 |[Beeline](hdinsight-hadoop-use-hive-beeline.md) または [SSH セッションから Hive](hdinsight-hadoop-use-hive-ssh.md) を使用します。 |

## <a name="storm"></a>Storm
| Windows ベースの場合 | Linux ベースの場合 |
| --- | --- |
| Storm ダッシュボード |Storm ダッシュ ボードは使用できません。 トポロジの送信方法については、「 [Linux ベースの HDInsight での Apache Storm トポロジのデプロイと管理](hdinsight-storm-deploy-monitor-topology-linux.md) 」を参照してください。 |
| Storm UI |Storm UI は https://CLUSTERNAME.azurehdinsight.net/stormui で利用できます。 |
| Visual Studio を使用して、C# またはハイブリッド トポロジを作成、デプロイ、および管理します。 |Visual Studio は、2016 年 10 月 28 日以降に作成された HDInsight クラスター上の Linux ベース Storm で、C# (SCP.NET) またはハイブリッド トポロジの作成、デプロイ、管理に使用できます。 |

## <a name="hbase"></a>HBase
Linux ベースのクラスターでは、HBase の znode の親は `/hbase-unsecure`です。 この値を、ネイティブの HBase Java API を使用するすべての Java クライアント アプリケーションの構成で設定する必要があります。

この値を設定するクライアント例については、「 [HDInsight (Hadoop) 環境の HBase を使用する Java アプリケーションを Maven で構築](hdinsight-hbase-build-java-maven.md) 」を参照してください。

## <a name="spark"></a>Spark
プレビュー期間中は、Windows クラスターで Spark クラスターを使用できました。 Spark GA は、Linux ベースのクラスターでのみ使用できます。 プレビュー版の Windows ベースの Spark クラスターからリリース版の Linux ベースの Spark クラスターへの移行パスはありません。

## <a name="known-issues"></a>既知の問題
### <a name="azure-data-factory-custom-net-activities"></a>Azure Data Factory カスタム .NET アクティビティ
現在、Azure Data Factory カスタム .NET アクティビティは Linux ベースの HDInsight クラスターでサポートされていません。 代わりに、以下のいずれかの方法を使用して、ADF パイプラインの一部としてカスタム アクティビティを実装する必要があります。

* Azure Batch プールで .NET アクティビティを実行します。 「 [Azure Data Factory パイプラインでカスタム アクティビティを使用する](../data-factory/data-factory-use-custom-activities.md)
* MapReduce アクティビティとしてアクティビティを実装します。 詳細については、「[Data Factory から MapReduce プログラムを起動する](../data-factory/data-factory-map-reduce.md)」をご覧ください。

### <a name="line-endings"></a>行の終わり
通常、Windows ベース システムの行の終わりには CRLF を使用しますが、Linux ベース システムでは LF を使用します。 行の終わりが CRLF のデータを生成する場合、またはそのようなデータが必要な場合には、LF 行の終わりを操作するためにプロデューサーとコンシューマーの変更が必要になることがあります。

たとえば、Azure PowerShell を使用して Windows ベース クラスター上の HDInsight に対してクエリを実行すると、CRLF のデータが返されます。 Linux ベースのクラスターで同じクエリを実行すると、LF が返されます。 Linux ベースのクラスターに移行する前に、これによってソリューションで問題が発生しないかどうかを調べる必要があります。

Linux クラスター ノードで直接実行されるスクリプトの場合は、行の終わりとして常に LF を使用する必要があります。 CRLF を使用すると、Linux ベースのクラスターでスクリプトを実行するときにエラーが発生する可能性があります。

スクリプトに CR 文字が埋め込まれた文字列が含まれていないことがわかっている場合は、以下のいずれかの方法を使用して行の終わりを一括変更することができます。

* **クラスターにアップロードする予定のスクリプトの場合**、次の PowerShell ステートメントを使用して、クラスターにスクリプトをアップロードする前に行の終わりを CRLF から LF に変更します。

      $original_file ='c:\path\to\script.py'
      $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
      [IO.File]::WriteAllText($original_file, $text)
* **クラスターで使用されているストレージに既に存在するスクリプトの場合**、Linux ベースのクラスターに対して SSH セッションで以下のコマンドを使用して、スクリプトを変更します。

      hdfs dfs -get wasbs:///path/to/script.py oldscript.py
      tr -d '\r' < oldscript.py > script.py
      hdfs dfs -put -f script.py wasbs:///path/to/script.py

## <a name="next-steps"></a>次のステップ

* [Linux ベースの HDInsight クラスターを作成する方法を確認する](hdinsight-hadoop-provision-linux-clusters.md)
* [SSH を使用して HDInsight に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Ambari を使用して Linux ベースのクラスターを管理する](hdinsight-hadoop-manage-ambari.md)

