---
title: "スクリプト アクションを使って HDInsight クラスターをカスタマイズする - Azure | Microsoft Docs"
description: "スクリプト アクションを使用して Linux ベースの HDInsight クラスターにカスタム コンポーネントを追加します。 スクリプト アクションは、クラスター ノード上の Bash スクリプトであり、クラスター構成のカスタマイズや、サービスとユーティリティ (Hue、Solr、R など) の追加に使用できます。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 48e85f53-87c1-474f-b767-ca772238cc13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: b309108b4edaf5d1b198393aa44f55fc6aca231e
ms.openlocfilehash: cbfdc3c4b6a6d0f3174c9d7512c3aadd926a56df
ms.contentlocale: ja-jp
ms.lasthandoff: 08/15/2017

---
# <a name="customize-linux-based-hdinsight-clusters-using-script-action"></a>スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする

HDInsight には、クラスターをカスタマイズするカスタム スクリプトを呼び出す **スクリプト アクション** という構成オプションがあります。 これらのスクリプトは、追加コンポーネントのインストールおよび構成設定の変更に使用されます。 スクリプト アクションは、クラスターの作成中または作成後に使用できます。

> [!IMPORTANT]
> 既に実行されているクラスター上でスクリプト アクションを使用する機能は、Linux ベースの HDInsight クラスターでのみ使用できます。
>
> Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。

スクリプト アクションは、HDInsight アプリケーションとして Azure Marketplace にも発行することができます。 このドキュメントの例では、PowerShell と .NET SDK からスクリプト アクション コマンドを使用して HDInsight アプリケーションをインストールする方法を示します。 HDInsight のアプリケーションの詳細については、「 [Azure Marketplace への HDInsight アプリケーションの発行](hdinsight-apps-publish-applications.md)」を参照してください。

## <a name="permissions"></a>アクセス許可

ドメイン参加済みの HDInsight クラスターを使用している場合、このクラスターでスクリプト アクションを使用するには次の 2 つの Ambari アクセス許可が必要になります。

* **AMBARI.RUN\_CUSTOM\_COMMAND**: Ambari の管理者ロールは、既定でこのアクセス許可を持っています。
* **CLUSTER.RUN\_CUSTOM\_COMMAND**: HDInsight クラスターの管理者および Ambari の管理者は、既定でこのアクセス許可を持っています。

ドメイン参加済みの HDInsight でのアクセス許可の設定については、[ドメイン参加済みの HDInsight クラスターの管理](hdinsight-domain-joined-manage.md) に関する記事を参照してください。

## <a name="access-control"></a>Access control

自身が管理者または所有者ではない Azure サブスクリプションを使用している場合、自身の Azure アカウントに、HDInsight クラスターが含まれるリソース グループに対して**共同作成者**以上のアクセスが設定されていることを確認する必要があります。

