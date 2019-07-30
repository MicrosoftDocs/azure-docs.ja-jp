---
title: スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする
description: スクリプト アクションを使用して Linux ベースの HDInsight クラスターにカスタム コンポーネントを追加します。 スクリプト アクションは、クラスター ノード上の Bash スクリプトであり、クラスター構成のカスタマイズや、サービスとユーティリティ (Hue、Solr、R など) の追加に使用できます。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 7885b03e9f92fc8e8c5b2c78049760cbed8d4dc7
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703975"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする

Azure HDInsight には、クラスターをカスタマイズするためにカスタム スクリプトを呼び出す**スクリプト アクション**という構成方法があります。 これらのスクリプトは、追加コンポーネントのインストールおよび構成設定の変更に使用されます。 スクリプト アクションは、クラスターの作成中または作成後に使用できます。

スクリプト アクションは、HDInsight アプリケーションとして Azure Marketplace にも発行することができます。 HDInsight のアプリケーションについて詳しくは、「[Azure Marketplace への HDInsight アプリケーションの発行](hdinsight-apps-publish-applications.md)」をご覧ください。

## <a name="permissions"></a>アクセス許可

ドメイン参加済みの HDInsight クラスターでは、クラスターでスクリプト アクションを使用するときに、次の 2 つの Apache Ambari アクセス許可が必要になります。

* **AMBARI.RUN\_CUSTOM\_COMMAND**。 Ambari の管理者ロールには、既定でこのアクセス許可があります。
* **CLUSTER.RUN\_CUSTOM\_COMMAND**。 HDInsight クラスターの管理者と Ambari の管理者はいずれも、既定でこのアクセス許可を持っています。

ドメイン参加済みの HDInsight でのアクセス許可の設定については、「[Enterprise セキュリティ パッケージを使用して HDInsight クラスターを管理する](./domain-joined/apache-domain-joined-manage.md)」をご覧ください。

## <a name="access-control"></a>アクセス制御

自分が管理者または所有者ではない Azure サブスクリプションを使用している場合、自分の Azure アカウントに、HDInsight クラスターが含まれるリソース グループに対して共同作成者以上のアクセスが設定されていることを確認する必要があります。

