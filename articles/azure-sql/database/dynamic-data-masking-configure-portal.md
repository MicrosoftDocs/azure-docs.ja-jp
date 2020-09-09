---
title: Azure portal:動的データ マスク
description: Azure portal での Azure SQL Database 動的データ マスクの使用方法
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 04/28/2020
ms.openlocfilehash: 12a187b29737ba9388d9b33ac8bcfedf1339c059
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85253785"
---
# <a name="get-started-with-sql-database-dynamic-data-masking-with-the-azure-portal"></a>Azure Portal で SQL Database 動的データ マスクを使用する
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

この記事では、Azure Portal で[動的データ マスク](dynamic-data-masking-overview.md)を実装する方法を示します。 [Azure SQL Database コマンドレット](https://docs.microsoft.com/powershell/module/az.sql/)または [REST API](https://docs.microsoft.com/rest/api/sql/) を使って動的データ マスクを実装することもできます。

> [!NOTE]
> この機能は、Azure Synapse のポータル (PowerShell または REST API を使用) または SQL Managed Instance を使用して設定することはできません。 詳細については、「 [Dynamic Data Masking](/sql/relational-databases/security/dynamic-data-masking)」を参照してください。

## <a name="set-up-dynamic-data-masking-for-your-database-using-the-azure-portal"></a>Azure Portal を使用してデータベースの動的データ マスクを設定する

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) を開きます。
2. マスクする機微なデータを含むデータベースの設定ページに移動します。
3. データベースの **[セキュリティ]** セクションの下にある **[動的データ マスキング]** ブレードをクリックします。

   ![ナビゲーション ウィンドウ](./media/dynamic-data-masking-configure-portal/4_ddm_settings_tile.png)

4. **動的データ マスク**構成ページには、推奨エンジンがマスク対象として推奨したデータベース列がいくつか表示される場合があります。 推奨を受け入れるには、1 つまたは複数の列の **[マスクの追加]** をクリックします。その列の既定タイプに基づきマスクが作成されます。 マスク機能を変更できます。その場合、マスク ルールをクリックし、マスク フィールド形式を別の形式に変更します。 必ず **[保存]** をクリックして設定を保存します。

    ![ナビゲーション ウィンドウ](./media/dynamic-data-masking-configure-portal/5_ddm_recommendations.png)

5. データベースの列にマスクを追加するには、 **[動的データ マスク]** 構成ページの一番上にある **[マスクの追加]** をクリックし、 **[マスク ルールの追加]** 構成ページを開きます。

    ![ナビゲーション ウィンドウ](./media/dynamic-data-masking-configure-portal/6_ddm_add_mask.png)

6. **[スキーマ]** 、 **[テーブル]** 、 **[列]** を選択し、マスクする指定のフィールドを定義します。
7. 機密データのマスク カテゴリの一覧から**マスク方法を選択**します。

    ![ナビゲーション ウィンドウ](./media/dynamic-data-masking-configure-portal/7_ddm_mask_field_format.png)

8. データ マスク ルール ページの **[追加]** をクリックして、動的データ マスク ポリシーのマスク ルールのセットを更新します。
9. マスクから除外し、マスクされていない機密データへのアクセスを与える SQL ユーザーまたは Azure Active Directory (Azure AD) の ID を入力します。 ユーザーをセミコロンで区切った一覧にします。 管理者特権を持つユーザーは常にマスクされていない元のデータにアクセスできます。

    ![ナビゲーション ウィンドウ](./media/dynamic-data-masking-configure-portal/8_ddm_excluded_users.png)

    > [!TIP]
    > アプリケーションの特権を持つユーザーに対してアプリケーション レイヤーがデリケートなデータを表示できるようにするには、アプリケーションでデータベースの照会に使用される SQL ユーザーまたは Azure AD の ID を追加します。 デリケートなデータの公開を最小限に抑えるには、この一覧に含める特権ユーザーの数を最小限にすることを強くお勧めします。

10. データ マスク構成ページの **[保存]** をクリックして、新しいまたは更新されたマスク ポリシーを保存します。

## <a name="next-steps"></a>次のステップ

- 動的データ マスクの概要については、「[動的データ マスク](dynamic-data-masking-overview.md)」をご覧ください。
- [Azure SQL Database コマンドレット](https://docs.microsoft.com/powershell/module/az.sql/)または [REST API](https://docs.microsoft.com/rest/api/sql/) を使って動的データ マスクを実装することもできます。
