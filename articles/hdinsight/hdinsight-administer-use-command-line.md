---
title: "Azure CLI を使用した Hadoop クラスターの管理 |Microsoft Docs"
description: "Azure CLI を使用して HDInsight で Hadoop クラスターを管理する方法"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: e70e24951481c33525a852479c9c9647a45e71ac
ms.lasthandoff: 03/21/2017


---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Azure CLI を使用した HDInsight での Hadoop クラスターの管理
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure コマンド ライン インターフェイス](../cli-install-nodejs.md) を使用して、Azure HDInsight で Hadoop クラスターを管理する方法について説明します。 Azure CLI は Node.js で実装されます。 Windows、Mac、Linux など、Node.js をサポートするいずれのプラットフォームでも使用できます。

この記事では、HDInsight での Azure CLI の使用についてのみ説明します。 Azure CLI の使用方法に関する一般的なガイドについては、「[Azure CLI のインストール][azure-command-line-tools]」を参照してください。

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **Azure CLI** - インストールと構成に関する情報は、 [Azure CLI のインストールと構成](../cli-install-nodejs.md) に関するページを参照してください。
* 次のコマンドを使用して、**Azure に接続**します。
  
        azure login
  
    職場か学校のアカウントを使用した認証の詳細については、「 [Azure CLI から Azure サブスクリプションへの接続する](../xplat-cli-connect.md)」をご覧ください。
* 次のコマンドを使用して、**Azure リソース マネージャー モードに切り替えます**。
  
        azure config mode arm

ヘルプを取得するには、 **-h** スイッチを使用します。  次に例を示します。

    azure hdinsight cluster create -h

## <a name="create-clusters"></a>クラスターの作成
「 [Azure CLI を使用した HDInsight の Linux ベースのクラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-cli.md)」を参照してください。

## <a name="list-and-show-cluster-details"></a>クラスターの一覧と詳細の表示
クラスターの一覧と詳細を表示するには、次のコマンドを使用します。

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]

## <a name="delete-clusters"></a>クラスターの削除
クラスターを削除するには、次のコマンドを使用します。

    azure hdinsight cluster delete <Cluster Name>

クラスターが含まれるリソース グループを削除して、クラスターを削除することもできます。 グループを削除すると、既定のストレージ アカウントを含め、グループ内のすべてのリソースが削除されることに注意してください。

    azure group delete <Resource Group Name>

## <a name="scale-clusters"></a>クラスターのスケール
Hadoop クラスターのサイズを変更するには:

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>クラスターの HTTP アクセスの有効化/無効化
    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>クラスターの RDP アクセスの有効化/無効化
      azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
      azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


## <a name="next-steps"></a>次のステップ
この記事では、さまざまな HDInsight クラスター管理タスクを実行する方法について説明しました。 詳細については、次の記事を参照してください。

* [Azure Portal を使用した HDInsight の管理][hdinsight-admin-portal]
* [Azure PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
* [Azure HDInsight の概要][hdinsight-get-started]
* [Azure CLI の使い方][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "クラスターの一覧と表示"

