---
title: "Azure Log Analytics に HDInsight クラスター管理ソリューションを追加する | Microsoft Docs"
description: "Azure Log Analytics を使って HDInsight クラスター用のカスタム ビューを作成する方法を説明します。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: nitinme
ms.openlocfilehash: 21b474e37ef0a6037e05ee1fe8e5088cb3e3601d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="add-hdinsight-cluster-management-solutions-to-log-analytics-preview"></a>Log Analytics に HDInsight クラスター管理ソリューションを追加する (プレビュー)

HDInsight では、Azure Log Analytics に追加できるクラスター固有の管理ソリューションが提供されます。 [管理ソリューション](../log-analytics/log-analytics-add-solutions.md)を使用すると、OMS に機能を追加して、Log Analytics に追加のデータおよび分析ツールを提供できます。 これらのソリューションは、HDInsight クラスターから重要なパフォーマンス メトリックを収集し、メトリックを検索するツールを提供します。 また、これらのソリューションは、HDInsight でサポートされるほとんどのクラスターの種類に対する視覚化とダッシュボードも提供します。 このソリューションで収集したメトリックを使用して、独自の監視ルールおよびアラートを作成できます。 

この記事では、クラスター固有の管理ソリューションを OMS ワークスペースに追加する方法を説明します。

## <a name="prerequisites"></a>前提条件

* Azure Log Analytics を使用するように HDInsight クラスターを構成しておく必要があります。 手順については、「[Log Analytics を使用して Azure HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-tutorial.md)」をご覧ください。

## <a name="add-cluster-specific-management-solutions"></a>クラスター固有の管理ソリューションを追加する

このセクションでは、既存の OMS ワークスペースに HBase クラスター管理ソリューションを追加します。 他の種類の HDInsight クラスター用の同様のソリューションは間もなく使用できるようになります。

1. OMS ダッシュボードを開きます。 Azure Portal で、Azure Log Analytics と関連付けた HDInsight クラスターのブレードを開き、[監視] タブをクリックして **[OMS ダッシュボードを開く]** をクリックします。

    ![OMS ダッシュボードを開く](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-log-analytics-open-oms-dashboard.png "OMS ダッシュボードを開く")

1. OMS ダッシュボードで、**[ソリューション ギャラリー]** または左側のウィンドウのビュー デザイナー アイコンをクリックします。

    ![OMS で管理ソリューションを追加する](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/hdinsight-add-management-solution-oms-portal.png "OMS で管理ソリューションを追加する")

2. ソリューション ギャラリーで **HDInsight HBase Monitoring** を探し、タイルをクリックします。

    ![HBase 管理ソリューションを見つける](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/find-hbase-management-solution.png "HBase 管理ソリューションを見つける")

3. 次の画面で、**[追加]** をクリックします。

     ![HBase 管理ソリューションを追加する](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/add-hbase-management-solution.png "HBase 管理ソリューションを追加する")

4. OMS ダッシュボードに HBase 管理ソリューションのタイルが表示されます。 (この記事の前提条件の一部として) OMS と関連付けたクラスターが HBase クラスターである場合、タイルにはクラスターの名前とクラスターのノード数が表示されます。

    ![追加された HBase 管理ソリューション](./media/hdinsight-hadoop-oms-log-analytics-management-solutions/added-hbase-management-solution.png "追加された HBase 管理ソリューション")

## <a name="next-steps"></a>次のステップ

* [Azure Log Analytics でクエリを実行して Azure HDInsight クラスターを監視する](hdinsight-hadoop-oms-log-analytics-use-queries.md)

## <a name="see-also"></a>関連項目

* [OMS Log Analytics の使用](https://blogs.msdn.microsoft.com/wei_out_there_with_system_center/2016/07/03/oms-log-analytics-create-tiles-drill-ins-and-dashboards-with-the-view-designer/)
* [Log Analytics でのアラート ルールの作成](../log-analytics/log-analytics-alerts-creating.md)
