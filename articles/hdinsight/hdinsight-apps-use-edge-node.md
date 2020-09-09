---
title: Azure HDInsight の Apache Hadoop クラスターで空のエッジ ノードを使用する
description: HDInsight クラスターに空のエッジ ノードを追加する方法。 クライアントとして使用され、HDInsight アプリケーションをテストまたはホストします。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: b5ea227ba75a9ecf6666883603dad97d02385b5c
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086485"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>HDInsight の Apache Hadoop クラスターで空のエッジ ノードを使用する

HDInsight クラスターに空のエッジ ノードを追加する方法について説明します。 空のエッジ ノードは、ヘッド ノードの場合と同じクライアント ツールがインストールされ、構成された Linux 仮想マシンです。 ただし、[Apache Hadoop](./hadoop/apache-hadoop-introduction.md) サービスは実行されていません。 エッジ ノードは、クラスターへのアクセス、クライアント アプリケーションのテスト、およびクライアント アプリケーションのホストに使用できます。

空のエッジ ノードは、既存の HDInsight クラスターに追加することも、クラスターの作成時にその新しいクラスターに追加することもできます。 空のエッジ ノードを追加するには、Azure Resource Manager テンプレートを使用します。  次のサンプルでは、テンプレートを使用して行う方法を示しています。

```json
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
                        "vmSize": "{}"
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
```

サンプルに示されているように、必要に応じて[スクリプト アクション](hdinsight-hadoop-customize-cluster-linux.md)を呼び出して、追加の構成を行うことができます。 たとえば、エッジ ノードでの [Apache Hue](hdinsight-hadoop-hue-linux.md) のインストールなどです。 このスクリプト アクションのスクリプトは、Web で公開されている必要があります。  たとえば、スクリプトが Azure Storage に格納されている場合、パブリック コンテナーまたはパブリック BLOB のいずれかを使用します。

