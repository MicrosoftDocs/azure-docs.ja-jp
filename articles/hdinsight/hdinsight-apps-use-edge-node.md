---
title: "HDInsight の Hadoop クラスターで空のエッジ ノードを使用する - Azure | Microsoft Docs"
description: "クライアントとして使用できる空のエッジ ノードを HDInsight クラスターに追加し、その後 HDInsight アプリケーションをテストおよびホストする方法について説明します。"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: cdc7d1b4-15d7-4d4d-a13f-c7d3a694b4fb
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: jgao
ms.openlocfilehash: e21dabcc6999b1f1047d334e782f723d0c03c2cb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-empty-edge-nodes-on-hadoop-clusters-in-hdinsight"></a>HDInsight の Hadoop クラスターで空のエッジ ノードを使用する

HDInsight クラスターに空のエッジ ノードを追加する方法について説明します。 空のエッジ ノードは、ヘッド ノードの場合と同じクライアント ツールがインストールされ、構成された Linux 仮想マシンですが、Hadoop サービスは実行されていません。 エッジ ノードは、クラスターへのアクセス、クライアント アプリケーションのテスト、およびクライアント アプリケーションのホストに使用できます。 

空のエッジ ノードは、既存の HDInsight クラスターに追加することも、クラスターの作成時にその新しいクラスターに追加することもできます。 空のエッジ ノードを追加するには、Azure Resource Manager テンプレートを使用します。  次のサンプルでは、テンプレートを使用して空のエッジ ノードを追加する方法を示しています。

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

サンプルに示すように、必要に応じて、[スクリプト アクション](hdinsight-hadoop-customize-cluster-linux.md)を呼び出して、エッジ ノードへの [Apache Hue](hdinsight-hadoop-hue-linux.md) のインストールなど、追加の構成を行うことができます。 このスクリプト アクションのスクリプトは、Web で公開されている必要があります。  たとえばスクリプトが Azure Storage に格納されている場合、パブリック コンテナーまたはパブリック BLOB を使用します。

エッジノードの仮想マシンのサイズは、HDInsight クラスター ワーカー ノードの VM サイズの要件を満たしている必要があります。 推奨されるワーカー ノードの VM サイズについては、「[HDInsight で Hadoop クラスターを作成する](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)」をご覧ください。

エッジ ノードを作成した後、SSH を使用してエッジ ノードに接続し、クライアント ツールを実行して HDInsight の Hadoop クラスターにアクセスすることができます。

