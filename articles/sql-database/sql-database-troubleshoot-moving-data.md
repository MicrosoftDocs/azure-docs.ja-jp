---
title: "サーバー間、サブスクリプション間、および Azure の内外にデータベースを移動します。"
description: "Azure SQL Database でデータとデータベースをコピー、移動、および移行するための簡単な手順。"
services: sql-database
documentationcenter: 
author: v-shysun
manager: felixwu
editor: 
ms.assetid: dea01868-5757-41e2-826c-ec26361a339c
ms.service: sql-database
ms.custom: migrate and move
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: v-shysun
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: bc88bb49c601f525c62c4dac4716a3f77a30172e


---
# <a name="move-databases-between-servers-between-subscriptions-and-in-and-out-of-azure"></a>サーバー間、サブスクリプション間、および Azure の内外にデータベースを移動する
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="to-move-a-database-to-a-different-server-in-the-same-subscription"></a>同じサブスクリプション内の別のサーバーにデータベースを移動するには
* [Azure Portal](https://portal.azure.com) で **[SQL データベース]** をクリックし、一覧からデータベースを選択してから、**[コピー]** をクリックします。 詳細については、「 [Azure SQL データベースのコピー](sql-database-copy.md) 」を参照してください。

## <a name="to-move-a-database-between-subscriptions"></a>サブスクリプション間でデータベースを移動するには
* [Azure ポータル](https://portal.azure.com)で **[SQL Server]** をクリックし、一覧からデータベースをホストするサーバーを選択します。 **[移動]**をクリックし、移動するリソースと移動先のサブスクリプションを選択します。

## <a name="to-migrate-a-sql-database-into-azure"></a>SQL データベースを Azure に移行するには
* データベースの互換性を確認し、ニーズに応じた適切な移行方法を選択します。 [SQL Server データベースの移行](sql-database-cloud-migrate.md)に関するページのガイドラインとオプションに従います。

## <a name="to-create-a-copy-of-a-database-for-use-outside-of-azure"></a>Azure の外部で使用するためにデータベースのコピーを作成するには
* [BACPAC ファイルをエクスポートします。](sql-database-export.md)




<!--HONumber=Nov16_HO3-->


