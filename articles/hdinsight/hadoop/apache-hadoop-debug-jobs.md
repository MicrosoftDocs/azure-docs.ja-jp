---
title: Apache Hadoop のデバッグ:ログの表示とエラー メッセージの解釈 - Azure HDInsight
description: PowerShell を使用して HDInsight を管理しているときに表示されることがあるエラー メッセージと、回復するために使用できる手順について説明します。
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 8ad2bdd0f12abad08515f0314b9c03cc971127cb
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059212"
---
# <a name="analyze-apache-hadoop-logs-in-azure-hdinsight"></a>Azure HDInsight の Apache Hadoop ログを分析する

Azure HDInsight 上の各 Hadoop クラスターでは、Azure ストレージ アカウントが既定のファイル システムとして使用されています。 このストレージ アカウントを、既定のストレージ アカウントと呼びます。 クラスターは、既定のストレージ アカウントの Azure Table Storage と Blob Storage を使用してそのログを格納します。  クラスターの既定のストレージ アカウントを調べるには、[HDInsight での Apache Hadoop クラスターの管理](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts)に関するページを参照してください。 ログは、クラスターが削除された後でも、ストレージ アカウントに保持されます。

## <a name="logs-written-to-azure-tables"></a>Azure テーブルに書き込まれたログ

Azure テーブルに書き込まれたログは、HDInsight クラスターで何が起こっているかを知るある程度の手がかりとなります。

HDInsight クラスターを作成すると、Linux ベースのクラスターの場合、既定の Table Storage 内に次の 6 つのテーブルが自動的に作成されます。

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

テーブルのファイル名は**u\<ClusterName > DDMonYYYYatHHMMSSsss\<TableName >** です。

これらのテーブルには次のフィールドが含まれます。

* ClusterDnsName
* ComponentName
* EventTimestamp
* Host
* MALoggingHash
* Message
* N
* PreciseTimeStamp
* Role
* RowIndex
* テナント
* TIMESTAMP
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>ログにアクセスするためのツール
これらのテーブルのデータにアクセスするには、次のようにさまざまなツールを使用できます。

* Visual Studio
* Azure ストレージ エクスプローラー
* Power Query for Excel

#### <a name="use-power-query-for-excel"></a>Power Query for Excel を使用する
Power Query は、[Microsoft Power Query for Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379) からインストールできます。 システム要件については、ダウンロード ページを参照してください。

**Power Query を使用してサービス ログを開いて分析する**