HDInsight クラスターを作成する場合は、Azure サブスクリプションに対して共同作成者以上のアクセスを持つ別のユーザーが、あらかじめ HDInsight のプロバイダーを登録しておく必要があります。 プロバイダーの登録は、サブスクリプションに対する共同作成者アクセス権を持つユーザーが、そのサブスクリプションで初めてリソースを作成したときに行われます。 また、[REST を使用してプロバイダーを登録する](https://msdn.microsoft.com/library/azure/dn790548.aspx)場合は、リソースの作成は不要です。

アクセス管理の操作について詳しくは、次の記事をご覧ください。

* [Azure Portal でのアクセス管理の概要](../role-based-access-control/overview.md)
* [Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../role-based-access-control/role-assignments-portal.md)

## <a name="understand-script-actions"></a>スクリプト アクションについて

スクリプト アクションは、HDInsight クラスター内のノードで実行される Bash スクリプトです。 スクリプト アクションの特性と機能を次に示します。

* HDInsight クラスターからアクセスできる URI に保存されている必要があります。 たとえば保存スペースとして、次の場所を使用できます。
    
    * 通常のクラスターの場合:
    
      * ADLS Gen1:HDInsight が Data Lake Storage へのアクセスに使用するサービス プリンシパルには、スクリプトに対する読み取りアクセスが必要です。 Data Lake Storage Gen1 に格納されているスクリプトの URI の形式は、`adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` になります。
      
      * Azure ストレージ アカウントの BLOB (HDInsight クラスターのプライマリ ストレージ アカウントまたはセカンダリ ストレージ アカウント)。 HDInsight には、両方のタイプのストレージ アカウントに対するアクセス権がクラスターの作成時に付与されます。

        > [!IMPORTANT]  
        > この Azure ストレージ アカウントでは、ストレージ キーのローテーションを行わないでください。そこにスクリプトが格納されている後続のスクリプト アクションが失敗します。

      * http:// パスを介してアクセス可能なパブリック ファイル共有サービス。 たとえば、Azure Blob、GitHub、OneDrive などです。

        URI の例については、「[スクリプト アクションのサンプル スクリプト](#example-script-action-scripts)」をご覧ください。

     * ESP を使用するクラスターの場合:
         
         * wasb://、wasbs://、または http[s]:// の URI がサポートされます。
            
* 特定の種類のノードのみで実行するように制限できます。 たとえば、ヘッド ノードやワーカー ノードなどです。

* 保存することも、アドホックに使うこともできます。

    保存済みスクリプトは、スケーリング操作でクラスターに追加される新しいワーカー ノードをカスタマイズするために使われます。 スケーリング操作が発生したとき、保存済みスクリプトによって、別の種類のノードに変更が適用されることもあります。 たとえば、ヘッド ノードなどです。

  > [!IMPORTANT]  
  > 保存済みスクリプト アクションには一意の名前が必要です。

    アドホック スクリプトは保存されません。 スクリプトの実行後にクラスターに追加された worker ノードには適用されません。 後でアドホック スクリプトを保存済みスクリプトに昇格したり、保存済みスクリプトをアドホック スクリプトに降格したりできます。

  > [!IMPORTANT]  
  > クラスターの作成時に使用されるスクリプト アクションは自動的に保存されます。
  >
  > 失敗したスクリプトは、保存するように指定した場合でも保存されません。

* 実行中にスクリプトによって使用されるパラメーターを受け取ることができます。

* クラスター ノードでルート レベルの権限を使用して実行されます。

* Azure portal、Azure PowerShell、Azure クラシック CLI、または HDInsight .NET SDK で使用できます。

クラスターには、実行されたすべてのスクリプトの履歴が保持されます。 履歴は、昇格または降格の操作のためスクリプトの ID を検索する必要がある場合に便利です。

> [!IMPORTANT]  
> スクリプト アクションで行われた変更を自動的に元に戻すことはできません。 スクリプトの変更は、手動またはスクリプトを使用して戻す必要があります。

### <a name="script-action-in-the-cluster-creation-process"></a>クラスターの作成処理でのスクリプト アクション

クラスターの作成時に使用されるスクリプト アクションは、既存のクラスターで実行されるスクリプト アクションとは少し異なります。

* このスクリプトは、自動的に保存されます。

* スクリプトのエラーによって、クラスターの作成プロセスが失敗することがあります。

次の図は、作成処理中にスクリプト アクションが実行された場合を示しています。

![クラスター作成時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトは HDInsight の構成中に実行されます。 スクリプトは、クラスター内の指定されたすべてのノードで並列して実行されます。 ノードのルート権限で実行されます。

> [!NOTE]  
> サービスの停止と開始などの操作も行うことができます (Apache Hadoop 関連のサービスも含みます)。 サービスを停止する場合は、スクリプトが完了する前に Ambari サービスや他の Hadoop 関連のサービスが実行していることを確認します。 これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているかどうかを確認する必要があります。


クラスターの作成時に、指定された順序で呼び出される。 複数のスクリプト アクションを指定できます。

> [!IMPORTANT]  
> スクリプト アクションは、60 分以内に完了する必要があります。完了しないとタイムアウトします。クラスターのプロビジョニング中に、スクリプトは他のセットアップ プロセスや構成プロセスと同時に実行されます。 CPU 時間やネットワーク帯域幅などのリソースの競合が原因で、開発環境の場合よりスクリプトの完了に時間がかかる可能性があります。
>
> スクリプトの実行時間を最小限に抑えるために、ソースからアプリケーションをダウンロードしてコンパイルするなどのタスクを実行しないようにしてください。 アプリケーションをプリコンパイルし、バイナリを Azure Storage に格納します。


### <a name="script-action-on-a-running-cluster"></a>実行中のクラスターでのスクリプト アクション

既に実行中のクラスターで実行されたスクリプトのエラーによって、クラスターの状態が自動的に失敗に変更されることはありません。 スクリプトが完了した後、クラスターは実行中状態に戻ります。

> [!IMPORTANT]  
> クラスターが実行中状態であっても、失敗したスクリプトによって何かが破壊されている可能性があります。 たとえば、スクリプトによって、クラスターに必要なファイルが削除されることがあります。
>
> スクリプト アクションは、ルート権限で実行されます。 スクリプトをクラスターに適用する前に、そのスクリプトによる処理を必ず理解しておく必要があります。

クラスターにスクリプトを適用すると、クラスターの状態は**実行中**から**承認済み**に変わります。 その後、**HDInsight 構成**に変化し、最後に、正常なスクリプトは**実行中**に戻ります。 スクリプトの状態はスクリプト アクションの履歴に記録されます。 この情報は、スクリプトが成功したか失敗したかを示します。 たとえば、`Get-AzHDInsightScriptActionHistory` PowerShell コマンドレットでは、スクリプトの状態が示されます。 次のテキストのような情報が返されます。

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!IMPORTANT]  
> クラスターが作成された後で、クラスターのユーザー、管理者、パスワードを変更した場合、このクラスターに対してスクリプト アクションを実行すると失敗する可能性があります。 worker ノードを対象とする保存済みスクリプト アクションがある場合、クラスターのサイズを変更すると、スクリプトは失敗する可能性があります。

## <a name="example-script-action-scripts"></a>スクリプト アクションのサンプル スクリプト

スクリプト アクションのスクリプトは、次のユーティリティで使用できます。

* Azure ポータル
* Azure PowerShell
* Azure クラシック CLI
* An HDInsight .NET SDK

HDInsight は、HDInsight クラスターで次のコンポーネントをインストールするためのスクリプトを提供します。

| EnableAdfsAuthentication | スクリプト |
| --- | --- |
| Azure Storage アカウントの追加 |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh` 「[HDInsight にストレージ アカウントを追加する](hdinsight-hadoop-add-storage.md)」をご覧ください。 |
| Hue のインストール |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh` 「[HDInsight Hadoop クラスターに Hue をインストールして使用する](hdinsight-hadoop-hue-linux.md)」をご覧ください。 |
| Giraph のインストール |`https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh` [HDInsight Hadoop クラスターへの Apache Giraph のインストール](hdinsight-hadoop-giraph-install-linux.md)に関する記事をご覧ください。 |
| Hive ライブラリの事前読み込み |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh` 「[HDInsight クラスターを作成するときにカスタム Apache Hive ライブラリを追加する](hdinsight-hadoop-add-hive-libraries.md)」をご覧ください。 |

## <a name="use-a-script-action-during-cluster-creation"></a>クラスターの作成時にスクリプト アクションを使用する

このセクションでは、HDInsight クラスターの作成時にスクリプト アクションを使用するさまざまな方法について説明します。

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>クラスターの作成時に Azure Portal からスクリプト アクションを使用する

1. 「[HDInsight で Apache Hadoop、Apache Spark、Apache Kafka などを使用してクラスターを設定する](hdinsight-hadoop-provision-linux-clusters.md)」で説明されているように、クラスターの作成を始めます。 クラスターを作成している間に、 __[クラスターの概要]__ ページが表示されます。 __[クラスターの概要]__ ページで、 __[詳細設定]__ の __[編集]__ リンクを選択します。

    ![[詳細設定] リンク](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. __[詳細設定]__ セクションで、 __[スクリプト アクション]__ を選択します。 __[スクリプト アクション]__ セクションで、 __[+ 新規で送信]__ を選択します。

    ![新しいスクリプト アクションの送信](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. __[スクリプトの選択]__ エントリで、事前に作成されたスクリプトを選択します。 カスタム スクリプトを使用するには、 __[カスタム]__ を選択します。 次に、スクリプトの __[名前]__ と __[バッシュ スクリプト URI]__ を指定します。

    ![選択したスクリプト形式でスクリプトを追加](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    スクリプト形式の要素を次の表に示します。

    | プロパティ | 値 |
    | --- | --- |
    | スクリプトの選択 | 独自のスクリプトを使用するには、 __[カスタム]__ を選択します。 それ以外の場合は、用意されているスクリプトのいずれかを選択します。 |
    | EnableAdfsAuthentication |スクリプト アクションの名前を指定します。 |
    | Bash スクリプト URI |スクリプトの URI を指定します。 |
    | Head/Worker/ZooKeeper |スクリプトを実行するノードを指定します: **[Head]** 、 **[Worker]** 、または **[ZooKeeper]** 。 |
    | parameters |スクリプトで必要な場合は、パラメーターを指定します。 |

    スケーリング操作中にスクリプトが確実に適用されるようにするには、 __[スクリプト操作を保持する]__ エントリを使用します。

5. __[作成]__ を選択してスクリプトを保存します。 別のスクリプトを追加するには、 __[+ 新規で送信]__ を使用します。

    ![複数のスクリプト アクション](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    スクリプトの追加が完了したら、 __[選択]__ ボタンを選択し、 __[次へ]__ ボタンをクリックして、 __[クラスターの概要]__ セクションに戻ります。

3. クラスターを作成するには、 __[クラスターの概要__] セクションで、 __[作成]__ を選択します。

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートからスクリプト アクションを使用する

スクリプト アクションを Azure Resource Manager テンプレートで使用できます。 例については、「[Create HDInsight Linux Cluster and run a script action (HDInsight Linux クラスターを作成してスクリプト アクションを実行する)](https://azure.microsoft.com/resources/templates/hdinsight-linux-run-script-action/)」をご覧ください。

この例では、次のコードを使用してスクリプト アクションが追加されます。

```json
"scriptActions": [
    {
        "name": "setenvironmentvariable",
        "uri": "[parameters('scriptActionUri')]",
        "parameters": "headnode"
    }
]
```

テンプレートをデプロイする方法について詳しくは、以下をご覧ください。

* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)

* [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>クラスターの作成時に Azure PowerShell からスクリプト アクションを使用する

このセクションでは、[Add-AzHDInsightScriptAction](https://docs.microsoft.com/powershell/module/az.hdinsight/add-azhdinsightscriptaction) コマンドレットを使用して、クラスターのカスタマイズを行うスクリプトを呼び出します。 始める前に、Azure PowerShell をインストールして構成します。 これらの PowerShell コマンドを使用するには、[Az モジュール](https://docs.microsoft.com/powershell/azure/overview)が必要です。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次のスクリプトでは、PowerShell を使用してクラスターを作成するときに、スクリプト アクションを適用する方法を示します。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

クラスターが作成されるまでに数分かかる場合があります。

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>クラスターの作成時に HDInsight .NET SDK からスクリプト アクションを使用する

HDInsight .NET SDK では、.NET アプリケーションから HDInsight を簡単に操作できるクライアント ライブラリが提供されています。 コード サンプルについては、「[.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action)」ご覧ください。

## <a name="apply-a-script-action-to-a-running-cluster"></a>実行中のクラスターにスクリプト アクションを適用する

このセクションでは、実行中のクラスターにスクリプト アクションを適用する方法を説明します。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>実行中のクラスターに Azure Portal からスクリプト アクションを適用する

[Azure portal](https://portal.azure.com) に移動します。

1. 左側のメニューから、 **[すべてのサービス]** を選択します。

1. **[ANALYTICS]** で **[HDInsight クラスター]** を選択します。

1. 一覧からクラスターを選択します。これにより、既定のビューが開きます。

1. 既定のビューの **[設定]** で、 **[スクリプト アクション]** を選択します。

1. **[スクリプト アクション]** ページの上部で、 **[+ 新規で送信]** を選択します。

    ![実行中のクラスターにスクリプトを追加する](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. __[スクリプトの選択]__ エントリで、事前に作成されたスクリプトを選択します。 カスタム スクリプトを使用するには、 __[カスタム]__ を選択します。 次に、スクリプトの __[名前]__ と __[バッシュ スクリプト URI]__ を指定します。

    ![選択したスクリプト形式でスクリプトを追加](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    スクリプト形式の要素を次の表に示します。

    | プロパティ | 値 |
    | --- | --- |
    | スクリプトの選択 | 独自のスクリプトを使用するには、 __[カスタム]__ を選択します。 それ以外の場合、提供されているスクリプトを選択します。 |
    | EnableAdfsAuthentication |スクリプト アクションの名前を指定します。 |
    | Bash スクリプト URI |スクリプトの URI を指定します。 |
    | Head/Worker/Zookeeper |スクリプトを実行するノードを指定します: **[Head]** 、 **[Worker]** 、または **[ZooKeeper]** 。 |
    | parameters |スクリプトで必要な場合は、パラメーターを指定します。 |

    スケーリング操作中にスクリプトが確実に適用されるようにするには、 __[スクリプト操作を保持する]__ エントリを使用します。

5. 最後に、 **[作成]** ボタンを選択して、スクリプトをクラスターに適用します。

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>実行中のクラスターに Azure PowerShell からスクリプト アクションを適用する

これらの PowerShell コマンドを使用するには、[Az モジュール](https://docs.microsoft.com/powershell/azure/overview)が必要です。

次の例では、実行中のクラスターにスクリプト アクションを適用する方法を示します。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

操作が完了すると、次のようなテキストが表示されます。

    OperationState  : Succeeded
    ErrorMessage    :
    Name            : Giraph
    Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
    Parameters      :
    NodeTypes       : {HeadNode, WorkerNode}

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>実行中のクラスターに Azure CLI からスクリプト アクションを適用する

始める前に、Azure CLI をインストールして構成します。 詳しくは、「[Azure クラシック CLI のインストール](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)」をご覧ください。

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

1. Azure Resource Manager モードに切り替えます。

    ```bash
    azure config mode arm
    ```

2. Azure サブスクリプションに対して認証を行います。

    ```bash
    azure login
    ```

3. 実行中のクラスターにスクリプト アクションを適用します。

    ```bash
    azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>
    ```

    このコマンドのパラメーターを省略した場合は、それを要求するメッセージが表示されます。 `-u` で指定したスクリプトがパラメーターを受け取る場合は、`-p` パラメーターを使用してそれらを指定できます。

    有効なノード タイプは、`headnode`、`workernode`、および `zookeeper` です。 スクリプトを複数のノード タイプに適用する必要がある場合、タイプをセミコロン `;` で区切って指定します。 たとえば、「 `-n headnode;workernode` 」のように入力します。

    スクリプトを保存するには、`--persistOnSuccess` を追加します。 また、`azure hdinsight script-action persisted set` を使用して、スクリプトを後日保存することもできます。

    ジョブが終了した後、次のようなテキストが出力されます。

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>REST API を使用して実行中のクラスターにスクリプト アクションを適用する

「[Cluster REST API in Azure HDInsight (Azure HDInsight でのクラスター REST API)](https://msdn.microsoft.com/library/azure/mt668441.aspx)」をご覧ください。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>実行中のクラスターに HDInsight .NET SDK からスクリプト アクションを適用する

.NET SDK を使用してスクリプトをクラスターに適用する例については、「[Apply a Script Action against a running Linux-based HDInsight cluster (実行中の Linux ベースの HDInsight クラスターに対してスクリプト アクションを適用する)](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)」をご覧ください。

## <a name="view-history-and-promote-and-demote-script-actions"></a>履歴を表示し、スクリプト アクションを昇格および降格する

### <a name="the-azure-portal"></a>Azure ポータル

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のメニューから、 **[すべてのサービス]** を選択します。

1. **[ANALYTICS]** で **[HDInsight クラスター]** を選択します。

1. 一覧からクラスターを選択します。これにより、既定のビューが開きます。

1. 既定のビューの **[設定]** で、 **[スクリプト アクション]** を選択します。

4. [スクリプト アクション] セクションに、このクラスター用のスクリプトの履歴が表示されます。 この情報には、保存されたスクリプトの一覧が含まれています。 次のスクリーンショットでは、このクラスターで Solr スクリプトが実行されたことが示されています。 スクリーンショットでは、保存されたスクリプトは示されていません。

    ![スクリプト操作](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. 履歴からスクリプトを選択すると、このスクリプトの **[プロパティ]** セクションが表示されます。 画面の上部から、スクリプトを再実行または昇格できます。

    ![スクリプト アクション、プロパティ](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. スクリプト アクション セクションのエントリの右側にある省略記号 **[...]** を選択して、アクションを実行することもできます。

    ![スクリプト アクション、省略記号](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="azure-powershell"></a>Azure PowerShell

| コマンドレット | Function |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |保存済みスクリプト アクションの情報を取得します。 |
| `Get-AzHDInsightScriptActionHistory` |クラスターに適用されたスクリプト アクションの履歴、または特定のスクリプトの詳細を取得します。 |
| `Set-AzHDInsightPersistedScriptAction` |アドホック スクリプト アクションを保存済みスクリプト アクションに昇格します。 |
| `Remove-AzHDInsightPersistedScriptAction` |保存済みスクリプト アクションをアドホック アクションに降格します。 |

> [!IMPORTANT]  
> `Remove-AzHDInsightPersistedScriptAction` では、スクリプトによって実行されたアクションは元に戻りません。 このコマンドレットは、保存済みフラグが削除されるだけです。

次のスクリプトの例では、昇格のコマンドレットを使用してから、スクリプトを降格しています。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="the-azure-classic-cli"></a>Azure クラシック CLI

| コマンドレット | Function |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |保存済みスクリプト アクションの一覧を取得します。 |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |特定の保存済みスクリプト アクションに関する情報を取得します。 |
| `azure hdinsight script-action history list <clustername>` |クラスターに適用されたスクリプト アクションの履歴を取得します。 |
| `azure hdinsight script-action history show <clustername> <scriptname>` |特定のスクリプト アクションに関する情報を取得します。 |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |アドホック スクリプト アクションを保存済みスクリプト アクションに昇格します。 |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |保存済みスクリプト アクションをアドホック アクションに降格します。 |

> [!IMPORTANT]  
> `azure hdinsight script-action persisted delete` では、スクリプトによって実行されたアクションは元に戻りません。 このコマンドレットは、保存済みフラグが削除されるだけです。

### <a name="the-hdinsight-net-sdk"></a>HDInsight .NET SDK

.NET SDK を使用してクラスターからスクリプトの履歴を取得し、スクリプトを昇格または降格する例については、「[Apply a Script Action against a running Linux-based HDInsight cluster (実行中の Linux ベースの HDInsight クラスターに対してスクリプト アクションを適用する)](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)」をご覧ください。

> [!NOTE]  
> この例では、.NET SDK を使用して HDInsight アプリケーションをインストールする方法も示します。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは Apache Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用します。 Microsoft Azure は、オープン ソース テクノロジの一般的なレベルのサポートを提供します。 詳しくは、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」の**サポート範囲**に関するセクションを参照してください。 HDInsight サービスでは、組み込みのコンポーネントに対してさらに高いレベルのサポートを提供しています。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

* **組み込みコンポーネント**。 これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。 次のコンポーネントは、このカテゴリに属します。

  * [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ResourceManager。
  * Hive クエリ言語 [HiveQL](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)。
  * [Apache Mahout](https://mahout.apache.org/)。 
    
    クラスター コンポーネントの完全な一覧は、「[HDInsight で使用できる Apache Hadoop コンポーネントおよびバージョンとは](hdinsight-component-versioning.md)」から入手できます。

* **カスタム コンポーネント**。 クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

> [!WARNING]  
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、 これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 Microsoft サポートで問題を解決できる場合があります。 または、オープン ソース テクノロジに関して、深い専門知識が入手できる場所への参加をお願いすることになる場合もあります。 多くのコミュニティ サイトを使用できます。 たとえば、[HDInsight の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)や、[Stack Overflow](https://stackoverflow.com) などです。 
>
> Apache プロジェクトにも、[Apache の Web サイト](https://apache.org)にプロジェクトのサイトがあります。 たとえば、[Hadoop](https://hadoop.apache.org/) などです。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。 コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。 以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. **ジョブの送信**。 Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。

2. **クラスターのカスタマイズ**。 クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。

3. **サンプル**。 よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。 これらのサンプルはサポートなしで提供されます。

## <a name="troubleshooting"></a>トラブルシューティング

Ambari の Web UI を使用すると、スクリプト アクションによってログに記録された情報を表示できます。 クラスターの作成中にスクリプトでエラーが発生した場合は、クラスターに関連付けられた既定のストレージ アカウントのログを利用することもできます。 このセクションでは、これら両方のオプションを使用してログを取得する方法について説明します。

### <a name="the-apache-ambari-web-ui"></a>Apache Ambari Web UI

1. ブラウザーで https://CLUSTERNAME.azurehdinsight.net に移動します。 **CLUSTERNAME** を、使用する HDInsight クラスターの名前に置き換えます。

    プロンプトが表示されたら、クラスターの管理者アカウント名 **admin** とパスワードを入力します。 Web フォームで管理者の資格情報の再入力が必要な場合があります。

2. ページ上部のバーから **[OPS]** エントリを選択します。 これにより、Ambari を使用してクラスターで実行される、現在と過去の操作の一覧が表示されます。

    ![Ambari Web UI バーで OPS を選択](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. **[Operations]** 列で **run\_customscriptaction** エントリを探します。 これらのエントリは、スクリプト アクションの実行時に作成されます。

    ![操作のスクリーンショット](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    **STDOUT** と **STDERR** の出力を表示するには、**run\customscriptaction** エントリを選択してリンクをたどります。 この出力結果はスクリプトの実行時に生成され、有益な情報が含まれていることがあります。

### <a name="access-logs-from-the-default-storage-account"></a>既定のストレージ アカウントからログにアクセスする

スクリプト エラーのためにクラスターの作成が失敗した場合、クラスター ストレージ アカウントにログが保持されます。

* ストレージ ログは、 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`にあります。

    ![操作のスクリーンショット](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    このディレクトリの下で、**ヘッド ノード**、**ワーカー ノード**、および **zookeeper ノード**ごとにログが整理されています。 次の例を参照してください。

    * **ヘッド ノード**: `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **ワーカー ノード**: `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper ノード**: `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 対応するホストのすべての **stdout** と **stderr** が、ストレージ アカウントにアップロードされます。 各スクリプト アクションに対して、**output-\*.txt** と **errors-\*.txt** が 1 つずつあります。 **output-*.txt** ファイルには、ホストで実行されたスクリプトの URI に関する情報が含まれます。 次のテキストはこの情報の例です。

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 同じ名前のスクリプト アクション クラスターを繰り返し作成できます。 そのような場合は、**DATE** フォルダー名に基づいて適切なログを識別できます。 たとえば、異なる日付で作成されるクラスターのフォルダー構造 **mycluster** は、ログ エントリには次のように表示されます。

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 同じ日に同じ名前のスクリプト アクション クラスターを作成する場合は、一意のプレフィックスを使用して該当するログ ファイルを識別できます。

* 12:00 AM (深夜 0 時) 近くにクラスターを作成すると、ログ ファイルが 2 日間にまたがる可能性があります。 そのような場合は、同じクラスターに日付が異なる 2 つのフォルダーが作成されます。

* 既定のコンテナーへのログ ファイルのアップロードは、特に大きなクラスターの場合、最大 5 分かかることがあります。 そのため、ログにアクセスする必要がある場合は、スクリプト アクションが失敗したときにクラスターをすぐに削除しないでください。

### <a name="ambari-watchdog"></a>Ambari ウォッチドッグ

> [!WARNING]  
> Linux ベースの HDInsight クラスターでは、Ambari ウォッチドッグ hdinsightwatchdog のパスワードは変更しないでください。 このアカウントのパスワードを変更すると、HDInsight クラスターで新しいスクリプト アクションを実行できなくなります。

### <a name="cant-import-name-blobservice"></a>名前 BlobService をインポートできない

__現象__。 スクリプト操作が失敗します。 Ambari で操作を表示すると、次のエラーに似たテキストが表示されます。

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__原因__。 このエラーは、HDInsight クラスターに含まれている Python Azure Storage クライアントをアップグレードする場合に発生します。 HDInsight は、Azure Storage クライアント 0.20.0 を予期しています。

__解決策__。 このエラーを解決するには、`ssh` を使用して各クラスター ノードを手動で接続します。 次のコマンドを実行して、ストレージ クライアントの正しいバージョンを再インストールします。

```bash
sudo pip install azure-storage==0.20.0
```

SSH を使用してクラスターに接続する方法については、「[SSH を使用して HDInsight (Apache Hadoop) に接続する](hdinsight-hadoop-linux-use-ssh-unix.md)」をご覧ください。

### <a name="history-doesnt-show-the-scripts-used-during-cluster-creation"></a>クラスターの作成時に使用されたスクリプトが履歴に表示されない

クラスターが 2016 年 3 月 15 日より前に作成された場合、スクリプト アクション履歴にエントリが表示されない可能性があります。 クラスターのサイズ変更を行うと、スクリプト アクション履歴にスクリプトが表示されます。

ただし、例外が 2 つあります。

* クラスターが 2015 年 9 月 1 日より前に作成された場合。 この日付は、スクリプト アクションが導入された日付です。 この日付より前に作成されたクラスターに関しては、クラスター作成にスクリプト アクションを使用できませんでした。

* クラスターを作成するときに、複数のスクリプト アクションを使用した場合。 または、複数のスクリプトに対して同じ名前を使用したか、複数のスクリプトに対して同じ名前と URI、異なるパラメーターを使用した場合。 この場合は、次のエラーが発生します。

    既存のスクリプトでスクリプト名が競合するため、このクラスターでは新しいスクリプト アクションを実行できません。 クラスターの作成時に指定されるスクリプト名はすべて一意である必要があります。 既存のスクリプトは、サイズ変更時に実行されます。

## <a name="next-steps"></a>次の手順

* [HDInsight 用のスクリプト アクションのスクリプトを開発する](hdinsight-hadoop-script-actions-linux.md)
* [HDInsight クラスターに Apache Giraph をインストールして使用する](hdinsight-hadoop-giraph-install-linux.md)
* [HDInsight に Azure ストレージ アカウントを追加する](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "クラスター作成時の段階"
