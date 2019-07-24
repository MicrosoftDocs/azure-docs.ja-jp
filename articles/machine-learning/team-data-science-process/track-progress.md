---
title: データ サイエンス プロジェクトの進行状況の追跡 - Team Data Science Process
description: データ サイエンス グループのマネージャー、チーム リーダー、プロジェクト リードが、データ サイエンス プロジェクトの進行状況を追跡できる方法です。
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7745a53084f4477f7b736ea9d130ffd3eed771f4
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55451990"
---
# <a name="tracking-the-progress-of-data-science-projects"></a>データ サイエンス プロジェクトの進行状況の追跡

データ サイエンス グループ マネージャー、チーム リーダー、プロジェクト リーダーは、プロジェクトの進行状況、プロジェクトに対して実行した作業とその実行者、To-do リストに残っている項目を追跡する必要があります。 

## <a name="azure-devops-dashboards"></a>Azure DevOps ダッシュ ボード
Azure DevOps を使用している場合は、アクティビティおよび指定されたアジャイル プロジェクトに関連付けられている作業項目を追跡するためにダッシュ ボードを構築することができます。 

Azure DevOps でダッシュ ボードとウィジェットを作成およびカスタマイズする方法の詳細については、次の一連の手順を参照してください。

- [ダッシュボードを追加および管理する](https://docs.microsoft.com/azure/devops/report/dashboards/dashboards)
- [ダッシュ ボードにウィジェットを追加する](https://docs.microsoft.com/azure/devops/report/dashboards/add-widget-to-dashboard)

## <a name="example-dashboard"></a>サンプルのダッシュ ボード

アジャイル データ サイエンス プロジェクトのスプリント アクティビティと、関連付けられているリポジトリへのコミット数を追跡するために構築された、簡単なサンプル ダッシュ ボードを次に示します。 **左上**にあるパネルは、次の内容を示しています。

- 現在のスプリントのカウントダウン 
- 過去 7 日間の各リポジトリのコミット数
- 特定のユーザーの作業項目 

それ以外のパネルはそれぞれ、プロジェクトの累積フロー ダイアグラム (CFD)、バーンダウン、およびバーンアップを表示します。

- **左下**:CFD は指定された状態の作業量を示します。承認済みは灰色、コミット済みは青色、完了済みは緑色で表されます。
- **右上**: 未完了の作業と残り時間を示したバーンダウン チャートです。
- **右下**: 完了済みの作業と総作業量を示したバーンアップ チャートです。

![dashboard](./media/track-progress/dashboard.png)

これらのグラフの作成方法については、[ダッシュ ボード](https://docs.microsoft.com/azure/devops/report/dashboards/)のクイックスタートおよびチュートリアルを参照してください。
 
## <a name="next-steps"></a>次の手順

また、**特定のシナリオ**のプロセスにおけるすべての手順を説明するチュートリアルも用意されています。 これらは、[サンプル チュートリアル](walkthroughs.md)の記事で簡単な説明と共にリンク付きで紹介されています。 チュートリアルでは、クラウド、オンプレミスのツール、サービスをワークフローまたはパイプラインに組み込んでインテリジェント アプリケーションを作成する方法を説明しています。 