1. **Microsoft Excel**を開きます。
2. **[Power Query]** メニューの **[Azure から]** をクリックし、 **[Microsoft Azure テーブル ストレージから]** をクリックします。
   
    ![HDInsight Hadoop Excel PowerQuery open Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. ストレージ アカウント名 (短い名前または FQDN) を入力します。
4. ストレージ アカウント キーを入力します。 次のようなテーブルの一覧が表示されます。
   
    ![HDInsight Hadoop logs stored in Azure Table storage](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. **[ナビゲーター]** ウィンドウで hadoopservicelog テーブルを右クリックし、 **[編集]** を選択します。 4 つの列が表示されます。 必要に応じて、 **[パーティション キー]** 、 **[行キー]** 、および **[タイムスタンプ]** 列を削除します。これには、削除する列を選択し、リボンのオプションから **[列の削除]** をクリックします。
6. [コンテンツ] 列にある展開アイコンをクリックし、Excel スプレッドシートにインポートする列を選択します。 このデモでは、TraceLevel と ComponentName を選択しています。これで、問題があるコンポーネントに関する基本的な情報が得られます。
   
    ![HDInsight Hadoop ログの Excel での列の選択](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png "HDInsight Hadoop ログの Excel での列の選択")
7. **[OK]** をクリックしてデータをインポートします。
8. **[TraceLevel]** 、[Role]、 **[ComponentName]** の各列を選択し、リボンの **[グループ化]** コントロールをクリックします。
9. [グループ化] ダイアログ ボックスで **[OK]** をクリックします。
10. [適用して閉じる] をクリックします。

これで、Excel を使用して、必要に応じてフィルター処理と並べ替えを実行できるようになりました。 問題の発生時にその問題を掘り下げるために、他の列 (Message など) を含めることもできますが、前に説明した列を選択してグループ化すると、Hadoop サービスで何が起こっているかを適切に把握できます。 setuplog テーブルと hadoopinstalllog テーブルにも同じ考え方が当てはまります。

#### <a name="use-visual-studio"></a>Visual Studio を使用する
**Visual Studio を使用する**

1. Visual Studio を開きます。
2. **[表示]** メニューの **[Cloud Explorer]** をクリックします。 または、単に **Ctrl + \,、Ctrl + X** キーを押します。
3. **Cloud Explorer** で **[リソースの種類]** を選択します。  そのほかに、 **[リソース グループ]** というオプションも選択できます。
4. **[ストレージ アカウント]** 、クラスターの既定のストレージ アカウント、 **[テーブル]** の順に展開します。
5. **hadoopservicelog**をダブルクリックします。
6. フィルターを追加します。 例:
   
        TraceLevel eq 'ERROR'
   
    ![HDInsight Hadoop ログの vs での列の選択](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png "HDInsight Hadoop ログの vs での列の選択")
   
    フィルターの作成の詳細については、「 [テーブル デザイナー用のフィルター文字列の作成](../../vs-azure-tools-table-designer-construct-filter-strings.md)」を参照してください。

## <a name="logs-written-to-azure-blob-storage"></a>Azure Blob Storage に書き込まれたログ
Azure テーブルに書き込まれたログは、HDInsight クラスターで何が起こっているかを知るある程度の手がかりとなります。 ただし、これらのテーブルには、問題の発生時にその問題をさらに掘り下げるのに役立つ、タスク レベルのログがありません。 HDInsight クラスターは、この次のレベルの詳細を提供するために、Templeton を使用して送信されるジョブのタスク ログを Blob Storage アカウントに書き込むように構成されています。 実質的に、これは、Microsoft Azure PowerShell コマンドレットまたは .NET Job Submission API を使用して送信されるジョブを意味します。RDP/コマンドライン アクセスを介してクラスターに送信されるジョブではありません。 

ログを表示するには、「[Linux ベースの HDInsight で Apache Hadoop YARN アプリケーション ログにアクセスする](../hdinsight-hadoop-access-yarn-app-logs-linux.md)」を参照してください。


アプリケーション ログの詳細については、[Apache Hadoop YARN におけるユーザー ログの管理とアクセスの簡略化](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/)に関するページを参照してください。


## <a name="view-cluster-health-and-job-logs"></a>クラスターの状態とジョブ ログの表示
### <a name="access-the-ambari-ui"></a>Ambari UI にアクセスする
Azure Portal から、HDInsight クラスター名をクリックし、クラスター ウィンドウを開きます。 クラスター ウィンドウから、 **[ダッシュボード]** をクリックします。

![HDInsight クラスター ダッシュボードの起動](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Yarn UI にアクセスする
Azure Portal から、HDInsight クラスター名をクリックし、クラスター ウィンドウを開きます。 クラスター ウィンドウから、 **[ダッシュボード]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。 Ambari で、左側のサービスの一覧から **[YARN]** を選択します。 表示されるページで、 **[クイック リンク]** を選択し、アクティブなヘッド ノード エントリとリソース マネージャー UI を選択します。

YARN UI では、次の操作を実行できます。

* **クラスターの状態を取得します**。 左側のウィンドウから、 **[Cluster]** を展開し、 **[About]** をクリックします。 割り当て済みメモリの合計、使用済みコア、クラスター リソース マネージャーの状態、クラスター バージョンなど、クラスターの状態に関する詳細が表示されます。
  
    ![YARN での HDInsight クラスター ダッシュボードの起動](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png "YARN での HDInsight クラスター ダッシュボードの起動")
* **ノードの状態を取得します**。 左側のウィンドウから、 **[Cluster]** を展開し、 **[Nodes]** をクリックします。 ここにはクラスターの全ノード、各ノードの HTTP アドレス、各ノードに割り当てられているリソースなどが一覧表示されます。
* **ジョブの状態を監視します**。 左側のウィンドウから、 **[Cluster]** を展開し、 **[Applications]** をクリックし、クラスター内のすべてのジョブを一覧表示します。 特定の状態 (新規、送信済み、実行中など) のジョブを確認する場合、 **[Applications]** の下にある該当リンクをクリックします。 さらに、ジョブ名をクリックすると、出力やログなど、ジョブに関する詳細がわかります。

### <a name="access-the-hbase-ui"></a>HBase UI にアクセスする
Azure Portal から、HDInsight HBase クラスター名をクリックし、クラスター ウィンドウを開きます。 クラスター ウィンドウから、 **[ダッシュボード]** をクリックします。 入力を求められたら、クラスターの管理者資格情報を入力します。 Ambari で、サービスの一覧から [HBase] を選択します。 ページの上部にある **[クイック リンク]** を選択し、アクティブな Zookeeper ノード リンクをポイントして、[HBase Master UI] をクリックします。

## <a name="hdinsight-error-codes"></a>HDInsight のエラー コード
このセクションで取り上げるエラー メッセージは、Azure HDInsight で Hadoop のユーザーが Azure PowerShell を使用してサービスを管理する際に直面する可能性のあるエラー状況を理解するのに役立ちます。また、エラーから回復するための手順も示されています。

一部のエラー メッセージは、Azure ポータルで HDinsight クラスターを管理している場合にも表示されます。 しかし、その場合に遭遇する可能性のあるエラー メッセージは、その状況で可能な対応策に制約があるため、さほどきめ細かいものではありません。 その他のエラー メッセージは、対応策が明白な文脈で提供されています。 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **説明**:Hive メタストアと Oozie メタストアにカスタム設定を使用するために、1 つ以上のコンポーネントに Azure SQL データベースの詳細を指定してください。
* **対応策**:ユーザーは、有効な SQL Azure metastore を指定して、要求を再試行する必要があります。  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **説明**:*nameOfYourRegion* リージョンにクラスターを作成できませんでした。 有効な HDInsight リージョンを使用して要求し直してください。
* **対応策**:顧客は、それらが現在サポートされているクラスター リージョンを作成する必要があります。該当するリージョンは、東南アジア、西ヨーロッパ、北ヨーロッパ、米国東部、米国西部です。  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **説明**:サーバーが、要求されたクラスター レコードを見つけることができませんでした。  
* **対応策**:操作をやり直してください。

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **説明**:クラスターの DNS 名 *yourDnsName* が無効です。 名前の先頭と末尾が英数字であり、使っている特殊文字は '-' だけであることを確認してください。  
* **対応策**:クラスターに有効な DNS 名を使用していること、つまり、先頭と末尾が英数字で、ダッシュ (-) 以外の特殊文字を含まないことを確認して、操作を再試行します。

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **説明**:クラスター名 *yourClusterName* は使用できません。 別の名前を選択してください。  
* **対応策**:ユーザーは、一意で存在しないクラスター名を指定して、再試行する必要があります。 ポータルを使用している場合は、作成処理中にクラスター名が既に使用されていると、その時点で通知されます。

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **説明**:クラスターのパスワードが無効です。 パスワードは、数字、大文字、小文字、特殊文字を少なくとも 1 文字ずつ組み合わせて、10 文字以上とし、スペースは使わず、ユーザー名を含まないようにします。  
* **対応策**:有効なクラスター パスワードを指定して操作を再試行します。

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **説明**:クラスターのユーザー名が無効です。 ユーザー名に特殊文字またはスペースが含まれていないことを確認してください。  
* **対応策**:有効なクラスター ユーザー名を指定して操作を再試行します。

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **説明**:クラスターの DNS 名 *yourDnsClusterName* が無効です。 名前の先頭と末尾が英数字であり、使っている特殊文字は '-' だけであることを確認してください。  
* **対応策**:有効な DNS クラスター ユーザー名を指定して操作を再試行します。

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **説明**:URI *yourcontainerURI* のコンテナー名と、要求本文の DNS 名 *yourDnsName* は同じである必要があります。  
* **対応策**:コンテナー名と DNS 名が同じであることを確認して操作を再試行します。

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **説明**:無効なクラスター構成です。 データ ノード定義がノード サイズに見つかりません。  
* **対応策**:操作をやり直してください。

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **説明**:クラスターでデプロイの削除に失敗しました  
* **対応策**:削除操作を再試行します。

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **説明**:サービスの構成エラー。 必要な DNS マッピング情報が見つかりません。  
* **対応策**:クラスターを削除して新しいクラスターを作成します。

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **説明**:クラスター コンテナー作成操作が重複しています。 *nameOfYourContainer* のレコードが存在しますが、Etag が一致しません。
* **対応策**:一意の名前をコンテナーに付けて、作成操作を再試行します。

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **説明**:ホストされるサービス *nameOfYourHostedService* には既にクラスターが含まれています。 ホストされるサービスに複数のクラスターを含めることはできません。  
* **対応策**:別のホストされるサービスでクラスターをホストします。

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **説明**:サーバーは、クラスターのデプロイの状態を更新できませんでした。  
* **対応策**:操作をやり直してください。 これが何度も起きる場合は、CSS にお問い合わせください。

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **説明**:クラスター *yourClusterName* がメンテナンスの一環として削除されました。 クラスターを作成し直してください。
* **対応策**:クラスターを作成し直します。

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **説明**:無効なクラスター構成です。 必要なヘッド ノード構成がノード サイズに見つかりません。
* **対応策**:操作をやり直してください。

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **説明**:ホストされるサービス *nameOfYourHostedService* を作成できません。 要求を再試行してください。  
* **対応策**:要求をやり直してください。

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **説明**:ホストされるサービス *nameOfYourHostedService* には、既に運用環境がデプロイされています。 ホストされるサービスに運用環境のデプロイを含めることはできません。 別のクラスター名を使用して要求を再試行してください。
* **対応策**:別のクラスター名を使用して要求を再試行します。

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **説明**:クラスターのホストされるサービス *nameOfYourHostedService* が見つかりませんでした。  
* **対応策**:クラスターがエラー状態である場合は、クラスターを削除し、再試行します。

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **説明**:ホストされるサービス *nameOfYourHostedService* には、関連付けられたデプロイがありません。  
* **対応策**:クラスターがエラー状態である場合は、クラスターを削除し、再試行します。

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **説明**:サブスクリプション ID *yourSubscriptionId* に、クラスター *yourClusterName* を作成するコアが残されていません。 必要: *resourcesRequired*、利用可能: *resourcesAvailable*。  
* **対応策**:サブスクリプションのリソースに空きを作るか、サブスクリプションで利用可能なリソースを増やし、クラスターの作成を再試行します。

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **説明**:サブスクリプション ID *yourSubscriptionId* に、新しいホストされるサービスがクラスター *yourClusterName* を作成するクォータがありません。  
* **対応策**:サブスクリプションのリソースに空きを作るか、サブスクリプションで利用可能なリソースを増やし、クラスターの作成を再試行します。

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **説明**:サーバーで内部エラーが発生しました。 要求を再試行してください。  
* **対応策**:要求をやり直してください。

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **説明**:Azure Storage の場所 *dataRegionName* が有効な場所ではありません。 リージョンが正しいことを確認し、要求を再試行してください。
* **対応策**:HDInsight をサポートするストレージの場所を選択し、クラスターが併置されていることを確認して、操作を再試行します。

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **説明**:データ ノードの VM サイズが無効です。 すべてのデータ ノードでサポートされているのは、'L VM' サイズのみです。  
* **対応策**:データ ノードでサポートされているノード サイズを指定し、操作を再試行します。

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **説明**:ヘッド ノードの VM サイズが無効です。 ヘッド ノードでサポートされているのは 'XL VM' サイズのみです。  
* **対応策**:ヘッド ノードでサポートされているノード サイズを指定して、操作を再試行します

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **説明**:使用されているサブスクリプション ID *yourSubscriptionId* に、クラスター *yourClusterName* の削除操作を実行するための十分なアクセス許可がありません。  
* **対応策**:クラスターがエラー状態である場合は、クラスターを削除して、再試行します。  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **説明**:外部ストレージ アカウントの BLOB コンテナー名 *yourContainerName* が無効です。 名前の先頭にアルファベットが使用され、名前には小文字、数字、およびダッシュのみが使用されていることを確認してください。  
* **対応策**:有効なストレージ アカウント BLOB コンテナー名を指定して、操作を再試行します。

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **説明**:秘密鍵の詳細を設定するには、外部ストレージ アカウント *yourStorageAccountName* の構成が必要です。  
* **対応策**:ストレージ アカウントの有効な秘密鍵を指定し、操作を再試行します。

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **説明**:バージョン ヘッダー *yourVersionHeader* が有効な形式 yyyy-mm-dd ではありません。  
* **対応策**:有効な形式のバージョン ヘッダーを指定して、要求を再試行します。

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **説明**:無効なクラスター構成です。 ヘッド ノード構成が複数見つかりました。  
* **対応策**:構成を編集して、ヘッド ノードが 1 つだけ指定されるようにします。

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **説明**:許容時間または最大再試行回数内に操作を完了できませんでした。 要求を再試行してください。  
* **対応策**:要求をやり直してください。

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **説明**:パラメーター *yourParameterName* を null または空にすることはできません。  
* **対応策**:パラメーターの有効な値を指定します。

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **説明**:クラスターの作成要求入力が 1 つ以上、無効です。 入力値が正しいことを確認して、要求を再試行してください。  
* **対応策**:入力値が正しいことを確認して、要求を再試行してください。

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **説明**:リージョン *yourRegionName* およびサブスクリプション ID *yourSubscriptionId* ではリージョン能力が利用できません。  
* **対応策**:HDInsight クラスターをサポートするリージョンを指定します。 パブリックにサポートされているリージョンは、東南アジア、西ヨーロッパ、北ヨーロッパ、米国東部、米国西部です。

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **説明**:ストレージ アカウント *yourStorageAccountName* がリージョン *currentRegionName* にあります。 これはクラスターのリージョン *yourClusterRegionName*と同じにする必要があります。  
* **対応策**:クラスターと同じリージョンにストレージ アカウントを指定します。または、データが既にストレージ アカウントにある場合は、既存のストレージ アカウントと同じリージョンに新しいクラスターを作成します。 ポータルを使っている場合は、この問題があると、事前に通知されます。

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **説明**:指定されたサブスクリプション ID *yourSubscriptionId* がアクティブではありません。  
* **対応策**:サブスクリプションを再度アクティブにするか、新しい有効なサブスクリプションを取得します。

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **説明**:サブスクリプション ID *yourSubscriptionId* が見つかりません。  
* **対応策**:サブスクリプション ID が有効であることを確認し、操作を再試行します。

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **説明**:DNS *yourDnsUrl* を解決できません。 BLOB エンドポイントの完全修飾 URL が指定されていることを確認してください。  
* **対応策**:有効な BLOB URL を指定します。 URL は、先頭が *http://* で末尾が *.com* であることを含め、完全に有効である必要があります。

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **説明**:リソース *yourDnsUrl* の場所を確認できません。 BLOB エンドポイントの完全修飾 URL が指定されていることを確認してください。  
* **対応策**:有効な BLOB URL を指定します。 URL は、先頭が *http://* で末尾が *.com* であることを含め、完全に有効である必要があります。

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **説明**:バージョン *specifiedVersion* およびサブスクリプション ID *yourSubscriptionId* ではバージョン能力が利用できません。  
* **対応策**:利用可能なバージョンを選択し、操作を再試行します。

### <a id="VersionNotSupported"></a>VersionNotSupported
* **説明**:バージョン *specifiedVersion* がサポートされていません。
* **対応策**:サポートされているバージョンを選択し、操作を再試行します。

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **説明**:バージョン *specifiedVersion* は Azure リージョン *specifiedRegion* では使用できません。  
* **対応策**:指定したリージョンでサポートされているバージョンを選択し、操作を再試行します。

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **説明**:無効なクラスター構成です。 必要な WASB アカウント構成が外部アカウントに見つかりません。  
* **対応策**:アカウントが存在し、構成で適切に指定されていることを確認して、操作を再試行します。

## <a name="next-steps"></a>次の手順

* [Linux ベースの HDInsight で Apache Hadoop サービスのヒープ ダンプを有効にする](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Apache Ambari Web UI を使用した HDInsight クラスターの管理](../hdinsight-hadoop-manage-ambari.md)
