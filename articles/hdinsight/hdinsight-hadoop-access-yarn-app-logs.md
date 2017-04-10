---
title: "プログラムで Hadoop YARN アプリケーション ログにアクセスする| Microsoft Docs"
description: "HDInsight の Hadoop クラスター上のアプリケーション ログにプログラムを使用してアクセスします。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 0198d6c9-7767-4682-bd34-42838cf48fc5
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: jgao
ROBOTS: NOINDEX
translationtype: Human Translation
ms.sourcegitcommit: 6407c371bc51461a05429fabaf38d3f9bc80d32c
ms.openlocfilehash: bd9133fde0c3ebfd915c8ae33daa6d0113b37889
ms.lasthandoff: 02/07/2017


---
# <a name="access-yarn-application-logs-on-windows-based-hdinsight"></a>Windows ベースの HDInsight での YARN アプリケーション ログへのアクセス
このトピックでは、Azure HDInsight の Windows ベースの Hadoop クラスターで完了した YARN (Yet Another Resource Negotiator) アプリケーションのログにアクセスする方法について説明します

> [!IMPORTANT]
> このドキュメントの情報は、Windows ベースの HDInsight クラスターに固有のものです。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Window での HDInsight の廃止](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date)に関する記事を参照してください。 Linux ベースの HDInsight クラスター上の YARN ログへのアクセスの詳細については、「 [Access YARN application logs on Linux-based Hadoop on HDInsight (HDInsight の Linux ベースの Hadoop 上の YARN アプリケーション ログにアクセスする)](hdinsight-hadoop-access-yarn-app-logs-linux.md)
> 


### <a name="prerequisites"></a>前提条件
* Windows ベースの HDInsight クラスター。  「 [HDInsight での Windows ベースの Hadoop クラスターの作成](hdinsight-provision-clusters.md)」を参照してください。

## <a name="yarn-timeline-server"></a>YARN タイムライン サーバー
<a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">YARN タイムライン サーバー</a> は完了したアプリケーションに関する汎用的な情報のほか、2 つの異なるインターフェイスを通じてフレームワーク固有のアプリケーション情報を提供します。 具体的には次の処理が行われます。

* HDInsight クラスター上での汎用アプリケーション情報の格納と取得はバージョン 3.1.1.374 以降で有効になります。
* タイムライン サーバーのフレームワーク固有のアプリケーション情報コンポーネントは、現在 HDInsight クラスターで使用できません。

アプリケーションの汎用情報には次のようなデータが含まれています。

* アプリケーション ID (アプリケーションの一意の識別子)
* アプリケーションを開始したユーザー
* アプリケーションを完了するために実行された試みに関する情報
* 特定のアプリケーションの試行で使用されたコンテナー

HDInsight クラスターで、この情報は Azure リソース マネージャーにより、既定の Azure ストレージ アカウントの既定のコンテナーにある履歴ストアに格納されます。 完了したアプリケーションに関するこの汎用データは、REST API を介して取得できます。

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN アプリケーションとログ
YARN はアプリケーションのスケジュール設定/監視からリソース管理を切り離すことで、複数のプログラミング モデル (MapReduce はそのうちの&1; つ) をサポートします。 これは、グローバルな *リソース マネージャー* (RM)、ワーカー ノードごとの*ノード マネージャー* (NM)、アプリケーションごとの*アプリケーション マスター* (AM) によって実現されます。 アプリケーションごとの AM は、アプリケーションを実行するためのリソース (CPU、メモリ、ディスク、ネットワーク) を RM と調整します。 RM は NM と連携して、これらのリソースに *コンテナー*としての許可を付与します。 AM は、RM によって自身に割り当てられたコンテナーの進行状況を追跡します。 アプリケーションはその性質によって、多くのコンテナーを必要とする場合があります。

さらに、クラッシュが発生した場合または AM と RM の間で通信が失われた場合、アプリケーションを完了するために、各アプリケーションで複数の*アプリケーション試行*が行われる場合があります。 そのため、アプリケーションの特定の試行にコンテナーが付与されます。 コンテナーは YARN アプリケーションによって実行される作業の基本単位のコンテキストを提供します。そのコンテキストの中で行われるすべての作業は、コンテナーが割り当てられた&1; つのワーカー ノードで実行されます。 詳細については、[YARN の概念][YARN-concepts]に関するページをご覧ください。

アプリケーションのログ (および関連するコンテナーのログ) は、問題のある Hadoop アプリケーションのデバッグに重要です。 YARN は、[ログの集計][log-aggregation]機能により、アプリケーションのログを収集、集計、格納するための便利なフレームワークを提供します。 ログの集計機能により、アプリケーションのログへのアクセスはより確実になります。この機能は、ワーカー ノード上のすべてのコンテナーでログを集計し、アプリケーションが終了した後でワーカー ノードごとに&1; つの集計されたログ ファイルとして既定のファイル システムに保存します。 アプリケーションは数百または数千のコンテナーを使用することがありますが、1 つのワーカー ノードで実行されるすべてのコンテナーのログは常に&1; つのファイルに集計されます。つまりアプリケーションで使用するワーカー ノードごとに&1; つのログ ファイルが生成されます。 ログの集計は、HDInsight クラスターでは既定で有効になっており (バージョン 3.0 以上)、集計されたログは、クラスターの既定のコンテナーの次の場所にあります。

    wasbs:///app-logs/<user>/logs/<applicationId>

ここで、*user* はアプリケーションを開始したユーザーの名前であり、*applicationId* は YARN リソース マネージャーにより割り当てられたアプリケーションの一意の識別子です。

集計されたログは、コンテナーでインデックスが作成され、[TFile][T-file] に[バイナリ形式][binary-format]で書かれているので、直接読み取ることはできません。 YARN は、アプリケーションまたは関連するコンテナーに対して、これらのログをプレーン テキストとしてダンプする CLI ツールを提供します。 次の YARN コマンドをクラスター ノード上で直接実行することで (RDP による接続後)、これらのログをプレーン テキストとして表示できます。

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI
YARN ResourceManager UI は、クラスターのヘッドノード上で実行され、Azure ポータル ダッシュボードからアクセスできます。 

1. [Azure ポータル](https://portal.azure.com/)にサインインします。 
2. 左側のメニューで、**[参照]** をクリックし、**[HDInsight クラスター]** をクリックし、YARN アプリケーション ログにアクセスする Windows ベースのクラスターをクリックします。
3. 上部のメニューで **[ダッシュボード]**をクリックします。 **[HDInsight クエリ コンソール]**という名前の新しいブラウザー タブにページが開かれます。
4. **[HDInsight クエリ コンソール]** で、**[Yarn UI]** をクリックします。

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

