---
title: "パフォーマンスに関する推奨事項の適用 - Azure SQL Database | Microsoft Docs"
description: "Azure Portal を使って、Azure SQL Database のパフォーマンスを最適化できるパフォーマンスの推奨事項を調べたり、ワークロードで見つかった問題を修正したりできます。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 07/05/2017
ms.author: sstein
ms.openlocfilehash: 018afaa8b08bd001e55693390e80c8e2c4f33a30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="find-and-apply-performance-recommendations"></a>パフォーマンスに関する推奨事項の検索と適用

Azure Portal を使って、Azure SQL Database のパフォーマンスを最適化できるパフォーマンスの推奨事項を調べたり、ワークロードで見つかった問題を修正したりできます。 Azure Portal の **[パフォーマンスの推奨事項]** ページでは、潜在的な影響に基づいて上位の推奨事項を検索することができます。 

## <a name="viewing-recommendations"></a>推奨事項の表示

パフォーマンスの推奨事項を表示して適用するには、Azure の適切な[ロールベースのアクセス制御](../active-directory/role-based-access-control-what-is.md)アクセス許可が必要です。 推奨事項を表示するには、**リーダー**権限と **SQL DB の共同作成者**権限が必要です。また、インデックスの作成または削除、およびインデックス作成のキャンセルなどのアクションを実行するには、**所有者**権限と **SQL DB の共同作成者**権限が必要です。

Azure Portal でパフォーマンスに関する推奨事項を調べるには、次の手順のようにします。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[その他のサービス]** > **[SQL Database]** の順に移動し、データベースを選択します。
3. **[パフォーマンスの推奨事項]** に移動し、選択したデータベースで使用できる推奨事項を表示します。

パフォーマンスの推奨事項は、次の図のようなテーブルに表示されます。

![推奨事項](./media/sql-database-advisor-portal/recommendations.png)

推奨事項は、パフォーマンスへの潜在的な影響ごとに、次のカテゴリの順に表示されます。

| 影響 | 説明 |
|:--- |:--- |
| 高 |高い影響を及ぼすお勧め候補は、パフォーマンスに最も重要な影響を与えます。 |
| 中 |中程度の影響を及ぼすおすすめ候補は、パフォーマンスを向上しますが、それほど著しい向上ではありません。 |
| 低 |低い影響を及ぼすおすすめ候補は、推奨事項を行わない場合よりも優れたパフォーマンスを提供しますが、大きくは改善されない可能性があります。 |


> [!NOTE]
> 何らかの推奨事項を見つけるには、Azure SQL Database は 1 日以上アクティビティを監視する必要があります。 Azure SQL Database は、ランダムでむらのある瞬間的なアクティビティよりも、一貫性のあるアクティビティのクエリ パターンをより簡単に最適化できます。 現時点で提示できる推奨事項がない場合は、**[パフォーマンスの推奨事項]** ページに、その理由を説明するメッセージが表示されます。
> 

さらに、これまでに行った操作の状態も確認できます。 推奨インデックスまたは状態を選択すると、その詳細が表示されます。

Azure Portal での "インデックスの作成" に関する推奨事項の例を次に示します。

