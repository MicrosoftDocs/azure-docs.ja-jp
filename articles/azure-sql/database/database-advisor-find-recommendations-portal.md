---
title: パフォーマンスに関する推奨事項の適用
description: Azure portal を使用して、データベースのパフォーマンスを最適化できるパフォーマンスの推奨事項を調べます。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 12/19/2018
ms.openlocfilehash: 748ac448ad8bf5c06e5be8b7a4a8b00a9b7af84b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96500888"
---
# <a name="find-and-apply-performance-recommendations"></a>パフォーマンスに関する推奨事項の検索と適用
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure portal を使って、Azure SQL Database でご利用のデータベースのパフォーマンスを最適化できるパフォーマンスの推奨事項を調べたり、ワークロードで確認された問題を修正したりすることができます。 Azure portal の **[パフォーマンスの推奨事項]** ページでは、潜在的な影響に基づいて上位の推奨事項を検索することができます。

## <a name="viewing-recommendations"></a>推奨事項の表示

パフォーマンスの推奨事項を表示して適用するには、Azure の適切な [Azureロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md) アクセス許可が必要です。 推奨事項を表示するには、**リーダー** 権限と **SQL DB の共同作成者** 権限が必要です。また、インデックスの作成または削除、およびインデックス作成のキャンセルなどのアクションを実行するには、**所有者** 権限と **SQL DB の共同作成者** 権限が必要です。

Azure portal でパフォーマンスに関する推奨事項を調べるには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. **[すべてのサービス]**  >  **[SQL Database]** の順に移動し、データベースを選択します。
3. **[パフォーマンスの推奨事項]** に移動し、選択したデータベースで使用できる推奨事項を表示します。

パフォーマンスの推奨事項は、次の図のようなテーブルに表示されます。

![スクリーンショットには、アクションと推奨事項の説明と共に、パフォーマンスに関する推奨事項がテーブルに示されています。](./media/database-advisor-find-recommendations-portal/recommendations.png)

推奨事項は、パフォーマンスへの潜在的な影響ごとに、次のカテゴリの順に表示されます。

| 影響 | 説明 |
|:--- |:--- |
| 高 |高い影響を及ぼすお勧め候補は、パフォーマンスに最も重要な影響を与えます。 |
| Medium |中程度の影響を及ぼすおすすめ候補は、パフォーマンスを向上しますが、それほど著しい向上ではありません。 |
| 低 |低い影響を及ぼすおすすめ候補は、推奨事項を行わない場合よりも優れたパフォーマンスを提供しますが、大きくは改善されない可能性があります。 |

> [!NOTE]
> 何らかの推奨事項を見つけるには、Azure SQL Database は 1 日以上アクティビティを監視する必要があります。 Azure SQL Database は、ランダムでむらのある瞬間的なアクティビティよりも、一貫性のあるアクティビティのクエリ パターンをより簡単に最適化できます。 現時点で提示できる推奨事項がない場合は、 **[パフォーマンスの推奨事項]** ページに、その理由を説明するメッセージが表示されます。

さらに、これまでに行った操作の状態も確認できます。 推奨事項または状態を選択すると、その詳細が表示されます。

Azure portal での "インデックスの作成" に関する推奨事項の例を次に示します。

