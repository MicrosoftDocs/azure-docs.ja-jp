---
title: "Azure Portal を使用した Azure SQL Database Advisor | Microsoft Docs"
description: "Azure SQL Database Advisor を Azure ポータルで使用して、現在のクエリのパフォーマンスを向上できる、既存の SQL Database 向けの推奨事項の確認および実装ができます。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: cda8a646-0584-4368-b28a-85cdd9b54fcd
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: f63bd47c15439e6fedc0f6edf579681697155592
ms.lasthandoff: 02/16/2017


---
# <a name="sql-database-advisor-using-the-azure-portal"></a>Azure Portal を使用した SQL Database Advisor

Azure SQL Database Advisor を Azure ポータルで使用して、現在のクエリのパフォーマンスを向上できる、既存の SQL Database 向けの推奨事項の確認および実装ができます。

## <a name="viewing-recommendations"></a>推奨事項の表示
[推奨事項] ページには、パフォーマンスの向上につながる可能性が高い順に推奨事項が表示されます。 さらに、これまでに行った操作の状態も確認できます。 推奨インデックスまたは状態を選択すると、その詳細が表示されます。

推奨事項を表示し適用するには、Azure の適切な[ロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)権限が必要です。 推奨事項を表示するには、**リーダー**権限と **SQL DB の共同作成者**権限が必要です。また、インデックスの作成または削除、およびインデックス作成のキャンセルなどのアクションを実行するには、**所有者**権限と **SQL DB の共同作成者**権限が必要です。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[その他のサービス]** > **[SQL Database]** の順にクリックし、データベースを選択します。
3. **[パフォーマンスの推奨事項]**を順にクリックし、選択したデータベースで使用できる推奨事項を表示します。

> [!NOTE]
> 推奨事項を得るには、データベースを約&1; 日使用し、いくつかのアクティビティを行う必要があります。 さらに、一貫性のアクティビティも行う必要もあります。 SQL Database Advisor は、ランダムでむらのある瞬間的なアクティビティよりも、一貫性のあるアクティビティのクエリ パターンをより簡単に最適化できます。 推奨事項を使用できない場合は、**[パフォーマンスの推奨事項]** ページに、その理由を説明するメッセージが表示されます。
> 
> 

![推奨事項](./media/sql-database-advisor-portal/recommendations.png)

Azure ポータルの、「スキーマの問題の修正」に関する推奨事項の例を次に示します。

![スキーマの問題の修正](./media/sql-database-advisor-portal/sql-database-advisor-schema-issue.png)

お勧め候補は、パフォーマンスへの潜在的な影響ごとに、次の&4; つのカテゴリに並べ替えられます。

| 影響 | 説明 |
|:--- |:--- |
| 高 |高い影響を及ぼすお勧め候補は、パフォーマンスに最も重要な影響を与えます。 |
| 中 |中程度の影響を及ぼすおすすめ候補は、パフォーマンスを向上しますが、それほど著しい向上ではありません。 |
| 低 |低い影響を及ぼすおすすめ候補は、推奨事項を行わない場合よりも優れたパフォーマンスを提供しますが、大きくは改善されない可能性があります。 |

### <a name="removing-recommendations-from-the-list"></a>一覧からの推奨事項の削除
推奨事項の一覧に削除したい項目が含まれている場合は、その項目を破棄することができます。

1. **[推奨事項]**の一覧にある項目を選択します。
2. **[詳細]** ブレードで **[破棄]** をクリックします。

必要な場合は、破棄した項目をもう一度 **[推奨事項]** の一覧に追加できます。

1. **[推奨事項]** ブレードの **[破棄された内容の表示]** をクリックします。
2. 破棄した項目を一覧から選択し、その詳細を表示します。
3. 必要に応じて、**[破棄を元に戻す]** をクリックして、インデックスを **[推奨事項]** の主な一覧に戻します。

## <a name="applying-recommendations"></a>推奨事項の適用
SQL Database Advisor では、次の&3; つのオプションのいずれかを使用して、推奨事項を有効にする方法を完全に制御できます。 

* 個々の推奨インデックスを&1; つずつ適用する。
* アドバイザーが推奨事項を自動的に適用できるようにします (現時点では、インデックスに関する推奨事項にのみ適用されます)。
* 推奨事項を手作業で実装するには、推奨される T-SQL スクリプトをデータベースに対して実行します。

任意の推奨インデックスを選択すると、その詳細が表示されます。その後、**[スクリプトの表示]** をクリックして、推奨事項の作成方法の詳細を確認します。

アドバイザーによって推奨事項が適用される間、データベースはオンラインのままです。つまり、SQL Database Advisor を使用することで、データベースがオフラインになることはありません。

### <a name="apply-an-individual-recommendation"></a>個々の推奨インデックスを適用する
推奨インデックスは、1 つずつ確認して受け入れることができます。

1. **[推奨事項]** ブレードで、推奨インデックスをクリックします。
2. **[詳細]** ブレードで、**[適用]** をクリックします。
   
    ![Apply recommendation](./media/sql-database-advisor-portal/apply.png)

