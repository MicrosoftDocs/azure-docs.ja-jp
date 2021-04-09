---
title: Azure HDInsight にカスタム Apache Hadoop アプリケーションをインストールする
description: Azure HDInsight で Apache Hadoop クラスター用の HDInsight アプリケーションをインストールする方法を説明します。
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 19dd5bf94b524ff3eb6eb601c77b503a0040bd75
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104867646"
---
# <a name="install-custom-apache-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight にカスタム Apache Hadoop アプリケーションをインストールする

この記事では、まだ Azure portal に発行されていない [Apache Hadoop](https://hadoop.apache.org/) アプリケーションを Azure HDInsight にインストールする方法について説明します。 この記事でインストールするアプリケーションは [Hue](https://gethue.com/) です。

HDInsight アプリケーションは、ユーザーが HDInsight クラスターにインストールできるアプリケーションです。  マイクロソフトや独立系ソフトウェア ベンダー (ISV) によって作成されるほか、ユーザーが独自に作成することもできます。  

## <a name="prerequisites"></a>前提条件

既存の HDInsight クラスターに HDInsight アプリケーションをインストールする場合は、対象となる HDInsight クラスターが必要です。 新たに作成する場合は、「 [クラスターの作成](hadoop/apache-hadoop-linux-tutorial-get-started.md)」を参照してください。 HDInsight クラスターを作成するときに HDInsight アプリケーションをインストールすることもできます。

## <a name="install-hdinsight-applications"></a>HDInsight アプリケーションをインストールする

HDInsight アプリケーションは、クラスターを作成するときにインストールするか、または既存の HDInsight クラスターにインストールすることができます。 Azure Resource Manager テンプレートの定義については、MSDN の「[Install an HDInsight application (HDInsight アプリケーションのインストール)](/rest/api/hdinsight/hdinsight-application)」を参照してください。

このアプリケーション (Hue) をデプロイするために必要なファイルは次のとおりです。

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json):HDInsight アプリケーションをインストールするための Resource Manager テンプレート。 MSDN の「[Install an HDInsight application (HDInsight アプリケーションのインストール)](/rest/api/hdinsight/hdinsight-application)」で、独自の Resource Manager テンプレートの開発について参照してください。
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh):エッジ ノードを構成するために Resource Manager テンプレートによって呼び出されるスクリプト アクション。
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz):hui-install_v0.sh から呼び出される hue のバイナリ ファイル。
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz):hui-install_v0.sh から呼び出される hue のバイナリ ファイル。
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz):hui-install_v0.sh から呼び出されるサンプル Web アプリケーション (Tomcat)。

### <a name="to-install-hue-to-an-existing-hdinsight-cluster"></a>既存の HDInsight クラスターに Hue をインストールするには

1. 次の画像を選択して Azure にサインインし、Azure portal で Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Resource Manager テンプレートは、[https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue) にあります。  この Resource Manager テンプレートの作成方法については、MSDN の「[Install an HDInsight application (HDInsight アプリケーションのインストール)](/rest/api/hdinsight/hdinsight-application)」を参照してください。

1. クラスターを含む既存の **リソース グループ** をドロップダウン リストから選択します。 クラスターと同じリソース グループを使用する必要があります。

1. アプリケーションのインストール先となるクラスターの名前を入力します。 このクラスターは既に存在していることが必要です。

1. **[上記の使用条件に同意する]** のチェック ボックスをオンにします。

1. **[購入]** を選択します。

インストールのステータスは、ポータル ダッシュボードにピン留めされたタイルまたはポータルの通知 (ポータル上部のベル アイコンをクリック) で確認できます。  アプリケーションのインストールには 10 分程度かかります。

### <a name="to-install-hue-while-creating-a-cluster"></a>クラスターの作成時に Hue をインストールするには

1. 次の画像を選択して Azure にサインインし、Azure portal で Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Resource Manager テンプレートは、[https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json) にあります。  この Resource Manager テンプレートの作成方法については、MSDN の「[Install an HDInsight application (HDInsight アプリケーションのインストール)](/rest/api/hdinsight/hdinsight-application)」を参照してください。

2. 手順に従ってクラスターを作成し、Hue をインストールします。 HDInsight クラスターの作成の詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