![インデックスの作成](./media/database-advisor-find-recommendations-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>推奨事項の適用

Azure SQL Database では、次の 3 つのオプションのいずれかを使用して、推奨事項を有効にする方法を完全に制御できます。

* 個々の推奨事項を 1 つずつ適用する。
* 自動チューニングを有効にして推奨事項を自動的に適用する。
* 推奨事項を手作業で実装するため、推奨される T-SQL スクリプトをデータベースに対して実行する。

任意の推奨事項を選択すると、その詳細が表示されます。その後、 **[スクリプトの表示]** をクリックして、推奨事項の作成方法の詳細を確認します。

推奨設定が適用されている間、データベースはオンラインのままになります。パフォーマンスの推奨設定または自動チューニングを使っても、データベースがオフラインになることはありません。

### <a name="apply-an-individual-recommendation"></a>個々の推奨事項を適用する

推奨事項は、1 つずつ確認して受け入れることができます。

1. **[推奨事項]** ページで、推奨事項を選びます。
2. **[詳細]** ページで、 **[適用]** ボタンをクリックします。

   ![Apply recommendation](./media/database-advisor-find-recommendations-portal/apply.png)

選んだ推奨事項がデータベースに適用されます。

### <a name="removing-recommendations-from-the-list"></a>一覧からの推奨事項の削除

推奨事項の一覧に削除したい項目が含まれている場合は、その項目を破棄することができます。

1. **[推奨事項]** の一覧で推奨事項を選び、詳細を開きます。
2. **[詳細]** ページで **[破棄]** をクリックします。

必要な場合は、破棄した項目をもう一度 **[推奨事項]** の一覧に追加できます。

1. **[推奨事項]** ページで **[破棄された内容の表示]** をクリックします。
2. 破棄した項目を一覧から選択し、その詳細を表示します。
3. 必要に応じて、 **[破棄を元に戻す]** をクリックして、インデックスを **[推奨事項]** の主な一覧に戻します。

> [!NOTE]
> SQL Database の[自動チューニング](automatic-tuning-overview.md)が有効で、一覧から推奨事項を手動で破棄した場合、このような推奨事項は自動的に適用されません。 推奨事項の破棄は、特定の推奨事項を適用しない必要がある場合にユーザーが自動チューニングを有効にできる便利な方法です。
> この動作は、[破棄を元に戻す] オプションを選択して、破棄した推奨事項を推奨事項の一覧に再び追加すれば元に戻すことができます。

### <a name="enable-automatic-tuning"></a>自動チューニングの有効化

推奨事項を自動的に実装するようにデータベースを設定できます。 推奨事項は、利用可能になると自動的に適用されます。 サービスによって管理されるすべての推奨事項と同様に、パフォーマンスに悪影響が生じる場合は、推奨事項は元に戻されます。

1. **[推奨事項]** ページで **[自動化]** をクリックします。

   ![Advisor settings](./media/database-advisor-find-recommendations-portal/settings.png)
2. 自動化するアクションを選択します。

   ![自動化するアクションをどこで選択するかを示したスクリーンショット。](./media/database-advisor-find-recommendations-portal/server.png)

> [!NOTE]
> **DROP_INDEX** オプションは現在パーティション切り替えやインデックス ヒントを使用するアプリケーションと互換性がありません。

目的の構成を選択したら、[適用] をクリックします。

### <a name="manually-apply-recommendations-through-t-sql"></a>T-SQL を使い手動で推奨事項を適用する

任意の推奨事項を選択し、 **[スクリプトの表示]** をクリックします。 データベースに対してこのスクリプトを実行し、推奨事項を手動で適用します。

*手動で実行されたインデックスは、パフォーマンスへの影響についてサービスによる監視や検証が行われません。* そのため、作成後にこれらのインデックスを監視して、パフォーマンスが向上するかどうかを検証し、必要に応じて調整または削除することをお勧めします。 インデックスの作成方法の詳細については、「 [CREATE INDEX (TRANSACT-SQL)](/sql/t-sql/statements/create-index-transact-sql)」を参照してください。 さらに、手動で適用した推奨事項は、24 時間から 48 時間、アクティブなままになり、推奨事項の一覧に表示された後、 自動的に取り消されます。 それよりも前に推奨事項を削除したい場合は、手動で破棄できます。

### <a name="canceling-recommendations"></a>推奨事項のキャンセル

状態が **保留中**、**検証中**、または **成功** の推奨事項はキャンセルすることができます。 ただし、 **実行中** の状態の推奨事項はキャンセルできないのでご注意ください。

1. **[チューニングの履歴]** 領域の推奨事項を選択して、 **[推奨事項の詳細]** ページを開きます。
2. **[キャンセル]** をクリックして推奨事項の適用プロセスを中止します。

## <a name="monitoring-operations"></a>操作の監視

推奨事項の適用は、瞬時に行われない場合があります。 ポータルには、推奨事項の状態に関する詳細情報が表示されます。 インデックスは次のいずれかの状態になります。

| Status | 説明 |
|:--- |:--- |
| 保留中 |推奨事項の適用コマンドは受信済みで、実行が予定されています。 |
| 実行中 |推奨事項は適用中です。 |
| Validating |推奨事項が正常に適用され、サービスは効果を測定しています。 |
| Success |推奨事項が正常に適用され、効果が測定されました。 |
| エラー |推奨事項の適用プロセス中にエラーが発生しました。 一時的な問題か、またはテーブルおよびスクリプトへのスキーマの変更が無効になっている可能性があります。 |
| 復元 |推奨事項は適用されましたが、非効率なインデックスと見なされたため、自動的に元に戻されています。 |
| 復元済み |推奨事項は元に戻されました。 |

一覧内で処理中の推奨事項をクリックすると、その詳細が表示されます。

![インプロセスの推奨事項の一覧を示すスクリーンショット。](./media/database-advisor-find-recommendations-portal/operations.png)

### <a name="reverting-a-recommendation"></a>推奨事項を元に戻す

パフォーマンスの推奨事項を使って推奨事項を適用した場合 (つまり、T-SQL スクリプトを手動で実行していない場合)、パフォーマンスへの悪影響が見つかると、変更は自動的に元に戻されます。 何らかの理由で推奨事項を元に戻すには、次の手順を実行します。

1. **[チューニングの履歴]** 領域で、正常に適用された推奨事項を選択します。
2. **[推奨事項の詳細]** ページで **[元に戻す]** をクリックします。

![推奨インデックス](./media/database-advisor-find-recommendations-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>インデックスの推奨によるパフォーマンスへの影響の監視

推奨事項が正常に実装されたら (現時点では、インデックスの操作とクエリのパラメーター化に関する推奨事項のみ)、[推奨事項の詳細] ページの **[クエリの洞察]** をクリックして [Query Performance Insight](query-performance-insight-use.md) を開き、よく使用されるクエリによるパフォーマンスへの影響を確認します。

![Monitor performance impact](./media/database-advisor-find-recommendations-portal/query-insights.png)

## <a name="summary"></a>まとめ

Azure SQL Database では、データベースのパフォーマンス向上のための推奨事項が提供されます。 T-SQL スクリプトが備わっているため、データベースを最適化して最終的にクエリのパフォーマンスを向上させるのに役立ちます。

## <a name="next-steps"></a>次のステップ

推奨事項を監視し、引き続きパフォーマンスの調整対象とします。 データベースのワークロードは動的であり、継続的に変化します。 Azure SQL Database は、お使いのデータベースのパフォーマンスを向上させる可能性がある推奨事項の監視と提供を継続します。

* Azure SQL Database での自動チューニングについて詳しくは、「[Automatic tuning](automatic-tuning-overview.md)」(自動チューニング) をご覧ください。
* Azure SQL Database のパフォーマンスに関する推奨事項の概要については、「[パフォーマンスに関する推奨事項](database-advisor-implement-performance-recommendations.md)」を参照してください。
* よく使用されるクエリによるパフォーマンスへの影響を確認する方法については、[クエリ パフォーマンスの洞察](query-performance-insight-use.md)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース

* [クエリ ストア](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)
* [CREATE INDEX](/sql/t-sql/statements/create-index-transact-sql)
* [Azure ロールベースのアクセス制御 (Azure RBAC)](../../role-based-access-control/overview.md)