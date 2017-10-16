---
title: "OMS を使用してロジック アプリの実行を監視して洞察を得る - Azure Logic Apps | Microsoft Docs"
description: "Log Analytics と Operations Management Suite (OMS) を使用してロジック アプリの実行を監視して洞察と豊富なデバッグの詳細を取得し、トラブルシューティングや診断に活用します"
author: divyaswarnkar
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/9/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 5afe01e713516277abc3c2dfcccfc6d36cfc70d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-get-insights-about-logic-app-runs-with-operations-management-suite-oms-and-log-analytics"></a>Operations Management Suite (OMS) と Log Analytics を使用してロジック アプリの実行を監視して洞察を得る

監視と豊富なデバッグ情報の獲得のために、ロジック アプリの作成と同時に Log Analytics をオンにできます。 Log Analytics では、Operations Management Suite (OMS) ポータルを通じて、ロジック アプリの実行を監視して診断ログを収集できます。 Logic Apps の管理ソリューションを OMS に追加すると、ロジック アプリの実行の状態の集計や、その状態、実行時間、再送信の状態、および相関関係 ID などの詳細情報を取得します。

このトピックでは、Log Analytics をオンにするか Logic Apps 管理ソリューションを OMS にインストールして、ロジック アプリの実行のランタイム イベントやデータを確認できます。

 > [!TIP]
 > 既存のロジック アプリを監視するには、これらの手順に従って[診断ログの記録をオンにしてロジック アプリのランタイム データを OMS に送信](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics)します。

## <a name="requirements"></a>必要条件

開始する前に、OMS ワークスペースを用意しておく必要があります。 [OMS ワークスペースの作成方法](../log-analytics/log-analytics-get-started.md)を確認してください。 

## <a name="turn-on-diagnostics-logging-when-creating-logic-apps"></a>ロジック アプリの作成時に診断ログの記録をオンにする

