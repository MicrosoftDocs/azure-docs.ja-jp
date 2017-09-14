---
title: "Azure HDInsight の安全な転送のストレージ アカウントで Hadoop クラスターを作成する | Microsoft Docs"
description: "安全な転送が有効になっている Azure ストレージ アカウントで HDInsight クラスターを作成する方法について説明します。"
keywords: "hadoop の作業開始,hadoop linux,hadoop クイックスタート,安全な転送,azure ストレージ アカウント"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/21/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 370b2f081930fe88527436a1a127309aed6681f0
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="create-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Azure HDInsight の安全な転送のストレージ アカウントで Hadoop クラスターを作成する

"[安全な転送が必須](../storage/common/storage-require-secure-transfer.md)" 機能は、アカウントへのすべての要求にセキュリティで保護された接続を経由するように強制することで、Azure ストレージ アカウントのセキュリティを強化します。 この機能と wasbs スキーマは、HDInsight クラスター バージョン 3.6 以降でのみサポートされます。 

## <a name="prerequisites"></a>前提条件
このチュートリアルを読み始める前に、次の項目を用意する必要があります。

* **Azure サブスクリプション**: 1 か月間の無料試用版アカウントを [azure.microsoft.com/free](https://azure.microsoft.com/free)で作成できます。
* **安全な転送が有効になっている Azure ストレージ アカウント**。 手順については、「[ストレージ アカウントの作成](../storage/common/storage-create-storage-account.md#create-a-storage-account)」と「[安全な転送が必須](../storage/common/storage-require-secure-transfer.md)」を参照してください。
* **ストレージ アカウント上の BLOB コンテナー**。 
## <a name="create-cluster"></a>クラスターの作成

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


このセクションでは、[Azure Resource Manager テンプレート](../azure-resource-manager/resource-group-template-deploy.md)を利用して、HDInsight で Hadoop クラスターを作成します。 テンプレートは、[GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/) にあります。 このチュートリアルを利用するにあたり、Resource Manager テンプレートの使用経験は必要ありません。 その他のクラスター作成方法と、このチュートリアルで使うプロパティの詳細については、[HDInsight クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)に関するページを参照してください。

1. 次の画像をクリックして Azure にサインインし、Azure Portal で Resource Manager テンプレートを開きます。 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-linux-tutorial-get-started/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. 手順に従って、以下の仕様のクラスターを作成します。 

    - HDInsight バージョン 3.6 を指定します。  既定のバージョンは 3.5 です。 バージョン 3.6 以降が必須です。
    - 安全な転送が有効になっているストレージ アカウントを指定します。
    - ストレージ アカウントの短い名前を使用します。
    - ストレージ アカウントと BLOB コンテナーのどちらも、事前に作成しておく必要があります。 

    手順については、「[クラスターの作成](./hdinsight-hadoop-linux-tutorial-get-started.md#create-cluster)」を参照してください。 

独自の構成ファイルを指定するためにスクリプト アクションを使用する場合は、以下の設定で wasbs を使用する必要があります。

- fs.defaultFS (コア サイト)
- spark.eventLog.dir 
- spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>追加のストレージ アカウントの追加

安全な転送が有効になっている追加のストレージ アカウントを追加するには、次のようないくつかのオプションがあります。

- Azure Resource Manager テンプレートの最後のセクションを変更します。
- [Azure Portal](https://portal.azure.com) を使用してクラスターを作成し、リンクされたストレージ アカウントを指定します。
- スクリプト アクションを使用して、既存の HDInsight クラスターに安全な転送が有効になっている追加のストレージ アカウントを追加します。  詳細については、「[HDInsight にストレージ アカウントを追加する](hdinsight-hadoop-add-storage.md)」を参照してください。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、HDInsight クラスターを作成し、ストレージ アカウントへの安全な転送を有効にする方法について説明しました。

HDInsight でデータを分析する方法の詳細については、次の記事を参照してください。

* Visual Studio から Hive クエリを実行する方法など、HDInsight で Hive を使用する方法の詳細については、[HDInsight での Hive の使用][hdinsight-use-hive]に関する記事を参照してください。
* データの変換に使用される言語 Pig の詳細については、[HDInsight での Pig の使用][hdinsight-use-pig]に関する記事を参照してください。
* Hadoop 上のデータを処理するプログラムを作成する方法の 1 つである MapReduce の詳細については、[HDInsight での MapReduce の使用][hdinsight-use-mapreduce]に関する記事を参照してください。
* HDInsight Tools for Visual Studio を使用して HDInsight 上のデータを分析する方法については、 [HDInsight Hadoop Tools for Visual Studio の使用開始](hdinsight-hadoop-visual-studio-tools-get-started.md)に関するページを参照してください。

HDInsight のデータの格納方法や HDInsight にデータを取り込む方法の詳細については、以下の記事を参照してください。

* HDInsight で Azure Storage を使用する方法の詳細については、[HDInsight での Azure Storage の使用](hdinsight-hadoop-use-blob-storage.md)に関するページを参照してください。
* データを HDInsight にアップロードする方法については、[データを HDInsight にアップロードする方法][hdinsight-upload-data]に関する記事を参照してください。

HDInsight クラスターの作成または管理の詳細については、以下の記事を参照してください。

* Linux ベースの HDInsight クラスターを管理する方法については、「 [Ambari を使用した HDInsight クラスターの管理](hdinsight-hadoop-manage-ambari.md)」を参照してください。
* HDInsight クラスターの作成時に選択できるオプションの詳細については、「 [HDInsight での Linux ベースの Hadoop クラスターの作成](hdinsight-hadoop-provision-linux-clusters.md)」を参照してください。
* Linux と Hadoop を使い慣れていて、HDInsight 上の Hadoop に関する詳細情報を確認するには、「 [Linux での HDInsight の使用](hdinsight-hadoop-linux-information.md)」を参照してください。 この記事では、次のような情報を提供します。
  
  * クラスターでホストされる URL (Ambari や WebHCat など)
  * Hadoop ファイルの場所とローカル ファイル システムの例
  * 既定のデータ ストアとして、HDFS ではなく Azure Storage (WASB) を使用する

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md



