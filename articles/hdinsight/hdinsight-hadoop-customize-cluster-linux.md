---
title: スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする
description: スクリプト アクションを使用して HDInsight クラスターにカスタム コンポーネントを追加します。 スクリプト アクションは、クラスター構成をカスタマイズするために使用できる Bash スクリプトです。 または、Hue、Solr、R などのサービスやユーティリティを追加します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, contperf-fy21q2
ms.date: 03/09/2021
ms.openlocfilehash: 0b0fc1062f9e57ab716aa0fa88f90924f0485b08
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864875"
---
# <a name="customize-azure-hdinsight-clusters-by-using-script-actions"></a>スクリプト アクションを使用して Azure HDInsight クラスターをカスタマイズする

Azure HDInsight には、クラスターをカスタマイズするためにカスタム スクリプトを呼び出す **スクリプト アクション** という構成方法があります。 これらのスクリプトは、追加コンポーネントのインストールおよび構成設定の変更に使用されます。 スクリプト アクションは、クラスターの作成中または作成後に使用できます。

スクリプト アクションは、HDInsight アプリケーションとして Azure Marketplace にも発行することができます。 HDInsight のアプリケーションについて詳しくは、「[Azure Marketplace への HDInsight アプリケーションの発行](hdinsight-apps-publish-applications.md)」をご覧ください。

## <a name="understand-script-actions"></a>スクリプト アクションについて

スクリプト アクションは、HDInsight クラスター内のノードで実行される Bash スクリプトです。 スクリプト アクションの特性と機能を次に示します。

