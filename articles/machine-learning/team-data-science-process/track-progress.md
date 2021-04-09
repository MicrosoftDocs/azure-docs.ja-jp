---
title: Team Data Science Process プロジェクトの進行状況の追跡
description: データ サイエンス グループのマネージャー、チーム リーダー、プロジェクト リーダーが、データ サイエンス プロジェクトの進行状況を追跡できる方法です。
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 78a543fabadcc0d4e1766af1bc5c65aac0dadebe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91358927"
---
# <a name="track-the-progress-of-data-science-projects"></a>データ サイエンス プロジェクトの進行状況の追跡

データ サイエンス グループのマネージャー、チーム リーダー、プロジェクト リーダーがプロジェクトの進行状況を追跡できます。  マネージャーは、行われた作業、作業の担当者、残っている作業を知る必要があります。   予測の管理は、成功する上での重要な要素です。

## <a name="azure-devops-dashboards"></a>Azure DevOps ダッシュ ボード

Azure DevOps を使用している場合は、アクティビティおよび指定されたアジャイル プロジェクトに関連付けられている作業項目を追跡するためにダッシュボードを構築することができます。 ダッシュボードの詳細については、「[ダッシュボード、レポート、ウィジェット](/azure/devops/report/dashboards/)」を参照してください。

Azure DevOps でダッシュボードとウィジェットの作成方法とカスタマイズ方法の手順については、次のクイックスタートを参照してください。

- [ダッシュボードを追加および管理する](/azure/devops/report/dashboards/dashboards)
- [ダッシュボードにウィジェットを追加する](/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>サンプルのダッシュ ボード

アジャイル データ サイエンス プロジェクトのスプリント アクティビティ (関連付けられているリポジトリへのコミット数を含む) を追跡するために構築された、簡単なサンプル ダッシュボードを次に示します。 

- **カウントダウン** タイルには、現在のスプリントに保持される日数が表示されます。 

- 2 つの **コード タイル** には、過去 7 日間の 2 つのプロジェクト リポジトリのコミット数が表示されます。 

- **TDSP Customer Project の作業項目** には、すべての作業項目のクエリの結果とその状態が表示されます。 

- **累積フローダイアグラム** (CFD) には、終了した作業項目とアクティブな作業項目の数が表示されます。

- **バーンダウン チャート** には、スプリントの残りの時間に対してまだ完了しているない作業が示されます。

- **バーンアップ チャート** には、スプリントの作業量の合計と比較して、完了した作業が示されます。

![Azure DevOps ダッシュボードの例を示すスクリーンショット。](./media/track-progress/dashboard.png)

## <a name="next-steps"></a>次のステップ

「[Team Data Science Process を実行するチュートリアル](walkthroughs.md)」では、プロセスのすべての手順を示すチュートリアルを一覧に示しています。 リンクされたシナリオでは、クラウドとオンプレミスのリソースをインテリジェントなアプリケーションに管理する方法を説明します。 
