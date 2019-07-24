---
title: Azure portal:SQL Database の動的データ マスク | Microsoft Docs
description: Azure Portal での SQL Database 動的データ マスクの使用方法
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/04/2018
ms.openlocfilehash: 3d5ab203268ced1951d2ba9c852ece5bd5467c68
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341937"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure Portal で SQL Database 動的データ マスクを使用する

この記事では、Azure Portal で[動的データ マスク](sql-database-dynamic-data-masking-get-started.md)を実装する方法を示します。 [Azure SQL Database コマンドレット](https://docs.microsoft.com/powershell/module/az.sql/)または [REST API](https://docs.microsoft.com/rest/api/sql/) を使って動的データ マスクを実装することもできます。

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Azure Portal を使用してデータベースの動的データ マスクを設定する

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
2. マスクする機微なデータを含むデータベースの設定ページに移動します。
3. **[動的データ マスク]** タイルをクリックして、**[動的データ マスク]** 構成ページを起動します。

   * この方法に代わって、下にスクロールして **[操作]** セクションを表示し、**[動的データ マスク]** をクリックすることもできます。

     ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/4_ddm_settings_tile.png)

4. **動的データ マスク**構成ページには、推奨エンジンがマスク対象として推奨したデータベース列がいくつか表示される場合があります。 推奨を受け入れるには、1 つまたは複数の列の **[マスクの追加]** をクリックします。その列の既定タイプに基づきマスクが作成されます。 マスク機能を変更できます。その場合、マスク ルールをクリックし、マスク フィールド形式を別の形式に変更します。 必ず **[保存]** をクリックして設定を保存します。

    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/5_ddm_recommendations.png)

5. データベースの列にマスクを追加するには、**[動的データ マスク]** 構成ページの一番上にある **[マスクの追加]** をクリックし、**[マスク ルールの追加]** 構成ページを開きます。

    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/6_ddm_add_mask.png)

6. **[スキーマ]**、**[テーブル]**、**[列]** を選択し、マスクする指定のフィールドを定義します。
7. 機密データのマスク カテゴリの一覧から **[マスク フィールド形式]** を選択します。

    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/7_ddm_mask_field_format.png)

8. データ マスク ルール ページの **[保存]** をクリックして、動的データ マスク ポリシーのマスク ルールのセットを更新します。
9. マスクから除外し、マスクされていない機密データへのアクセスを与える SQL ユーザーまたは AAD の ID を入力します。 ユーザーをセミコロンで区切った一覧にします。 管理者特権を持つユーザーは常にマスクされていない元のデータにアクセスできます。

    ![ナビゲーション ウィンドウ](./media/sql-database-dynamic-data-masking-get-started/8_ddm_excluded_users.png)

    > [!TIP]
    > アプリケーションの特権を持つユーザーに対してアプリケーション レイヤーがデリケートなデータを表示できるようにするには、アプリケーションストアでデータベースの照会に使用される SQL ユーザーまたは AAD の ID を追加します。 デリケートなデータの公開を最小限に抑えるには、この一覧に含める特権ユーザーの数を最小限にすることを強くお勧めします。

10. データ マスク構成ページの **[保存]** をクリックして、新しいまたは更新されたマスク ポリシーを保存します。

## <a name="next-steps"></a>次の手順

* 動的データ マスクの概要については、「[動的データ マスク](sql-database-dynamic-data-masking-get-started.md)」をご覧ください。
* [Azure SQL Database コマンドレット](https://docs.microsoft.com/powershell/module/az.sql/)または [REST API](https://docs.microsoft.com/rest/api/sql/) を使って動的データ マスクを実装することもできます。