- HDInsight クラスターからアクセスできる URI に保存されている必要があります。 たとえば保存スペースとして、次の場所を使用できます。

  - 通常 (非 ESP) のクラスターの場合:
    - Data Lake Storage Gen1/Gen2: HDInsight が Data Lake Storage へのアクセスに使用するサービス プリンシパルには、スクリプトに対する読み取りアクセスが必要です。 Data Lake Storage Gen1 に格納されているスクリプトの URI の形式は、`adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` になります。
    - Azure ストレージ アカウントの BLOB (HDInsight クラスターのプライマリ ストレージ アカウントまたはセカンダリ ストレージ アカウント)。 HDInsight には、両方のタイプのストレージ アカウントに対するアクセス権がクラスターの作成時に付与されます。

    > [!IMPORTANT]  
    > この Azure ストレージ アカウントでは、ストレージ キーのローテーションを行わないでください。そこにスクリプトが格納されている後続のスクリプト アクションが失敗します。

    - `http://` パスを介してアクセス可能なパブリック ファイル共有サービス。 たとえば、Azure Blob、GitHub、OneDrive などです。 URI の例については、「[スクリプト アクションのサンプル スクリプト](#example-script-action-scripts)」をご覧ください。
  - ESP を使用するクラスターの場合、`wasb://`、`wasbs://`、または `http[s]://` の URI がサポートされます。

- 特定の種類のノードのみで実行するように制限できます。 たとえば、ヘッド ノードやワーカー ノードなどです。
- 保存することも、"*アドホック*" に使うこともできます。

  - 保存済みスクリプト アクションには一意の名前が必要です。 保存済みスクリプトは、スケーリング操作でクラスターに追加される新しいワーカー ノードをカスタマイズするために使われます。 スケーリング操作が発生したとき、保存済みスクリプトによって、別の種類のノードに変更が適用されることもあります。 たとえば、ヘッド ノードなどです。
  - "*アドホック*" スクリプトは保存されません。 クラスターの作成時に使用されるスクリプト アクションは自動的に保存されます。 スクリプトの実行後にクラスターに追加された worker ノードには適用されません。 後で "*アドホック*" スクリプトを保存済みスクリプトに昇格したり、保存済みスクリプトを "*アドホック*" スクリプトに降格したりすることができます。 失敗したスクリプトは、保存するように指定した場合でも保存されません。

- 実行中にスクリプトによって使用されるパラメーターを受け取ることができます。
- クラスター ノードでルート レベルの権限を使用して実行されます。
- Azure portal、Azure PowerShell、Azure CLI、または HDInsight .NET SDK で使用できます。
- VM 上のサービス ファイルを削除または変更するスクリプト アクションは、サービスの正常性と可用性に影響を与える可能性があります。

クラスターには、実行されたすべてのスクリプトの履歴が保持されます。 履歴は、昇格または降格の操作のためスクリプトの ID を検索する必要がある場合に便利です。

> [!IMPORTANT]  
> スクリプト アクションで行われた変更を自動的に元に戻すことはできません。 スクリプトの変更は、手動またはスクリプトを使用して戻す必要があります。

## <a name="permissions"></a>アクセス許可

ドメイン参加済みの HDInsight クラスターでは、クラスターでスクリプト アクションを使用するときに、次の 2 つの Apache Ambari アクセス許可が必要になります。

* **AMBARI.RUN\_CUSTOM\_COMMAND**。 Ambari の管理者ロールには、既定でこのアクセス許可があります。
* **CLUSTER.RUN\_CUSTOM\_COMMAND**。 HDInsight クラスターの管理者と Ambari の管理者はいずれも、既定でこのアクセス許可を持っています。

ドメイン参加済みの HDInsight でのアクセス許可の設定については、「[Enterprise セキュリティ パッケージを使用して HDInsight クラスターを管理する](./domain-joined/apache-domain-joined-manage.md)」をご覧ください。

## <a name="access-control"></a>アクセス制御

自分が管理者または所有者ではない Azure サブスクリプションを使用している場合、自分の Azure アカウントには、HDInsight クラスターが含まれるリソース グループに対して `Contributor` 以上のアクセスが設定されている必要があります。

プロバイダーは、Azure サブスクリプションに対する共同作成者以上のアクセス権を持つユーザーによって前もって登録されていることが必要です。 プロバイダーの登録は、サブスクリプションに対する共同作成者アクセス権を持つユーザーが、リソースを作成したときに行われます。 リソースを作成しない場合は、[REST を使用してプロバイダーを登録する](/rest/api/resources/providers#Providers_Register)方法に関する記事を参照してください。

アクセス管理の操作について詳しくは、次の記事をご覧ください。

- [Azure Portal でのアクセス管理の概要](../role-based-access-control/overview.md)
- [Azure サブスクリプション リソースへのアクセスを管理するための Azure ロールの割り当て](../role-based-access-control/role-assignments-portal.md)

## <a name="methods-for-using-script-actions"></a>スクリプト アクションを使用する方法

スクリプト アクションは、クラスターが最初に作成されたとき、または既存のクラスターで実行されているときに実行されるように構成できます。

### <a name="script-action-in-the-cluster-creation-process"></a>クラスターの作成処理でのスクリプト アクション

クラスターの作成時に使用されるスクリプト アクションは、既存のクラスターで実行されるスクリプト アクションとは少し異なります。

- このスクリプトは、自動的に保存されます。
- スクリプトのエラーによって、クラスターの作成プロセスが失敗することがあります。

次の図は、作成処理中にスクリプト アクションが実行された場合を示しています。


:::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/cluster-provisioning-states.png" alt-text="クラスター作成時の段階" border="false":::

スクリプトは HDInsight の構成中に実行されます。 スクリプトは、クラスター内の指定されたすべてのノードで並列して実行されます。 ノードのルート権限で実行されます。

サービスの停止と開始などの操作も行うことができます (Apache Hadoop 関連のサービスも含みます)。 サービスを停止する場合は、スクリプトが完了する前に、Ambari やその他の Hadoop 関連のサービスが実行していることを確認します。 これらの必要なサービスでは、クラスターの作成時にクラスターが正常に稼動しているかどうかの確認が行われます。

クラスターの作成時に、指定された順序で呼び出される。 複数のスクリプト アクションを指定できます。

> [!IMPORTANT]  
> スクリプト アクションは、60 分以内に完了する必要があります。完了しないとタイムアウトします。クラスターのプロビジョニング中に、スクリプトは他のセットアップ プロセスや構成プロセスと同時に実行されます。 CPU 時間やネットワーク帯域幅などのリソースの競合が原因で、開発環境の場合よりスクリプトの完了に時間がかかる可能性があります。
>
> スクリプトの実行時間を最小限に抑えるために、ソースからアプリケーションをダウンロードしてコンパイルするなどのタスクを実行しないようにしてください。 アプリケーションをプリコンパイルし、バイナリを Azure Storage に格納します。

### <a name="script-action-on-a-running-cluster"></a>実行中のクラスターでのスクリプト アクション

既に実行中のクラスターで実行されたスクリプトのエラーによって、クラスターの状態が自動的に失敗に変更されることはありません。 スクリプトが完了した後、クラスターは実行中状態に戻ります。 クラスターが実行中状態であっても、失敗したスクリプトによって何かが破壊されている可能性があります。 たとえば、スクリプトによって、クラスターに必要なファイルが削除されることがあります。

スクリプト アクションは、ルート権限で実行されます。 スクリプトをクラスターに適用する前に、そのスクリプトによる処理を理解しておく必要があります。

クラスターにスクリプトを適用すると、クラスターの状態は **実行中** から **承認済み** に変わります。 その後、**HDInsight 構成** に変化し、最後に、正常なスクリプトは **実行中** に戻ります。 スクリプトの状態はスクリプト アクションの履歴に記録されます。 この情報は、スクリプトが成功したか失敗したかを示します。 たとえば、`Get-AzHDInsightScriptActionHistory` PowerShell コマンドレットでは、スクリプトの状態が示されます。 次のテキストのような情報が返されます。

```output
ScriptExecutionId : 635918532516474303
StartTime         : 8/14/2017 7:40:55 PM
EndTime           : 8/14/2017 7:41:05 PM
Status            : Succeeded
```

> [!IMPORTANT]  
> クラスターが作成された後で、クラスターのユーザー、管理者、パスワードを変更した場合、このクラスターに対してスクリプト アクションを実行すると失敗する可能性があります。 worker ノードを対象とする保存済みスクリプト アクションがある場合、クラスターのサイズを変更すると、スクリプトは失敗する可能性があります。

## <a name="example-script-action-scripts"></a>スクリプト アクションのサンプル スクリプト

スクリプト アクションのスクリプトは、次のユーティリティで使用できます。

* Azure portal
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight は、HDInsight クラスターで次のコンポーネントをインストールするためのスクリプトを提供します。

| 名前 | スクリプト |
| --- | --- |
| Azure Storage アカウントの追加 |`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`. 「[HDInsight にストレージ アカウントを追加する](hdinsight-hadoop-add-storage.md)」をご覧ください。 |
| Hue のインストール |`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`. 「[HDInsight Hadoop クラスターに Hue をインストールして使用する](hdinsight-hadoop-hue-linux.md)」をご覧ください。 |
| Hive ライブラリの事前読み込み |`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`. 「[HDInsight クラスターを作成するときにカスタム Apache Hive ライブラリを追加する](hdinsight-hadoop-add-hive-libraries.md)」をご覧ください。 |

## <a name="script-action-during-cluster-creation"></a>クラスターの作成時のスクリプト アクション

このセクションでは、HDInsight クラスターの作成時にスクリプト アクションを使用するさまざまな方法について説明します。

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>クラスターの作成時に Azure Portal からスクリプト アクションを使用する

1. 「[Azure portal を使用して HDInsight で Linux ベースのクラスターを作成する](hdinsight-hadoop-create-linux-clusters-portal.md)」で説明されているように、クラスターの作成を開始します。 **[構成と価格]** タブで、**[+ スクリプト アクションの追加]** を選択します。

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/azure-portal-cluster-configuration-scriptaction.png" alt-text="Azure portal クラスター スクリプト操作":::

1. __[スクリプトの選択]__ エントリで、事前に作成されたスクリプトを選択します。 カスタム スクリプトを使用するには、__[カスタム]__ を選択します。 次に、スクリプトの __[名前]__ と __[バッシュ スクリプト URI]__ を指定します。

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="選択したスクリプト形式でスクリプトを追加":::

   スクリプト形式の要素を次の表に示します。

   | プロパティ | 値 |
   | --- | --- |
   | スクリプトの選択 | 独自のスクリプトを使用するには、__[カスタム]__ を選択します。 それ以外の場合は、用意されているスクリプトのいずれかを選択します。 |
   | 名前 |スクリプト アクションの名前を指定します。 |
   | Bash スクリプト URI |スクリプトの URI を指定します。 |
   | Head/Worker/ZooKeeper |スクリプトを実行するノードを指定します: **[ヘッド]** 、 **[ワーカー]** 、または **[ZooKeeper]** 。 |
   | パラメーター |スクリプトで必要な場合は、パラメーターを指定します。 |

   スケーリング操作中にスクリプトが確実に適用されるようにするには、__[スクリプト操作を保持する]__ エントリを使用します。

1. __[作成]__ を選択してスクリプトを保存します。 別のスクリプトを追加するには、__[+ 新規で送信]__ を使用します。

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts-actions.png" alt-text="HDInsight の複数のスクリプト アクション":::

   スクリプトの追加が完了したら、**[構成と価格]** タブに戻ります。

1. 残りのクラスター作成手順は、通常どおりに行います。

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

- [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)
- [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>クラスターの作成時に Azure PowerShell からスクリプト アクションを使用する

このセクションでは、[Add-AzHDInsightScriptAction](/powershell/module/az.hdinsight/add-azhdinsightscriptaction) コマンドレットを使用して、クラスターのカスタマイズを行うスクリプトを呼び出します。 始める前に、Azure PowerShell をインストールして構成します。 これらの PowerShell コマンドを使用するには、[Az モジュール](/powershell/azure/)が必要です。

次のスクリプトでは、PowerShell を使用してクラスターを作成するときに、スクリプト アクションを適用する方法を示します。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

クラスターが作成されるまでに数分かかる場合があります。

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>クラスターの作成時に HDInsight .NET SDK からスクリプト アクションを使用する

HDInsight .NET SDK では、.NET アプリケーションから HDInsight を簡単に操作できるクライアント ライブラリが提供されています。 コード サンプルについては、「[スクリプト操作](/dotnet/api/overview/azure/hdinsight#script-actions)」を参照してください。

## <a name="script-action-to-a-running-cluster"></a>実行中のクラスターに対するスクリプト アクション

このセクションでは、実行中のクラスターにスクリプト アクションを適用する方法を説明します。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>実行中のクラスターに Azure Portal からスクリプト アクションを適用する

1. [Azure portal](https://portal.azure.com) にサインインし、自分のクラスターを見つけます。

1. 既定のビューの **[設定]** で、**[スクリプト アクション]** を選択します。

1. **[スクリプト アクション]** ページの上部で、**[+ 新規で送信]** を選択します。

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png" alt-text="実行中のクラスターにスクリプトを追加する":::

1. __[スクリプトの選択]__ エントリで、事前に作成されたスクリプトを選択します。 カスタム スクリプトを使用するには、__[カスタム]__ を選択します。 次に、スクリプトの __[名前]__ と __[バッシュ スクリプト URI]__ を指定します。

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdinsight-select-script.png" alt-text="選択したスクリプト形式でスクリプトを追加":::

   スクリプト形式の要素を次の表に示します。

   | プロパティ | 値 |
   | --- | --- |
   | スクリプトの選択 | 独自のスクリプトを使用するには、 __[カスタム]__ を選択します。 それ以外の場合、提供されているスクリプトを選択します。 |
   | 名前 |スクリプト アクションの名前を指定します。 |
   | Bash スクリプト URI |スクリプトの URI を指定します。 |
   | ヘッド/ワーカー/Zookeeper |スクリプトを実行するノードを指定します: **[ヘッド]** 、 **[ワーカー]** 、または **[ZooKeeper]** 。 |
   | パラメーター |スクリプトで必要な場合は、パラメーターを指定します。 |

   スケーリング操作中にスクリプトが確実に適用されるようにするには、__[スクリプト操作を保持する]__ エントリを使用します。

1. 最後に、**[作成]** ボタンを選択して、スクリプトをクラスターに適用します。

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>実行中のクラスターに Azure PowerShell からスクリプト アクションを適用する

これらの PowerShell コマンドを使用するには、[Az モジュール](/powershell/azure/)が必要です。 次の例では、実行中のクラスターにスクリプト アクションを適用する方法を示します。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=105-117)]

操作が完了すると、次のようなテキストが表示されます。

```output
OperationState  : Succeeded
ErrorMessage    :
Name            : Giraph
Uri             : https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
Parameters      :
NodeTypes       : {HeadNode, WorkerNode}
```

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-cli"></a>実行中のクラスターに Azure CLI からスクリプト アクションを適用する

始める前に、Azure CLI をインストールして構成します。 最新バージョンを使用していることを確認します。 詳細については、「 [Azure CLI のインストール](/cli/azure/install-azure-cli)」を参照してください。

1. Azure サブスクリプションに対して認証を行います。

   ```azurecli
   az login
   ```

1. 実行中のクラスターにスクリプト アクションを適用します。

   ```azurecli
   az hdinsight script-action execute --cluster-name CLUSTERNAME --name SCRIPTNAME --resource-group RESOURCEGROUP --roles ROLES
   ```

   有効なロールは、`headnode`、`workernode`、`zookeepernode`、`edgenode` です。 スクリプトを複数のノードの種類に適用する必要がある場合は、ロールをスペースで区切ります。 たとえば、「 `--roles headnode workernode` 」のように入力します。

   スクリプトを保存するには、`--persist-on-success` を追加します。 また、`az hdinsight script-action promote` を使用して、スクリプトを後日保存することもできます。

### <a name="apply-a-script-action-to-a-running-cluster-by-using-rest-api"></a>REST API を使用して実行中のクラスターにスクリプト アクションを適用する

「[Cluster REST API in Azure HDInsight (Azure HDInsight でのクラスター REST API)](/rest/api/hdinsight/hdinsight-cluster)」をご覧ください。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>実行中のクラスターに HDInsight .NET SDK からスクリプト アクションを適用する

.NET SDK を使用してスクリプトをクラスターに適用する例については、「[Apply a Script Action against a running Linux-based HDInsight cluster (実行中の Linux ベースの HDInsight クラスターに対してスクリプト アクションを適用する)](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)」をご覧ください。

## <a name="view-history-and-promote-and-demote-script-actions"></a>履歴を表示し、スクリプト アクションを昇格および降格する

### <a name="the-azure-portal"></a>Azure ポータル

1. [Azure portal](https://portal.azure.com) にサインインし、自分のクラスターを見つけます。

1. 既定のビューの **[設定]** で、**[スクリプト アクション]** を選択します。

1. [スクリプト アクション] セクションに、このクラスター用のスクリプトの履歴が表示されます。 この情報には、保存されたスクリプトの一覧が含まれています。 次のスクリーンショットでは、このクラスターで Solr スクリプトが実行されたことが示されています。 スクリーンショットでは、保存されたスクリプトは示されていません。

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png" alt-text="ポータル スクリプト アクションの送信履歴":::

1. 履歴からスクリプトを選択すると、このスクリプトの **[プロパティ]** セクションが表示されます。 画面の上部から、スクリプトを再実行または昇格できます。

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png" alt-text="スクリプト アクション プロパティの昇格":::

1. また、スクリプト アクション セクションのエントリの右側にある省略記号 **[...]** を選択して、アクションを実行することもできます。

   :::image type="content" source="./media/hdinsight-hadoop-customize-cluster-linux/hdi-delete-promoted-sa.png" alt-text="保存済みスクリプト アクションの削除":::

### <a name="azure-powershell"></a>Azure PowerShell

| コマンドレット | 機能 |
| --- | --- |
| `Get-AzHDInsightPersistedScriptAction` |保存済みスクリプト アクションの情報を取得します。 このコマンドレットは、スクリプトによって実行された操作を元に戻すのではなく、保存されたフラグだけを削除します。|
| `Get-AzHDInsightScriptActionHistory` |クラスターに適用されたスクリプト アクションの履歴、または特定のスクリプトの詳細を取得します。 |
| `Set-AzHDInsightPersistedScriptAction` |`ad hoc` スクリプト アクションを保存済みスクリプト アクションに昇格します。 |
| `Remove-AzHDInsightPersistedScriptAction` |保存済みスクリプト アクションを `ad hoc` アクションに降格します。 |

次のスクリプトの例では、昇格のコマンドレットを使用してから、スクリプトを降格しています。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="azure-cli"></a>Azure CLI

| command | 説明 |
| --- | --- |
| [`az hdinsight script-action delete`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-delete) |クラスターの指定した永続化されたスクリプト アクションを削除します。 このコマンドは、スクリプトによって実行された操作を元に戻すのではなく、保存されたフラグだけを削除します。|
|[`az hdinsight script-action execute`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-execute)|指定した HDInsight クラスター上でスクリプト アクションを実行します。|
| [`az hdinsight script-action list`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list) |指定したクラスターに対する永続化されたスクリプト アクションを一覧表示します。 |
|[`az hdinsight script-action list-execution-history`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-list-execution-history)|指定したクラスターに対するスクリプトの実行履歴をすべて一覧表示します。|
|[`az hdinsight script-action promote`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-promote)|指定したアドホック スクリプトの実行を、保存済みスクリプトに昇格します。|
|[`az hdinsight script-action show-execution-details`](/cli/azure/hdinsight/script-action#az-hdinsight-script-action-show-execution-details)|指定されたスクリプト実行 ID のスクリプト実行の詳細を取得します。|

### <a name="hdinsight-net-sdk"></a>HDInsight .NET SDK

.NET SDK を使用してクラスターからスクリプトの履歴を取得し、スクリプトを昇格または降格する例については、「[Apply a Script Action against a running Linux-based HDInsight cluster (実行中の Linux ベースの HDInsight クラスターに対してスクリプト アクションを適用する)](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)」をご覧ください。

> [!NOTE]  
> この例では、.NET SDK を使用して HDInsight アプリケーションをインストールする方法も示します。

## <a name="next-steps"></a>次のステップ

* [HDInsight 用のスクリプト アクションのスクリプトを開発する](hdinsight-hadoop-script-actions-linux.md)
* [HDInsight に Azure ストレージ アカウントを追加する](hdinsight-hadoop-add-storage.md)
* [スクリプト操作のトラブルシューティング](troubleshoot-script-action.md)
