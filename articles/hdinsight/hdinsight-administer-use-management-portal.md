---
title: "Azure Portal を使用した HDInsight での Windows ベースの Hadoop クラスターの管理 | Microsoft Docs"
description: "HDInsight サービスを管理する方法を学習します。 HDInsight クラスターを作成し、対話型 JavaScript コンソールを開いて、Hadoop コマンド コンソールを開きます。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 9295a988-bd88-453a-8c8b-55fa103bf39c
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 58011fe25a96edfe2744990180b2f2866537bd37
ms.openlocfilehash: ec35e177d1bb94671b1703020c9aa863a3805af1
ms.lasthandoff: 02/07/2017


---
# <a name="manage-windows-based-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure Portal を使用した HDInsight での Windows ベースの Hadoop クラスターの管理

[Azure Portal][azure-portal] を使用して、Azure HDInsight で Windows ベースの Hadoop クラスターを作成し、Hadoop ユーザーのパスワードを変更して、クラスターで Hadoop コマンド コンソールにアクセスできるようにリモート デスクトップ プロトコル (RDP) を有効にすることができます。

この記事の情報は、Windows ベースの HDInsight クラスターにのみ適用されます。 Linux ベースのクラスターの管理については、「[Azure Portal を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-portal-linux.md)」を参照してください。

> [!IMPORTANT]
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。


## <a name="prerequisites"></a>前提条件