### <a name="other-installation-methods"></a>その他のインストール方法

Azure ポータルのほか、[Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-powershell) や [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-using-azure-cli) を使用して Resource Manager テンプレートを呼び出すこともできます。

## <a name="validate-the-installation"></a>インストールを検証する

Azure ポータルでアプリケーションのステータスを確認し、アプリケーションが正しくインストールされていることを確認できます。 さらに、すべての HTTP エンドポイントが正しく設定されていること、また Web ページが存在する場合は正しく表示されることを確認することもできます。

**Hue** では、次の手順を使用できます。

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインします。
1. アプリケーションをインストールしたクラスターを選択します。
1. **[設定]** メニューで **[アプリケーション]** を選択します。
1. 一覧から **[hue]** を選択して、プロパティを表示します。  
1. [Web ページ] リンクを選択して Web サイトを検証します。

### <a name="azure-cli"></a>Azure CLI

`CLUSTERNAME` および `RESOURCEGROUP` を関連する値に置き換え、次のコマンドを入力します。

* HDInsight クラスターのすべてのアプリケーションを一覧表示する。

    ```azurecli
    az hdinsight application list --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

* 指定したアプリケーションのプロパティを取得する。

    ```azurecli
    az hdinsight application show --name hue --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
    ```

## <a name="troubleshoot-the-installation"></a>インストールのトラブルシューティング

アプリケーションのインストールの状態は、ポータル通知で確認できます (ポータル上部のベル アイコンをクリック)。

アプリケーションのインストールが失敗した場合は、エラー メッセージとデバッグ情報を次の 3 つの場所で確認できます。

* HDInsight アプリケーション: 一般的なエラー情報です。

    ポータルでクラスターを開き、[設定] で [アプリケーション] を選択します。

    :::image type="content" source="./media/hdinsight-apps-install-custom-applications/hdinsight-apps-error.png" alt-text="hdinsight applications application installation error":::

* HDInsight のスクリプト アクション:HDInsight アプリケーションのエラー メッセージがスクリプト アクションのエラーを示している場合、その詳細がスクリプト アクション ウィンドウに表示されます。

    [設定] で [スクリプト アクション] を選択します。 スクリプト アクションの履歴に、エラー メッセージが表示されます。

    :::image type="content" source="./media/hdinsight-apps-install-custom-applications/hdinsight-apps-script-action-error.png" alt-text="hdinsight applications script action error":::

* Apache Ambari Web UI:インストール スクリプトがエラーの原因であった場合は、Ambari Web UI を使用して、インストール スクリプトの完全なログを確認します。

    詳細については、[スクリプト アクションのトラブルシューティング](./troubleshoot-script-action.md)に関するページを参照してください。

## <a name="remove-hdinsight-applications"></a>HDInsight アプリケーションの削除

### <a name="azure-portal"></a>Azure portal

1. [Azure portal](https://portal.azure.com) にサインインします。
1. アプリケーションをインストールしたクラスターを選択します。
1. **[設定]** メニューで **[アプリケーション]** を選択します。
1. 削除するアプリケーションを右クリックし、 **[削除]** を選択します。
1. **[はい]** を選択して確定します。

### <a name="azure-cli"></a>Azure CLI

`NAME`、`CLUSTERNAME`、および `RESOURCEGROUP` を関連する値に置き換え、次のコマンドを入力します。

```azurecli
az hdinsight application delete --name NAME --cluster-name CLUSTERNAME --resource-group RESOURCEGROUP
```

## <a name="next-steps"></a>次のステップ

* [MSDN:Install an HDInsight application (HDInsight アプリケーションのインストール)](/rest/api/hdinsight/hdinsight-application): HDInsight アプリケーションをデプロイするための Resource Manager テンプレートの作成方法について確認します。
* [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md):HDInsight アプリケーションをクラスターにインストールする方法について確認します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md):カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について確認します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [Resource Manager テンプレートを使用して HDInsight で Linux ベースの Apache Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Resource Manager テンプレートを呼び出して HDInsight クラスターを作成する方法を確認します。
* [HDInsight で空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md): HDInsight クラスター、テスト HDInsight アプリケーション、およびホスティング HDInsight アプリケーションにアクセスするために空のエッジ ノードを使用する方法を確認します。
