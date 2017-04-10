---
title: "Azure Portal: SQL Database の動的データ マスク | Microsoft Docs"
description: "Azure Portal での SQL Database 動的データ マスクの使用方法"
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: 
ms.assetid: 2
ms.service: sql-database
ms.custom: secure and protect
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/22/2016
ms.author: ronitr; ronmat
translationtype: Human Translation
ms.sourcegitcommit: 07913359addaa49fe8d408a8e363f554419e4de2
ms.openlocfilehash: 16db41c83ca5c582aca7581ae9b6a03a3d99ab15
ms.lasthandoff: 02/16/2017


---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure Portal で SQL Database 動的データ マスクを使用する

このトピックでは、Azure Portal で[動的データ マスク](sql-database-dynamic-data-masking-get-started.md)を実装する方法を示します。 [Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)または [REST API](https://msdn.microsoft.com/library/dn505719.aspx) を使って動的データ マスクを実装することもできます。


## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Azure ポータル使用によるデータベースの動的データ マスク設定
1. [https://portal.azure.com](https://portal.azure.com)で Azure ポータルを起動します。
2. マスクするデリケートなデータを含むデータベースの設定ブレードに移動します。
3. **[動的データ マスク]** タイルをクリックして、**[動的データ マスク]** 構成ブレードを起動します。
   
   * この方法に代わって、下にスクロールして **[操作]** セクションを表示し、**[動的データ マスク]** をクリックすることもできます。
     
     ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)<br/><br/>
4. **動的データ マスク**構成ブレードには、推奨エンジンがマスク対象として推奨したデータベース列がいくつか表示される場合があります。 推奨を受け入れるには、1 つまたは複数の列の **[マスクの追加]** をクリックします。その列の既定タイプに基づきマスクが作成されます。 マスク機能を変更できます。その場合、マスク ルールをクリックし、マスク フィールド形式を別の形式に変更します。 必ず **[保存]** をクリックして設定を保存します。
   
    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)<br/><br/>
5. データベースの列にマスクを追加するには、**[動的データ マスク]** 構成ブレードの一番上にある **[マスクの追加]** をクリックし、**[マスク ルールの追加]** 構成ブレードを開きます。
   
    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)<br/><br/>
6. **[スキーマ]**、**[テーブル]**、**[列]** を選択し、マスクする指定のフィールドを定義します。
7. 機密データのマスク カテゴリの一覧から **[マスク フィールド形式]** を選択します。
   
    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)<br/><br/>        
8. データ マスク ルール ブレードの **[保存]** をクリックして、動的データ マスク ポリシーのマスク ルールのセットを更新します。
9. マスクから除外し、マスクされていない機密データへのアクセスを与える SQL ユーザーまたは AAD の ID を入力します。 ユーザーをセミコロンで区切った一覧にします。 管理者特権を持つユーザーは常にマスクされていない元のデータにアクセスできることに注意してください。
   
    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)
   
   > [!TIP]
   > アプリケーションの特権を持つユーザーに対してアプリケーション レイヤーがデリケートなデータを表示できるようにするには、アプリケーションストアでデータベースの照会に使用される SQL ユーザーまたは AAD の ID を追加します。 デリケートなデータの公開を最小限に抑えるには、この一覧に含める特権ユーザーの数を最小限にすることを強くお勧めします。
   > 
   > 
10. データ マスク構成ブレードの **[保存]** をクリックして、新しいマスク ルールまたは更新されたマスク ポリシーを保存します。


## <a name="next-steps"></a>次のステップ

* 動的データ マスクの概要については、「[動的データ マスク](sql-database-dynamic-data-masking-get-started.md)」をご覧ください。
* [Azure SQL Database コマンドレット](https://msdn.microsoft.com/library/azure/mt574084.aspx)または [REST API](https://msdn.microsoft.com/library/dn505719.aspx) を使って動的データ マスクを実装することもできます。