この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **Azure ストレージ アカウント** - HDInsight クラスターは、既定のファイル システムとして Azure BLOB ストレージ コンテナーを使用します。 Azure BLOB ストレージと HDInsight クラスターのシームレスな統合の詳細については、 [HDInsight での Azure BLOB ストレージの使用](hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。 Azure ストレージ アカウントの作成の詳細については、 [ストレージ アカウントの作成方法](../storage/storage-create-storage-account.md)に関するページを参照してください。

## <a name="open-the-portal"></a>ポータルを開く
1. [https://portal.azure.com](https://portal.azure.com)にサインインします。
2. ポータルを開くと、次の操作を行うことができます。

   * 左側のメニューの **[新規]** をクリックして新しいクラスターを作成する。

       ![新しい [HDInsight クラスター] ボタン](./media/hdinsight-administer-use-management-portal/azure-portal-new-button.png)
   * 左側のメニューの **[HDInsight クラスター]** をクリックする。

       ![Azure ポータル [HDInsight クラスター] ボタン](./media/hdinsight-administer-use-management-portal/azure-portal-hdinsight-button.png)

     左側のメニューに **[HDInsight]** が表示されていない場合は、**[参照]** をクリックする。

     ![Azure ポータル [参照] ボタン](./media/hdinsight-administer-use-management-portal/azure-portal-browse-button.png)

## <a name="create-clusters"></a>クラスターの作成
ポータルを使用した作成手順については、「[HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md)」をご覧ください。

HDInsight は、広範囲の Hadoop コンポーネントで動作します。 検証およびサポートされているコンポーネントの一覧については、「[Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)」を参照してください。 次のいずれかのオプションを使用して、HDInsight をカスタマイズできます。

* Script Action を使用してカスタム スクリプトを実行します。これによってクラスターをカスタマイズして、クラスター構成を変更することや、Giraph や Solr などのカスタム コンポーネントをインストールすることができます。 詳しくは、「[Script Action を使って HDInsight をカスタマイズする](hdinsight-hadoop-customize-cluster.md)」をご覧ください。
* クラスターの作成中に、HDInsight .NET SDK または Azure PowerShell でクラスター カスタマイズ パラメーターを使用します。 そうすることで、クラスターの有効期間中はこれらの構成変更が保持され、Azure プラットフォームが保守のために定期的に実行するクラスター ノードの再イメージ化の影響も受けません。 クラスター カスタマイズ パラメーターの使用方法の詳細については、「[HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md)」をご覧ください。
* Mahout や Cascading などの一部のネイティブ Java コンポーネントは、JAR ファイルとしてクラスター上で実行できます。 これらの JAR ファイルは、Azure BLOB ストレージに分配し、Hadoop ジョブ送信メカニズムによって HDInsight クラスターに送信できます。 詳細については、 [プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)に関するページを参照してください。

  > [!NOTE]
  > HDInsight クラスターへの JAR ファイルのデプロイ、または HDInsight クラスターでの JAR ファイルの呼び出しに関する問題がある場合は、 [Microsoft サポート](https://azure.microsoft.com/support/options/)にお問い合わせください。
  >
  > Cascading は HDInsight ではサポートされておらず、Microsoft サポートの対象でもありません。 サポートされているコンポーネントの一覧については、[HDInsight で提供されるクラスター バージョンの新機能](hdinsight-component-versioning.md)に関する記事をご覧ください。
  >
  >

リモート デスクトップ接続を使用したクラスターへのカスタム ソフトウェアのインストールはサポートされていません。 クラスターの再作成が必要な場合にファイルが失われるため、ヘッド ノードのドライブにはファイルを保存しないでください。 Azure BLOB ストレージにファイルを保存することをお勧めします。 BLOB ストレージは永続的です。

## <a name="list-and-show-clusters"></a>クラスターの一覧と表示
1. [https://portal.azure.com](https://portal.azure.com)にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックする。
3. クラスター名をクリックします。 クラスターの一覧が長い場合は、ページの上部でフィルターを使用できます。
4. 一覧にあるクラスターをダブルクリックして詳細を表示します。

    **メニューと基本情報**:

    ![Azure Portal HDInsight クラスター 基本情報](./media/hdinsight-administer-use-management-portal/hdinsight-essentials.png)

   * メニューをカスタマイズするには、メニュー上の任意の場所を右クリックし、 **[カスタマイズ]**をクリックします。
   * **[設定]** と **[すべての設定]**: クラスター用の **[設定]** ブレードを表示します。このブレードを使用して、クラスターの詳細な構成情報にアクセスできます。
   * **[ダッシュボード]**、**[クラスター ダッシュボード]**、**[URL]: これらはすべてクラスター ダッシュボードにアクセスするために使用します。クラスター ダッシュボードは、Linux ベースのクラスター用の Ambari Web です。-**[SSH (Secure Shell)]**: Secure Shell (SSH) 接続を使用してクラスターに接続する方法を表示します。
   * **[クラスターのスケーリング]**: このクラスターの worker ノードの数を変更できます。
   * **[削除]**: クラスターを削除します。
   * **[クイック スタート] \(![cloud and thunderbolt icon = quickstart](./media/hdinsight-administer-use-portal-linux/quickstart.png))**: HDInsight の操作を開始するために役立つ情報を表示します。
   * **[ユーザー] \(![ユーザー アイコン](./media/hdinsight-administer-use-portal-linux/users.png))**: このクラスターの*ポータル管理*に対する権限を、Azure サブスクリプションの他のユーザーに設定できます。

     > [!IMPORTANT]
     > これは、Azure ポータルでのこのクラスターへのアクセスと権限 *"だけ"* に影響し、どのユーザーが HDInsight クラスターに接続でき、ジョブを送信できるかには影響しません。
     >
     >
   * **[タグ] \(![タグ アイコン](./media/hdinsight-administer-use-portal-linux/tags.png))**: タグを使用してキーと値のペアを作成し、クラウド サービスのカスタム分類を定義できます。 たとえば、 **プロジェクト**という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。
   * **[Ambari Views]**: Ambari Web へのリンク。

     > [!IMPORTANT]
     > HDInsight クラスターによって提供されるサービスを管理するには、Ambari Web または Ambari REST API を使用する必要があります。 Ambari の使用の詳細については、「 [Ambari を使用した HDInsight クラスターの管理 (プレビュー)](hdinsight-hadoop-manage-ambari.md)」をご覧ください。
     >
     >

     **[使用状況]**:

     ![Azure Portal HDInsight クラスター使用状況](./media/hdinsight-administer-use-management-portal/hdinsight-portal-cluster-usage.png)
5. **[設定]**をクリックします。

    ![Azure Portal HDInsight クラスター使用状況](./media/hdinsight-administer-use-management-portal/hdinsight.portal.cluster.settings.png)

   * **[プロパティ]**: クラスターのプロパティを表示します。
   * **[クラスター AAD ID]**:
   * **[Azure ストレージ キー]**: 既定のストレージ アカウントとそのキーを表示します。 ストレージ アカウントは、クラスターの作成処理中に構成します。
   * **[クラスター ログイン]**: クラスターの HTTP ユーザー名とパスワードを変更します。
   * **[外部メタストア]**: Hive メタストアと Oozie メタストアを表示します。 メタストアを構成できるのは、クラスターの作成処理中のみです。
   * **[クラスターのスケーリング]**: クラスターの worker ノードの数を増減します。
   * **[リモート デスクトップ]**: リモート デスクトップ (RDP) アクセスを有効および無効にし、RDP ユーザー名を構成します。  RDP ユーザー名は HTTP ユーザー名とは別にする必要があります。
   * **[指名パートナー]**:

     > [!NOTE]
     > これは使用可能な設定の汎用リストです。すべてのクラスターの種類でこれらの設定がすべて表示されるわけではありません。
     >
     >
6. **[プロパティ]**をクリックします。

    次のプロパティ セクションが表示されます。

   * **[ホスト名]**: クラスター名。
   * **[クラスター URL]**。
   * **[状態]**: Aborted、Accepted、ClusterStorageProvisioned、AzureVMConfiguration、HDInsightConfiguration、Operational、Running、Error、Deleting、Deleted、Timedout、DeleteQueued、DeleteTimedout、DeleteError、PatchQueued、CertRolloverQueued、ResizeQueued、ClusterCustomization が表示されます。
   * **[リージョン]**: Azure の場所。 サポートされている Azure の場所の一覧については、「 **HDInsight の価格** 」の [[リージョン]](https://azure.microsoft.com/pricing/details/hdinsight/)ボックスの一覧をご覧ください。
   * **[データの作成日]**。
   * **[オペレーティング システム]**: **Windows** または **Linux**。
   * **[タイプ]**: Hadoop、HBase、Storm、Spark。
   * **[バージョン]**。 [HDInsight のバージョン](hdinsight-component-versioning.md)
   * **[サブスクリプション]**: サブスクリプション名。
   * **[サブスクリプション ID]**。
   * **[プライマリ データ ソース]**。 既定の Hadoop ファイル システムとして使用される Azure BLOB ストレージ アカウント。
   * **[worker ノードの価格レベル]**。
   * **[ヘッド ノードの価格レベル]**。

## <a name="delete-clusters"></a>クラスターの削除
クラスターを削除しても、既定のストレージ アカウントまたはリンクされたストレージ アカウントは削除されません。 同じストレージ アカウントと同じメタストアを使用してクラスターを再作成することができます。

1. [Portal][azure-portal] にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上部のメニューの **[削除]** をクリックし、指示に従います。

「 [クラスターの一時停止またはシャットダウン](#pauseshut-down-clusters)」もご覧ください。

## <a name="scale-clusters"></a>クラスターのスケール
クラスターのスケール設定機能を使用すると、Azure HDInsight で実行しているクラスターによって使用される worker ノードの数を、クラスターを再作成することなく、変更できます。

> [!NOTE]
> HDInsight バージョン 3.1.3 以降を使用しているクラスターのみがサポートされます。 クラスターのバージョンがわからない場合、[プロパティ] ページを確認できます。  「[クラスターの一覧と表示](#list-and-show-clusters)」を参照してください。
>
>

HDInsight でサポートされているクラスターの種類ごとに、データ ノード数を変更した場合の影響:

* Hadoop は、

    保留中または実行中のジョブに影響を与えることなく、実行中の Hadoop クラスター内の worker ノードの数をシームレスに増加できます。 処理の進行中に新しいジョブを送信することもできます。 スケール設定処理の失敗は正常に処理され、クラスターは常に機能状態になります。

    データ ノードの数を減らして Hadoop クラスターのスケールを小さくした場合、クラスター内の一部のサービスが再起動されます。 これにより、スケール設定処理の完了時に、実行中および保留中のすべてのジョブが失敗します。 ただし、処理が完了した後にジョブを再送信できます。
* HBase

    実行中の HBase クラスターに対して、ノードの追加または削除をシームレスに実行できます。 地域サーバーは、スケール設定処理の完了の数分以内に自動的に分散されます。 ただし、クラスターのヘッドノードにログインし、コマンド プロンプト ウィンドウから次のコマンドを実行して、地域サーバーを手動で分散することもできます。

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

    HBase シェルの使用方法の詳細については、以下を参照してください。
* Storm

    実行中の Storm クラスターに対して、データ ノードの追加または削除をシームレスに実行できます。 ただし、スケール設定処理が正常に完了した後、トポロジのバランス再調整が必要になります。

    バランス再調整は、次の&2; つの方法で実行できます。

  * Storm Web UI
  * コマンド ライン インターフェイス (CLI) ツール

    詳細については、 [Apache Storm に関するドキュメント](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) をご覧ください。

    Storm Web UI は、HDInsight クラスターで使用できます。

    ![HDInsight Storm の規模のバランス調整](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    CLI コマンドを使用して Storm トポロジのバランスを再調整する方法を次の例で示します。

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**クラスターのスケールを設定するには**

1. [Portal][azure-portal] にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[クラスターの拡大縮小]** をクリックします。
4. **[worker ノードの数]**を入力します。 クラスター ノードの数の制限は Azure サブスクリプションによって異なります。 制限値を上げるには、課金サポートにお問い合わせください。  コスト情報にはノード数の変更が反映されます。

    ![HDInsight Hadoop HBase Storm および Spark の規模](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>クラスターの一時停止またはシャットダウン
Hadoop ジョブの大半は、たまにしか実行されないバッチ ジョブです。 ほとんどの Hadoop クラスターでは、クラスターが処理に使用されていない期間がかなりあります。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。
また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

このプロセスをプログラムで実行する方法は数多くあります。

* Azure Data Factory を使用する。 オンデマンドおよび自己定義型の HDInsight のリンクされたサービスについては、「[Azure HDInsight のリンクされたサービス](../data-factory/data-factory-compute-linked-services.md)」および「[Azure Data Factory を使用した変換と分析](../data-factory/data-factory-data-transformation-activities.md)」をご覧ください。
* Azure PowerShell を使用する。  「 [HDInsight での Hive を使用したフライト遅延データの分析](hdinsight-analyze-flight-delay-data.md)」をご覧ください。
* Azure CLI を使用する。 「 [Azure CLI を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-command-line.md)」をご覧ください。
* HDInsight .NET SDK を使用する。 「 [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)」をご覧ください。

価格情報については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」をご覧ください。 ポータルからクラスターを削除する方法については、「[クラスターの削除](#delete-clusters)

## <a name="change-cluster-username"></a>クラスターのユーザー名の変更
HDInsight クラスターは、2 つのユーザー アカウントを持つことができます。 HDInsight クラスターのユーザー アカウントは、作成プロセスで作成されます。 RDP を使用してクラスターにアクセスするために、RDP ユーザー アカウントを作成することもできます。 「[リモート デスクトップを有効にする](#connect-to-hdinsight-clusters-by-using-rdp)」を参照してください。

**HDInsight クラスターのユーザー名とパスワードを変更するには**

1. [Portal][azure-portal] にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[クラスター ログイン]** をクリックします。
4. **[クラスター ログイン]** が有効になっている場合は、ユーザー名とパスワードを変更する前に、**[無効化]** をクリックしてから **[有効化]** をクリックする必要があります。
5. **[クラスター ログイン名]** と **[クラスター ログイン パスワード]** を変更し、**[保存]** をクリックします。

    ![HDInsight クラスターの HTTP ユーザーのユーザー名とパスワードの変更](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="grantrevoke-access"></a>アクセス権の付与/取り消し
HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

既定では、これらのサービスへのアクセス許可が付与されます。 Azure ポータルからアクセス許可を取り消す/付与することができます。

> [!NOTE]
> アクセス許可を付与するか、取り消すことで、クラスターのユーザー名とパスワードがリセットされます。
>
>

**HTTP Web サービスのアクセス許可を付与する/取り消すには**

1. [Portal][azure-portal] にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[クラスター ログイン]** をクリックします。
4. **[クラスター ログイン]** が有効になっている場合は、ユーザー名とパスワードを変更する前に、**[無効化]** をクリックしてから **[有効化]** をクリックする必要があります。
5. **[クラスター ログイン ユーザー名]** と **[クラスター ログイン パスワード]** に、クラスターの新しいユーザー名とパスワードをそれぞれ入力します。
6. **[保存]**をクリックします。

    ![HDInsight による HTTP Web サービス アクセスの付与と削除](./media/hdinsight-administer-use-management-portal/hdinsight.portal.change.username.password.png)

## <a name="find-the-default-storage-account"></a>既定のストレージ アカウントの検索
各 HDInsight クラスターには、既定のストレージ アカウントが設定されています。 クラスターの既定のストレージ アカウントとそのキーは、 **[設定]**/**[プロパティ]**/**[Azure ストレージ キー]**。 「[クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。

## <a name="find-the-resource-group"></a>リソース グループの検索
Azure Resource Manager モードでは、各 HDInsight クラスターは Azure リソース グループと共に作成されます。 クラスターが属している Azure リソース グループは、次の場所に表示されます。

* クラスター一覧の **リソース グループ** 列。
* クラスターの **[基本情報]** タイル。  

「[クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。

## <a name="open-hdinsight-query-console"></a>HDInsight クエリ コンソールを開く
HDInsight クエリ コンソールには、次の機能が用意されています。

* **[Hive エディター]**: Hive ジョブを送信するための GUI Web インターフェイス。  「[クエリ コンソールを使用して Hive クエリを実行](hdinsight-hadoop-use-hive-query-console.md)」を参照してください。

    ![HDInsight ポータル Hive エディター](./media/hdinsight-administer-use-management-portal/hdinsight-hive-editor.png)
* **[ジョブ履歴]**: Hadoop ジョブを監視します。  

    ![HDInsight ポータル ジョブ履歴](./media/hdinsight-administer-use-management-portal/hdinsight-job-history.png)

    **[クエリ名]** をクリックすると、ジョブのプロパティ、**ジョブ クエリ**、**ジョブの出力など、ジョブの詳細が表示されます。 また、クエリと出力の両方をワークステーションにダウンロードすることもできます。
* **[ファイル ブラウザー]**: 既定のストレージ アカウントとリンクされたストレージ アカウントを参照します。

    ![HDInsight ポータル ファイル ブラウザー](./media/hdinsight-administer-use-management-portal/hdinsight-file-browser.png)

    スクリーンショットでは、種類に **<Account>** とあり、項目が Azure ストレージ アカウントであることを示しています。  アカウント名をクリックすると、ファイルを参照できます。
* **[Hadoop UI]**。

    ![HDInsight ポータル Hadoop UI](./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-ui.png)

    **[Hadoop UI]*では、ファイルを参照したり、ログを確認したりできます。
* **[Yarn UI]**。

    ![HDInsight ポータル Yarn UI](./media/hdinsight-administer-use-management-portal/hdinsight-yarn-ui.png)

## <a name="run-hive-queries"></a>Hive クエリの実行
ポータルから Hive ジョブを実行するには、HDInsight クエリ コンソールで **[Hive エディター]** をクリックします。 「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」をご覧ください。

## <a name="monitor-jobs"></a>ジョブの監視
ポータルからジョブを監視するには、HDInsight クエリ コンソールで **[ジョブ履歴]** をクリックします。 「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」をご覧ください。

## <a name="browse-files"></a>Browse files
既定のストレージ アカウントおよびリンクされたストレージ アカウントに格納されているファイルを参照するには、HDInsight クエリ コンソールで **[ファイル ブラウザー]** をクリックします。 「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」をご覧ください。

また、HDInsight コンソールの **[Hadoop UI]** から **[ファイル システムの参照]** ユーティリティを使用することもできます。  「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」をご覧ください。

## <a name="monitor-cluster-usage"></a>クラスターの使用状況の監視
HDInsight クラスター ブレードの **[使用状況]** セクションには、サブスクリプションで HDInsight 用に使用できるコアの数、このクラスターに割り当てられているコアの数、およびこのクラスター内のノードへのコアの割り当て方法に関する情報が表示されます。 「 [クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。

> [!IMPORTANT]
> HDInsight クラスターによって提供されるサービスを監視するには、Ambari Web または Ambari REST API を使用する必要があります。 Ambari の使用の詳細については、「[Ambari を使用した HDInsight クラスターの管理 (プレビュー)](hdinsight-hadoop-manage-ambari.md)
>
>

## <a name="open-hadoop-ui"></a>Hadoop UI を開く
クラスターの監視、ファイル システムの参照、ログの確認を行うには、HDInsight クエリ コンソールで **[Hadoop UI]** をクリックします。 「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」をご覧ください。

## <a name="open-yarn-ui"></a>Yarn UI を開く
Yarn のユーザー インターフェイスを使用するには、HDInsight クエリ コンソールで **[Yarn UI]** をクリックします。 「[HDInsight クエリ コンソールを開く](#open-hdinsight-query-console)」をご覧ください。

## <a name="connect-to-clusters-using-rdp"></a>RDP を使用したクラスターへの接続
クラスターの作成時に指定したクラスターの資格情報を使用するとクラスター上のサービスにアクセスできますが、リモート デスクトップを介してクラスター自体にアクセスすることはできません。 クラスターにプロビジョニングするとき、あるいはクラスターにプロビジョニングした後、リモート デスクトップ アクセスをオンにできます。 作成時にリモート デスクトップを有効にする手順については、「[HDInsight で Hadoop クラスターを作成する](hdinsight-provision-clusters.md)」をご覧ください。

**リモート デスクトップを有効にするには**

1. [Portal][azure-portal] にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[リモート デスクトップ]** をクリックします。
4. **[有効期限]**、**[リモート デスクトップのユーザー名]**、**[リモート デスクトップのパスワード]** に入力し、**[有効化]** をクリックします。

    ![HDInsight によるリモート デスクトップ構成の有効化と無効化](./media/hdinsight-administer-use-management-portal/hdinsight.portal.remote.desktop.png)

    [有効期限] の既定値は&1; 週間です。

   > [!NOTE]
   > HDInsight .NET SDK を使用して、クラスターに対するリモート デスクトップを有効にすることもできます。 HDInsight クライアント オブジェクトで、**EnableRdp** メソッドを次の形式で使用します: **client.EnableRdp(clustername, location, "rdpuser", "rdppassword", DateTime.Now.AddDays(6))**。 同様に、クラスターに対するリモート デスクトップを無効にするには、 **client.DisableRdp(clustername, location)**を使用します。 これらのメソッドの詳細については、 [HDInsight .NET SDK のリファレンス](http://go.microsoft.com/fwlink/?LinkId=529017)を参照してください。 この方法は、Windows で実行されている HDInsight クラスターにのみ適用できます。
   >
   >

**RDP を使用してクラスターに接続するには**

1. [Portal][azure-portal] にサインインします。
2. 左メニューから **[すべて参照]** をクリックし、**[HDInsight クラスター]** をクリックし、クラスター名をクリックします。
3. 上メニューから **[設定]** をクリックし、**[リモート デスクトップ]** をクリックします。
4. **[接続]** クリックし、指示に従います。 接続が無効の場合は、最初に有効する必要があります。 必ずリモート デスクトップ ユーザーのユーザー名とパスワードを使用してください。  クラスターのユーザー資格情報は使用できません。

## <a name="open-hadoop-command-line"></a>Hadoop コマンド ラインを開く
リモート デスクトップを使用してクラスターに接続して Hadoop コマンド ラインを使用するには、まず、先のセクションで説明したように、クラスターに対するリモート デスクトップ アクセスを有効にする必要があります。

**Hadoop コマンド ラインを開くには**

1. リモート デスクトップを使用してクラスターに接続します。
2. デスクトップで、 **[Hadoop コマンド ライン]**をダブルクリックします。

    ![HDI.HadoopCommandLine][image-hadoopcommandline]

    Hadoop コマンドの詳細については、 [Hadoop コマンド リファレンス](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/CommandsManual.html)を参照してください。

先のスクリーンショットで、フォルダー名には Hadoop のバージョン番号が埋め込まれています。 バージョン番号は、クラスターにインストールされている Hadoop コンポーネントのバージョンに基づいて変更できます。 Hadoop 環境変数を使用して、これらのフォルダーを参照できます。 次に例を示します。

    cd %hadoop_home%
    cd %hive_home%
    cd %hbase_home%
    cd %pig_home%
    cd %sqoop_home%
    cd %hcatalog_home%

## <a name="next-steps"></a>次のステップ
この記事では、ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。 詳細については、次の記事を参照してください。

* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターの作成](hdinsight-provision-clusters.md)
* [プログラムによる Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-management-portal/hdinsight-hadoop-command-line.png "Hadoop コマンド ライン"

