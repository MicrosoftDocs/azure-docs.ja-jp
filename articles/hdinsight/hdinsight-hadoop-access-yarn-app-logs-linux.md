---
title: "Linux ベース HDInsight で Hadoop YARN アプリケーション ログにアクセスする - Azure | Microsoft Docs"
description: "コマンドラインと Web ブラウザーの両方を使用して、Linux ベース HDInsight (Hadoop) クラスターで YARN アプリケーション ログにアクセスする方法について説明します。"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 3ec08d20-4f19-4a8e-ac86-639c04d2f12e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 7bf5d568e59ead343ff2c976b310de79a998673b
ms.openlocfilehash: fbbbddc47f24a46eac9bc64d4420ee8429ed4ad1
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="access-yarn-application-logs-on-linux-based-hdinsight"></a>Linux ベースの HDInsight での YARN アプリケーション ログへのアクセス

Azure HDInsight の Hadoop クラスター上の YARN (Yet Another Resource Negotiator) アプリケーションのログにアクセスする方法について説明します。

> [!IMPORTANT]
> このドキュメントの手順では、Linux を使用する HDInsight クラスターが必要です。 Linux は、バージョン 3.4 以上の HDInsight で使用できる唯一のオペレーティング システムです。 詳細については、「[HDInsight コンポーネントのバージョン管理](hdinsight-component-versioning.md#hdinsight-windows-retirement)」を参照してください。

## <a name="YARNTimelineServer"></a>YARN タイムライン サーバー

[YARN タイムライン サーバー](http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html)は、2 つの異なるインターフェイスを通じて、完了したアプリケーションに関する全般的な情報と、フレームワーク固有のアプリケーション情報を提供します。 具体的には次の処理が行われます。

* HDInsight クラスター上での汎用アプリケーション情報の格納と取得はバージョン 3.1.1.374 以降で有効になります。
* タイムライン サーバーのフレームワーク固有のアプリケーション情報コンポーネントは、現在 HDInsight クラスターで使用できません。

アプリケーションの汎用情報には次の種類のデータが含まれています。

* アプリケーション ID (アプリケーションの一意の識別子)
* アプリケーションを開始したユーザー
* アプリケーションを完了するために実行された試みに関する情報
* 特定のアプリケーションの試行で使用されたコンテナー

## <a name="YARNAppsAndLogs"></a>YARN アプリケーションとログ

YARN はアプリケーションのスケジュール設定/監視からリソース管理を切り離すことで、複数のプログラミング モデル (MapReduce はそのうちの 1 つ) をサポートします。 YARN は、グローバルな *リソース マネージャー* (RM)、ワーカー ノードごとの*ノード マネージャー* (NM)、アプリケーションごとの*アプリケーション マスター* (AM) を使用します。 アプリケーションごとの AM は、アプリケーションを実行するためのリソース (CPU、メモリ、ディスク、ネットワーク) を RM と調整します。 RM は NM と連携して、これらのリソースに *コンテナー*としての許可を付与します。 AM は、RM によって自身に割り当てられたコンテナーの進行状況を追跡します。 アプリケーションはその性質によって、多くのコンテナーを必要とする場合があります。

各アプリケーションが、複数の "*アプリケーション試行*" で構成されていることがあります。 アプリケーションが失敗した場合、新しい試行として再試行される場合があります。 各試行は、コンテナーで実行されます。 ある意味、コンテナーは YARN アプリケーションによって実行される作業の基本単位に対して、コンテキストを提供します。 コンテナーのコンテキストで行われる作業はすべて、コンテナーが割り当てられた 1 つのワーカー ノードで実行されます。 詳細については、[YARN の概念][YARN-concepts]に関するページをご覧ください。

アプリケーションのログ (および関連するコンテナーのログ) は、問題のある Hadoop アプリケーションのデバッグに重要です。 YARN は、[ログの集計][log-aggregation]機能により、アプリケーションのログを収集、集計、格納するための便利なフレームワークを提供します。 ログの集計機能により、アプリケーション ログへのアクセスがさらに確実になります。 この機能により、ワーカー ノード上のすべてのコンテナーのログが集計され、ワーカー ノードごとに 1 つの集計ログとして保存されます。 ログは、アプリケーションの完了後に既定のファイル システムに保存されます。 アプリケーションは数百または数千のコンテナーを使用することがありますが、1 つのワーカー ノードで実行されるすべてのコンテナーのログは常に 1 つのファイルに集計されます。 したがって、アプリケーションで使用するワーカー ノードごとに存在するログは 1 つのみです。 ログの集計は、既定で HDInsight クラスター バージョン 3.0 以降で有効になります。 集計されたログは、クラスターの既定のストレージに配置されます。 次のパスは、ログへの HDFS パスです。

    /app-logs/<user>/logs/<applicationId>

このパスで、`user` はアプリケーションを開始したユーザーの名前です。 `applicationId` は、YARN RM からアプリケーションに割り当てられた一意の識別子です。

集計されたログは、コンテナーでインデックスが作成され、[TFile][T-file] に[バイナリ形式][binary-format]で書かれているので、直接読み取ることはできません。 YARN ResourceManager Logs または CLI ツールを使用して、対象のアプリケーションまたはコンテナーのログをプレーン テキストとして表示します。

## <a name="yarn-cli-tools"></a>YARN CLI ツール

YARN CLI ツールを使用するには、まず SSH を使用して HDInsight クラスターに接続する必要があります。 詳細については、[HDInsight での SSH の使用](hdinsight-hadoop-linux-use-ssh-unix.md)に関するページを参照してください。

次のいずれかのコマンドを実行することで、これらのログをプレーン テキストとして表示できます。

    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
    yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>

これらのコマンドを実行するときは、&lt;applicationId>、&lt;user-who-started-the-application>、&lt;containerId>、&lt;worker-node-address> の情報を指定します。

## <a name="yarn-resourcemanager-ui"></a>YARN ResourceManager UI

YARN ResourceManager UI はクラスターのヘッド ノードで実行されます。 Ambari Web UI を使ってアクセスします。 YARN ログを表示するには、次の手順を使用します。

1. Web ブラウザーで、https://CLUSTERNAME.azurehdinsight.net に移動します。 CLUSTERNAME を、使用する HDInsight クラスターの名前に置き換えます。
2. 左側のサービスの一覧で、 **[YARN]**を選択します。

    ![選択された YARN サービス](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnservice.png)
3. **[クイック リンク]** ボックスの一覧で、クラスター ヘッドノードのいずれかを選択し、**[ResourceManager Log]** を選択します。

    ![YARN クイック リンク](./media/hdinsight-hadoop-access-yarn-app-logs-linux/yarnquicklinks.png)

    YARN のログへのリンクの一覧が表示されます。

[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf
[binary-format]:https://issues.apache.org/jira/browse/HADOOP-3315
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/

