---
title: "Stretch Database のデータベースとテーブルを特定する - Azure | Microsoft Docs"
description: "Stretch Database の候補となるデータベースとテーブルを特定する方法について説明します。"
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 9253c5a2-f135-4782-95ec-8ff39454c2c0
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: bcb0a66425439522e0c9a353798ac70505b91e39
ms.openlocfilehash: 0f1a7feea79c73d7b80343fcc9898509268d111c


---
# <a name="identify-databases-and-tables-for-stretch-database-by-running-stretch-database-advisor"></a>Stretch Database Advisor を実行して Stretch Database のデータベースとテーブルを特定する
Stretch Database の候補となるデータベースとテーブルを特定するには、SQL Server 2016 Upgrade Advisor をダウンロードし、Stretch Database Advisor を実行します。 Stretch Database Advisor はブロック問題も特定します。

## <a name="download-and-install-upgrade-advisor"></a>Upgrade Advisor のダウンロードとインストール
Upgrade Advisor を [ここ](http://go.microsoft.com/fwlink/?LinkID=613421)からダウンロードし、インストールします。 このツールは、SQL Server インストール メディアには含まれていません。

## <a name="run-the-stretch-database-advisor"></a>Stretch Database Advisor の実行
1. Upgrade Advisor を実行します。
2. **[シナリオ]** を選択してから、**[STRETCH DATABASE ADVISOR の実行]** を選択します。
3. **[Stretch Database Advisor の実行]** ブレードで、**[分析するデータベースの選択]** をクリック します。
4. **[データベースの選択]** ブレードで、サーバー名と認証情報を入力または選択します。 **[接続]**をクリックします。
5. 選択したサーバー上のデータベースの一覧が表示されます。 分析するデータベースを選択します。 **[選択]**をクリックします。
6. **[Stretch Database Advisor の実行]** ブレードで、**[実行]** をクリック します。  分析が実行されます。

## <a name="review-the-results"></a>結果の確認
1. 分析が完了したら、**[分析済みデータベース]** ブレードで、分析したデータベースのいずれかを選択し、**[分析結果]** ブレードを表示します。

   **[分析結果]** ブレードには、既定の推奨基準に適合する、選択されたデータベースの推奨テーブルが一覧表示されます。
2. **[分析結果]** ブレード上のテーブルの一覧で、推奨テーブルのいずれかを選択し、**[テーブル結果]** ブレードを表示します。

   ブロック問題が存在する場合は、選択されたテーブルのブロック問題が **[テーブル結果]** ブレードに一覧表示されます。 Stretch Database Advisor によって検出されるブロック問題については、「 [Stretch Database の制限事項](sql-server-stretch-database-limitations.md)」をご覧ください。
3. **[テーブル結果]** ブレード上のブロックの問題の一覧でいずれかの問題を選択すると、その詳細な情報が表示されて、軽減手順が提案されます。 選択したテーブルを Stretch Database 用に構成する場合は、推奨される軽減手順を実行します。

## <a name="next-step"></a>次のステップ
Stretch Database を有効にします。

* **データベース**の Stretch Database を有効にするには、「 [データベースの Stretch Database を有効にする](sql-server-stretch-database-enable-database.md)」をご覧ください。
* Stretch が データベースで既に有効な場合に、別の **テーブル**の Stretch Database を有効にするには、「 [テーブルの Stretch Database を有効にする](sql-server-stretch-database-enable-table.md)」をご覧ください。

## <a name="see-also"></a>関連項目
[Stretch Database の制限事項](sql-server-stretch-database-limitations.md)

[データベースの Stretch Database を有効にする](sql-server-stretch-database-enable-database.md)

[テーブルの Stretch Database を有効にする](sql-server-stretch-database-enable-table.md)



<!--HONumber=Jan17_HO4-->


