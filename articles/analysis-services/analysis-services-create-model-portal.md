---
title: "Azure Analysis Services の Web デザイナーを使用して表形式モデルを作成する | Microsoft Docs"
description: "Azure ポータルで Web デザイナーを使用して Azure Analysis Services の表形式モデルを作成する方法について説明します。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/21/2017
ms.author: owend
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: bd58f1845dabf6afb47ce27236d14479677a8808
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="create-a-model-in-azure-portal"></a>Azure ポータルでモデルを作成する

Azure ポータルの Azure Analysis Services Web デザイナー (プレビュー) 機能は、表形式モデルの作成と編集、およびブラウザーでのモデル データのクエリを迅速かつ簡単に実行する方法を提供します。 

この Web デザイナーは、**プレビュー**段階であることに留意してください。 新しい機能は常に追加されていますが、プレビュー段階で利用できる機能は限られています。 高度なモデルの開発とテストを実行する場合は、Visual Studio (SSDT) と SQL Server Management Studio (SSMS) を使用することが最善の方法です。

## <a name="prerequisites"></a>前提条件

- Standard または Developer レベルの Azure Analysis Services サーバー。 Web デザイナーを使用して作成される新しいモデルは、これらのレベルでのみサポートされる DirectQuery です。
- データソースとしての Azure SQL Database、Azure SQL Data Warehouse、または Power BI Desktop (.pbix) ファイル。 Power BI Desktop ファイルから作成される新しいモデルは、Azure SQL Database、Azure SQL Data Warehouse、Oracle、および Teradata データ ソースをサポートします。
- Azure SQL Database または Azure SQL Data Warehouse データ ソースに接続するための SQL Server アカウントとパスワード。

## <a name="to-create-a-new-tabular-model"></a>新しい表形式モデルを作成するには

1. サーバーの **[概要]**ブレードで **[Web デザイナー]** を選択し、**[開く]** をクリックします。

    ![Azure ポータルでモデルを作成する](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. **[Web デザイナー]** > **[モデル]** を選択し、**[+ 追加]** をクリックします。

    ![Azure ポータルでモデルを作成する](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. **[新しいモデル]** にモデルの名前を入力し、データ ソースを選択します。

    ![Azure ポータルの [新しいモデル] ダイアログ](./media/analysis-services-create-model-portal/aas-create-portal-new-model.png)

4. **[接続]** で、接続プロパティを入力します。 SQL Server アカウントのユーザー名とパスワードを使用する必要があります。

     ![Azure ポータルの [接続] ダイアログ](./media/analysis-services-create-model-portal/aas-create-portal-connect.png)

5. **[テーブルとビュー]** で、モデルに含めるテーブルを選択し、**[作成]** をクリックします。 キー ペアによるテーブル間のリレーションシップが自動的に作成されます。

     ![テーブルとビューを選択する](./media/analysis-services-create-model-portal/aas-create-portal-tables.png)

新しいモデルがブラウザーに表示されます。 ここから、以下を実行できます。   

- クエリ デザイナーにフィールドをドラッグし、フィルターを追加することで、モデル データをクエリします。
- テーブルに新しいメジャーを作成します。
- JSON エディターを使用してモデル メタデータを編集します。
- Visual Studio (SSDT)、Power BI Desktop、または Excel でモデルを開きます。

![テーブルとビューを選択する](./media/analysis-services-create-model-portal/aas-create-portal-query.png)

> [!NOTE]
> ブラウザーでモデルのメタデータを編集するか新しいメジャーを作成するとに、これらの変更が Azure 内のモデルに保存されます。 SSDT、Power BI Desktop、または Excel でもモデルを操作している場合、モデルが同期から外れる可能性があります。


## <a name="next-steps"></a>次のステップ 
[データベース ロールとユーザーの管理](analysis-services-database-users.md)  
[Excel を使用した接続](analysis-services-connect-excel.md)  