エッジノードの仮想マシンのサイズは、HDInsight クラスター ワーカー ノードの VM サイズの要件を満たしている必要があります。 推奨されるワーカー ノードの VM サイズについては、「[HDInsight で Apache Hadoop クラスターを作成する](hdinsight-hadoop-provision-linux-clusters.md#cluster-type)」をご覧ください。

エッジ ノードを作成した後、SSH を使用してエッジ ノードに接続し、クライアント ツールを実行して HDInsight の Hadoop クラスターにアクセスすることができます。

> [!WARNING]
> エッジ ノードにインストールされているカスタム コンポーネントは、Microsoft からビジネス上合理的なサポートを受けることができます。 これにより、発生する問題を解決できる場合があります。 または、追加の支援を受けるために、コミュニティ リソースを参照することもできます。 コミュニティから支援を受けることができる、最もアクティブなサイトの一部を次に示します。
>
> * [HDInsight に関する Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-hdinsight.html)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Apache テクノロジを使用している場合、[https://apache.org](https://apache.org) にある Apache の各プロジェクト サイト (例: [Apache Hadoop](https://hadoop.apache.org/) サイト) で支援を受けられる可能性があります。

> [!IMPORTANT]
> Ubuntu イメージは、公開から 3 か月以内に、新しい HDInsight クラスターの作成のために入手できるようになります。 2019 年 1 月時点で、実行中のクラスター (エッジ ノードを含む) に修正プログラムは自動適用**されません**。 お客様は、スクリプトによるアクションまたはその他のメカニズムを使用して、実行中のクラスターに修正プログラムを適用する必要があります。  詳細については、「[HDInsight 用の OS の修正プログラム](./hdinsight-os-patching.md)」を参照してください。

## <a name="add-an-edge-node-to-an-existing-cluster"></a>既存のクラスターにエッジ ノードを追加する

このセクションでは、Resource Manager テンプレートを使用して既存の HDInsight クラスターにエッジ ノードを追加します。  Resource Manager テンプレートは、 [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/)にあります。 Resource Manager テンプレートは、https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh にあるスクリプト アクションを呼び出します。このスクリプトではアクションは実行されません。  これは、Resource Manager テンプレートからのスクリプト アクションの呼び出しを示すためのものです。

1. 次の画像を選択して Azure にサインインし、Azure portal で Azure Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. 次のプロパティを構成します。

    |プロパティ |説明 |
    |---|---|
    |サブスクリプション|クラスターの作成に使用される Azure サブスクリプションを選択します。|
    |Resource group|既存の HDInsight クラスターに使用されるリソース グループを選択します。|
    |場所|既存の HDInsight クラスターの場所を選択します。|
    |クラスター名|既存の HDInsight クラスターの名前を入力します。|

1. エッジ ノードを作成するには、 **[上記の使用条件に同意する]** をオンにし、 **[購入]** を選択します。

> [!IMPORTANT]  
> 必ず、既存の HDInsight クラスター用に使用される Azure リソース グループを選択します。  正しく選択しなかった場合、"ネストされたリソースに対して要求された操作を実行できません。 親リソース '&lt;クラスター名>' が見つかりません。" というエラー メッセージが表示されます。

## <a name="add-an-edge-node-when-creating-a-cluster"></a>クラスター作成時にエッジ ノードを追加する

このセクションでは、Resource Manager テンプレートを使用して、エッジ ノードを含む HDInsight クラスターを作成します。  Resource Manager テンプレートは [Azure クイック スタート テンプレート ギャラリー](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/)にあります。 Resource Manager テンプレートは、https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh にあるスクリプト アクションを呼び出します。このスクリプトではアクションは実行されません。  これは、Resource Manager テンプレートからのスクリプト アクションの呼び出しを示すためのものです。

1. HDInsight クラスターがない場合は作成します。  [HDInsight での Hadoop の使用](hadoop/apache-hadoop-linux-tutorial-get-started.md)に関するページをご覧ください。

1. 次の画像を選択して Azure にサインインし、Azure portal で Azure Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. 次のプロパティを構成します。

    |プロパティ |説明 |
    |---|---|
    |サブスクリプション|クラスターの作成に使用される Azure サブスクリプションを選択します。|
    |Resource group|クラスターの新しいリソース グループを作成します。|
    |場所|リソース グループの場所を選びます。|
    |クラスター名|新しく作成するクラスターの名前を入力します。|
    |[Cluster Login User Name]\(クラスター ログイン ユーザー名\)|Hadoop HTTP ユーザー名を入力します。  既定の名前は **admin**です。|
    |[クラスター ログイン パスワード]|HTTP ユーザー パスワードを入力します。|
    |SSH ユーザー名|SSH ユーザー名を入力します。 既定の名前は **sshuser**です。|
    |SSH パスワード|SSH ユーザー パスワードを入力します。|
    |スクリプト アクションのインストール|この記事では既定値のままにします。|

    一部のプロパティは、テンプレートにハードコーディングされています:クラスターの種類、クラスターのワーカー ノード数、エッジ ノードのサイズ、およびエッジ ノードの名前。

1. エッジ ノードを使用してクラスターを作成するには、 **[上記の使用条件に同意する]** をオンにし、 **[購入]** を選択します。

## <a name="add-multiple-edge-nodes"></a>複数のエッジ ノードの追加

HDInsight クラスターには複数のエッジ ノードを追加できます。  複数エッジ ノード構成は、Azure Resource Manager テンプレートを使用することによってのみ行うことができます。  この記事の冒頭にあるテンプレート サンプルを参照してください。  作成するエッジ ノードの数を反映させるには、**targetInstanceCount** を更新します。

## <a name="access-an-edge-node"></a>エッジ ノードにアクセスする

エッジ ノードの SSH エンドポイントは、&lt;エッジ ノード名>.&lt;クラスター名>-ssh.azurehdinsight.net:22 です。  たとえば、new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22 のようになります。

エッジ ノードは、Azure Portal ではアプリケーションとして表示されます。  Portal には、SSH を使用してエッジ ノードにアクセスするための情報が表示されます。

**エッジ ノードの SSH エンドポイントを確認するには**

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. エッジ ノードを含む HDInsight クラスターを開きます。
3. **[アプリケーション]** を選択します。 エッジ ノードが表示されます。  既定の名前は **new-edgenode**です。
4. エッジ ノードを選択します。 SSH エンドポイントが表示されます。

**エッジ ノードで Hive を使用するには**

1. SSH を使用してエッジ ノードに接続します。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

2. SSH を使用してエッジ ノードに接続したら、次のコマンドを使用して Hive コンソールを開きます。

    ```console
    hive
    ```

3. 次のコマンドを使用して、クラスター内の Hive テーブルを表示します。

    ```hiveql
    show tables;
    ```

## <a name="delete-an-edge-node"></a>エッジ ノードを削除する

Azure Portal からエッジ ノードを削除できます。

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. エッジ ノードを含む HDInsight クラスターを開きます。
3. **[アプリケーション]** を選択します。 エッジ ノードの一覧が表示されます。  
4. 削除するエッジ ノードを右クリックし、 **[削除]** を選択します。
5. **[はい]** を選択して確定します。

## <a name="next-steps"></a>次のステップ

この記事では、エッジ ノードを追加する方法とエッジ ノードにアクセスする方法について学習しました。 詳細については、以下の記事をお読みください。

* [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md):HDInsight アプリケーションをクラスターにインストールする方法について確認します。
* [カスタム HDInsight アプリケーションをインストールする](hdinsight-apps-install-custom-applications.md): 未発行の HDInsight アプリケーションを HDInsight にデプロイする方法について確認します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md):カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
* [MSDN:HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx):HDInsight アプリケーションを定義する方法を確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [Resource Manager テンプレートを使用して HDInsight で Linux ベースの Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Resource Manager テンプレートを呼び出して HDInsight クラスターを作成する方法を確認します。
