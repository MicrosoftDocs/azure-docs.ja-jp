---
title: "Azure Portal を使用した Azure SQL Database のコピー | Microsoft Docs"
description: "Azure SQL Database のコピーの作成"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: daa6f079-13ed-462f-b346-e201aa61681b
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 752dcb97101f4a8288125e3e41167095620d130f
ms.lasthandoff: 03/28/2017


---
# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Azure ポータルを使用した Azure SQL Database のコピー

次の手順では、 [Azure ポータル](https://portal.azure.com) を使用して、同じサーバーまたは別のサーバーに SQL データベースをコピーする方法を説明します。 

> [!NOTE]
> [PowerShell](scripts/sql-database-copy-database-to-new-server-powershell.md) または [Transact-SQL](sql-database-copy-transact-sql.md) を使用して SQL データベースをコピーすることもできます。
>

SQL データベースをコピーするには、以下の項目が必要です。

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合、このページの上部の **無料評価版** をクリックしてからこの記事に戻り、最後まで完了してください。
* コピーする SQL データベース。 SQL Database がない場合は、「 [最初の Azure SQL Database を作成する](sql-database-get-started.md)」という記事の手順に従って 1 つ作成してください。

## <a name="copy-your-sql-database"></a>SQL データベースのコピー
コピーするデータベースの SQL データベース ページを開きます。

1. [Azure ポータル](https://portal.azure.com)にアクセスします。
2. **[More services (その他のサービス)]** > **[SQL Database]**、目的のデータベースの順にクリックします。
3. SQL データベース ページで、 **[コピー]**をクリックします。
   
   ![SQL Database](./media/sql-database-copy-portal/sql-database-copy.png)
4. **[コピー]** ページには、既定のデータベース名が入力されています。 必要に応じて (サーバー上のすべてのデータベースに一意の名前を付ける必要がある場合など) 別の名前を入力します。
5. **[対象サーバー]**を選択します。 ターゲット サーバーとは、データベース コピーが作成される場所です。 同じサーバーまたは別のサーバーにデータベースをコピーできます。 サーバーを作成したり、一覧から既存のサーバーを選択したりできます。 
6. **[ターゲット サーバー]** を選択すると、**[エラスティック プール]** オプションと **[価格レベル]** オプションが有効になります。 サーバーにプールがある場合は、そこにデータベースをコピーできます。
7. **[OK]** をクリックし、コピー プロセスを開始します。
   
   ![SQL Database](./media/sql-database-copy-portal/copy-page.png)

## <a name="monitor-the-progress-of-the-copy-operation"></a>コピー操作の進行状況の監視
* コピーの開始後、ポータル通知をクリックすると、詳細が表示されます。
  
    ![通知][3]
  
    ![監視][4]

## <a name="verify-the-database-is-live-on-the-server"></a>サーバーにデータベースが存在することの確認
* **[More services (その他のサービス)]** > **[SQL Database]** の順にクリックし、新しいデータベースが**オンライン**であることを確認します。

## <a name="resolve-logins"></a>ログインの解決
コピー操作が完了した後にログインを解決するには、 [ログインの解決](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)

## <a name="next-steps"></a>次のステップ
* 別の論理サーバーにデータベースをコピーする場合のユーザーおよびログインの管理の詳細については、「[障害復旧後にセキュリティを管理する方法](sql-database-geo-replication-security-config.md)」をご覧ください。
* Azure Portal を使用してデータベースを BACPAC ファイルにエクスポートするには、[Azure Portal を使用した BACPAC へのデータベースのエクスポート](sql-database-export-portal.md)に関する記事をご覧ください。
* [ビジネス継続性の概要](sql-database-business-continuity.md)
* [SQL Database のドキュメント](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->

[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png