また、HDInsight クラスターを作成する場合は、**共同作成者**以上のアクセスを持つ別のユーザーがあらかじめ HDInsight のプロバイダーを登録しておく必要があります。 プロバイダーの登録は、サブスクリプションに対する共同作成者アクセス権を持つユーザーが、そのサブスクリプションで初めてリソースを作成したときに行われます。 ただし、[REST を使ってプロバイダーを登録](https://msdn.microsoft.com/library/azure/dn790548.aspx)する方法もあり、その場合、リソースの作成は不要です。

アクセス管理の操作の詳細については、次のドキュメントを参照してください。

* [Azure Portal でのアクセス管理の概要](../active-directory/role-based-access-control-what-is.md)
* [Azure サブスクリプション リソースへのアクセスをロールの割り当てによって管理する](../active-directory/role-based-access-control-configure.md)

## <a name="understanding-script-actions"></a>スクリプト アクションについて

スクリプト アクションの実体は、URI とパラメーターを指定された Bash スクリプトです。 スクリプトは、HDInsight クラスター内のノードで実行されます。 スクリプト アクションの特性と機能を次に示します。

* HDInsight クラスターからアクセスできる URI に保存されている必要があります。 たとえば保存スペースとして、次の場所を使用できます。

    * HDInsight クラスターからアクセス可能な **Azure Data Lake Store** アカウント。 HDInsight での Azure Data Lake Store の使用について詳しくは、[Data Lake Store での HDInsight クラスターの作成](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)に関する記事を参照してください。

        Data Lake Store に保管しているスクリプトを使用する場合、URI の形式は `adl://DATALAKESTOREACCOUNTNAME.azuredatalakestore.net/path_to_file` になります。

        > [!NOTE]
        > HDInsight が Data Lake Store へのアクセスに使用するサービス プリンシパルには、スクリプトに対する読み取りアクセスが必要です。

    * **Azure Storage アカウント**の BLOB (HDInsight クラスターのプライマリ ストレージ アカウントまたはセカンダリ ストレージ アカウント)。 HDInsight には、両方のタイプのストレージ アカウントに対するアクセス権がクラスターの作成時に付与されます。

    * 一般のファイル共有サービス (Azure BLOB、GitHub、OneDrive、Dropbox など)。

        URI の例については、「[スクリプト アクションのサンプル スクリプト](#example-script-action-scripts)」セクションを参照してください。

        > [!WARNING]
        > __汎用__の Azure ストレージ アカウントがサポートされるのは HDInsight のみです。 現時点では、__Blob Storage__ タイプのアカウントはサポートされません。

* **特定のノード タイプでのみ実行するように** (ヘッド ノードやワーカー ノードなど) に制限できます。

  > [!NOTE]
  > HDInsight Premium で使用する場合は、エッジ ノードでスクリプトを使用するように指定できます。

* **保存**することも、**アドホック**に使うこともできます。

    **保存済み**スクリプトは、スクリプトの実行後にクラスターに追加された worker ノードに適用されます。 クラスターのスケールアップ時がその一例です。

    保存済みスクリプトによって、ヘッド ノードなどの別のタイプのノードに変更が適用されることもあります。

  > [!IMPORTANT]
  > 保存済みスクリプト アクションには一意の名前が必要です。

    **アドホック** スクリプトは保存されません。 スクリプトの実行後にクラスターに追加された worker ノードには適用されませんが、 後でアドホック スクリプトを保存済みスクリプトに昇格したり、保存済みスクリプトをアドホック スクリプトに降格したりできます。

  > [!IMPORTANT]
  > クラスターの作成時に使用されるスクリプト アクションは自動的に保存されます。
  >
  > 失敗したスクリプトは、保存するように指定した場合でも保存されません。

* 実行中にスクリプトによって使用される**パラメーター**を受け取ることができます。
* クラスター ノードで**ルート レベルの権限**を使用して実行されます。
* **Azure Portal**、**Azure PowerShell**、**Azure CLI**、または **HDInsight .NET SDK** で使用できます。

クラスターには、実行されたすべてのスクリプトの履歴が保持されます。 履歴は、昇格または降格の操作のためスクリプトの ID を検索する必要がある場合に便利です。

> [!IMPORTANT]
> スクリプト アクションで行われた変更を自動的に元に戻すことはできません。 スクリプトの変更は、手動またはスクリプトを使用して戻す必要があります。


### <a name="script-action-in-the-cluster-creation-process"></a>クラスターの作成処理でのスクリプト アクション

クラスターの作成時に使用されるスクリプト アクションは、既存のクラスターで実行されるスクリプト アクションとは少し異なります。

* このスクリプトは、**自動的に保存**されます。
* スクリプトの**エラー**によって、クラスターの作成プロセスが失敗することがあります。

次の図は、作成処理中にスクリプト アクションが実行された場合を示しています。

![クラスター作成時の HDInsight クラスターのカスタマイズと段階][img-hdi-cluster-states]

スクリプトは HDInsight の構成中に実行されます。 この段階で、スクリプトは、クラスター内の指定されたすべてのノードで並列して実行され、それらのノードに対するルート権限で実行されます。

> [!NOTE]
> スクリプトはルート レベルの権限でクラスター ノードで実行されるため、Hadoop 関連のサービスを含め、サービスの停止や開始などの操作を行うことができます。 サービスを停止する場合は、スクリプトの実行が完了する前に Ambari サービスや他の Hadoop 関連のサービスが稼働していることを確認する必要があります。 これらのサービスでは、クラスターの作成時にクラスターが正常に稼動しているかどうかを確認する必要があります。


クラスターの作成時に、指定された順序で呼び出される 複数のスクリプト アクションを指定できます。

> [!IMPORTANT]
> スクリプト アクションは 60 分以内に完了する必要があります。そうしないと、タイムアウトします。 クラスターのプロビジョニング中に、スクリプトは他のセットアップ プロセスや構成プロセスと同時に実行されます。 CPU 時間やネットワーク帯域幅などのリソースの競合が原因で、開発環境の場合よりスクリプトの完了に時間がかかる可能性があります。
>
> スクリプトの実行時間を最小限に抑えるために、ソースからアプリケーションをダウンロードしてコンパイルするなどのタスクを実行しないようにしてください。 代わりに、アプリケーションを事前にコンパイルし、バイナリを Azure Storage に格納して、


### <a name="script-action-on-a-running-cluster"></a>実行中のクラスターでのスクリプト アクション

クラスターの作成時に使用されるスクリプトとは異なり、既に実行中のクラスターで実行されたスクリプトのエラーによって、クラスターの状態が自動的に失敗に変更されることはありません。 スクリプトが完了すると、クラスターは "実行中" 状態に戻ります。

> [!IMPORTANT]
> クラスターが "実行中" 状態であっても、失敗したスクリプトによって何かが破壊されている可能性があります。 たとえば、スクリプトによって、クラスターに必要なファイルが削除されることがあります。
>
> スクリプト アクションはルート権限で実行されるため、スクリプトをクラスターに適用する前に、そのスクリプトによる処理を必ず理解しておく必要があります。

スクリプトをクラスターに適用すると、スクリプトが適切であれば、クラスターの状態は **[実行中]** から **[承諾済み]**、**[HDInsight 構成]** の順に変わり、最後に **[実行中]** に戻ります。 スクリプトの状態はスクリプト アクションの履歴に記録されます。その情報を使用して、スクリプトが成功したか失敗したかを確認できます。 たとえば、`Get-AzureRmHDInsightScriptActionHistory` PowerShell コマンドレットを使用して、スクリプトの状態を表示できます。 次のテキストのような情報が返されます。

    ScriptExecutionId : 635918532516474303
    StartTime         : 8/14/2017 7:40:55 PM
    EndTime           : 8/14/2017 7:41:05 PM
    Status            : Succeeded

> [!NOTE]
> クラスターが作成された後に、クラスター ユーザー (管理者) のパスワードを変更した場合、このクラスターに対してスクリプト アクションを実行すると失敗する可能性があります。 worker ノードを対象とする保存済みスクリプト アクションがある場合、クラスターのサイズを変更すると、スクリプトは失敗する可能性があります。

## <a name="example-script-action-scripts"></a>スクリプト アクションのサンプル スクリプト

スクリプト アクションのスクリプトは、次のユーティリティで使用できます。

* Azure ポータル
* Azure PowerShell
* Azure CLI
* HDInsight .NET SDK

HDInsight は、HDInsight クラスターで次のコンポーネントをインストールするためのスクリプトを提供します。

| 名前 | スクリプト |
| --- | --- |
| **Azure のストレージ アカウントの追加** |https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh。 「[HDInsight に Azure ストレージ アカウントを追加する](hdinsight-hadoop-add-storage.md)」を参照してください。 |
| **Hue のインストール** |https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh。 [HDInsight クラスターでの Hue のインストールおよび使用](hdinsight-hadoop-hue-linux.md)に関する記事を参照してください。 |
| **Presto のインストール** |https://raw.githubusercontent.com/hdinsight/presto-hdinsight/master/installpresto.sh 「[HDInsight Hadoop クラスターに Presto をインストールして使用する](hdinsight-hadoop-install-presto.md)」を参照してください。 |
| **Solr のインストール** |https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh。 「 [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install-linux.md)」をご覧ください。 |
| **Giraph のインストール** |https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh。 「 [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install-linux.md)」をご覧ください。 |
| **Hive ライブラリの事前読み込み** |https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh。 「[HDInsight クラスター作成時の Hive ライブラリの追加](hdinsight-hadoop-add-hive-libraries.md)」を参照してください。 |
| **Mono のインストールまたは更新** | https://hdiconfigactions.blob.core.windows.net/install-mono/install-mono.bash 「[HDInsight での Mono のインストールまたは更新](hdinsight-hadoop-install-mono.md)」を参照してください。 |

## <a name="use-a-script-action-during-cluster-creation"></a>クラスターの作成時にスクリプト アクションを使用する

このセクションでは、HDInsight クラスターの作成時にスクリプト アクションをさまざまな方法で使用する例を紹介します。

### <a name="use-a-script-action-during-cluster-creation-from-the-azure-portal"></a>クラスターの作成時に Azure Portal からスクリプト アクションを使用する

1. [HDInsight での Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関する記事の説明に従って、クラスターの作成を開始します。 __[クラスターの概要]__ セクションに達したら、停止します。

2. __[クラスターの概要]__ セクションで、__[詳細設定]__ の __[編集]__ リンクを選択します。

    ![[詳細設定] リンク](./media/hdinsight-hadoop-customize-cluster-linux/advanced-settings-link.png)

3. __[詳細設定]__ セクションで、__[スクリプト アクション]__ を選択します。 __[スクリプト アクション]__ セクションで、__[+ 新規で送信]__ を選択します。

    ![新しいスクリプト アクションの送信](./media/hdinsight-hadoop-customize-cluster-linux/add-script-action.png)

4. __[スクリプトの選択]__ エントリで、事前に作成されたスクリプトを選択します。 カスタム スクリプトを使用するには、__[カスタム]__ を選択し、スクリプトの __[名前]__ と __[バッシュ スクリプト URI]__ を指定します。

    ![選択したスクリプト形式でスクリプトを追加](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    スクリプト形式の要素を次の表に示します。

    | プロパティ | 値 |
    | --- | --- |
    | スクリプトの選択 | 独自のスクリプトを使用するには、__[カスタム]__ を選択します。 それ以外の場合は、用意されているスクリプトのいずれかを選択します。 |
    | 名前 |スクリプト アクションの名前を指定します。 |
    | Bash スクリプト URI |クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。 |
    | ヘッド/ワーカー/Zookeeper |カスタマイズ スクリプトが実行されるノード (**[ヘッド]**、**[ワーカー]**、または **[ZooKeeper]**) を指定します。 |
    | パラメーター |スクリプトで必要な場合は、パラメーターを指定します。 |

    スケーリング操作中にスクリプトが確実に適用されるようにするには、__[Persist this script action]\(このスクリプト アクションを保持\)__ エントリを使用します。

5. __[作成]__ を選択してスクリプトを保存します。 別のスクリプトを追加するには、__[+ 新規で送信]__ を使用します。

    ![複数のスクリプト アクション](./media/hdinsight-hadoop-customize-cluster-linux/multiple-scripts.png)

    スクリプトの追加が完了したら、__[選択]__ ボタンをクリックし、__[次へ]__ ボタンをクリックして、__[クラスターの概要]__ セクションに戻ります。

3. クラスターを作成するには、__[クラスターの概要__] セクションで、__[作成]__ を選択します。

### <a name="use-a-script-action-from-azure-resource-manager-templates"></a>Azure リソース マネージャーのテンプレートからスクリプト アクションを使用する

このセクションの例は、Azure Resource Manager テンプレートでスクリプト アクションを使用する方法を示しています。

#### <a name="before-you-begin"></a>開始する前に

* コンピューターを構成して HDInsight Powershell コマンドレットを実行する方法については、「 [Azure PowerShell のインストールおよび構成](/powershell/azure/overview)」をご覧ください。
* テンプレートを作成する方法の手順については、「 [Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」をご覧ください。
* リソース マネージャーで Azure PowerShell を使用したことがない場合は、「 [Azure リソース マネージャーでの Azure PowerShell の使用](../azure-resource-manager/powershell-azure-resource-manager.md)」を参照してください。

#### <a name="create-clusters-using-script-action"></a>スクリプト アクションを使用してクラスターを作成する

1. コンピューター上の場所に次のテンプレートをコピーします。 このテンプレートにより、クラスター内のヘッド ノードと worker ノードに Giraph がインストールされます。 JSON テンプレートが有効かどうかも確認できます。 テンプレートの内容を [JSONLint](http://jsonlint.com/)というオンラインの JSON 検証ツールに貼り付けます。

            {
            "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "clusterLocation": {
                    "type": "string",
                    "defaultValue": "West US",
                    "allowedValues": [ "West US" ]
                },
                "clusterName": {
                    "type": "string"
                },
                "clusterUserName": {
                    "type": "string",
                    "defaultValue": "admin"
                },
                "clusterUserPassword": {
                    "type": "securestring"
                },
                "sshUserName": {
                    "type": "string",
                    "defaultValue": "username"
                },
                "sshPassword": {
                    "type": "securestring"
                },
                "clusterStorageAccountName": {
                    "type": "string"
                },
                "clusterStorageAccountResourceGroup": {
                    "type": "string"
                },
                "clusterStorageType": {
                    "type": "string",
                    "defaultValue": "Standard_LRS",
                    "allowedValues": [
                        "Standard_LRS",
                        "Standard_GRS",
                        "Standard_ZRS"
                    ]
                },
                "clusterStorageAccountContainer": {
                    "type": "string"
                },
                "clusterHeadNodeCount": {
                    "type": "int",
                    "defaultValue": 1
                },
                "clusterWorkerNodeCount": {
                    "type": "int",
                    "defaultValue": 2
                }
            },
            "variables": {
            },
            "resources": [
                {
                    "name": "[parameters('clusterStorageAccountName')]",
                    "type": "Microsoft.Storage/storageAccounts",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-05-01-preview",
                    "dependsOn": [ ],
                    "tags": { },
                    "properties": {
                        "accountType": "[parameters('clusterStorageType')]"
                    }
                },
                {
                    "name": "[parameters('clusterName')]",
                    "type": "Microsoft.HDInsight/clusters",
                    "location": "[parameters('clusterLocation')]",
                    "apiVersion": "2015-03-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Storage/storageAccounts/', parameters('clusterStorageAccountName'))]"
                    ],
                    "tags": { },
                    "properties": {
                        "clusterVersion": "3.2",
                        "osType": "Linux",
                        "clusterDefinition": {
                            "kind": "hadoop",
                            "configurations": {
                                "gateway": {
                                    "restAuthCredential.isEnabled": true,
                                    "restAuthCredential.username": "[parameters('clusterUserName')]",
                                    "restAuthCredential.password": "[parameters('clusterUserPassword')]"
                                }
                            }
                        },
                        "storageProfile": {
                            "storageaccounts": [
                                {
                                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                                    "isDefault": true,
                                    "container": "[parameters('clusterStorageAccountContainer')]",
                                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                                }
                            ]
                        },
                        "computeProfile": {
                            "roles": [
                                {
                                    "name": "headnode",
                                    "targetInstanceCount": "[parameters('clusterHeadNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installGiraph",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                },
                                {
                                    "name": "workernode",
                                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                                    "hardwareProfile": {
                                        "vmSize": "Large"
                                    },
                                    "osProfile": {
                                        "linuxOperatingSystemProfile": {
                                            "username": "[parameters('sshUserName')]",
                                            "password": "[parameters('sshPassword')]"
                                        }
                                    },
                                    "scriptActions": [
                                        {
                                            "name": "installR",
                                            "uri": "https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh",
                                            "parameters": ""
                                        }
                                    ]
                                }
                            ]
                        }
                    }
                }
            ],
            "outputs": {
                "cluster":{
                    "type" : "object",
                    "value" : "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
                }
            }
        }
2. Azure PowerShell を起動し、Azure アカウントにログインします。 資格情報を提供すると、コマンドがアカウントの情報を返します。

        Add-AzureRmAccount

        Id                             Type       ...
        --                             ----
        someone@example.com            User       ...
3. 複数のサブスクリプションがある場合、デプロイに使用するサブスクリプション ID を提供します。

        Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

    > [!NOTE]
    > `Get-AzureRmSubscription` を使用して、アカウントに関連付けられているすべてのサブスクリプションのリストを取得することができます。これには、各サブスクリプション ID が含まれます。

4. 既存のリソース グループがない場合は、リソース グループを作成します。 ソリューションに必要なリソース グループと場所の名前を指定します。 新しいリソース グループの概要が返されます。

        New-AzureRmResourceGroup -Name myresourcegroup -Location "West US"

        ResourceGroupName : myresourcegroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. リソース グループに新しいデプロイを作成するには、**New-AzureRmResourceGroupDeployment** コマンドを実行して必要なパラメーターを指定します。 パラメーターには、次のデータが含まれます。

    * デプロイの名前
    * リソース グループの名前
    * 作成したテンプレートのパスまたは URL

  テンプレートでパラメーターが必要な場合は、それらのパラメーターも渡す必要があります。 この場合は、クラスターに R をインストールするスクリプト アクションでパラメーターは必要ありません。

        New-AzureRmResourceGroupDeployment -Name mydeployment -ResourceGroupName myresourcegroup -TemplateFile <PathOrLinkToTemplate>

    テンプレートで定義されているパラメーターの値を指定するよう要求されます。

1. リソース グループをデプロイすると、デプロイメントの概要が表示されます。

          DeploymentName    : mydeployment
          ResourceGroupName : myresourcegroup
          ProvisioningState : Succeeded
          Timestamp         : 8/14/2017 7:00:27 PM
          Mode              : Incremental
          ...

2. デプロイメントに失敗した場合は、次のコマンドレットを使用してエラーに関する情報を取得できます。

        Get-AzureRmResourceGroupDeployment -ResourceGroupName myresourcegroup -ProvisioningState Failed

### <a name="use-a-script-action-during-cluster-creation-from-azure-powershell"></a>クラスターの作成時に Azure PowerShell からスクリプト アクションを使用する

このセクションでは、 [Add-AzureRmHDInsightScriptAction](https://msdn.microsoft.com/library/mt603527.aspx) コマンドレットで、スクリプト アクションを使用し、クラスターのカスタマイズを行うスクリプトを呼び出します。 次に進む前に、Azure PowerShell をインストールして構成したことを確認します。 ワークステーションを構成して HDInsight PowerShell コマンドレットを実行する方法については、「 [Azure PowerShell のインストールおよび構成](/powershell/azure/overview)」を参照してください。

次のスクリプトは、PowerShell を使用してクラスターを作成するときに、スクリプト アクションを適用する方法を示します。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=5-90)]

クラスターが作成されるまでに数分かかる場合があります。

### <a name="use-a-script-action-during-cluster-creation-from-the-hdinsight-net-sdk"></a>クラスターの作成時に HDInsight .NET SDK からスクリプト アクションを使用する

HDInsight .NET SDK は、.NET アプリケーションから HDInsight を簡単に操作できるクライアント ライブラリを提供します。 コード サンプルについては、「 [.NET SDK を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-script-action)」を参照してください。

## <a name="apply-a-script-action-to-a-running-cluster"></a>実行中のクラスターにスクリプト アクションを適用する

このセクションでは、実行中のクラスターにスクリプト アクションを適用する方法を説明します。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-azure-portal"></a>実行中のクラスターに Azure Portal からスクリプト アクションを適用する

1. [Azure ポータル](https://portal.azure.com)で HDInsight クラスターを選択します。

2. HDInsight クラスターの概要から **[スクリプト アクション]** タイルを選択します。

    ![Script actions tile](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > **[すべての設定]** を選択した後、[設定] セクションから **[スクリプト アクション]** を選択してもかまいません。

3. [スクリプト アクション] セクションの上部の **[+ 新規で送信]** を選択します。

    ![実行中のクラスターにスクリプトを追加する](./media/hdinsight-hadoop-customize-cluster-linux/add-script-running-cluster.png)

4. __[スクリプトの選択]__ エントリで、事前に作成されたスクリプトを選択します。 カスタム スクリプトを使用するには、__[カスタム]__ を選択し、スクリプトの __[名前]__ と __[バッシュ スクリプト URI]__ を指定します。

    ![選択したスクリプト形式でスクリプトを追加](./media/hdinsight-hadoop-customize-cluster-linux/select-script.png)

    スクリプト形式の要素を次の表に示します。

    | プロパティ | 値 |
    | --- | --- |
    | スクリプトの選択 | 独自のスクリプトを使用するには、__[カスタム]__ を選択します。 それ以外の場合、提供されているスクリプトを選択します。 |
    | 名前 |スクリプト アクションの名前を指定します。 |
    | Bash スクリプト URI |クラスターのカスタマイズのために呼び出されるスクリプトへの URI を指定します。 |
    | ヘッド/ワーカー/Zookeeper |カスタマイズ スクリプトが実行されるノード (**[ヘッド]**、**[ワーカー]**、または **[ZooKeeper]**) を指定します。 |
    | パラメーター |スクリプトで必要な場合は、パラメーターを指定します。 |

    スケーリング操作中にスクリプトが確実に適用されるようにするには、__[スクリプト操作を保持する]__ エントリを使用します。

5. 最後に、**[作成]** ボタンを使用して、スクリプトをクラスターに適用します。

### <a name="apply-a-script-action-to-a-running-cluster-from-azure-powershell"></a>実行中のクラスターに Azure PowerShell からスクリプト アクションを適用する

次に進む前に、Azure PowerShell をインストールして構成したことを確認します。 ワークステーションを構成して HDInsight PowerShell コマンドレットを実行する方法については、「 [Azure PowerShell のインストールおよび構成](/powershell/azure/overview)」を参照してください。

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

次に進む前に、Azure CLI をインストールして構成したことを確認します。 詳細については、「 [Azure CLI のインストール](../cli-install-nodejs.md)」を参照してください。

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

1. Azure Resource Manager モードに切り替えるには、コマンド ラインで次のコマンドを実行します。

        azure config mode arm

2. 次のコマンドを使用して、Azure サブスクリプションに対して認証します。

        azure login

3. 次のコマンドを使用して、実行中のクラスターにスクリプトを適用します。

        azure hdinsight script-action create <clustername> -g <resourcegroupname> -n <scriptname> -u <scriptURI> -t <nodetypes>

    このコマンドのパラメーターを省略した場合は、それを要求するメッセージが表示されます。 `-u` で指定したスクリプトがパラメーターを受け取る場合は、`-p` パラメーターを使用してそれらを指定できます。

    有効なノード タイプは、`headnode`、`workernode`、および `zookeeper` です。 スクリプトを複数のノード タイプに適用する必要がある場合、タイプを ; で区切って指定します。 たとえば、「 `-n headnode;workernode`」のように入力します。

    スクリプトを保存するには、 `--persistOnSuccess`を追加します。 また、`azure hdinsight script-action persisted set` を使用して、スクリプトを後日保存することもできます。

    ジョブが完了すると、次のような出力が返されます。

        info:    Executing command hdinsight script-action create
        + Executing Script Action on HDInsight cluster
        data:    Operation Info
        data:    ---------------
        data:    Operation status:
        data:    Operation ID:  b707b10e-e633-45c0-baa9-8aed3d348c13
        info:    hdinsight script-action create command OK

### <a name="apply-a-script-action-to-a-running-cluster-using-rest-api"></a>REST API を使用して実行中のクラスターにスクリプト アクションを適用する

「 [Run Script Actions on a running cluster (実行中のクラスターでスクリプト アクションを実行する)](https://msdn.microsoft.com/library/azure/mt668441.aspx)」を参照してください。

### <a name="apply-a-script-action-to-a-running-cluster-from-the-hdinsight-net-sdk"></a>実行中のクラスターに HDInsight .NET SDK からスクリプト アクションを適用する

.NET SDK を使用してスクリプトをクラスターに適用する例については、 [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)を参照してください。

## <a name="view-history-promote-and-demote-script-actions"></a>履歴を表示して、スクリプト アクションを昇格、降格する

### <a name="using-the-azure-portal"></a>Azure ポータルの使用

1. [Azure ポータル](https://portal.azure.com)で HDInsight クラスターを選択します。

2. HDInsight クラスターの概要から **[スクリプト アクション]** タイルを選択します。

    ![Script actions tile](./media/hdinsight-hadoop-customize-cluster-linux/scriptactionstile.png)

   > [!NOTE]
   > **[すべての設定]** を選択した後、[設定] セクションから **[スクリプト アクション]** を選択してもかまいません。

4. [スクリプト アクション] セクションに、このクラスター用のスクリプトの履歴が表示されます。 この情報には、保存されたスクリプトの一覧が含まれています。 次のスクリーンショットでは、Solr スクリプトがこのクラスターで実行されていますが、 保存されたスクリプト アクションはありません。

    ![[スクリプト アクション] セクション](./media/hdinsight-hadoop-customize-cluster-linux/script-action-history.png)

5. 履歴からスクリプトを選択すると、そのスクリプトの [プロパティ] セクションが表示されます。 画面の上部から、スクリプトを再実行または昇格できます。

    ![スクリプト アクションの [プロパティ] ブレード](./media/hdinsight-hadoop-customize-cluster-linux/promote-script-actions.png)

6. [スクリプト アクション] セクションのエントリの右側にある **[...]** を使用してアクションを実行することもできます。

    ![Script actions ... usage](./media/hdinsight-hadoop-customize-cluster-linux/deletepromoted.png)

### <a name="using-azure-powershell"></a>Azure PowerShell の使用

| 使用 | 目的 |
| --- | --- |
| Get-AzureRmHDInsightPersistedScriptAction |保存済みスクリプト アクションの情報を取得します |
| Get-AzureRmHDInsightScriptActionHistory |クラスターに適用されたスクリプト アクションの履歴、または特定のスクリプトの詳細を取得します |
| Set-AzureRmHDInsightPersistedScriptAction |アドホック スクリプト アクションを保存済みスクリプト アクションに昇格します |
| Remove-AzureRmHDInsightPersistedScriptAction |保存済みスクリプト アクションをアドホック アクションに降格します |

> [!IMPORTANT]
> `Remove-AzureRmHDInsightPersistedScriptAction` を使用しても、スクリプトによって実行されたアクションは元に戻りません。 このコマンドレットは、保存済みフラグが削除されるだけです。

次のスクリプトの例では、昇格のコマンドレットを使用してから、スクリプトを降格しています。

[!code-powershell[main](../../powershell_scripts/hdinsight/use-script-action/use-script-action.ps1?range=123-140)]

### <a name="using-the-azure-cli"></a>Azure CLI の使用

| 使用 | 目的 |
| --- | --- |
| `azure hdinsight script-action persisted list <clustername>` |保存済みスクリプト アクションの一覧を取得します。 |
| `azure hdinsight script-action persisted show <clustername> <scriptname>` |特定の保存済みスクリプト アクションに関する情報を取得します。 |
| `azure hdinsight script-action history list <clustername>` |クラスターに適用されたスクリプト アクションの履歴を取得します。 |
| `azure hdinsight script-action history show <clustername> <scriptname>` |特定のスクリプト アクションに関する情報の取得 |
| `azure hdinsight script action persisted set <clustername> <scriptexecutionid>` |アドホック スクリプト アクションを保存済みスクリプト アクションに昇格します |
| `azure hdinsight script-action persisted delete <clustername> <scriptname>` |保存済みスクリプト アクションをアドホック アクションに降格します |

> [!IMPORTANT]
> `azure hdinsight script-action persisted delete` を使用しても、スクリプトによって実行されたアクションは元に戻りません。 このコマンドレットは、保存済みフラグが削除されるだけです。

### <a name="using-the-hdinsight-net-sdk"></a>HDInsight .NET SDK を使用する場合

.NET SDK を使用してクラスターからスクリプトの履歴を取得し、スクリプトを昇格または降格する例については、 [https://github.com/Azure-Samples/hdinsight-dotnet-script-action](https://github.com/Azure-Samples/hdinsight-dotnet-script-action)を参照してください。

> [!NOTE]
> この例では、.NET SDK を使用して HDInsight アプリケーションをインストールする方法も示します。

## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>HDInsight クラスターで使用するオープン ソース ソフトウェアのサポート

Microsoft Azure HDInsight サービスは Hadoop を中心に形成されたオープン ソース テクノロジのエコシステムを利用します。 Microsoft Azure は、オープン ソース テクノロジの一般的なレベルのサポートを提供します。 詳細については、「[Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」の**サポート範囲**に関するセクションを参照してください。 HDInsight サービスでは、組み込みのコンポーネントに対してさらに高いレベルのサポートを提供しています。

HDInsight サービスで利用できるオープン ソース コンポーネントには、2 つの種類があります。

* **組み込みコンポーネント** - これらのコンポーネントは、HDInsight クラスターにプレインストールされており、クラスターの主要な機能を提供します。 たとえば、YARN リソース マネージャー、Hive クエリ言語 (HiveQL)、Mahout ライブラリなどがこのカテゴリに属します。 クラスター コンポーネントの完全な一覧は、「[HDInsight で提供される Hadoop クラスター バージョンの新機能](hdinsight-component-versioning.md)」から入手できます。
* **カスタム コンポーネント** - クラスターのユーザーは、コミュニティで入手できるコンポーネントや自作のコンポーネントを、インストールするか、ワークロード内で使用することができます。

> [!WARNING]
> HDInsight クラスターに用意されているコンポーネントは全面的にサポートされており、 これらのコンポーネントに関連する問題の分離と解決については、Microsoft サポートが支援します。
>
> カスタム コンポーネントについては、問題のトラブルシューティングを進めるための支援として、商業的に妥当な範囲のサポートを受けることができます。 Microsoft サポートによって問題が解決する場合もあれば、オープン ソース テクノロジに関する深い専門知識を入手できる場所への参加をお願いする場合もあります。 たとえば、[HDInsight についての MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)や [http://stackoverflow.com](http://stackoverflow.com) などの数多くのコミュニティ サイトを利用できます。 また、Apache プロジェクトには、[http://apache.org](http://apache.org) に [Hadoop](http://hadoop.apache.org/) などのプロジェクト サイトもあります。

HDInsight サービスでは、カスタム コンポーネントを使用する方法をいくつか用意しています。 コンポーネントの用途やクラスターへのインストール方法にかかわらず、同じレベルのサポートが適用されます。 以下は、HDInsight クラスターでのカスタム コンポーネントの用途として、最も一般的な方法の一覧です。

1. ジョブの送信 - Hadoop や他の種類のジョブを、カスタム コンポーネントを実行または使用するクラスターに送信できます。

2. クラスターのカスタマイズ - クラスター作成時に、追加設定や、クラスター ノードにインストールするカスタム コンポーネントを指定できます。

3. サンプル - よく利用されるカスタム コンポーネントに対しては、それらを HDInsight クラスターで使用する方法について Microsoft やその他の提供者がサンプルを用意している場合があります。 これらのサンプルはサポートなしで提供されます。

## <a name="troubleshooting"></a>トラブルシューティング

Ambari の Web UI を使用すると、スクリプト アクションによってログに記録された情報を表示できます。 クラスターの作成中にスクリプトでエラーが発生した場合は、クラスターに関連付けられた既定のストレージ アカウントのログを利用することもできます。 このセクションでは、これら両方のオプションを使用してログを取得する方法について説明します。

### <a name="using-the-ambari-web-ui"></a>Ambari Web UI を使用する

1. ブラウザーで、https://CLUSTERNAME.azurehdinsight.net に移動します。 CLUSTERNAME を、使用する HDInsight クラスターの名前に置き換えます。

    プロンプトが表示されたら、クラスターの管理者アカウント名 (admin) とパスワードを入力します。 Web フォームで管理者の資格情報の再入力が必要な場合があります。

2. ページ上部のバーから **[OPS]** エントリを選択します。 これにより、Ambari を使用してクラスターで実行される、現在と過去の操作の一覧が表示されます。

    ![Ambari Web UI バーで OPS を選択](./media/hdinsight-hadoop-customize-cluster-linux/ambari-nav.png)

3. **[Operations]** 列で **run\_customscriptaction** エントリを探します。 これらのエントリは、スクリプト アクションの実行時に作成されます。

    ![操作のスクリーンショット](./media/hdinsight-hadoop-customize-cluster-linux/ambariscriptaction.png)

    STDOUT と STDERR の出力を表示するには、run\customscriptaction エントリを選択してリンクをたどります。 この出力結果はスクリプトの実行時に生成され、有益な情報が含まれていることがあります。

### <a name="access-logs-from-the-default-storage-account"></a>既定のストレージ アカウントからログにアクセスする

スクリプト アクションで発生したエラーが原因でクラスターの作成に失敗した場合、クラスター ストレージ アカウントからスクリプト アクション ログに直接アクセスすることもできます。

* ストレージ ログは、 `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\CLUSTER_NAME\DATE`にあります。

    ![操作のスクリーンショット](./media/hdinsight-hadoop-customize-cluster-linux/script_action_logs_in_storage.png)

    このディレクトリの下で、ヘッド ノード、worker ノード、および zookeeper ノードごとにログが整理されています。 次に例をいくつか示します。

    * **ヘッド ノード** - `<uniqueidentifier>AmbariDb-hn0-<generated_value>.cloudapp.net`

    * **ワーカー ノード** - `<uniqueidentifier>AmbariDb-wn0-<generated_value>.cloudapp.net`

    * **Zookeeper ノード** - `<uniqueidentifier>AmbariDb-zk0-<generated_value>.cloudapp.net`

* 対応するホストのすべての stdout と stderr が、ストレージ アカウントにアップロードされます。 各スクリプト アクションに対して、**output-\*.txt** と **errors-\*.txt** が 1 つずつあります。 output-*.txt ファイルには、ホストで実行されたスクリプトの URI に関する情報が含まれます。 たとえば、次のように入力します。

        'Start downloading script locally: ', u'https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh'

* 同じ名前のスクリプト アクション クラスターを繰り返し作成できます。 そのような場合は、DATE フォルダー名に基づいて適切なログを識別できます。 たとえば、異なる日付で作成されるクラスターのフォルダー構造 (mycluster) は、ログ エントリには次のように表示されます。

    `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-04` `\STORAGE_ACCOUNT_NAME\DEFAULT_CONTAINER_NAME\custom-scriptaction-logs\mycluster\2015-10-05`

* 同じ日に同じ名前のスクリプト アクション クラスターを作成する場合は、一意のプレフィックスを使用して該当するログ ファイルを識別できます。

* 12:00 AM (深夜 0 時) 近くにクラスターを作成すると、ログ ファイルが 2 日間にまたがる可能性があります。 そのような場合は、同じクラスターに日付が異なる 2 つのフォルダーが作成されます。

* 既定のコンテナーへのログ ファイルのアップロードは、特に大きなクラスターの場合、最大 5 分かかることがあります。 そのため、ログにアクセスする必要がある場合は、スクリプト アクションが失敗したときにクラスターをすぐに削除しないでください。

### <a name="ambari-watchdog"></a>Ambari ウォッチドッグ

> [!WARNING]
> Linux ベースの HDInsight クラスターでは、Ambari ウォッチドッグ (hdinsightwatchdog) のパスワードは変更しないでください。 このアカウントのパスワードを変更すると、HDInsight クラスターで新しいスクリプト アクションを実行できなくなります。

### <a name="cant-import-name-blobservice"></a>名前 BlobService をインポートできない

__現象__: スクリプトの操作が失敗します。 Ambari で操作を表示すると、次のエラーに似たテキストが表示されます。

```
Traceback (most recent call list):
  File "/var/lib/ambari-agent/cache/custom_actions/scripts/run_customscriptaction.py", line 21, in <module>
    from azure.storage.blob import BlobService
ImportError: cannot import name BlobService
```

__原因__: このエラーは、HDInsight クラスターに含まれている Python Azure Storage クライアントをアップグレードする場合に発生します。 HDInsight は、Azure Storage クライアント 0.20.0 を予期しています。

__解決策__: このエラーを解決するには、`ssh` を使用して各クラスター ノードを手動で接続し、次のコマンドを使用して、正しいストレージ クライアントのバージョンを再インストールします。

```
sudo pip install azure-storage==0.20.0
```

SSH を使用してクラスターに接続する方法については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

### <a name="history-doesnt-show-scripts-used-during-cluster-creation"></a>クラスターの作成時に使用されたスクリプトが履歴に表示されません

クラスターが 2016 年 3 月 15 日より前に作成された場合、スクリプト アクション履歴にエントリが表示されない可能性があります。 ただし、2016 年 3 月 15 日より後にそのクラスターのサイズを変更すると、スクリプトはサイズ変更操作の一部としてクラスター内の新しいノードに適用されるため、クラスター作成時のスクリプトの使用は履歴に表示されます。

ただし、例外が 2 つあります。

* クラスターが 2015 年 9 月 1 日より前に作成された場合。 スクリプト アクションが導入される前であるため、 この日付より前に作成されたクラスターについては、作成時にスクリプト アクションを使用できませんでした。

* クラスターの作成時に複数のスクリプト アクションを使用した場合、複数のスクリプトに対して同じ名前を使用した場合、または複数のスクリプトに対して同じ名前と URI、異なるパラメーターを使用した場合。 この場合は、次のエラーが発生します。

    既存のスクリプトでスクリプト名が競合するため、このクラスターでは新しいスクリプト アクションを実行できません。 クラスターの作成時に指定されるスクリプト名はすべて一意である必要があります。 既存のスクリプトは、サイズ変更時に実行されます。

## <a name="next-steps"></a>次のステップ

* [HDInsight 用の Script Action スクリプトの開発](hdinsight-hadoop-script-actions-linux.md)
* [HDInsight クラスターに Solr をインストールして使用する](hdinsight-hadoop-solr-install-linux.md)
* [HDInsight クラスターに Giraph をインストールして使用する](hdinsight-hadoop-giraph-install-linux.md)
* [HDInsight に Azure ストレージ アカウントを追加する](hdinsight-hadoop-add-storage.md)

[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster-linux/HDI-Cluster-state.png "クラスター作成時の段階"

