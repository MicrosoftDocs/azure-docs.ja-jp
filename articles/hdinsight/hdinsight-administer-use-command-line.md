---
title: Azure クラシック CLI を使用して Apache Hadoop クラスターを管理する - Azure HDInsight
description: Azure クラシック CLI を使用して Azure HDInsight の Apache Hadoop クラスターを管理する方法について説明します。
services: hdinsight
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: tyfox
ms.openlocfilehash: 61be4088034d8cc557c67c5b6de4972766654a39
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202386"
---
# <a name="manage-apache-hadoop-clusters-in-hdinsight-using-the-azure-classic-cli"></a>Azure クラシック CLI を使用して HDInsight の Apache Hadoop クラスターを管理する
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

[Azure クラシック CLI](../cli-install-nodejs.md) を使用して Azure HDInsight の [Apache Hadoop](https://hadoop.apache.org/) クラスターを管理する方法について説明します。 Classic CLI は Node.js で実装されます。 Windows、Mac、Linux など、Node.js をサポートするいずれのプラットフォームでも使用できます。

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

## <a name="prerequisites"></a>前提条件
この記事を読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**。 [Azure 無料試用版の取得](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)に関するページを参照してください。
* **Azure Classic CLI** - インストールと構成に関する情報は、[Azure Classic CLI のインストールと構成](../cli-install-nodejs.md) に関するページを参照してください。
* 次のコマンドを使用して、**Azure に接続**します。

    ```cli
    azure login
    ```
  
    職場か学校のアカウントを使用した認証の詳細については、[Azure Classic CLI から Azure サブスクリプションへの接続する](/cli/azure/authenticate-azure-cli)に関するページを参照してください。
* 次のコマンドを使用して、**Azure リソース マネージャー モードに切り替えます**。
  
    ```cli
    azure config mode arm
    ```

ヘルプを取得するには、 **-h** スイッチを使用します。  例: 

```cli
azure hdinsight cluster create -h
```

## <a name="create-clusters-with-the-cli"></a>CLI を使用したクラスターの作成
「[Azure CLI を使用した HDInsight クラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-cli.md)」を参照してください。

## <a name="list-and-show-cluster-details"></a>クラスターの一覧と詳細の表示
クラスターの一覧と詳細を表示するには、次のコマンドを使用します。

```cli
azure hdinsight cluster list
azure hdinsight cluster show <Cluster Name>
```

![クラスター一覧のコマンドライン表示][image-cli-clusterlisting]

## <a name="delete-clusters"></a>クラスターの削除
クラスターを削除するには、次のコマンドを使用します。

```cli
azure hdinsight cluster delete <Cluster Name>
```

クラスターが含まれるリソース グループを削除して、クラスターを削除することもできます。 グループを削除すると、既定のストレージ アカウントを含め、グループ内のすべてのリソースが削除されることに注意してください。

```cli
azure group delete <Resource Group Name>
```

## <a name="scale-clusters"></a>クラスターのスケール
Apache Hadoop クラスターのサイズを変更するには:

```cli
azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>
```


## <a name="enabledisable-http-access-for-a-cluster"></a>クラスターの HTTP アクセスの有効化/無効化

```cli
azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
azure hdinsight cluster disable-http-access [options] <Cluster Name>
```

## <a name="next-steps"></a>次の手順
この記事では、さまざまな HDInsight クラスター管理タスクを実行する方法について説明しました。 詳細については、次の記事を参照してください。

* [Azure portal を使用して HDInsight の Apache Hadoop クラスターを管理する](hdinsight-administer-use-portal-linux.md)
* [Azure PowerShell を使用した HDInsight の管理][hdinsight-admin-powershell]
* [Azure HDInsight の概要][hdinsight-get-started]
* [Azure Classic CLI の使い方][azure-command-line-tools]

[azure-command-line-tools]: ../cli-install-nodejs.md
[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md
[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/command-line-list-of-clusters.png "クラスターの一覧と表示"