### <a name="enable-automatic-index-management"></a>インデックスの自動管理を有効にする
推奨事項を自動的に実装するように SQL Database Advisor を設定できます。 推奨インデックスは、利用可能になると自動的に適用されます。 サービスによって管理されるすべてのインデックス操作と同様に、パフォーマンスに悪影響が生じる場合は、推奨インデックスは元に戻されます。

1. **[推奨事項]** ブレードで **[自動化]** をクリックします。
   
    ![Advisor settings](./media/sql-database-advisor-portal/settings.png)
2. インデックスを自動的に**作成**または**削除**するようにアドバイザーを設定します。
   
    ![推奨インデックス](./media/sql-database-advisor-portal/automation.png)

### <a name="manually-run-the-recommended-t-sql-script"></a>推奨される T-SQL スクリプトを手動で実行する
任意の推奨インデックスを選択し、 **[スクリプトの表示]**をクリックします。 データベースに対してこのスクリプトを実行し、推奨インデックスを手動で適用します。

*手動で実行されたインデックスは、パフォーマンスへの影響についてサービスによる監視や検証が行われません。* そのため、作成後にこれらのインデックスを監視して、パフォーマンスが向上するかどうかを検証し、必要に応じて調整または削除することをお勧めします。 インデックスの作成方法の詳細については、「 [CREATE INDEX (TRANSACT-SQL)](https://msdn.microsoft.com/library/ms188783.aspx)」を参照してください。

### <a name="canceling-recommendations"></a>推奨事項のキャンセル
状態が**保留中**、**確認中**、または**成功**の推奨事項はキャンセルすることができます。 ただし、 **実行中** の状態の推奨事項はキャンセルできないのでご注意ください。

1. **[チューニングの履歴]** 領域の推奨事項を選択して、**[推奨事項の詳細]**ブレードを開きます。
2. **[キャンセル]** をクリックして推奨事項の適用プロセスを中止します。

## <a name="monitoring-operations"></a>操作の監視
推奨インデックスの適用は、瞬時に行われない場合があります。 ポータルには、推奨インデックスの操作の状態に関する詳細情報が表示されます。 インデックスは次のいずれかの状態になります。

| 状態 | 説明 |
|:--- |:--- |
| Pending |推奨インデックスの適用コマンドは受信済みで、実行が予定されています。 |
| 実行中 |推奨インデックスは適用中です。 |
| 成功 |推奨インデックスが正常に適用されました。 |
| エラー |推奨インデックスの適用プロセス中にエラーが発生しました。 一時的な問題か、またはテーブルおよびスクリプトへのスキーマの変更が無効になっている可能性があります。 |
| 復元 |推奨インデックスは適用されましたが、非効率なインデックスと見なされたため、自動的に元に戻されています。 |
| 復元済み |推奨インデックスは元に戻されました。 |

一覧内で処理中の推奨インデックスをクリックすると、その詳細が表示されます。

![推奨インデックス](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>推奨インデックスを元に戻す
アドバイザーを使用して推奨インデックスを適用した場合 (つまり、T-SQL スクリプトを手動で実行していない場合)、パフォーマンスへの悪影響が見つかるとインデックスは自動的に元に戻されます。 何らかの理由で推奨インデックスを元に戻すには、次の手順を実行します。

1. **[チューニングの履歴]** 領域で、正常に適用された推奨インデックスを選択します。
2. **[推奨事項の詳細]**ブレードで **[元に戻す]** をクリックします。

![推奨インデックス](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>推奨インデックスによるパフォーマンスへの影響の監視
推奨事項が正常に実装されたら (現時点では、インデックスの操作とクエリのパラメーター化に関する推奨事項のみ)、[推奨事項の詳細] ブレードの **[クエリの洞察]** をクリックして [Query Performance Insight](sql-database-query-performance.md) を開き、よく使用されるクエリによるパフォーマンスへの影響を確認します。

![Monitor performance impact](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>概要
SQL Database Advisor は、データベースのパフォーマンス向上のための推奨事項を提供します。 SQL Database Advisor には、T-SQL スクリプトのほか、個別管理オプションと完全自動管理オプション (現時点ではインデックスのみ) が備わっているため、データベースを最適化して最終的にクエリ パフォーマンスを向上させるのに役立ちます。

## <a name="next-steps"></a>次のステップ
推奨事項を監視し、引き続きパフォーマンスの調整対象とします。 データベースのワークロードは動的であり、継続的に変化します。 SQL Database Advisor では、お使いのデータベースのパフォーマンスを向上させる可能性がある推奨事項の監視と提供を継続します。 

* SQL Database Advisor の概要については、「 [SQL Database Advisor](sql-database-advisor.md) 」 を参照してください。
* よく使用されるクエリによるパフォーマンスへの影響を確認する方法については、[クエリ パフォーマンスの洞察](sql-database-query-performance.md)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [クエリ ストア](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [ロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)


