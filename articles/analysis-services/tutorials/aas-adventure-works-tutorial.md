---
title: Azure Analysis Services Adventure Works チュートリアル | Microsoft Docs
description: Azure Analysis Services の Adventure Works チュートリアルを紹介します｡
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 92bab3e6dcea0b6b234d361a346698be15088fc0
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2019
ms.locfileid: "54191515"
---
# <a name="azure-analysis-services---adventure-works-tutorial"></a>Azure Analysis Services - Adventure Works チュートリアル

このチュートリアルは､Visual Studio [Analysis Services プロジェクト](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftAnalysisServicesModelingProjects) または [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) を使用して 1400 互換性レベルで表形式モデルを作成､配備する方法に関するレッスンで構成されます｡  
Analysis Services および表形式モデルが初めてという方は、このチュートリアルを最後まで終えることで、Visual Studio を使用して基本的な表形式モデルを作成してデプロイする方法を最短で学ぶことができます。 前提条件となるものをすべて整えた後､このチュートリアルを終えるのに要する時間は 2 ～ 3 時間です｡  
  
## <a name="what-you-learn"></a>学習内容   
  
-   Visual Studio において **1400 互換性レベル**で新しい表形式モデルのプロジェクトを作成する方法
  
-   リレーショナル データベースから表形式モデルのプロジェクト ワークスペース データベースにデータをインポートする方法  
  
-   モデル内の表間のリレーションシップを作成､管理する方法  
  
-   計算列､メジャー､重要な業務指標の分析に有用な主要業績評価指標を作成する方法  
  
-   業務およびアプリケーションに固有のビューポイントを提供することによってより容易にモデルデータを参照するのに役立つパースペクティブと階層を作成､管理する方法  
  
-   テーブルのデータをより小さな論理部分に分割し､それぞれを独立して処理することを可能にするパーティションを作成する方法  
  
-   ユーザー メンバーとともにロールを作成することによってモデル オブジェクトとモデル データのセキュリティを保護する方法  
  
-   Visual Studio を使用して **Azure Analysis Services** サーバーまたは **SQL Server 2017 Analysis Services** サーバーに表形式モデルをデプロイする方法  
  
## <a name="prerequisites"></a>前提条件  
このチュートリアルを完了するには、次のものが必要です。  
  
-   Azure Analysis Services サーバー。 無料の [試用版 Azure Analysis Services ](https://azure.microsoft.com/services/analysis-services/) へのサインアップと [サーバーの作成](../analysis-services-create-server.md) 

-   **AdventureWorksDW サンプル データベース**がインストールされた [Azure SQL Data Warehouse](../../sql-data-warehouse/create-data-warehouse-portal.md)、または [Adventure Works サンプル データベース](https://github.com/Microsoft/sql-server-samples/releases/tag/adventureworks)がインストールされた SQL Server Data Warehouse。

    **重要:** オンプレミスの SQL Server Data Warehouse にサンプル データベースをインストールして、Azure Analysis Services サーバーにモデルをデプロイする場合は、[オンプレミスのデータ ゲートウェイ](../analysis-services-gateway.md)が必要です。

-   最新バージョンの [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) for Visual Studio。

-   最新バージョンの [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)    

-   [Power BI Desktop](https://powerbi.microsoft.com/desktop/) または Excel などのクライアント アプリケーション 

## <a name="scenario"></a>シナリオ  
このチュートリアルは､Adventure Works Cycles という架空の会社を題材にしています｡ Adventure Works は、自転車、部品、および付属品を製造し、北米、ヨーロッパ、およびアジアに流通市場を持つ大きな多国籍の製造企業です。 従業員は 500 人です。 また､Adventure Works はその市場拠点で複数の地域担当販売チームも有しています｡ このチュートリアルでは､営業ユーザーのために AdventureWorksDW データベースのインターネット販売データを分析するための表形式モデルを作成します｡  
  
チュートリアル全体は､いくつかのレッスンから構成されています｡ 各レッスンには、いくつかのタスクがあります。 レッスンを終えるには､各タスクを順に終了する必要があります｡ 1 つのレッスンには､似たような結果になるタスクがいくつ含まれることがありますが､各タスクの実行手順は少しずつ異なります｡ これは､同じタスクであってもその実行方法が複数あることを意味しており､前のレッスンやタスクで学習したスキルを活用して課題に取り組んでもらうことを目的としています｡  
  
レッスンの目的は、SSDT に含まれる機能の多くを使用することによって基本的な表形式モデルを作成する具体的な手順を示すことです。 レッスンはそれぞれその前のレッスンに基づいているため､全レッスンを順序通りに行ってください｡
  
このチュートリアルでは、Azure Portal でのサーバーの管理、SSMS を使用したサーバーやデータベースの管理、クライアント アプリケーションを使用したモデル データの参照についてのレッスンは提供していません。 


## <a name="lessons"></a>レッスン  
このチュートリアルには､次のレッスンが含まれていません｡  
  
|レッスン|推定所要時間|  
|----------|------------------------------|  
|[1 - 新しい表形式モデル プロジェクトを作成する](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)|10 分|  
|[2 - データを取得する](../tutorials/aas-lesson-2-get-data.md)|10 分|  
|[3 - 日付テーブルとしてマークする](../tutorials/aas-lesson-3-mark-as-date-table.md)|3 分|  
|[4 - リレーションシップを作成する](../tutorials/aas-lesson-4-create-relationships.md)|10 分|  
|[5 - 計算列を作成する](../tutorials/aas-lesson-5-create-calculated-columns.md)|約 15 分|
|[6 - メジャーを作成する](../tutorials/aas-lesson-6-create-measures.md)|30 分|  
|[7 - 主要業績評価指標 (KPI) を作成する](../tutorials/aas-lesson-7-create-key-performance-indicators.md)|約 15 分|  
|[8 - パースペクティブを作成する](../tutorials/aas-lesson-8-create-perspectives.md)|5 分|  
|[9 - 階層を作成する](../tutorials/aas-lesson-9-create-hierarchies.md)|20 分|  
|[10 - パーティションを作成する](../tutorials/aas-lesson-10-create-partitions.md)|約 15 分|  
|[11 - ロールを作成する](../tutorials/aas-lesson-11-create-roles.md)|約 15 分|  
|[12 - Excel で分析する](../tutorials/aas-lesson-12-analyze-in-excel.md)|5 分| 
|[13 - デプロイする](../tutorials/aas-lesson-13-deploy.md)|5 分|  
  
## <a name="supplemental-lessons"></a>補助レッスン  
これらのレッスンは必ずしもチュートリアルに必須のレッスンではありませんが､表形式モデルに対する高度なオーサリング機能の理解を深めるうえで有用です｡  
  
|レッスン|推定所要時間|  
|----------|------------------------------|  
|[詳細列](../tutorials/aas-supplemental-lesson-detail-rows.md)|10 分|
|[動的セキュリティ](../tutorials/aas-supplemental-lesson-dynamic-security.md)|30 分|
|[不規則な階層](../tutorials/aas-supplemental-lesson-ragged-hierarchies.md)|20 分| 

  
## <a name="next-steps"></a>次の手順  
作業を開始するには、「[レッスン 1: 新しい表形式モデル プロジェクトを作成する](../tutorials/aas-lesson-1-create-a-new-tabular-model-project.md)」を参照してください。  
  
  
  

