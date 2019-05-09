---
title: Azure Databricks に対するリージョンのディザスター リカバリー
description: この記事では、Azure Databricks におけるディザスター リカバリーの手法について説明します。
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 354f6014e3230b65a0c4f1cd7507e58ca94474dd
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188103"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Azure Databricks クラスターに対するリージョンのディザスター リカバリー

この記事では、Azure Databricks クラスターに適したディザスター リカバリー アーキテクチャと、その設計を実現する手順について説明します。

## <a name="azure-databricks-architecture"></a>Azure Databricks のアーキテクチャ

アーキテクチャを全体的な視点から眺めてみましょう。Azure portal から Azure Databricks ワークスペースを作成すると、選択した Azure リージョン (米国西部など) に、ご利用のサブスクリプションの Azure リソースとして[マネージド アプライアンス](../managed-applications/overview.md)がデプロイされます。 このアプライアンスは、ご利用のサブスクリプションで利用できる Azure Storage アカウントおよび[ネットワーク セキュリティ グループ](../virtual-network/manage-network-security-group.md)と共に [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) にデプロイされます。 この仮想ネットワークが Databricks ワークスペースに対する境界レベルのセキュリティを提供し、またネットワーク セキュリティ グループによって保護されることになります。 Databricks クラスターは、ワークスペース内でワーカー VM とドライバー VM の種類、Databricks Runtime のバージョンを指定することによって作成できます。 永続データを格納するストレージ アカウントは、Azure Blob Storage と Azure Data Lake Store のどちらかになります。 クラスターの作成後は、ノートブック、REST API、ODBC/JDBC エンドポイントを特定のクラスターにアタッチすることにより、そのエンドポイントを介してジョブを実行することができます。

Databricks ワークスペース環境の管理と監視は、Databricks のコントロール プレーンで行います。 クラスターの作成をはじめとするすべての管理操作は、コントロール プレーンから開始されます。 メタデータ (スケジュールされたジョブなど) はすべて Azure Database に格納され、geo レプリケーションによるフォールト トレランスが確保されます。

![Databricks のアーキテクチャ](media/howto-regional-disaster-recovery/databricks-architecture.png)

このアーキテクチャの利点の 1 つは、ユーザーがそのアカウントにある任意のストレージ リソースに Azure Databricks を接続できることです。 計算 (Azure Databricks) とストレージの両方を別々にスケーリングできることが主な利点となります。

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>リージョンのディザスター リカバリーのトポロジを作成する方法

前出したアーキテクチャの説明を見てもわかるように、Azure Databricks を使ったビッグ データ パイプラインには、Azure Storage、Azure Database などの各種データ ソースをはじめ、さまざまなコンポーネントが存在します。 Azure Databricks は、ビッグ データ パイプラインの "*計算*" をつかさどるコンポーネントです。 このコンポーネントは "*一時性*" を特徴としています。つまり、データはあくまで Azure Storage に格納されますが、不要なときには計算料金が課金されないよう、"*計算*" コンポーネント (Azure Databricks クラスター) は終了することができます。 ジョブの待ち時間が大きくならないよう、"*計算*" コンポーネント (Azure Databricks) とストレージ ソースは同じリージョン内に存在する必要があります。  

リージョンのディザスター リカバリーのトポロジを独自に作成するには、次の要件を満たす必要があります。

   1. 複数の Azure Databricks ワークスペースは、別個の Azure リージョンにプロビジョニングします。 たとえばプライマリ Azure Databricks ワークスペースを米国東部 2 に作成するなら、 セカンダリ ディザスター リカバリー Azure Databricks ワークスペースは、別のリージョン (米国西部など) に作成する必要があります。

   2. [geo 冗長ストレージ](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage)を使用します。 Azure Databricks に関連付けられているデータは、既定で Azure Storage に格納されます。 Databricks ジョブの結果も Azure Blob Storage に格納されます。そのため、処理されたデータには持続性があり、クラスターが停止された後も高可用性が保たれます。 Storage と Databricks クラスターは併置されるため、プライマリ リージョンが利用できなくなってもセカンダリ リージョンのデータにアクセスできるよう、geo 冗長ストレージを使用する必要があります。

   3. セカンダリ リージョンの作成後、ユーザー、ユーザーのフォルダー、ノートブック、クラスター構成、ジョブ構成、ライブラリ、ストレージ、初期化スクリプトを移行し、アクセスの制御を再構成する必要があります。 その他詳しい情報については、次のセクションで取り上げます。

## <a name="detailed-migration-steps"></a>詳細な移行手順

1. **お使いのコンピューター上で Databricks のコマンド ライン インターフェイスを設定する**

   このコマンド ライン インターフェイスには、Azure Databricks REST API が使いやすいようラップされています。そのためこの記事では、自動化された手順の大半でコマンド ライン インターフェイスを使用したコード例が多くなっています。

   移行手順を実施する前に、実際に作業を行う予定のデスクトップ コンピューターまたは仮想マシンに databricks-cli をインストールしてください。 詳しくは、[Databricks CLI のインストール](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)に関するページをご覧ください。

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > この記事に掲載されている python スクリプトは、実行環境として Python 2.7 以上、3.x 未満が想定されています。

2. **2 つのプロファイルを構成する**

   1 つはプライマリ ワークスペース用に、もう 1 つはセカンダリ ワークスペース用に構成します。

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   以降、この記事の各手順に出現するコード ブロックでは、対応するワークスペース コマンドを使ってプロファイルを切り替えます。 コード ブロックごとに、実際に作成するプロファイルの名前で置き換えてください。

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   必要であれば、コマンド ラインから手動で切り替えることもできます。

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Azure Active Directory ユーザーを移行する**

   プライマリ ワークスペースに存在する Azure Active Directory ユーザーをセカンダリ ワークスペースに手動で追加します。

