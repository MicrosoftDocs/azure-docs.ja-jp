---
title: "Windows PC を使用して HDInsight の Hadoop で作業する - Azure | Microsoft Docs"
description: "Windows PC から HDInsight の Hadoop で作業します。 PowerShell、Visual Studio、および Linux ツールを使用してクラスターを管理し、クエリを実行します。 .NET でビッグ データ ソリューションを開発します。"
services: hdinsight
keywords: "Windows の Hadoop, Windows 用の Hadoop"
author: cjgronlund
manager: jhubbard
ms.author: cgronlun
ms.date: 05/17/2017
ms.topic: article
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.openlocfilehash: e4f231c1f9b903d6cc7f2b062b30d2a072be8493
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="work-in-the-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Windows PC から HDInsight の Hadoop エコシステムで作業する

HDInsight の Hadoop エコシステムで作業するための Windows PC での開発および管理オプションについて説明します。 

HDInsight は、Linux 上で開発されたオープンソース テクノロジである Apache Hadoop と Hadoop コンポーネントに基づいています。 HDInsight Version 3.4 以降では、クラスターを作成する OS として、Ubuntu Linux ディストリビューションを使用します。 ただし、HDInsight は、Windows クライアントまたは Windows 開発環境から操作することができます。

## <a name="use-powershell-for-deployment-and-management-tasks"></a>デプロイおよび管理タスクのための PowerShell の使用
Azure PowerShell は、HDInsight のデプロイおよび管理タスクを Windows から制御し自動化するために使用できるスクリプティング環境です。

PowerShell で実行できるタスクの例を次に示します。

