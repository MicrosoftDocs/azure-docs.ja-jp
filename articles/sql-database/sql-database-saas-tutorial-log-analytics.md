---
title: "SQL Database のマルチテナント アプリで Log Analytics を使用する | Microsoft Docs"
description: "Azure SQL Database のサンプル Wingtip SaaS アプリで Log Analytics (OMS) を設定して使用します"
keywords: "SQL データベース チュートリアル"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: billgib; sstein
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 26f6f519ecb3abf6343dc2776aa141dff99ced15
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---
# <a name="setup-and-use-log-analytics-oms-with-the-wingtip-saas-app"></a>Wingtip SaaS アプリで Log Analytics (OMS) を設定して使用する

このチュートリアルでは、エラスティック プールとデータベースを監視するために、*Log Analytics ([OMS](https://www.microsoft.com/cloud-platform/operations-management-suite))* を設定して使用します。 このチュートリアルは[パフォーマンスの監視と管理を行うためのチュートリアル](sql-database-saas-tutorial-performance-monitoring.md)に基づいています。ここでは、*Log Analytics* を使用して、Azure ポータルで提供される監視とアラートの機能を強化する方法を示します。 Log Analytics は、数百のプールや数千のデータベースをサポートするため、大規模な監視とアラートに適しています。 それは、単一監視ソリューションも提供し、複数の Azure サブスクリプションのさまざまなアプリケーションと Azure サービスの監視を統合することができます。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Log Analytics (OMS) をインストールして構成する
> * Log Analytics を使用してプールとデータベースを監視する

このチュートリアルを完了するには、次の前提条件を満たしておく必要があります。

* Wingtip SaaS アプリがデプロイされている。 5 分未満でデプロイするには、[Wingtip SaaS アプリケーションのデプロイと確認](sql-database-saas-tutorial.md)に関するページを参照してください。
* Azure PowerShell がインストールされている。 詳しくは、「[Azure PowerShell を使ってみる](https://docs.microsoft.com/powershell/azure/get-started-azureps)」をご覧ください。

SaaS のシナリオとパターン、および監視ソリューションの要件に対する影響については、[パフォーマンスの監視と管理を行うためのチュートリアル](sql-database-saas-tutorial-performance-monitoring.md)を参照してください。

## <a name="monitoring-and-managing-performance-with-log-analytics-oms"></a>Log Analytics (OMS) を使用したパフォーマンスの監視と管理

SQL Database では、データベースとプールに対する監視とアラートを使用できます。 この組み込みの監視とアラートはリソースに固有であり、少数のリソースでは便利ですが、多数のインストールの監視や、さまざまなリソースとサブスクリプション間の統一されたビューの提供には適していません。

大規模なシナリオでは、Log Analytics を使用できます。 これは、出力された診断ログとログ分析ワークスペースで収集されたテレメトリを分析する独立した Azure サービスであり、多くのサービスからテレメトリを収集できます。また、このサービスを使用して、クエリを実行し、アラートを設定できます。 Log Analytics には、オペレーション データを分析して視覚化できるクエリ言語とデータ視覚化ツールが組み込まれています。 SQL Analytics ソリューションには、エラスティック プールとデータベースの監視とアラートを行うためのさまざまな定義済みのビューとクエリが用意されています。さらに、独自のアドホック クエリを追加でき、必要に応じてそれらを保存できます。 OMS には、カスタム ビュー デザイナーも用意されています。

Log Analytics のワークスペースと分析ソリューションは、Azure ポータルと OMS の両方で開くことができます。 Azure ポータルは新しいアクセス ポイントですが、一部の領域では OMS ポータルの背後に配置される場合があります。

### <a name="start-the-load-generator-to-create-data-to-analyze"></a>ロード ジェネレーターを起動して分析するデータを作成する

1. **PowerShell ISE** で **Demo-PerformanceMonitoringAndManagement.ps1** を開きます。 このスクリプトは、このチュートリアル中にいくつかのロード生成シナリオで実行するため、開いたままにしておいてください。
1. テナント数が 5 未満の場合は、より興味深い監視コンテキストを提供するために、テナントのバッチをプロビジョニングします。
   1. **$DemoScenario = 1** **(テナントのバッチのプロビジョニング)** を設定します。
   1. **F5** キーを押して、スクリプトを実行します。

1. **$DemoScenario = 2** **(標準強度負荷の生成 (およそ 40 DTU))** を設定します。
1. **F5** キーを押して、スクリプトを実行します。

## <a name="get-the-wingtip-application-scripts"></a>Wingtip アプリケーションのスクリプトを取得する

Wingtip Tickets のスクリプトとアプリケーションのソース コードは、[WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) Github リポジトリから入手できます。 スクリプト ファイルは、[Learning Modules フォルダー](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)にあります。 **Learning Modules** フォルダーを、フォルダー構造を保ったままローカル コンピューターにダウンロードします。

## <a name="installing-and-configuring-log-analytics-and-the-azure-sql-analytics-solution"></a>Log Analytics と Azure SQL Analytics ソリューションをインストールして構成する

Log Analytics は構成する必要がある独立したサービスです。 Log Analytics は、ログ データ、テレメトリ、およびメトリックをログ分析ワークスペース内に収集します。 ワークスペースは Azure の他のリソースと同じようにリソースであるため、作成する必要があります。 ワークスペースは監視対象のアプリケーションと同じリソース グループ内に作成する必要はありませんが、多くの場合は、そうすることが最も道理にかなっています。 Wingtip SaaS アプリの場合、これを行うと、リソース グループを削除するだけで、アプリケーションでワークスペースを簡単に削除できます。

1. **PowerShell ISE** で ...\\Learning Modules\\Performance Monitoring and Management\\Log Analytics\\*Demo-LogAnalytics.ps1* を開きます。
1. **F5** キーを押して、スクリプトを実行します。

この時点で、Azure ポータル (または、OMS ポータル) で Log Analytics を開くことができます。 Log Analytics ワークスペース内にテレメトリが収集されて視覚化されるまで、数分かかります。 システムがデータを収集する時間を長くすればするほど、エクスペリエンスは興味深いものになります。 このタイミングで、ロード ジェネレーターが実行中であることを確認してから少し休憩することをお勧めします。


## <a name="use-log-analytics-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Log Analytics と SQL Analytics ソリューションを使用してプールとデータベースを監視する


この演習では、Log Analytics と OMS ポータルを開き、データベースとプールで収集されるテレメトリを調べます。

1. [Azure ポータル](https://portal.azure.com)に移動し、[その他のサービス] をクリックして Log Analytics を検索することで、Log Analytics を開きます。

   ![Log Analytics を開く](media/sql-database-saas-tutorial-log-analytics/log-analytics-open.png)

1. *wtploganalytics-&lt;USER&gt;* という名前のワークスペースを選択します。

1. **[概要]** を選択して、Azure ポータルで Log Analytics ソリューションを選択します。
   ![overview-link](media/sql-database-saas-tutorial-log-analytics/click-overview.png)

    **重要**: ソリューションがアクティブになるまで数分かかる可能性があります。 しばらくお待ちください。

1. [Azure SQL Analytics] タイルをクリックして開きます。

    ![概要](media/sql-database-saas-tutorial-log-analytics/overview.png)

    ![分析](media/sql-database-saas-tutorial-log-analytics/analytics.png)

1. ソリューション ブレードのビューは横方向にスクロールします。スクロール バーは下部に表示されます (必要に応じてブレードを更新してください)。

1. さまざまなビューまたは個々のリソースをクリックすることでドリルダウン エクスプローラーを開いて、データを調べます。エクスプローラーでは、左上の時間スライダーを使用するか垂直バーをクリックして、より短いタイム スライスにフォーカスできます。 このビューでは、個々のデータベースまたはプールを選択して、特定のリソースにフォーカスできます。

    ![グラフ](media/sql-database-saas-tutorial-log-analytics/chart.png)

1. ソリューション ブレードに戻り、右端までスクロールすると、保存済みのクエリが表示されます。表示されたクエリをクリックして開き、内容を調べることができます。 これらのクエリを変更してテストしたり、興味深いクエリを作成して保存し、再び開いて他のリソースで使用したりできます。

1. Log Analytics ワークスペース ブレードに戻り、OMS ポータルを開いてソリューションをそこで開きます。

    ![OMS](media/sql-database-saas-tutorial-log-analytics/oms.png)

1. OMS ポータルでは、アラートを構成することができます。 データベース DTU ビューのアラート部分をクリックします。

1. 表示される Log Search ビューには、メトリックを表す棒グラフが表示されます。

    ![ログ検索](media/sql-database-saas-tutorial-log-analytics/log-search.png)

1. ツールバーの [アラート] をクリックすると、アラート構成が表示され、それを変更することができます。

    ![アラート ルールの追加](media/sql-database-saas-tutorial-log-analytics/add-alert.png)

Log Analytics と OMS での監視とアラートは、各ブレード リソースでのリソース固有のアラートとは異なり、ワークスペース内のデータに対するクエリに基づいています。 したがって、すべてのデータベースを対象とするアラートを定義でき、データベースごとに 1 つのアラートを定義する必要はありません。 または、複数のリソースの種類を対象とする複合クエリを使用するアラートを作成できます。 クエリは、ワークスペースで利用可能なデータによってのみ制限されます。

Log Analytics for SQL Database は、ワークスペース内のデータ量に基づいて課金されます。 このチュートリアルでは、Free ワークスペースを作成しました。このワークスペースの制限は、1 日あたり 500 MB です。 この制限に達すると、データはワークスペースに追加されなくなります。


## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Log Analytics (OMS) をインストールして構成する
> * Log Analytics を使用してプールとデータベースを監視する

[テナント分析のチュートリアル](sql-database-saas-tutorial-tenant-analytics.md)

## <a name="additional-resources"></a>その他のリソース

* [Wingtip SaaS アプリケーションの初期のデプロイに基づく作業のための追加のチュートリアル](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md)
* [OMS](https://blogs.technet.microsoft.com/msoms/2017/02/21/azure-sql-analytics-solution-public-preview/)