![インデックスの作成](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>推奨事項の適用
Azure SQL Database では、次の 3 つのオプションのいずれかを使用して、推奨事項を有効にする方法を完全に制御できます。 

* 個々の推奨インデックスを 1 つずつ適用する。
* 自動チューニングを有効にして推奨事項を自動的に適用する。
* 推奨事項を手作業で実装するため、推奨される T-SQL スクリプトをデータベースに対して実行する。

任意の推奨インデックスを選択すると、その詳細が表示されます。その後、**[スクリプトの表示]** をクリックして、推奨事項の作成方法の詳細を確認します。

推奨設定が適用されている間、データベースはオンラインのままになります。パフォーマンスの推奨設定または自動チューニングを使っても、データベースがオフラインになることはありません。

### <a name="apply-an-individual-recommendation"></a>個々の推奨インデックスを適用する
推奨インデックスは、1 つずつ確認して受け入れることができます。

1. **[推奨事項]** ブレードで、推奨事項を選びます。
2. **[詳細]** ブレードで、**[適用]** ボタンをクリックします。
   
    ![Apply recommendation](./media/sql-database-advisor-portal/apply.png)

選んだ推奨事項がデータベースに適用されます。

### <a name="removing-recommendations-from-the-list"></a>一覧からの推奨事項の削除
推奨事項の一覧に削除したい項目が含まれている場合は、その項目を破棄することができます。

1. **[推奨事項]** の一覧で推奨事項を選び、詳細を開きます。
2. **[詳細]** ブレードで **[破棄]** をクリックします。

必要な場合は、破棄した項目をもう一度 **[推奨事項]** の一覧に追加できます。

1. **[推奨事項]** ブレードの **[破棄された内容の表示]** をクリックします。
2. 破棄した項目を一覧から選択し、その詳細を表示します。
3. 必要に応じて、**[破棄を元に戻す]** をクリックして、インデックスを **[推奨事項]** の主な一覧に戻します。


### <a name="enable-automatic-tuning"></a>自動チューニングの有効化
推奨事項を自動的に実装するように Azure SQL Database を設定できます。 推奨インデックスは、利用可能になると自動的に適用されます。 サービスによって管理されるすべての推奨事項と同様に、パフォーマンスに悪影響が生じる場合は、推奨事項は元に戻されます。

1. **[推奨事項]** ブレードで **[自動化]** をクリックします。
   
    ![Advisor settings](./media/sql-database-advisor-portal/settings.png)
2. 自動化するアクションを選択します。
   
    ![推奨インデックス](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>推奨される T-SQL スクリプトを手動で実行する
任意の推奨インデックスを選択し、 **[スクリプトの表示]**をクリックします。 データベースに対してこのスクリプトを実行し、推奨インデックスを手動で適用します。

*手動で実行されたインデックスは、パフォーマンスへの影響についてサービスによる監視や検証が行われません。* そのため、作成後にこれらのインデックスを監視して、パフォーマンスが向上するかどうかを検証し、必要に応じて調整または削除することをお勧めします。 インデックスの作成方法の詳細については、「 [CREATE INDEX (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms188783.aspx)」を参照してください。

### <a name="canceling-recommendations"></a>推奨事項のキャンセル
状態が**保留中**、**確認中**、または**成功**の推奨事項はキャンセルすることができます。 ただし、 **実行中** の状態の推奨事項はキャンセルできないのでご注意ください。

1. **[チューニングの履歴]** 領域の推奨事項を選択して、**[推奨事項の詳細]**ブレードを開きます。
2. **[キャンセル]** をクリックして推奨事項の適用プロセスを中止します。

## <a name="monitoring-operations"></a>操作の監視
推奨インデックスの適用は、瞬時に行われない場合があります。 ポータルには、推奨事項の状態に関する詳細情報が表示されます。 インデックスは次のいずれかの状態になります。

| 状態 | 説明 |
|:--- |:--- |
| Pending |推奨インデックスの適用コマンドは受信済みで、実行が予定されています。 |
| 実行中 |推奨インデックスは適用中です。 |
| 確認中 |推奨事項が正常に適用され、サービスは効果を測定しています。 |
| 成功 |推奨事項が正常に適用され、効果が測定されました。 |
| エラー |推奨インデックスの適用プロセス中にエラーが発生しました。 一時的な問題か、またはテーブルおよびスクリプトへのスキーマの変更が無効になっている可能性があります。 |
| 復元 |推奨インデックスは適用されましたが、非効率なインデックスと見なされたため、自動的に元に戻されています。 |
| 復元済み |推奨インデックスは元に戻されました。 |

一覧内で処理中の推奨インデックスをクリックすると、その詳細が表示されます。

![推奨インデックス](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>推奨インデックスを元に戻す
パフォーマンスの推奨事項を使って推奨事項を適用した場合 (つまり、T-SQL スクリプトを手動で実行していない場合)、パフォーマンスへの悪影響が見つかると、推奨事項は自動的に元に戻されます。 何らかの理由で推奨インデックスを元に戻すには、次の手順を実行します。

1. **[チューニングの履歴]** 領域で、正常に適用された推奨インデックスを選択します。
2. **[推奨事項の詳細]**ブレードで **[元に戻す]** をクリックします。

![推奨インデックス](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>推奨インデックスによるパフォーマンスへの影響の監視
推奨事項が正常に実装されたら (現時点では、インデックスの操作とクエリのパラメーター化に関する推奨事項のみ)、[推奨事項の詳細] ブレードの **[クエリの洞察]** をクリックして [Query Performance Insight](sql-database-query-performance.md) を開き、よく使用されるクエリによるパフォーマンスへの影響を確認します。

![Monitor performance impact](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>概要
Azure SQL Database は、データベースのパフォーマンス向上のための推奨事項を提供します。 T-SQL スクリプトのほか、個別オプションと完全自動オプションが備わっているため、データベースを最適化して最終的にクエリのパフォーマンスを向上させるのに役立ちます。

## <a name="next-steps"></a>次のステップ
推奨事項を監視し、引き続きパフォーマンスの調整対象とします。 データベースのワークロードは動的であり、継続的に変化します。 Azure SQL Database は、継続的に監視を行い、データベースのパフォーマンスが向上する可能性のある推奨事項を提供します。 

* Azure SQL Database での自動チューニングについて詳しくは、「[Automatic tuning](sql-database-automatic-tuning.md)」(自動チューニング) をご覧ください。
* Azure SQL Database のパフォーマンスに関する推奨事項の概要については、「[パフォーマンスに関する推奨事項](sql-database-advisor.md)」をご覧ください。
* よく使用されるクエリによるパフォーマンスへの影響を確認する方法については、[クエリ パフォーマンスの洞察](sql-database-query-performance.md)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [クエリ ストア](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [ロールベースのアクセス制御](../active-directory/role-based-access-control-what-is.md)

