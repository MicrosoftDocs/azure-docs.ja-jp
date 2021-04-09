---
title: 専用 SQL プール (以前の SQL DW) の Transparent Data Encryption (ポータル)
description: Azure Synapse Analytics での専用 SQL プール (以前の SQL DW) の Transparent Data Encryption (TDE)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: da4be6f4bc8335e0976a0a4a87c4d232b2a2285f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98676308"
---
# <a name="get-started-with-transparent-data-encryption-tde-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Synapse Analytics での専用 SQL プール (以前の SQL DW) の Transparent Data Encryption (TDE) の概要

> [!div class="op_single_selector"]
>
> * [セキュリティの概要](sql-data-warehouse-overview-manage-security.md)
> * [認証](sql-data-warehouse-authentication.md)
> * [暗号化 (ポータル)](sql-data-warehouse-encryption-tde.md)
> * [暗号化 (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)

## <a name="required-permissions"></a>必要なアクセス許可

Transparent Data Encryption (TDE) を有効にするには、管理者か dbmanager ロールのメンバーである必要があります。

## <a name="enabling-encryption"></a>暗号化の有効化

TDE を有効にするには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[透過的なデータ暗号化]** オプションを選択します ![ポータル設定](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. **[ON]** 設定を選択します ![ポータル設定オン](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-on.png)
5. **[保存]** を選択します
   ![ポータル設定保存](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save.png)  

## <a name="disabling-encryption"></a>暗号化の無効化

TDE を無効にするには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)
2. データベース ブレードで **[設定]** ボタンをクリックします。
3. **[透過的なデータ暗号化]** オプションを選択します ![ポータル設定](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings.png)
4. **[OFF]** 設定を選択します ![ポータル設定オフ](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-off.png)
5. **[保存]** を選択します
   ![ポータル設定保存 2](./media/sql-data-warehouse-security-tde/sql-data-warehouse-security-tde-portal-settings-save2.png)  

## <a name="encryption-dmvs"></a>暗号化の DMV

暗号化は次の DMV を使用して確認することができます。

* [sys.databases](/sql/relational-databases/system-catalog-views/sys-databases-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
* [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
