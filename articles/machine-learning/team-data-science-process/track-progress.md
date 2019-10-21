---
title: Team Data Science Process プロジェクトの進行状況の追跡
description: データ サイエンス グループのマネージャー、チーム リーダー、プロジェクト リーダーが、データ サイエンス プロジェクトの進行状況を追跡できる方法です。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/26/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 28bd3e558294c000ba65a1c60fe227bbae7e82dd
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244081"
---
# <a name="track-the-progress-of-data-science-projects"></a>データ サイエンス プロジェクトの進行状況の追跡

データ サイエンス グループ マネージャー、チーム リーダー、プロジェクト リーダーは、行われた作業、作業の担当者、残っている作業など、プロジェクトの進捗状況を追跡できます。 

## <a name="azure-devops-dashboards"></a>Azure DevOps ダッシュ ボード

Azure DevOps を使用している場合は、アクティビティおよび指定されたアジャイル プロジェクトに関連付けられている作業項目を追跡するためにダッシュボードを構築することができます。 ダッシュボードの詳細については、「[ダッシュボード、レポート、ウィジェット](/azure/devops/report/dashboards/)」を参照してください。

Azure DevOps でダッシュボードとウィジェットの作成方法とカスタマイズ方法の手順については、次のクイックスタートを参照してください。

- [ダッシュボードを追加および管理する](/azure/devops/report/dashboards/dashboards)
- [ダッシュボードにウィジェットを追加する](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>サンプルのダッシュ ボード

アジャイル データ サイエンス プロジェクトのスプリント アクティビティ (関連付けられているリポジトリへのコミット数を含む) を追跡するために構築された、簡単なサンプル ダッシュボードを次に示します。 

- **カウントダウン** タイルには、現在のスプリントに保持される日数が表示されます。 

- 2 つの**コード タイル**には、過去 7 日間の 2 つのプロジェクト リポジトリのコミット数が表示されます。 

- **TDSP Customer Project の作業項目**には、すべての作業項目のクエリの結果とその状態が表示されます。 

- **累積フローダイアグラム** (CFD) には、終了した作業項目とアクティブな作業項目の数が表示されます。

- **バーンダウン チャート**には、スプリントの残りの時間に対してまだ完了しているない作業が示されます。

- **バーンアップ チャート**には、スプリントの作業量の合計と比較して、完了した作業が示されます。

![ダッシュボード](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>次の手順

「[Team Data Science Process を実行するチュートリアル](walkthroughs.md)」には、特定のシナリオのプロセスのすべての手順を示すチュートリアルと、リンクとサムネイルの説明が記載されています。 これらのリンクされたシナリオでは、クラウドとオンプレミスのツールおよびサービスをワークフローまたはパイプラインに組み込んで、インテリジェントなアプリケーションを作成する方法について説明します。 
