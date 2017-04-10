---
title: "クエリのパフォーマンスに関する推奨事項 - Azure SQL Database | Microsoft Docs"
description: "Azure SQL Database Advisor は、現在のクエリのパフォーマンスを向上できる、既存の SQL Database 向けの推奨事項を提供します。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 1db441ff-58f5-45da-8d38-b54dc2aa6145
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 09/30/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: cf627b92399856af2b9a58ab155fac6730128f85
ms.openlocfilehash: a8d0b08abc7e3c688f9ab79499b3459b33f06848
ms.lasthandoff: 02/02/2017


---
# <a name="sql-database-advisor"></a>SQL Database Advisor

Azure SQL Database は学習してアプリケーションに適応し、カスタマイズされた推奨事項を提供します。それによって、SQL Database のパフォーマンスを最大化できます。 SQL Database Advisor は、インデックスの作成と削除、クエリのパラメーター化、およびスキーマの問題の修正に関する推奨事項を提供します。 SQL Database Advisor は、SQL Database の使用履歴を分析することで、パフォーマンスを評価します。 これにより、データベースの一般的なワークロードを実行する上で最適な推奨事項が提供されます。 

次の推奨事項は Azure SQL Database サーバーを対象としています。 現時点では、インデックスの作成や削除に関する推奨事項が自動的に適用されるよう設定できます。詳細については、[インデックスの自動管理](sql-database-advisor-portal.md#enable-automatic-index-management)に関するセクションをご覧ください。

## <a name="create-index-recommendations"></a>インデックスの作成に関する推奨事項
**インデックスの作成** に関する推奨事項が表示されます。

## <a name="drop-index-recommendations"></a>インデックスの削除に関する推奨事項
**インデックスの削除** に関する推奨事項が表示されます (現時点ではプレビュー段階であり、インデックスの重複にのみ適用されます)。

## <a name="parameterize-queries-recommendations"></a>クエリのパラメーター化に関する推奨事項
継続的に再コンパイルされてはいるもののクエリ実行プランが同じままのクエリが&1; つ以上あると、**クエリのパラメーター化**に関する推奨事項が表示されます。 こういった条件により、強制パラメーター化の適用が可能になり、クエリ プランをキャッシュし再利用して、パフォーマンスの向上とリソースの使用量削減を実現することができます。 

SQL Server に対して発行されたすべてのクエリは、実行プランの生成のために、最初にコンパイルされる必要があります。 生成された各プランはプラン キャッシュに追加され、同じクエリのその後の実行では、キャッシュからプランを再利用できるため、追加のコンパイルの必要がなくなります 。 

パラメーター化されていない値を含むクエリを送信するアプリケーションは、パラメーター値の異なるすべてのクエリの実行プランが再コンパイルされる、パフォーマンス オーバーヘッドにつながる可能性があります。 多くの場合、パラメーター値の異なる同じクエリによって同一の実行プランが生成されますが、これらのプランは個別にプラン キャッシュに追加されます。 実行プランの再コンパイルにより、データベース リソースが消費され、クエリ実行時間の増加とプラン キャッシュのオーバーフローが発生するため、プランがキャッシュから削除される原因となります。 SQL Server のこの動作は、データベースで強制パラメーター化のオプションを設定することにより変更できます。 

推奨事項が与える影響の予測に役立つよう、実際の CPU 使用率、および予測される CPU 使用率 (推奨事項が適用されたと仮定した場合) の比較が提供されます。 CPU 使用率の削減に加えて、クエリ実行時間では、コンパイルにかかる時間が短縮されます。 プラン キャッシュのオーバーヘッドも減少し、ほとんどのプランがキャッシュに保持されて再利用されるようになります。 **[適用]** をクリックして、迅速かつ簡単にこの推奨事項を適用できます。 

推奨事項を適用すると、データベースの強制パラメーター化が速やかに有効になり、約 24 時間続く監視プロセスが開始されます。 監視期間後に、推奨事項が適用される前後 24 時間の、データベースの CPU 使用率を表示した検証レポートを確認できます。 SQL Database Advisor は、パフォーマンスの不具合が検出された場合に、推奨事項の適用を自動的に元に戻す安全メカニズムを備えています。

## <a name="fix-schema-issues-recommendations"></a>スキーマの問題の修正に関する推奨事項
SQL Database サービスが、Azure SQL Database で発生したスキーマ関連の SQL エラー数が異常であることを検出すると、**スキーマの問題の修正**に関する推奨事項が表示されます。 この推奨事項は、通常、データベースでスキーマ関連のエラー (無効な列名、無効なオブジェクト名など) が&1; 時間に複数件発生した場合に表示されます。

「スキーマの問題」は、SQL クエリの定義とデータベース スキーマの定義に不整合があった場合に発生する、SQL Server の構文エラーの種類の&1; つです。 たとえば、クエリで想定される列の&1; つがターゲット テーブルで見つからない、あるいはその逆の場合です。 

Azure SQL Database サービスが、Azure SQL Database で発生したスキーマ関連の SQL エラー数が異常であることを検出すると、「スキーマの問題の修正」に関する推奨事項が表示されます。 下の表は、スキーマの問題に関連したエラーを示しています。

| SQL エラー コード | メッセージ |
| --- | --- |
| 201 |プロシージャまたは関数 '*' にはパラメーター '*' が必要ですが、指定されませんでした。 |
| 207 |列名 '*' が無効です。 |
| 208 |オブジェクト名 '*' が無効です。 |
| 213 |列名または指定された値の数がテーブルの定義と一致しません。 |
| 2812 |ストアド プロシージャ '*' が見つかりませんでした。 |
| 8144 |プロシージャまたは関数 * に指定された引数が多すぎます。 |

## <a name="next-steps"></a>次のステップ
推奨事項を監視し、引き続きパフォーマンスの調整対象とします。 データベースのワークロードは動的であり、継続的に変化します。 SQL Database Advisor では、お使いのデータベースのパフォーマンスを向上させる可能性がある推奨事項の監視と提供を継続します。 

* Azure ポータルで SQL Database Advisor を使用する手順については、 [Azure ポータルの SQL Database Advisor](sql-database-advisor-portal.md) に関する記事を参照してください。
* よく使用されるクエリによるパフォーマンスへの影響を確認する方法については、[クエリ パフォーマンスの洞察](sql-database-query-performance.md)に関する記事をご覧ください。

## <a name="additional-resources"></a>その他のリソース
* [クエリ ストア](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [ロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)


