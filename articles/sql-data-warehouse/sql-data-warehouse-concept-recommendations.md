---
title: SQL Data Warehouse のレコメンデーション - 概念 | Microsoft Docs
description: SQL Data Warehouse のレコメンデーションとそれらがどのように生成されるかについて説明します
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b275f23209979e1a8068ecd99465f7b52392bc6c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58082910"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL Data Warehouse のレコメンデーション

この記事では、Azure Advisor を通じて SQL Data Warehouse によって提供されるレコメンデーションについて説明します。  

SQL Data Warehouse は、お使いのデータ ウェアハウスのパフォーマンスが終始最適化されるように、レコメンデーションを提供します。 データ ウェアハウスのレコメンデーションは [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) と強固に統合され、[Azure portal](https://aka.ms/Azureadvisor) 内でベスト プラクティスを直接提供します。 SQL Data Warehouse はお使いのデータ ウェアハウスの現在の状態を分析し、利用統計情報を収集して、アクティブなワークロードに対するレコメンデーションを毎日提供します。 以下にサポートされるデータ ウェアハウスのレコメンデーションのシナリオの概要と、推奨されるアクションを適用する方法を示します。

SQL Data Warehouse Advisor についてフィードバックがある、または問題が発生した場合は、[sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com) にお問い合わせください。   

今すぐ[こちら](https://aka.ms/Azureadvisor)をクリックしてレコメンデーションを確認してください。 現在、この機能は Gen2 データ ウェアハウスにのみ適用できます。 

## <a name="data-skew"></a>データ スキュー

データ スキューは、ワークロードの実行時に余分なデータ移動やリソースのボトルネックを引き起こす可能性があります。 次のドキュメントでは、データ スキューを特定し、最適な配布キーを選択することでその発生を回避する方法について説明します。

- [スキューを特定して削除する](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>統計情報がない、または統計情報が古い

最適でない統計情報は、SQL Data Warehouse のクエリ オプティマイザーが最適でないクエリ計画を作成する原因となるため、クエリ パフォーマンスに深刻な影響を及ぼす可能性があります。 次のドキュメントでは、統計情報の作成や更新に関するベスト プラクティスについて説明します。

- [テーブル統計情報の作成と更新](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

これらのレコメンデーションによって影響を受けるテーブルの一覧を表示するには、次の [T-SQL スクリプト](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)を実行します。 Advisor は、同じ T-SQL スクリプトを継続的に実行してこれらのレコメンデーションを生成します。

## <a name="replicate-tables"></a>テーブルのレプリケート

レプリケート テーブルのレコメンデーションについて、Advisor は、以下の物理的特性に基づいてテーブルの候補を検出します。

- レプリケート テーブルのサイズ
- 列数の合計
- テーブルの分散の種類
- パーティションの数

Advisor は、テーブル アクセスの頻度、返された平均行数、データ ウェアハウスのサイズとアクティビティに関連するしきい値などのワークロード ベースのヒューリスティックを継続的に活用し、品質の高いレコメンデーションが生成されるようにしています。 

以下は、レプリケート テーブルの各レコメンデーションについて Azure portal に示されることがある、ワークロード ベースのヒューリスティックについての説明です。

- [Scan avg]\(スキャン平均値) - 各テーブル アクセスでテーブルから返された行数の割合の、過去 7 日間にわたる平均値
- Frequent read, no update(頻繁な読み取り、更新なし - アクセス アクティビティを示しているが過去 7 日間にテーブルは更新されていないことを示します
- [Read/update ratio]\(読み取り/更新の比率) - 過去 7 日間にわたる、テーブルがアクセスされた頻度とテーブルが更新された回数との比率
- [Activity]\(アクティビティ) - アクセスのアクティビティに基づいて、使用状況を測定します。 これは、テーブル アクセス アクティビティを、過去 7 日間の、データ ウェアハウス間にわたる平均テーブル アクセス アクティビティと比較します。 

現在、Advisor に一度に表示されるのは、最上位のアクティビティを優先順位付けするクラスター化列ストア インデックスを含め、最大 4 つのレプリケート テーブルの候補のみです。

> [!IMPORTANT]
> レプリケート テーブルのレコメンデーションは、完全に試験済みの状態ではなく、アカウント データの移動操作は考慮されていません。 これをヒューリスティックとして追加することには取り組んでいますが、それまでは、レコメンデーションの適用後、実際のワークロードを必ず検証する必要があります。 ワークロードの状況を悪化させるレプリケート テーブルのレコメンデーションが見つかった場合は、sqldwadvisor@service.microsoft.com までご連絡ください。 レプリケート テーブルに関する詳細については、次の[ドキュメント](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table)を参照してください。
