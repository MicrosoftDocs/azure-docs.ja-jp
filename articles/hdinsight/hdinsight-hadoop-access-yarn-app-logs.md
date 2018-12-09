---
title: Hadoop YARN アプリケーション ログにプログラムを使用してアクセスする - Azure
description: HDInsight の Hadoop クラスター上のアプリケーション ログにプログラムを使用してアクセスします。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/25/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 62499c35fd71d83f80a60e0511e6a27ce0109275
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/28/2018
ms.locfileid: "52495862"
---
# <a name="access-apache-hadoop-yarn-application-logs-on-windows-based-hdinsight"></a>Windows ベースの HDInsight 上の Apache Hadoop YARN アプリケーション ログにアクセスする
このドキュメントでは、Azure HDInsight の Windows ベースの Apache Hadoop クラスターで完了した [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) アプリケーションのログにアクセスする方法について説明します

> [!IMPORTANT]
> このドキュメントの情報は、Windows ベースの HDInsight クラスターに固有のものです。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、[Windows での HDInsight の提供終了](hdinsight-component-versioning.md#hdinsight-windows-retirement)に関する記事を参照してください。 Linux ベースの HDInsight クラスター上の YARN ログへのアクセスについて詳しくは、「[Access Apache Hadoop YARN application logs on Linux-based Apache Hadoop on HDInsight](hdinsight-hadoop-access-yarn-app-logs-linux.md)」(Linux ベースの Apache HDInsight で Apache Hadoop YARN アプリケーション ログにアクセスする) をご覧ください
>


### <a name="prerequisites"></a>前提条件
* Windows ベースの HDInsight クラスター。  「[Create Windows-based Apache Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)」(HDInsight での Windows ベースの Apache Hadoop クラスターの作成) を参照してください。

## <a name="yarn-timeline-server"></a>YARN タイムライン サーバー
<a href="http://hadoop.apache.org/docs/r2.4.1/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Apache Hadoop YARN タイムライン サーバー</a>は完了したアプリケーションに関する汎用的な情報のほか、2 つの異なるインターフェイスを通じてフレームワーク固有のアプリケーション情報を提供します。 具体的には次の処理が行われます。

* HDInsight クラスター上での汎用アプリケーション情報の格納と取得はバージョン 3.1.1.374 以降で有効になります。
* タイムライン サーバーのフレームワーク固有のアプリケーション情報コンポーネントは、現在 HDInsight クラスターで使用できません。

アプリケーションの汎用情報には次のようなデータが含まれています。

* アプリケーション ID (アプリケーションの一意の識別子)
* アプリケーションを開始したユーザー
* アプリケーションを完了するために実行された試みに関する情報
* 特定のアプリケーションの試行で使用されたコンテナー

HDInsight クラスターでは、この情報は Azure Resource Manager によって保存されます。 情報は、クラスターの既定のストレージの履歴ストアに保存されます。 完了したアプリケーションに関するこの汎用データは、REST API を介して取得できます。

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps


## <a name="yarn-applications-and-logs"></a>YARN アプリケーションとログ
YARN では、アプリケーションのスケジュール設定/監視からリソース管理を切り離すことで、複数のプログラミング モデルをサポートします。 YARN は、グローバルな *リソース マネージャー* (RM)、ワーカー ノードごとの*ノード マネージャー* (NM)、アプリケーションごとの*アプリケーション マスター* (AM) を使用します。 アプリケーションごとの AM は、アプリケーションを実行するためのリソース (CPU、メモリ、ディスク、ネットワーク) を RM と調整します。 RM は NM と連携して、これらのリソースに *コンテナー*としての許可を付与します。 AM は、RM によって自身に割り当てられたコンテナーの進行状況を追跡します。 アプリケーションはその性質によって、多くのコンテナーを必要とする場合があります。

* 各アプリケーションが、複数の "*アプリケーション試行*" で構成されていることがあります。 
* アプリケーションの特定の試行にコンテナーが付与されます。 
* コンテナーは、作業の基本単位のコンテキストを提供します。 
* コンテナーのコンテキストで行われる作業は、コンテナーが割り当てられた 1 つのワーカー ノードで実行されます。 

詳細については、[Apache Hadoop YARN の概念][YARN-concepts]に関するページをご覧ください。

アプリケーションのログ (および関連するコンテナーのログ) は、問題のある Hadoop アプリケーションのデバッグに重要です。 YARN は、[ログの集計][log-aggregation]機能により、アプリケーションのログを収集、集計、格納するための便利なフレームワークを提供します。 ログの集計機能により、アプリケーションのログへのアクセスはより確実になります。この機能は、ワーカー ノード上のすべてのコンテナーでログを集計し、アプリケーションが終了した後でワーカー ノードごとに 1 つの集計されたログ ファイルとして既定のファイル システムに保存します。 アプリケーションは数百または数千のコンテナーを使用することがありますが、1 つのワーカー ノードで実行されるすべてのコンテナーのログは 1 つのファイルに集計されます。つまり、アプリケーションで使用するワーカー ノードごとに 1 つのログ ファイルが生成されます。 ログの集計は、HDInsight クラスターでは既定で有効になっており (バージョン 3.0 以上)、集計されたログは、クラスターの既定のコンテナーの次の場所にあります。

    wasb:///app-logs/<user>/logs/<applicationId>

ここで、*user* はアプリケーションを開始したユーザーの名前であり、*applicationId* は YARN リソース マネージャーにより割り当てられたアプリケーションの一意の識別子です。

集計されたログは、コンテナーでインデックスが作成され、[TFile][T-file] に[バイナリ形式][binary-format]で書かれているので、直接読み取ることはできません。 YARN は、アプリケーションまたは関連するコンテナーに対して、これらのログをプレーン テキストとしてダンプする CLI ツールを提供します。 次の YARN コマンドをクラスター ノード上で直接実行することで (RDP による接続後)、これらのログをプレーン テキストとして表示できます。

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>


## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI
YARN ResourceManager UI は、クラスターのヘッドノード上で実行され、Azure ポータル ダッシュボードからアクセスできます。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. 左側のメニューで、**[参照]** をクリックし、**[HDInsight クラスター]** をクリックし、YARN アプリケーション ログにアクセスする Windows ベースのクラスターをクリックします。
3. 上部のメニューで **[ダッシュボード]** をクリックします。 **[HDInsight クエリ コンソール]** という新しいブラウザー タブにページが開きます。
4. **[HDInsight クエリ コンソール]** で、**[Yarn UI]** をクリックします。

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