1. [Azure Portal](https://portal.azure.com) で、ロジック アプリを作成します。 **[新規]** > **[Enterprise Integration]** > **[Logic App]** > **[作成]** の順に選択します。

   ![ロジック アプリを作成します](media/logic-apps-monitor-your-logic-apps-oms/find-logic-apps-azure.png)

2. **[ロジック アプリの作成]** ページで、次に示すタスクを実行します。

   1. ロジック アプリに名前を付け、Azure サブスクリプションを選択します。 
   2. Azure リソース グループを作成するか選択します。
   3. **[Log Analytics]** を **[オン]** にします。 
   ロジック アプリの実行データを送信する OMS ワークスペースを選択します。 
   4. 準備ができたら、**[ダッシュボードにピン留めする]** > **[作成]** を選択します。

      ![ロジック アプリを作成する](./media/logic-apps-monitor-your-logic-apps-oms/create-logic-app.png)

      この手順を完了すると、Azure が OMS ワークスペースに関連付けられているロジック アプリを作成します。 
      また、この手順では OMS ワークスペースに Logic Apps 管理ソリューションを自動的にインストールします。

3. OMS でロジック アプリの実行を確認するには、[この手順を続行します](#view-logic-app-runs-oms)。

## <a name="install-the-logic-apps-management-solution-in-oms"></a>OMS に Logic Apps 管理ソリューションをインストールする

ロジック アプリの作成時にすでに Log Analytics をオンにしている場合は、この手順をスキップします。 OMS に Logic Apps 管理ソリューションがすでにインストールされています。

1. [Azure Portal](https://portal.azure.com) で、**[その他のサービス]** を選択します。 次に示すように、"log analytics" を検索し、**[Log Analytics]** を選択します。

   ![[Log Analytics] を選択する](media/logic-apps-monitor-your-logic-apps-oms/find-log-analytics.png)

2. **[Log Analytics]** で、ご利用の OMS ワークスペースを検索して選択します。 

   ![OMS ワークスペースを選択する](media/logic-apps-monitor-your-logic-apps-oms/select-logic-app.png)

3. **[管理]** で、**[OMS ポータル]** を選択します。

   ![[OMS ポータル] を選択する](media/logic-apps-monitor-your-logic-apps-oms/oms-portal-page.png)

4. OMS ホーム ページに、アップグレード バナーが表示されている場合は、そのバナーを選択して、最初に OMS ワークスペースをアップグレードします。 その後で、**[ソリューション ギャラリー]** を選択します。

   ![[ソリューション ギャラリー] を選択する](media/logic-apps-monitor-your-logic-apps-oms/solutions-gallery.png)

5. **[すべてのソリューション]** で、**[Logic Apps Management]\(Logic Apps 管理\)** ソリューションのタイルを検索して選択します。

   ![[Logic Apps Management]\(Logic Apps 管理\) を選択する](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-management-tile2.png)

6. ソリューションを OMS ワークスペースにインストールするには、**[追加]** を選択します。

   ![[Logic Apps Management]\(Logic Apps 管理\) の [追加] を選択する](media/logic-apps-monitor-your-logic-apps-oms/add-logic-apps-management-solution.png)

<a name="view-logic-app-runs-oms"></a>

## <a name="view-your-logic-app-runs-in-your-oms-workspace"></a>OMS ワークスペースでロジック アプリの実行を表示する

1. ロジック アプリの実行の数と状態を確認するには、OMS ワークスペースの概要ページに移動します。 **[Logic Apps Management]\(Logic Apps 管理\)** タイルの詳細を確認します。

   ![ロジック アプリの実行の数と状態を示す概要タイル](media/logic-apps-monitor-your-logic-apps-oms/overview.png)

   > [!Note]
   > [Logic Apps Management]\(Logic Apps 管理\) タイルではなく、このアップグレード バナーが表示されている場合は、そのバナーを選択して、最初に OMS ワークスペースをアップグレードします。
  
   > !["OMS ワークスペース" をアップグレードする](media/logic-apps-monitor-your-logic-apps-oms/oms-upgrade-banner.png)

2. ロジック アプリの実行に関する詳細が含まれる概要を確認するには、**[Logic Apps Management]\(Logic Apps 管理\)** タイルを選択します。

   ここでは、ロジック アプリの実行は名前または実行状態でグループ化されます。

   ![ロジック アプリの実行状態の概要](media/logic-apps-monitor-your-logic-apps-oms/logic-apps-runs-summary.png)
   
3. 特定のロジック アプリまたは状態のすべての実行を表示するには、ロジック アプリまたは状態の行を選択します。

   特定のロジック アプリのすべての実行を表示する例は次の通りです。

   ![あるロジック アプリまたは状態の実行を表示する](media/logic-apps-monitor-your-logic-apps-oms/logic-app-run-details.png)

   > [!NOTE]
   > 再送信された実行から生じる実行については、**[再送信]** 列に [はい] と表示されます。

4. これらの結果をフィルター処理するには、クライアント側およびサーバー側の両方でフィルター処理を実行します。

   * クライアント側のフィルター: 各列で、必要なフィルターを選択します。 
   次に例をいくつか示します。

     ![列フィルターの例](media/logic-apps-monitor-your-logic-apps-oms/filters.png)

   * サーバー側のフィルター: 特定の時間枠を選択または表示される実行の数を制限するには、ページ上部のスコープ コントロールを使用します。 
   既定では、一度に 1000 件のレコードのみ表示されます。 
   
     ![時間枠を変更する](media/logic-apps-monitor-your-logic-apps-oms/change-interval.png)
 
5. すべてのアクションと特定の実行の詳細を表示するには、行を選択します。これにより [ログ検索] ページが表示されます。 

   * この情報を表で確認するには、**[表]** を選択します。
   * クエリを変更するには、検索バーでクエリ文字列を編集します。 
   より高いエクスペリエンスを得るには、**[高度な分析]** を選択します。

     ![ロジック アプリの実行のアクションと詳細を表示する](media/logic-apps-monitor-your-logic-apps-oms/log-search-page.png)

     [Azure Log Analytics] ページで、クエリを更新して表から結果を確認できます。 
     このクエリは [Kusto クエリ言語](https://docs.loganalytics.io/docs/Language-Reference)を使用します。他の結果を表示するには編集します。 

     ![Azure Log Analytics - クエリ ビュー](media/logic-apps-monitor-your-logic-apps-oms/query.png)

## <a name="next-steps"></a>次のステップ

* [B2B メッセージを監視する](../logic-apps/logic-apps-monitor-b2b-message.md)