> [!WARNING] 
> HDInsight での空のエッジ ノードの使用は、現在プレビュー段階です。 エッジ ノードにインストールされているカスタム コンポーネントは、Microsoft からビジネス上合理的なサポートを受けることができます。 これにより、発生する問題を解決できる場合があります。 または、コミュニティ リソースを利用することで、さらなる支援を受けられる場合もあります。 コミュニティから支援を受けることができる、最もアクティブなサイトの一部を次に示します。
>
> * [HDInsight の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [http://stackoverflow.com](http://stackoverflow.com)
>
> Apache テクノロジを使用している場合、[http://apache.org](http://apache.org) にある Apache の各プロジェクト サイト (例: [Hadoop](http://hadoop.apache.org/) サイト) で支援を受けられる可能性があります。

## <a name="add-an-edge-node-to-an-existing-cluster"></a>既存のクラスターにエッジ ノードを追加する
このセクションでは、Resource Manager テンプレートを使用して既存の HDInsight クラスターにエッジ ノードを追加します。  Resource Manager テンプレートは、 [GitHub](https://azure.microsoft.com/en-us/resources/templates/101-hdinsight-linux-add-edge-node/)にあります。 Resource Manager テンプレートは、https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh にあるスクリプト アクションを呼び出します。このスクリプトでは操作は実行されません。  これは、Resource Manager テンプレートからのスクリプト アクションの呼び出しを説明するためのものです。

**既存のクラスターに空のエッジ ノードを追加するには**

1. HDInsight クラスターがない場合は作成します。  「[Hadoop チュートリアル: HDInsight で Hadoop を使用する](hdinsight-hadoop-linux-tutorial-get-started.md)」をご覧ください。
2. 次の画像をクリックして Azure にサインインし、Azure Portal で Azure Resource Manager テンプレートを開きます。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. 次のプロパティを構成します。
   
   * **サブスクリプション**: クラスターの作成に使用する Azure サブスクリプションを選択します。
   * **リソース グループ**: 既存の HDInsight クラスター用に使用されるリソース グループを選択します。
   * **場所**: 既存の HDInsight クラスターの場所を選択します。
   * **クラスター名**: 既存の HDInsight クラスターの名前を入力します。
   * **エッジ ノードのサイズ**: いずれかの VM サイズを選択します。 VM サイズは、ワーカー ノードの VM サイズの要件を満たしている必要があります。 推奨されるワーカー ノードの VM サイズについては、「[HDInsight で Hadoop クラスターを作成する](hdinsight-hadoop-provision-linux-clusters.md#cluster-types)」をご覧ください。
   * **エッジ ノードのプレフィックス**: 既定値は **new** です。  既定値を使用する場合、エッジ ノードの名前は **new-edgenode**です。  プレフィックスは、Portal でカスタマイズできます。 テンプレートからフル ネームをカスタマイズすることもできます。

4. エッジ ノードを作成するには、**[上記の使用条件に同意する]** をオンにし、**[購入]** をクリックします。

>[!IMPORTANT]
> 必ず、既存の HDInsight クラスター用に使用される Azure リソース グループを選択します。  正しく選択しなかった場合、"ネストされたリソースに対して要求された操作を実行できません。 親リソース '&lt;クラスター名>' が見つかりません。" というエラー メッセージが表示されます。

## <a name="add-an-edge-node-when-creating-a-cluster"></a>クラスター作成時にエッジ ノードを追加する
このセクションでは、Resource Manager テンプレートを使用して、エッジ ノードを含む HDInsight クラスターを作成します。  Resource Manager テンプレートは [Azure クイック スタート テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)にあります。 Resource Manager テンプレートは、https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh にあるスクリプト アクションを呼び出します。このスクリプトでは操作は実行されません。  これは、Resource Manager テンプレートからのスクリプト アクションの呼び出しを説明するためのものです。

**既存のクラスターに空のエッジ ノードを追加するには**

1. HDInsight クラスターがない場合は作成します。  [HDInsight での Hadoop の使用](hdinsight-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。
2. 次の画像をクリックして Azure にサインインし、Azure Portal で Azure Resource Manager テンプレートを開きます。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. 次のプロパティを構成します。
   
   * **サブスクリプション**: クラスターの作成に使用する Azure サブスクリプションを選択します。
   * **リソース グループ**: クラスターの新しいリソース グループを作成します。
   * **場所**: リソース グループの場所を選択します。
   * **クラスター名**: 新しく作成するクラスターの名前を入力します。
   * **クラスター ログイン ユーザー名**: Hadoop HTTP ユーザー名を入力します。  既定の名前は **admin**です。
   * **クラスター ログイン パスワード**: HTTP ユーザー パスワードを入力します。
   * **SSH ユーザー名**: SSH ユーザー名を入力します。 既定の名前は **sshuser**です。
   * **SSH パスワード**: SSH ユーザー パスワードを入力します。
   * **スクリプト アクションのインストール**: このチュートリアルでは既定値のままにします。
     
     一部のプロパティはテンプレートでハードコードされています: クラスターの種類、クラスターのワーカー ノード数、エッジ ノードのサイズ、およびエッジ ノードの名前。
4. エッジ ノードを使用してクラスターを作成するには、**[上記の使用条件に同意する]** をオンにし、**[購入]** をクリックします。

## <a name="access-an-edge-node"></a>エッジ ノードにアクセスする
エッジ ノードの SSH エンドポイントは、&lt;エッジ ノード名>.&lt;クラスター名>-ssh.azurehdinsight.net:22 です。  たとえば、new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22 のようになります。

エッジ ノードは、Azure Portal ではアプリケーションとして表示されます。  Portal には、SSH を使用してエッジ ノードにアクセスするための情報が表示されます。

**エッジ ノードの SSH エンドポイントを確認するには**

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. エッジ ノードを含む HDInsight クラスターを開きます。
3. クラスター ブレードで、 **[アプリケーション]** をクリックします。 エッジ ノードが表示されます。  既定の名前は **new-edgenode**です。
4. エッジ ノードをクリックします。 SSH エンドポイントが表示されます。

**エッジ ノードで Hive を使用するには**

1. SSH を使用してエッジ ノードに接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. SSH を使用してエッジ ノードに接続したら、次のコマンドを使用して Hive コンソールを開きます。
   
        hive
3. 次のコマンドを使用して、クラスター内の Hive テーブルを表示します。
   
        show tables;

## <a name="delete-an-edge-node"></a>エッジ ノードを削除する
Azure Portal からエッジ ノードを削除できます。

**エッジ ノードにアクセスするには**

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. エッジ ノードを含む HDInsight クラスターを開きます。
3. クラスター ブレードで、 **[アプリケーション]** をクリックします。 エッジ ノードの一覧が表示されます。  
4. 削除するエッジ ノードを右クリックし、 **[削除]**をクリックします。
5. **[はい]** をクリックして確定します。

## <a name="next-steps"></a>次のステップ
この記事では、エッジ ノードを追加する方法とエッジ ノードにアクセスする方法について説明しました。 詳細については、次の記事を参照してください。

* [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md): HDInsight アプリケーションをクラスターにインストールする方法について確認します。
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
* [MSDN: HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションを定義する方法について確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [Resource Manager テンプレートを使用して HDInsight で Linux ベースの Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Resource Manager テンプレートを呼び出して HDInsight クラスターを作成する方法を確認します。