4. **ユーザー フォルダーとノートブックを移行する**

   以下の Python コードを使用して、サンドボックス化されたユーザー環境を移行します。この環境に、ユーザーごとの入れ子になったフォルダー構造やノートブックが含まれます。

   > [!NOTE]
   > この手順でライブラリはコピーされません。この操作は、基になる API でサポートされていません。

   次の Python スクリプトをファイルにコピーして保存し、Databricks コマンド ラインから実行します。 たとえば、「 `python scriptname.py` 」のように入力します。

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **クラスターの構成を移行する**

   ノートブックの移行後、必要に応じてクラスターの構成を新しいワークスペースに移行することができます。 この手順は databricks-cli を使ってほぼすべて自動化されています (クラスターの構成をすべて移行するのではなく、自身で選んで移行する場合を除く)。

   > [!NOTE]
   > クラスター構成の作成エンドポイントは、あいにく存在しません。また、このスクリプトは、各クラスターを直ちに作成しようと試みます。 ご利用のサブスクリプションで使用できるコア数が不足していた場合、クラスターの作成に失敗します。 構成が正常に転送されれば、このエラーは無視してしてかまいません。

   次に示したスクリプトは、以前のクラスター ID から新しいクラスター ID へのマッピングを出力します。このマッピングは、後からジョブ (既存のクラスターを使うように構成されたジョブ) の移行に使用します。

   次の Python スクリプトをファイルにコピーして保存し、Databricks コマンド ラインから実行します。 たとえば、「 `python scriptname.py` 」のように入力します。

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **ジョブの構成を移行する**

   前の手順でクラスターの構成を移行した場合、必要であれば、ジョブの構成を新しいワークスペースに移行することができます。 この手順は databricks-cli を使ってすべて自動化されています (ジョブの構成をすべて移行するのではなく、自身で選んで移行する場合を除く)。

   > [!NOTE]
   > スケジュールされたジョブの構成には、"スケジュール" 情報も含まれています。そのため既定では、移行後すぐに、構成されているタイミングで動作が開始されます。 したがって、以前のワークスペースと新しいワークスペースが重複して実行されるのを防ぐため、次のコード ブロックでは、スケジュール情報を移行中にすべて削除しています。 そのようなジョブについては、切り替えの準備が完了した後で、スケジュールを構成してください。

   ジョブの構成には、新しいクラスターまたは既存のクラスターの設定が必要です。 既存のクラスターが使用されている場合、以下のスクリプト/コードは、以前のクラスター ID を新しいクラスター ID に置き換えようと試みます。

   以下の Python スクリプトをファイルにコピーして保存してください。 `old_cluster_id` と `new_cluster_id` の値は、前の手順 (クラスターの移行) で得られた出力内容に置き換えてください。 これを databricks-cli コマンド ラインで実行します (例: `python scriptname.py`)。

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **ライブラリを移行する**

   現在、ライブラリをワークスペース間で直接移行する方法はありません。 そうしたライブラリは、新しいワークスペースに手動で再インストールする必要があります。 カスタム ライブラリをワークスペースにアップロードする [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) と [Libraries CLI](https://github.com/databricks/databricks-cli#libraries-cli) とを組み合わせて自動化することはできます。

8. **Azure Blob Storage と Azure Data Lake Store のマウントを移行する**

   ノートブック ベースのソリューションを使って、[Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html) と [Azure Data Lake Store (Gen 1)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html) のマウント ポイントをすべて手動で再マウントします。 プライマリ ワークスペースにはストレージ リソースがマウント済みかと思いますので、それをセカンダリ ワークスペースについても行う必要があります。 マウントのための外部 API は存在しません。

9. **クラスター初期化スクリプトを移行する**

   すべてのクラスター初期化スクリプトは、[DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) を使用して、以前のワークスペースから新しいワークスペースに移行できます。 まず、必要なスクリプトを `dbfs:/dat abricks/init/..` からローカル デスクトップまたは仮想マシンにコピーします。 次に、それらのスクリプトを新しいワークスペースに同じパスでコピーします。

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **アクセスの制御を手動で再構成して再適用する**

    既存のプライマリ ワークスペースが Premium レベル (SKU) を使用するように構成されている場合、高い確率で [Access Control 機能](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control)も使用していることが考えられます。

    Access Control 機能をご利用の場合は、アクセスの制御を各種リソース (ノートブック、クラスター、ジョブ、テーブル) に手動で再適用してください。

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Azure エコシステムのディザスター リカバリー

他の Azure サービスを使用している場合は、それらのサービスにもディザスター リカバリーのベスト プラクティスを必ず実装してください。 たとえば、外部の Hive metastore インスタンスを使用する場合は、[Azure SQL Server](../sql-database/sql-database-disaster-recovery.md)、[Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)、[Azure Database for MySQL](../mysql/concepts-business-continuity.md) のいずれかまたはすべてのディザスター リカバリーを検討する必要があります。 ディザスター リカバリーの全般的な情報については、「[Azure アプリケーションのディザスター リカバリー](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications)」を参照してください。

## <a name="next-steps"></a>次の手順

詳しくは、[Azure Databricks のドキュメント](https://docs.azuredatabricks.net/user-guide/index.html)をご覧ください。
