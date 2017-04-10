---
title: "Azure Portal を使用した HDInsight での Hadoop クラスターの管理 | Microsoft Docs"
description: "Azure Portal を使用して HDInsight クラスターを作成および管理する方法について説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 5a76f897-02e8-4437-8f2b-4fb12225854a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 82c12f4a2a24e190babfd88ff667e24d6c45ebac
ms.lasthandoff: 03/25/2017


---
# <a name="manage-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure ポータルを使用した HDInsight での Hadoop クラスターの管理
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure Portal][azure-portal] を使用すると、Azure HDInsight で Hadoop クラスターを管理できます。 他のツールを使用して HDInsight で Hadoop クラスターを管理する方法については、タブ セレクターを使用してください。 

**前提条件**

この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。

## <a name="open-the-portal"></a>ポータルを開く
1. [https://portal.azure.com](https://portal.azure.com)にサインインします。
2. ポータルを開くと、次の操作を行うことができます。
   
   * 左側のメニューの **[新規]** をクリックして新しいクラスターを作成する。
     
       ![新しい [HDInsight クラスター] ボタン](./media/hdinsight-administer-use-portal-linux/azure-portal-new-button.png)
   * 左側のメニューの **[HDInsight クラスター]** をクリックして既存のクラスターを一覧表示する。
     
       ![Azure ポータル [HDInsight クラスター] ボタン](./media/hdinsight-administer-use-portal-linux/azure-portal-hdinsight-button.png)

       HDInsight クラスターが表示されない場合は、一覧の最後の **[その他のサービス]** をクリックし、**[インテリジェンス + 分析]**セクションで **[HDInsight クラスター]** をクリックします。

## <a name="create-clusters"></a>クラスターの作成
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

HDInsight は、広範囲の Hadoop コンポーネントで動作します。 検証およびサポートされているコンポーネントの一覧については、「 [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)」を参照してください。 一般的なクラスターの作成に関する詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。 

## <a name="list-and-show-clusters"></a>クラスターの一覧と表示
1. [https://portal.azure.com](https://portal.azure.com)にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックして既存のクラスターを一覧表示します。
3. クラスター名をクリックします。 クラスターの一覧が長い場合は、ページの上部でフィルターを使用できます。
4. クラスターの一覧から概要ページを表示するクラスターをクリックします。
   
    ![Azure Portal HDInsight クラスター 基本情報](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials.png)
   
    **[概要] メニュー:**

   * **ダッシュ ボード**: クラスターのダッシュボード (Linux ベースのクラスターの場合は Ambari Web) を開きます。
   * **[SSH (Secure Shell)]**: Secure Shell (SSH) 接続を使用してクラスターに接続する方法を表示します。
   * **[クラスターのスケーリング]**: このクラスターの worker ノードの数を変更できます。
   * **[削除]**: クラスターを削除します。

    **左側のメニュー:**
   
   * **[アクティビティ ログ]**: アクティビティ ログを表示およびクエリします。
   * **[アクセス制御 (IAM)]**: ロールの割り当てを使用します。  「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../active-directory/role-based-access-control-configure.md)」を参照してください。
   * **[タグ]**: キーと値のペアを設定して、クラウド サービスのカスタム分類を定義できます。 たとえば、 **プロジェクト**という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。
   * **[問題の診断と解決]**: トラブルシューティング情報を表示します。
   * **[ロック]**: クラスターの変更または削除を防ぐためのロックを追加します。
   * **[Automation スクリプト]**: クラスター向けの Azure Resource Manager テンプレートを表示およびエクスポートします。 現時点では、依存している Azure ストレージ アカウントのみをエクスポートできます。 「[Azure Resource Manager テンプレートを使用して、HDInsight での Linux ベースの Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md)」を参照してください。
   * **[クイック スタート]**: HDInsight の操作を開始するために役立つ情報を表示します。
   * **[HDInsight 用のツール]**: HDInsight 関連ツールのヘルプ情報です。
   * **[クラスター ログイン]**: クラスターのログイン情報を表示します。
   * **[サブスクリプションのコアの利用状況]**: サブスクリプションの使用されたコアと利用可能なコアを表示します。 
   * **[クラスターのスケーリング]**: クラスターの worker ノードの数を増減します。 「[クラスターのスケール](hdinsight-administer-use-management-portal.md#scale-clusters)」を参照してください。
   * **[SSH (Secure Shell)]**: Secure Shell (SSH) 接続を使用してクラスターに接続する方法を表示します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。
   * **[HDInsight パートナー]**: 現在の HDInsight パートナーを追加または削除します。
   * **[外部メタストア]**: Hive メタストアと Oozie メタストアを表示します。 メタストアを構成できるのは、クラスターの作成処理中のみです。 「[Hive/Oozie メタストアの使用](hdinsight-hadoop-provision-linux-clusters.md#use-hiveoozie-metastore)」を参照してください。
   * **[スクリプト アクション]**: クラスター上の Bash スクリプトを実行します。 「 [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。
   * **[アプリケーション]**: HDInsight アプリケーションを追加/削除します。  「[カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)」を参照してください。
   * **[プロパティ]**: クラスターのプロパティを表示します。
   * **[ストレージ アカウント]**: ストレージ アカウントとキーを表示します。 ストレージ アカウントは、クラスター作成プロセス中に構成されます。
   * **[クラスター AAD ID]**: 
   * **[新しいサポート要求]**: Microsoft サポートのサポート チケットを作成できます。

6. **[プロパティ]**をクリックします。
   
    プロパティは次のとおりです。
   
   * **[ホスト名]**: クラスター名。
   * **[クラスター URL]**:  Ambari Web インターフェイスの URL。
   * **[状態]**: Aborted、Accepted、ClusterStorageProvisioned、AzureVMConfiguration、HDInsightConfiguration、Operational、Running、Error、Deleting、Deleted、Timedout、DeleteQueued、DeleteTimedout、DeleteError、PatchQueued、CertRolloverQueued、ResizeQueued、ClusterCustomization が表示されます。
   * **[リージョン]**: Azure の場所。 サポートされている Azure の場所の一覧については、「 **HDInsight の価格** 」の [[リージョン]](https://azure.microsoft.com/pricing/details/hdinsight/)ボックスの一覧をご覧ください。
   * **[作成日]**。
   * **[オペレーティング システム]**: **Windows** または **Linux**。
   * **[タイプ]**: Hadoop、HBase、Storm、Spark。 
   * **[バージョン]**。 [HDInsight のバージョン](hdinsight-component-versioning.md)
   * **[サブスクリプション]**: サブスクリプション名。
   * **[既定のデータ ソース]**: クラスターの既定のファイル システムです。
   * **[Worker ノードのサイズ]**。
   * **[ヘッド ノードのサイズ]**。

## <a name="delete-clusters"></a>クラスターの削除
クラスターを削除しても、既定のストレージ アカウントまたはリンクされたストレージ アカウントは削除されません。 同じストレージ アカウントと同じメタストアを使用してクラスターを再作成することができます。 クラスターを再作成するときに、新しい既定の BLOB コンテナーを使用することをお勧めします。

1. [Portal][azure-portal] にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックする。 **[HDInsight クラスター]** が表示されない場合は、先に **[その他のサービス]** をクリックします。
3. 削除するクラスターをクリックします。
4. 上部のメニューの **[削除]** をクリックし、指示に従います。

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
  
    バランス再調整は、次の 2 つの方法で実行できます。
  
  * Storm Web UI
  * コマンド ライン インターフェイス (CLI) ツール
    
    詳細については、 [Apache Storm に関するドキュメント](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) をご覧ください。
    
    Storm Web UI は、HDInsight クラスターで使用できます。
    
    ![HDInsight Storm のスケールのバランス調整](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.storm.rebalance.png)
    
    CLI コマンドを使用して Storm トポロジのバランスを再調整する方法を次の例で示します。
    
        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors
        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

**クラスターのスケールを設定するには**

1. [Portal][azure-portal] にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックする。
3. スケールを設定するクラスターをクリックします。
3. **[クラスターのスケール設定]** をクリックします。
4. **[worker ノードの数]**を入力します。 クラスター ノードの数の制限は Azure サブスクリプションによって異なります。 制限値を上げるには、課金サポートにお問い合わせください。  コスト情報にはノード数の変更が反映されます。
   
    ![HDInsight Hadoop HBase Storm および Spark のスケール](./media/hdinsight-administer-use-portal-linux/hdinsight.portal.scale.cluster.png)

## <a name="pauseshut-down-clusters"></a>クラスターの一時停止またはシャットダウン

Hadoop ジョブの大半は、たまにしか実行されないバッチ ジョブです。 ほとんどの Hadoop クラスターでは、クラスターが処理に使用されていない期間がかなりあります。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。
また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

このプロセスをプログラムで実行する方法は数多くあります。

* Azure Data Factory を使用する。 オンデマンドの HDInsight がリンクされたサービスを作成する詳細については、「 [Azure Data Factory を使用した HDInsight でのオンデマンドの Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-create-linux-clusters-adf.md) 」を参照してください。
* Azure PowerShell を使用する。  「 [HDInsight での Hive を使用したフライト遅延データの分析](hdinsight-analyze-flight-delay-data.md)」をご覧ください。
* Azure CLI を使用する。 「 [Azure CLI を使用した HDInsight での Hadoop クラスターの管理](hdinsight-administer-use-command-line.md)」をご覧ください。
* HDInsight .NET SDK を使用する。 「 [HDInsight での Hadoop ジョブの送信](hdinsight-submit-hadoop-jobs-programmatically.md)」をご覧ください。

価格情報については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」をご覧ください。 ポータルからクラスターを削除する方法については、「 [クラスターの削除](#delete-clusters)

## <a name="change-passwords"></a>パスワードの変更
HDInsight クラスターは、2 つのユーザー アカウントを持つことができます。 HDInsight クラスターのユーザー アカウント ( HTTP ユーザー アカウントとも呼ばれます) および SSH ユーザー アカウントは作成プロセス中に作成されます。 Ambari Web UI を使用してクラスターのユーザー アカウントのユーザー名とパスワードを変更し、スクリプト アクションを使用して SSH ユーザー アカウントを変更することができます。

### <a name="change-the-cluster-user-password"></a>クラスター ユーザーのパスワードの変更
Ambari Web UI を使用してクラスターのユーザー パスワードを変更することができます。 Ambari にログインするには、既存のクラスター ユーザー名とパスワードを使用する必要があります。

> [!NOTE]
> クラスター ユーザー (管理者) のパスワードを変更した場合、このクラスターに対して失敗するスクリプト アクションを実行する可能性があります。 worker ノードを対象とする保存済みスクリプト アクションがある場合、サイズの変更操作を通じてノードをクラスターに追加すると、これらのスクリプト アクションは失敗する可能性があります。 スクリプト アクションの詳細については、「 [スクリプト アクションを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。
> 
> 

1. HDInsight クラスター ユーザーの資格情報を使用して Ambari Web UI にサインインします。 既定のユーザー名は **admin**です。 URL は **https://&lt;HDInsight Cluster Name>azurehdinsight.net** です。
2. 上部のメニューの **[Admin]** をクリックしてから [Ambari の管理] をクリックします。 
3. 左側のメニューで **[ユーザー]**をクリックします。
4. **[Admin]**をクリックします。
5. **[パスワードの変更]**をクリックします。

その後、Ambari は、クラスター内のすべてのノードでパスワードを変更します。

### <a name="change-the-ssh-user-password"></a>SSH ユーザーのパスワードの変更
1. テキスト エディターを使用して、次のテキストを **changepassword.sh** という名前のファイルに保存します。
   
   > [!IMPORTANT]
   > 行の終わりとして LF を使用するエディターを使用する必要があります。 エディターで CRLF が使用される場合、スクリプトは動作しません。
   > 
   > 
   
        #! /bin/bash
        USER=$1
        PASS=$2
   
        usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
2. HTTP または HTTPS アドレスを使用して、HDInsight からアクセスできるストレージの場所にファイルをアップロードします。 たとえば、OneDrive や Azure Blob ストレージなどの公開ファイル ストアです。 URI (HTTP または HTTPS アドレス) をファイルに保存します。これは、次の手順で必要になります。
3. Azure Portal で、**[HDInsight クラスター]** をクリックします。
4. HDInsight クラスターをクリックします。
4. **[スクリプト アクション]** をクリックします。
4. **[スクリプト アクション]** ブレードの上部で、**[新規で送信]** を選択します。 **[スクリプト アクションの送信]** ブレードで、次の情報を指定します。
   
   | フィールド | 値 |
   | --- | --- |
   | 名前 |SSH パスワードの変更 |
   | Bash スクリプト URI |changepassword.sh ファイルへの URI |
   | ノード (ヘッド、worker、Nimbus、Supervisor、Zookeeper など) |表示するすべてのノード型に ✓ |
   | パラメーター |SSH ユーザー名と新しいパスワードを入力します。 ユーザー名とパスワードの間に、スペースを 1 つ入れる必要があります。 |
   | このスクリプト アクションを保持する… |このフィールドはオフのままにします。 |
5. **[作成]** を選択してスクリプトを適用します。 スクリプトの完了後は、新しいパスワードで SSH を使用して、クラスターに接続することができます。

## <a name="grantrevoke-access"></a>アクセス権の付与/取り消し
HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

既定では、これらのサービスへのアクセス許可が付与されます。 [Azure CLI](hdinsight-administer-use-command-line.md#enabledisable-http-access-for-a-cluster) と [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access) を使用して、アクセス許可を取り消すかまたは付与することができます。

## <a name="find-the-subscription-id"></a>サブスクリプション ID の検索

**Azure サブスクリプション ID を検索するには**

1. [Portal][azure-portal] にサインインします。
2. **[サブスクリプション]** をクリックします。 各サブスクリプションには、名前と ID があります。

各クラスターは、Azure サブスクリプションに関連付けられています。 サブスクリプション ID はクラスターの **[基本情報]** タイルに表示されます。 「 [クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。

## <a name="find-the-resource-group"></a>リソース グループの検索
Azure Resource Manager モードでは、各 HDInsight クラスターは Azure Resource Manager グループと共に作成されます。 クラスターが属している Resource Manager グループは、次の場所に表示されます。

* クラスター一覧の **リソース グループ** 列。
* クラスターの **[基本情報]** タイル。  

「 [クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。

## <a name="find-the-default-storage-account"></a>既定のストレージ アカウントの検索
各 HDInsight クラスターには、既定のストレージ アカウントが設定されています。 クラスターの既定のストレージ アカウントとそのキーは、**[ストレージ アカウント]** の下に表示されます。 「[クラスターの一覧と表示](#list-and-show-clusters)」を参照してください。

## <a name="run-hive-queries"></a>Hive クエリの実行
Azure ポータルから直接 Hive ジョブを実行することはできませんが、Ambari Web UI の Hive ビューを使用することができます。

**Ambari Hive ビューを使用して Hive クエリを実行するには**

1. HDInsight クラスター ユーザーの資格情報を使用して Ambari Web UI にサインインします。 既定のユーザー名は **admin**です。 URL は **https://&lt;HDInsight Cluster Name>azurehdinsight.net** です。
2. 次のスクリーンショットのように Hive ビューを開きます。  
   
    ![HDInsight Hive ビュー](./media/hdinsight-administer-use-portal-linux/hdinsight-hive-view.png)
3. ページの上部にある **[クエリ]** をクリックします。
4. **[クエリ エディター]** で Hive クエリを入力してから、**[実行]** をクリックします。

## <a name="monitor-jobs"></a>ジョブの監視
「 [Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md#monitoring)」を参照してください。

## <a name="browse-files"></a>Browse files
Azure ポータルを使用して、既定のコンテナーのコンテンツを参照できます。

1. [https://portal.azure.com](https://portal.azure.com)にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックして既存のクラスターを一覧表示します。
3. クラスター名をクリックします。 クラスターの一覧が長い場合は、ページの上部でフィルターを使用できます。
4. クラスターの左側のメニューから、**[ストレージ アカウント]** をクリックします。
5. ストレージ アカウントをクリックします。
7. **[BLOB]** タイルをクリックします。
8. 既定のコンテナー名をクリックします。

## <a name="monitor-cluster-usage"></a>クラスターの使用状況の監視
HDInsight クラスター ブレードの **[使用状況]** セクションには、サブスクリプションで HDInsight 用に使用できるコアの数、このクラスターに割り当てられているコアの数、およびこのクラスター内のノードへのコアの割り当て方法に関する情報が表示されます。 「 [クラスターの一覧と表示](#list-and-show-clusters)」をご覧ください。

> [!IMPORTANT]
> HDInsight クラスターによって提供されるサービスを監視するには、Ambari Web または Ambari REST API を使用する必要があります。 Ambari の使用の詳細については、「 [Ambari を使用した HDInsight クラスターの管理 (プレビュー)](hdinsight-hadoop-manage-ambari.md)
> 
> 

## <a name="connect-to-a-cluster"></a>クラスターへの接続
「 [SSH による HDInsight での Hive と Hadoop の使用](hdinsight-hadoop-use-hive-ssh.md#ssh)」を参照してください。

## <a name="next-steps"></a>次のステップ
この記事では、ポータルを使用して HDInsight クラスターを作成する方法、および Hadoop コマンド ライン ツールを開く方法について説明しました。 詳細については、次の記事を参照してください。

* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターの作成](hdinsight-provision-clusters.md)
* [HDInsight での Hive の使用](hdinsight-use-hive.md)
* [HDInsight での Pig の使用](hdinsight-use-pig.md)
* [HDInsight での Sqoop の使用](hdinsight-use-sqoop.md)
* [Azure HDInsight の概要](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Azure HDInsight でサポートされている Hadoop のバージョン](hdinsight-component-versioning.md)

[azure-portal]: https://portal.azure.com
[image-hadoopcommandline]: ./media/hdinsight-administer-use-portal-linux/hdinsight-hadoop-command-line.png "Hadoop コマンド ライン"

