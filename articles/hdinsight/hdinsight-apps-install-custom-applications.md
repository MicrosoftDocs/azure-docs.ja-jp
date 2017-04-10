---
title: "Azure HDInsight へのカスタム Hadoop アプリケーションのインストール | Microsoft Docs"
description: "HDInsight アプリケーションに HDInsight アプリケーションをインストールする方法について説明します。"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e556b29c-8176-4bc5-a90b-aa01abfd3aee
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 275b1cd91347fdfcc36e0e8a6448ccd8818a507d
ms.lasthandoff: 03/25/2017


---
# <a name="install-custom-hadoop-applications-on-azure-hdinsight"></a>Azure HDInsight へのカスタム Hadoop アプリケーションのインストール

この記事では、まだ Azure Portal に発行されていない Hadoop アプリケーションを Azure HDInsight にインストールする方法について説明します。 この記事でインストールするアプリケーションは [Hue](http://gethue.com/) です。

HDInsight アプリケーションは、ユーザーが Linux ベースの HDInsight クラスターにインストールすることのできるアプリケーションです。  マイクロソフトや独立系ソフトウェア ベンダー (ISV) によって作成されるほか、ユーザーが独自に作成することもできます。  

その他の関連記事:

* [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md): HDInsight アプリケーションをクラスターにインストールする方法について説明します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について説明します。
* [MSDN: HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションを定義する方法について説明します。

## <a name="prerequisites"></a>前提条件
既存の HDInsight クラスターに HDInsight アプリケーションをインストールする場合は、対象となる HDInsight クラスターが必要です。 新たに作成する場合は、「 [クラスターの作成](hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。 HDInsight クラスターを作成するときに HDInsight アプリケーションをインストールすることもできます。

## <a name="install-hdinsight-applications"></a>HDInsight アプリケーションをインストールする
HDInsight アプリケーションは、クラスターを作成するときにインストールするか、または既存の HDInsight クラスターにインストールすることができます。 Azure Resource Manager テンプレートの定義については、MSDN の「 [Install an HDInsight application (HDInsight アプリケーションのインストール)](https://msdn.microsoft.com/library/mt706515.aspx)」を参照してください。

このアプリケーション (Hue) をデプロイするために必要なファイルは次のとおりです。

* [azuredeploy.json](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/azuredeploy.json): HDInsight アプリケーションをインストールするための Resource Manager テンプレート。 独自の Resource Manager テンプレートの作成については、MSDN の「 [Install an HDInsight application (HDInsight アプリケーションのインストール)](https://msdn.microsoft.com/library/mt706515.aspx) 」を参照してください。
* [hue-install_v0.sh](https://github.com/hdinsight/Iaas-Applications/blob/master/Hue/scripts/Hue-install_v0.sh): Resource Manager テンプレートによって呼び出されるスクリプト アクション。エッジ ノードの構成に使用されます。
* [hue-binaries.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install_v0.sh から呼び出される hue のバイナリ ファイル。
* [hue-binaries-14-04.tgz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/hue-binaries-14-04.tgz): hui-install_v0.sh から呼び出される hue のバイナリ ファイル。
* [webwasb-tomcat.tar.gz](https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv01/webwasb-tomcat.tar.gz): hui-install_v0.sh から呼び出されるサンプル Web アプリケーション (Tomcat)。

**既存の HDInsight クラスターに Hue をインストールするには**

1. 次の画像をクリックして Azure にサインインし、Azure ポータルで Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fhdinsight%2FIaas-Applications%2Fmaster%2FHue%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    このボタンをクリックすると、Azure ポータルの Resource Manager テンプレートが開きます。  この Resource Manager テンプレートは、 [https://github.com/hdinsight/Iaas-Applications/tree/master/Hue](https://github.com/hdinsight/Iaas-Applications/tree/master/Hue)にあります。  この Resource Manager テンプレートの作成方法については、MSDN の「 [Install an HDInsight application (HDInsight アプリケーションのインストール)](https://msdn.microsoft.com/library/mt706515.aspx)」を参照してください。
2. **[パラメーター]** ブレードで、次の各項目を入力します。

   * **ClusterName**: アプリケーションのインストール先となるクラスターの名前を入力します。 このクラスターは既に存在していることが必要です。
3. **[OK]** をクリックしてパラメーターを保存します。
4. **[カスタム デプロイ]** ブレードで「**リソース グループ**」と入力します。  リソース グループとは、クラスターや依存するストレージ アカウントなどのリソースをひとまとめにしたコンテナーです。 クラスターと同じリソース グループを使用する必要があります。
5. **[法律条項]** をクリックし、**[作成]** をクリックします。
6. **[ダッシュボードにピン留めする]** チェック ボックスがオンになっていることを確認して、**[作成]** をクリックします。 インストールのステータスは、ポータル ダッシュボードにピン留めされたタイルまたはポータルの通知 (ポータル上部のベル アイコンをクリック) で確認できます。  アプリケーションのインストールには 10 分程度かかります。

**クラスターの作成時に Hue をインストールするには**

1. 次の画像をクリックして Azure にサインインし、Azure ポータルで Resource Manager テンプレートを開きます。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fhdinsightapps%2Fcreate-linux-based-hadoop-cluster-in-hdinsight.json" target="_blank"><img src="./media/hdinsight-apps-install-custom-applications/deploy-to-azure.png" alt="Deploy to Azure"></a>

    このボタンをクリックすると、Azure ポータルの Resource Manager テンプレートが開きます。  この Resource Manager テンプレートは [https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json](https://hditutorialdata.blob.core.windows.net/hdinsightapps/create-linux-based-hadoop-cluster-in-hdinsight.json)にあります。  この Resource Manager テンプレートの作成方法については、MSDN の「 [Install an HDInsight application (HDInsight アプリケーションのインストール)](https://msdn.microsoft.com/library/mt706515.aspx)」を参照してください。
2. 手順に従ってクラスターを作成し、Hue をインストールします。 HDInsight クラスターの作成の詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。

Azure ポータルのほか、[Azure PowerShell](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-powershell) や [Azure CLI](hdinsight-hadoop-create-linux-clusters-arm-templates.md#deploy-with-cli) を使用して Resource Manager テンプレートを呼び出すこともできます。

## <a name="validate-the-installation"></a>インストールを検証する
Azure ポータルでアプリケーションのステータスを確認し、アプリケーションが正しくインストールされていることを確認できます。 さらに、すべての HTTP エンドポイントが正しく設定されていること、また Web ページが存在する場合は正しく表示されることを確認することもできます。

**Hue ポータルを開くには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックします。  表示されない場合は、**[参照]** をクリックし、**[HDInsight クラスター]** をクリックしてください。
3. アプリケーションをインストールしたクラスターをクリックします。
4. **[設定]** ブレードで **[全般]** カテゴリの **[アプリケーション]** をクリックします。 **[インストール済みアプリ]** ブレードの一覧に **hue** が表示されます。
5. 一覧の **[hue]** をクリックしてプロパティを一覧表示します。  
6. Web ページ リンクをクリックして Web サイトを検証します。具体的には、ブラウザーで HTTP エンドポイントを開いて Hue の Web UI が正しく表示されることを確認し、SSH 使用して SSH エンドポイントを開きます。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

## <a name="troubleshoot-the-installation"></a>インストールのトラブルシューティング
アプリケーションのインストールの状態は、ポータル通知で確認できます (ポータル上部のベル アイコンをクリック)。

アプリケーションのインストールが失敗した場合は、エラー メッセージとデバッグ情報を次の 3 つの場所で確認できます。

* HDInsight アプリケーション: 一般的なエラー情報です。

    ポータルでクラスターを開き、[設定] ブレードで [アプリケーション] をクリックします。

    ![hdinsight applications application installation error](./media/hdinsight-apps-install-applications/hdinsight-apps-error.png)
* HDInsight のスクリプト アクション: HDInsight アプリケーションのエラー メッセージがスクリプト アクションのエラーを示している場合、その詳細がスクリプト アクション ウィンドウに表示されます。

    [設定] ブレードで、[スクリプト アクション] をクリックします。 スクリプト アクションの履歴に、エラー メッセージが表示されます。

    ![hdinsight applications script action error](./media/hdinsight-apps-install-applications/hdinsight-apps-script-action-error.png)
* Ambari Web UI: インストール スクリプトがエラーの原因であった場合は、Ambari Web UI を使用して、インストール スクリプトの完全なログを確認します。

    詳細については、「 [トラブルシューティング](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)」を参照してください。

## <a name="remove-hdinsight-applications"></a>HDInsight アプリケーションの削除
HDInsight アプリケーションを削除する方法は複数あります。

### <a name="use-portal"></a>ポータルの使用
**ポータルを使用してアプリケーションを削除するには**

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. 左側のメニューの **[HDInsight クラスター]** をクリックします。  表示されない場合は、**[参照]** をクリックし、**[HDInsight クラスター]** をクリックしてください。
3. アプリケーションをインストールしたクラスターをクリックします。
4. **[設定]** ブレードで **[全般]** カテゴリの **[アプリケーション]** をクリックします。 インストールされているアプリケーションの一覧が表示されます。 このチュートリアルでは、**[インストール済みアプリ]** ブレードの一覧に **hue** が表示されます。
5. 削除するアプリケーションを右クリックし、 **[削除]**をクリックします。
6. **[はい]** をクリックして確定します。

ポータルからは、クラスターを削除したり、アプリケーションが含まれているリソース グループを削除したりすることもできます。

### <a name="use-azure-powershell"></a>Azure PowerShell の使用
Azure PowerShell を使用して、クラスターを削除したり、リソース グループを削除したりできます。 [Azure PowerShell を使用したクラスターの削除](hdinsight-administer-use-powershell.md#delete-clusters)に関するセクションを参照してください。

### <a name="use-azure-cli"></a>Azure CLI の使用
Azure CLI を使用して、クラスターを削除したり、リソース グループを削除したりできます。 [Azure CLI を使用したクラスターの削除](hdinsight-administer-use-command-line.md#delete-clusters)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ
* [MSDN 記事を参照して HDInsight アプリケーションをインストールする](https://msdn.microsoft.com/library/mt706515.aspx): HDInsight アプリケーションをデプロイするための Resource Manager テンプレートの作成方法について確認します。
* [HDInsight アプリケーションをインストールする](hdinsight-apps-install-applications.md): HDInsight アプリケーションをクラスターにインストールする方法について説明します。
* [HDInsight アプリケーションを発行する](hdinsight-apps-publish-applications.md): カスタム HDInsight アプリケーションを Azure Marketplace に発行する方法について説明します。
* [スクリプト アクションを使用して Linux ベースの HDInsight クラスターをカスタマイズする](hdinsight-hadoop-customize-cluster-linux.md): スクリプト アクションを使用してアプリケーションを追加インストールする方法を確認します。
* [Resource Manager テンプレートを使用して HDInsight で Linux ベースの Hadoop クラスターを作成する](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Resource Manager テンプレートを呼び出して HDInsight クラスターを作成する方法を確認します。
* [HDInsight で空のエッジ ノードを使用する](hdinsight-apps-use-edge-node.md): HDInsight クラスター、テスト HDInsight アプリケーション、およびホスティング HDInsight アプリケーションにアクセスするために空のエッジ ノードを使用する方法を確認します。