* [PowerShell を使用したクラスターの作成](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
* [PowerShell を使用して Hive クエリを実行](hdinsight-hadoop-use-hive-powershell.md)
* [PowerShell を使用したクラスターの管理](hdinsight-administer-use-powershell.md)

[Azure PowerShell のインストールと構成](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)に関するページの手順に従い、最新バージョンを入手してください。 Azure Resource Manager 用の新しいコマンドレットを使用するようにスクリプトを変更する必要がある場合は、「[HDInsight クラスター用の Azure Resource Manager ベースの開発ツールに移行する](hdinsight-hadoop-development-using-azure-resource-manager.md)」を参照してください。

## <a name="utilities-you-can-run-in-a-browser"></a>ブラウザーで実行できるユーティリティ
次のユーティリティは、ブラウザーで動作する Web UI を備えています。
* **[Azure Cloud Shell (プレビュー)](https://docs.microsoft.com/azure/cloud-shell/quickstart)** は、ブラウザーおよび Azure Portal で動作する対話型のコマンドライン シェルです。
* **[Ambari Web UI](hdinsight-hadoop-manage-ambari.md)** は、Azure Portal に用意されている管理および監視ユーティリティで、次のようなさまざまな種類のジョブを管理するために使用できます。
    * [Ambari で REST API を使用する](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Ambari の Hive ビュー](hdinsight-hadoop-use-hive-ambari-view.md)
    * [Ambari の Tez ビュー](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Data Lake (Hadoop) Tools for Visual Studio
Storm トポロジをデプロイおよび管理するには、Data Lake Tools for Visual Studio を使用します。 Data Lake Tools をインストールすると SCP.NET SDK もインストールされるため、Visual Studio を使用して C# Storm トポロジを開発できます。

次の例に進む前に、[Data Lake Tools for Visual Studio をインストールして試してください](hdinsight-hadoop-visual-studio-tools-get-started.md)。 

Visual Studio と Data Lake Tools for Visual Studio を使用して実行できるタスクの例:
* [Visual Studio から Storm トポロジをデプロイおよび管理する](hdinsight-storm-deploy-monitor-topology-linux.md)。
* [Visual Studio を使用して Storm の C# トポロジを開発する](hdinsight-storm-develop-csharp-visual-studio-topology.md)。 このビットには、Azure Cosmos DB、SQL Database などのデータベースに接続できる Storm トポロジのサンプル テンプレートが含まれています。

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio および .NET SDK 

クラスターの管理やビッグ データ アプリケーションの開発のために Visual Studio で .NET SDK を使用することができます。 他の IDE を次のタスクに使用することもできますが、例では Visual Studio を使用しています。

Visual Studio で .NET SDK を使用して実行できるタスクの例:
* [.NET Framework アプリケーションから HDInsight でクラスターを作成し、操作する](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* [.NET SDK を使用して Hive クエリを実行する](hdinsight-hadoop-use-hive-dotnet-sdk.md)
* [Hadoop の Hive と Pig ストリーミングで C# ユーザー定義関数を使用する](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

> ヒント: .NET ソリューションを Windows ベースの HDInsight クラスターで実行している場合は、Linux ベースのクラスターへの移行を計画することをお勧めします。 詳細については、「[Windows ベースの HDInsight から Linux ベースの HDInsight への .NET ソリューションの移行](hdinsight-hadoop-migrate-dotnet-to-linux.md)」を参照してください。

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Spark クラスター用 Intellij IDEA および Eclipse IDE
[Intellij IDEA](https://www.jetbrains.com/idea/download) と [Eclipse IDE](https://www.eclipse.org/downloads/) のどちらも次の操作に使用できます。
* Scala Spark アプリケーションを開発して HDInsight Spark クラスターに送信する。
* Spark クラスター リソースにアクセスする。
* Scala Spark アプリケーションをローカルで開発して実行する。

方法については、次の記事を参照してください。 
* Intellij IDEA: [Azure Toolkit for Intellij プラグインと Scala SDK を使用して Spark アプリケーションを作成する](hdinsight-apache-spark-intellij-tool-plugin.md)
* Eclipse 用 Eclipse IDE または Scala IDE:[ Azure Toolkit for Eclipse を使用して Spark アプリケーションを作成する](hdinsight-apache-spark-eclipse-tool-plugin.md) 


## <a name="notebooks-on-spark-for-data-scientists"></a>データ サイエンティスト向けの Spark のノートブック 
HDInsight の Apache Spark クラスターには、Jupyter ノートブックと共に使用できる Zeppelin ノートブックおよびカーネルが含まれています。 

* [Spark クラスターでカーネルを Jupyter ノートブックと共に使用して Spark アプリケーションをテストする方法について学ぶ](hdinsight-apache-spark-zeppelin-notebook.md)
* [Spark クラスターで Zeppelin ノートブックを使用して Spark ジョブを実行する方法について学ぶ](hdinsight-apache-spark-jupyter-notebook-kernels.md) 


## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Windows での Linux ベースのツールおよびテクノロジの実行

Linux のみで提供されているツールまたはテクノロジを使用する必要がある場合は、次のオプションを検討してください。

* **Bash (ベータ) on Windows 10** は、Windows 上で Linux サブシステムを提供します。 Bash では、専用の Linux インストールを管理することなく、Linux ユーティリティを直接実行することができます。 [Bash ベータ on Windows 10 をインストールして実行する](https://msdn.microsoft.com/commandline/wsl/install_guide)
* **Docker for Windows** は、多くの Linux ベースのツールへのアクセスを提供し、Windows から直接実行できます。 たとえば、Docker を使用して、Windows から Hive 用 Beeline クライアントを直接実行できます。 また、Docker を使用して、ローカルの Jupyter ノートブックを実行し、HDInsight の Spark にリモート接続することもできます。 [Docker for Windows を使用する](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](http://mobaxterm.mobatek.net/)** を使用すると、SSH 接続経由でクラスター ファイル システムをグラフィカルに参照することができます。

## <a name="next-steps"></a>次のステップ
Linux ベースのクラスターで作業するのが初めての方は、次の記事を参照してください。
* [Hadoop、Kafka、Spark、その他のクラスターを設定する](hdinsight-hadoop-provision-linux-clusters.md)
* [Linux 上の HDInsight クラスターに関するヒント](hdinsight-hadoop-linux-information.md)