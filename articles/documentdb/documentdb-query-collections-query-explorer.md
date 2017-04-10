---
title: "Azure DocumentDB ポータル ツール: クエリ エクスプローラー | Microsoft Docs"
description: "DocumentDB クエリ エクスプローラーについて説明します。これは、SQL クエリを作成して NoSQL DocumentDB コレクションに対して実行するための、Azure ポータルの SQL クエリ エディターです。"
keywords: "SQL クエリの記述, SQL クエリ エディター"
services: documentdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: ac378240-b11f-4522-ae9f-09da3a6f9c16
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: fba82c5c826da7d1912814b61c5065ca7f726011
ms.openlocfilehash: c4c2cd8ddcb541b2619f7e1b140222899f15f87c
ms.lasthandoff: 02/23/2017


---
# <a name="write-edit-and-run-sql-queries-for-documentdb-using-query-explorer-in-the-azure-portal"></a>Azure Portal でクエリ エクスプローラーを使用して DocumentDB に対する SQL クエリを作成、編集、実行する
この記事では、[Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) クエリ エクスプローラーの概要について説明します。これは、[DocumentDB コレクション](documentdb-create-collection.md)に対する SQL クエリを作成、編集、実行できる Azure Portal のツールです。

1. [Azure Portal](https://portal.azure.com) の左側のナビゲーションで、![Azure DocumentDB アイコン](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **[NoSQL (DocumentDB)]** をクリックします。 

    **[NoSQL (DocumentDB)]** が表示されない場合は、下にある **[その他のサービス]** をクリックしてから、![Azure DocumentDB アイコン](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png) **[NoSQL (DocumentDB)]** をクリックします。
2. リソース メニューの **[クエリ エクスプローラー]**をクリックします。 
   
    ![Screenshot of the Azure portal with Query Explorer highlighted](./media/documentdb-query-collections-query-explorer/queryexplorercommand.png)
3. **[クエリ エクスプローラー]** ブレードで、ドロップダウン リストから、クエリの対象となる**データベース**と**コレクション**を選択し、実行するクエリを入力します。 
   
    **[データベース]** と **[コレクション]** の各ドロップダウン リストには、クエリ エクスプローラーを起動したコンテキストに応じて値が設定されています。 
   
    既定のクエリ `SELECT TOP 100 * FROM c` が表示されています。  既定のクエリをそのまま使用することも、[SQL クエリのチート シート](documentdb-sql-query-cheat-sheet.md)または [SQL クエリと SQL 構文](documentdb-sql-query.md)に関するページで説明されている SQL クエリ言語を使用して独自のクエリを作成することもできます。
   
    **[クエリの実行]** をクリックして、結果を表示します。
   
    ![Screenshot of writing SQL queries in Query Explorer, a SQL query editor](./media/documentdb-query-collections-query-explorer/queryexplorerinitial.png)
4. **[結果]** ブレードには、クエリの出力が表示されます。 
   
    ![Screenshot of results of writing SQL queries in Query Explorer](./media/documentdb-query-collections-query-explorer/queryresults1.png)

## <a name="work-with-results"></a>結果を操作する
既定では、クエリ エクスプローラーは 100 個ずつ結果を返します。  クエリの結果が 100 個を超える場合は、**[次のページ]** と **[前のページ]** のコマンドを使用して結果セット間を移動できます。

![クエリ エクスプローラーの改ページ位置の自動調整のサポート](./media/documentdb-query-collections-query-explorer/queryresultspagination.png)

クエリが成功すると、**[情報]** ウィンドウに、要求の課金、クエリによって生じるラウンド トリップの数、現在表示されている結果のセット、より多くの結果があるかどうか、などのメトリックが表示されます。これらには、先ほど説明したように、**[次のページ]** コマンドを使用してアクセスすることができます。

![クエリ エクスプローラーのクエリ情報のスクリーンショット](./media/documentdb-query-collections-query-explorer/queryinformation.png)

## <a name="use-multiple-queries"></a>複数のクエリを使用する
複数のクエリを使用していて、それらをすばやく切り替えたい場合は、**[クエリ エクスプローラー]** ブレードのクエリ テキスト ボックスにすべてのクエリを入力します。その後、実行するクエリを強調表示して **[クエリの実行]** をクリックすると、結果が表示されます。

![Screenshot of writing multiple SQL queries in Query Explorer (a SQL query editor) and highlighting and running individual queries](./media/documentdb-query-collections-query-explorer/queryexplorerhighlightandrun.png)

## <a name="add-queries-from-a-file-into-the-sql-query-editor"></a>ファイルから SQL クエリ エディターにクエリを追加する
**[ファイルの読み込み]** コマンドを使用して、既存のファイルの内容を読み込むことができます。

![Screenshot showing how to load SQL queries from a file into Query Explorer using Load File](./media/documentdb-query-collections-query-explorer/loadqueryfile.png)

## <a name="troubleshoot"></a>トラブルシューティング
クエリがエラーになった場合には、トラブルシューティングに役立つようなエラーの一覧がクエリ エクスプローラーに表示されます。

![クエリ エクスプローラーのクエリ エラーのスクリーンショット](./media/documentdb-query-collections-query-explorer/queryerror.png)

## <a name="run-documentdb-sql-queries-outside-the-portal"></a>ポータル以外で DocumentDB SQL クエリを実行する
Azure ポータルのクエリ エクスプローラーは、DocumentDB に対して SQL クエリを実行するための&1; つの手段にすぎません。 [REST API](https://msdn.microsoft.com/library/azure/dn781481.aspx) または[クライアント SDK](documentdb-sdk-dotnet.md) を使用して SQL クエリを実行することもできます。 他の方法の詳細については、「 [SQL クエリの実行](documentdb-sql-query.md#ExecutingSqlQueries)

## <a name="next-steps"></a>次のステップ
クエリ エクスプローラーでサポートされている DocumentDB SQL 文法の詳細については、[SQL クエリと SQL 構文](documentdb-sql-query.md)に関するページを参照するか、[SQL クエリのチート シート](documentdb-sql-query-cheat-sheet.md)を印刷してください。
また、[Query Playground](https://www.documentdb.com/sql/demo) で試してみることもできます。ここでは、サンプル データセットを使用して、オンラインでクエリをテストすることができます。


