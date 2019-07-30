---
title: Azure Portal を使用して HDInsight の Apache Hadoop クラスターを管理する
description: Azure Portal を使用して HDInsight クラスターを作成および管理する方法について説明します。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: hrasheed
ms.openlocfilehash: c745fceca5efa66b1b23661001d93ddb287fe37b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460633"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-by-using-the-azure-portal"></a>Azure Portal を使用して HDInsight の Apache Hadoop クラスターを管理する

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure portal](https://portal.azure.com) を使用して、Azure HDInsight の [Apache Hadoop](https://hadoop.apache.org/) クラスターを管理できます。 他のツールを使って HDInsight で Hadoop クラスターを管理する方法については、上のタブ セレクターを使ってください。

## <a name="prerequisites"></a>前提条件

HDInsight 上の 既存の Apache Hadoop クラスター。  「[Azure Portal を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-portal.md)」を参照してください。

## <a name="getting-started"></a>Getting Started (概要)
[https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="showClusters"></a>クラスターの一覧と表示
**[HDInsight クラスター]** ページに既存のクラスターの一覧が表示されます。  ポータルから:
1. 左側のメニューから、 **[すべてのサービス]** を選択します。
2. **[ANALYTICS]** の **[HDInsight クラスター]** を選択します。

## <a name="homePage"></a> クラスター ホーム ページ 
[ **[HDInsight クラスター]** ](#showClusters) ページから、ご自身のクラスター名を選択します。  これにより、次の図のような **[概要]** ビューが開きます。

![Azure Portal HDInsight クラスター 基本情報](./media/hdinsight-administer-use-portal-linux/hdinsight-essentials2.png)

**上部のメニュー:**  

| Item| 説明 |
|---|---|
|Move|クラスターを別のリソース グループまたは別のサブスクリプションに移動します。|
|削除|クラスターを削除します。 |
|更新|ビューを更新します。|

**左側のメニュー:**  
  - **左上のメニュー**

    | Item| 説明 |
    |---|---|
    |概要|クラスターの全般的な情報が表示されます。|
    |アクティビティ ログ|アクティビティ ログを表示およびクエリします。|
    |アクセス制御 (IAM)|ロールの割り当てを使用します。  「[Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../role-based-access-control/role-assignments-portal.md)」を参照してください。|
    |Tags|キーと値のペアを設定して、クラウド サービスのカスタム分類を定義できます。 たとえば、 **プロジェクト**という名前のキーを作成し、特定のプロジェクトに関連付けられているすべてのサービスに共通の値を使用できます。|
    |問題の診断と解決|トラブルシューティング情報を表示します。|
    |クイック スタート|HDInsight の操作を開始するために役立つ情報を表示します。|
    |ツール|HDInsight 関連ツールのヘルプ情報です。|

  - **設定メニュー**  

    | Item| 説明 |
    |---|---|
    |クラスター サイズ|クラスターの worker ノードの数を確認したり、増減したりします。 「[クラスターのスケール](hdinsight-administer-use-portal-linux.md#scale-clusters)」を参照してください。|
    |クォータ制限|サブスクリプションの使用されているコアと使用可能なコアを表示します。|
    |SSH およびクラスターのログイン|Secure Shell (SSH) 接続を使用してクラスターに接続する方法を表示します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。|
    |Data Lake Storage Gen1|Data Lake Storage Gen1 へのアクセスを構成します。  「[クイック スタート:HDInsight のクラスターを設定する](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)」をご覧ください。|
    |ストレージ アカウント|ストレージ アカウントとキーを表示します。 ストレージ アカウントは、クラスター作成プロセス中に構成されます。|
    |[アプリケーション]|HDInsight アプリケーションを追加/削除します。  「[カスタム HDInsight アプリケーションのインストール](hdinsight-apps-install-custom-applications.md)」を参照してください。|
    |スクリプト操作|クラスター上の Bash スクリプトを実行します。 「 [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」をご覧ください。|
    |外部メタストア|[Apache Hive](https://hive.apache.org/) メタストアと [Apache Oozie](https://oozie.apache.org/) メタストアを表示します。 メタストアを構成できるのは、クラスターの作成処理中のみです。|
    |HDInsight パートナー|現在の HDInsight パートナーを追加または削除します。|
    |properties|[クラスターのプロパティ](#properties)を表示します。|
    |ロック|クラスターの変更または削除を防ぐためのロックを追加します。|
    |テンプレートをエクスポートする|クラスター向けの Azure Resource Manager テンプレートを表示およびエクスポートします。 現時点では、依存している Azure ストレージ アカウントのみをエクスポートできます。 「[Resource Manager テンプレートを使用して HDInsight で Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md)」を参照してください。|

  - **監視メニュー**

    | Item| 説明 |
    |---|---|
    |アラート|アラートとアクションを管理します。|
    |メトリック|Azure Monitor ログでクラスター メトリックを監視します。|
    |診断の設定|診断メトリックを格納する場所に関する設定。|
    |Operations Management Suite|Azure Operations Management Suite (OMS) および Azure Monitor ログでクラスターを監視します。|

  - **[サポート + トラブルシューティング] メニュー**

    | Item| 説明 |
    |---|---|
    |リソース ヘルス|「[Azure Resource Health の概要](../service-health/resource-health-overview.md)」をご覧ください。|
    |新しいサポート要求|Microsoft サポートのサポート チケットを作成できます。|

## <a name="properties"></a> クラスターのプロパティ

[クラスター ホーム ページ](#homePage)の **[設定]** で、 **[プロパティ]** を選択します。

|Item | 説明 |
|---|---|
|ホスト名|クラスター名。|
|クラスター URL|Ambari Web インターフェイスの URL。|
|プライベート エンドポイント|クラスターのプライベート エンドポイント。|
|Secure Shell (SSH)|SSH でクラスターにアクセスするときに使うユーザー名とホスト名。|
|状態|次のいずれか:Aborted、Accepted、ClusterStorageProvisioned、AzureVMConfiguration、HDInsightConfiguration、Operational、Running、Error、Deleting、Deleted、Timedout、DeleteQueued、DeleteTimedout、DeleteError、PatchQueued、CertRolloverQueued、ResizeQueued、ClusterCustomization。|
|リージョン|Azure の場所。 サポートされている Azure の場所の一覧については、「**HDInsight の価格**」の [[リージョン]](https://azure.microsoft.com/pricing/details/hdinsight/) ボックスの一覧をご覧ください。|
|作成日|クラスターがデプロイされた日付。|
|オペレーティング システム|**[Windows]** または **[Linux]** 。|
|TYPE|Hadoop、HBase、Storm、Spark。|
|バージョン|「[HDInsight で使用可能な Hadoop コンポーネントとバージョンとは](hdinsight-component-versioning.md)」をご覧ください。|
|サブスクリプション|サブスクリプションの名前。|
|既定のデータ ソース|クラスターの既定のファイル システムです。|
|ワーカー ノードのサイズ|worker ノードの選択されている VM のサイズ。|
|ヘッド ノードのサイズ|ヘッド ノードの選択されている VM のサイズ。|
|仮想ネットワーク|クラスターのデプロイ先の仮想ネットワークの名前 (デプロイ時に選択された場合)。|

## <a name="move-clusters"></a>クラスターの移動

HDInsight クラスターを別の Azure リソース グループまたは別のサブスクリプションに移動できます。

[クラスター ホーム ページ](#homePage)から:

1. 上部のメニューから **[移動]** を選択します。
2. **[別のリソース グループに移動する]** または **[別のサブスクリプションに移動する]** を選択します。
3. 新しいページで説明されている手順に従います。

## <a name="delete-clusters"></a>クラスターの削除
クラスターを削除しても、既定のストレージ アカウントまたはリンクされたストレージ アカウントは削除されません。 同じストレージ アカウントと同じメタストアを使用してクラスターを再作成することができます。 クラスターを再作成するときに、新しい既定の BLOB コンテナーを使うことをお勧めします。

[クラスター ホーム ページ](#homePage)から:

1. 上部のメニューで **[削除]** を選択します。
2. 新しいページで説明されている手順に従います。

「 [クラスターの一時停止またはシャットダウン](#pauseshut-down-clusters)」もご覧ください。

## <a name="add-additional-storage-accounts"></a>追加のストレージ アカウントの追加

クラスターが作成されたら、Azure ストレージ アカウントや Azure Data Lake Storage アカウントを追加できます。 詳細については、「[HDInsight にストレージ アカウントを追加する](./hdinsight-hadoop-add-storage.md)」を参照してください。

## <a name="scale-clusters"></a>クラスターのスケール

クラスターのスケール設定機能を使うと、Azure HDInsight クラスターによって使われる worker ノードの数を、クラスターを再作成することなく、変更できます。

詳細については、「[HDInsight クラスターのスケーリング](./hdinsight-scaling-best-practices.md)」を参照してください。

## <a name="pauseshut-down-clusters"></a>クラスターの一時停止またはシャットダウン

Hadoop ジョブの大半は、たまにしか実行されないバッチ ジョブです。 ほとんどの Hadoop クラスターでは、クラスターが処理に使用されていない期間がかなりあります。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。
また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

このプロセスをプログラムで実行する方法は数多くあります。

* Azure Data Factory を使用する。 オンデマンドの HDInsight がリンクされたサービスを作成する詳細については、[Azure Data Factory を使用して HDInsight でオンデマンドの Linux ベースの Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-adf.md)に関するページを参照してください。
* Azure PowerShell を使用する。  「 [HDInsight での Hive を使用したフライト遅延データの分析](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)」をご覧ください。
* Azure CLI を使用する。 「[Azure CLI を使用した Azure HDInsight クラスターの管理](hdinsight-administer-use-command-line.md)」を参照してください。
* HDInsight .NET SDK を使用する。 [Apache Hadoop ジョブを送信する](hadoop/submit-apache-hadoop-jobs-programmatically.md)に関するページをご覧ください。

価格情報については、「 [HDInsight の価格](https://azure.microsoft.com/pricing/details/hdinsight/)」をご覧ください。 ポータルからクラスターを削除する方法については、「[クラスターの削除](#delete-clusters)

## <a name="upgrade-clusters"></a>クラスターをアップグレードする

「[HDInsight クラスターを新しいバージョンにアップグレードする](./hdinsight-upgrade-cluster.md)」を参照してください。

## <a name="open-the-apache-ambari-web-ui"></a>Apache Ambari Web UI を開く

Ambari は、RESTful API がサポートする直感的で使いやすい Hadoop 管理 Web UI トを提供します。 Ambari によって、システム管理者が Hadoop クラスターを管理および監視できます。

[クラスター ホーム ページ](#homePage)から:

1. **[クラスター ダッシュボード]** を選択します。

    ![HDInsight Hadoop クラスター メニュー](./media/hdinsight-administer-use-portal-linux/hdinsight-azure-portal-cluster-menu2.png)

1. 新しいページから **[Ambari ホーム]** を選択します。
1. クラスターのユーザー名とパスワードを入力します。  既定のクラスター ユーザー名は _admin_ です。

詳細については、「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。

## <a name="change-passwords"></a>パスワードの変更
HDInsight クラスターは、2 つのユーザー アカウントを持つことができます。 HDInsight クラスター ユーザー アカウント (HTTP ユーザー アカウント) と SSH ユーザー アカウントが、作成プロセス中に作成されます。 ポータルを使用してクラスターのユーザー アカウントのパスワードを変更し、スクリプト アクションを使用して SSH ユーザー アカウントを変更することができます。

### <a name="change-the-cluster-user-password"></a>クラスター ユーザーのパスワードの変更

> [!NOTE]  
> クラスター ユーザー (管理者) のパスワードを変更すると、このクラスターに対して実行されたスクリプト アクションが失敗する可能性があります。 worker ノードを対象とする保存済みスクリプト アクションがある場合、サイズの変更操作を通じてノードをクラスターに追加すると、これらのスクリプト アクションは失敗する可能性があります。 スクリプト アクションの詳細については、「 [スクリプト アクションを使って HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md)」を参照してください。

[クラスター ホーム ページ](#homePage)から:
1. **[設定]** で **[SSH およびクラスターのログイン]** を選択します。
2. **[資格情報のリセット]** を選択します。
3. テキスト ボックスに新しいパスワードを入力し、確認します。
4. **[OK]** を選択します。

クラスター内のすべてのノードでパスワードが変更されます。

### <a name="change-the-ssh-user-password"></a>SSH ユーザーのパスワードの変更
1. テキスト エディターを使用して、次のテキストを **changepassword.sh** という名前のファイルに保存します。

    > [!IMPORTANT]  
    > 行の終わりとして LF を使用するエディターを使用する必要があります。 エディターで CRLF が使用される場合、スクリプトは動作しません。

    ```bash
    #! /bin/bash
    USER=$1
    PASS=$2
    usermod --password $(echo $PASS | openssl passwd -1 -stdin) $USER
    ```

2. HTTP または HTTPS アドレスを使用して、HDInsight からアクセスできるストレージの場所にファイルをアップロードします。 たとえば、OneDrive や Azure Blob ストレージなどの公開ファイル ストアです。 URI (HTTP または HTTPS アドレス) をファイルに保存します。この URI は、次の手順で必要になります。
3. [クラスター ホーム ページ](#homePage)の **[設定]** で、 **[スクリプト アクション]** を選択します。
4. **[スクリプト アクション]** ページで、 **[新規で送信]** を選択します。
5. **[スクリプト アクションの送信]** ページで、次の情報を入力します。

   | フィールド | 値 |
   | --- | --- |
   | スクリプトの種類 | ドロップダウン リストから **[- カスタム]** を選択します。|
   | Name |"SSH パスワードの変更" |
   | Bash スクリプト URI |changepassword.sh ファイルへの URI |
   | ノードの種類:(ヘッド、worker、Nimbus、Supervisor、Zookeeper など) |表示するすべてのノード型に ✓ |
   | parameters |SSH ユーザー名と新しいパスワードを入力します。 ユーザー名とパスワードの間に、スペースを 1 つ入れる必要があります。 |
   | このスクリプト アクションを保持する… |このフィールドはオフのままにします。 |

6. **[作成]** を選択してスクリプトを適用します。 スクリプトの完了後は、新しいパスワードで SSH を使用して、クラスターに接続することができます。

## <a name="grantrevoke-access"></a>アクセス権の付与/取り消し
HDInsight クラスターには、以下の HTTP Web サービスがあります (これらすべてのサービスには、REST ベースのエンドポイントがあります)。

* ODBC
* JDBC
* Ambari
* Oozie
* Templeton

既定では、これらのサービスへのアクセス許可が付与されます。 [Azure PowerShell](hdinsight-administer-use-powershell.md#grantrevoke-access) を使用して、アクセス許可を取り消すか付与することができます。

## <a name="find-the-subscription-id"></a>サブスクリプション ID の検索
各クラスターは、Azure サブスクリプションに関連付けられています。  Azure サブスクリプション ID は[クラスター ホーム ページ](#homePage)から表示されます。

## <a name="find-the-resource-group"></a>リソース グループの検索
Azure Resource Manager モードでは、各 HDInsight クラスターは Azure Resource Manager グループと共に作成されます。 Resource Manager グループは[クラスター ホーム ページ](#homePage)から表示されます。

## <a name="find-the-storage-accounts"></a>ストレージ アカウントを検索する
HDInsight クラスターは、Azure Storage アカウントまたは Azure Data Lake Storage のいずれかを使用してデータを格納します。 各 HDInsight クラスターは、1 つの既定のストレージ アカウントと、複数のリンクされたストレージ アカウントを持つことができます。 ストレージ アカウントを一覧表示するには、**クラスター ホーム ページ**の **[設定]** で [[ストレージ アカウント]](#homePage) を選択します。

## <a name="monitor-jobs"></a>ジョブの監視
「[Ambari Web UI を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md#monitoring)」を参照してください。

## <a name="cluster-size"></a>クラスター サイズ

[クラスター ホーム ページ](#homePage)の **[クラスター サイズ]** タイルには、このクラスターに割り当てられているコアの数と、このクラスター内のノードに割り当てられているコアの数が表示されます。

> [!IMPORTANT]  
> HDInsight クラスターによって提供されるサービスを監視するには、Ambari Web または Ambari REST API を使用する必要があります。 Ambari の使用について詳しくは、[Apache Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)に関するページをご覧ください

## <a name="connect-to-a-cluster"></a>クラスターへの接続

* [HDInsight での Apache Hive の使用](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [HDInsight で SSH を使用する](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>次の手順

この記事では、基本的な管理機能をいくつか説明しました。 詳細については、次の記事を参照してください。

* [Azure PowerShell を使用した HDInsight の管理](hdinsight-administer-use-powershell.md)
* [Azure CLI を使用した HDInsight の管理](hdinsight-administer-use-command-line.md)
* [HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Ambari REST API の使用に関する詳細](hdinsight-hadoop-manage-ambari-rest-api.md)
* [HDInsight での Apache Hive の使用](hadoop/hdinsight-use-hive.md)
* [HDInsight 上で Apache Sqoop を使用する](hadoop/hdinsight-use-sqoop.md)
* [HDInsight 上の Apache Hive と Apache Pig で Python ユーザー定義関数 (UDF) を使用する](hadoop/python-udf-hdinsight.md)
* [Azure HDInsight でサポートされている Apache Hadoop のバージョン](hdinsight-component-versioning.md)