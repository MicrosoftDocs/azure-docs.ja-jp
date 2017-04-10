---
title: "Stretch 対応データベースをバックアップする | Microsoft Docs"
description: "Stretch が有効なデータベースをバックアップする方法について説明します。"
services: sql-server-stretch-database
documentationcenter: 
author: Antvgski
manager: johnmac
editor: douglasl
ms.assetid: a196f858-ef8f-47b5-b9db-bb7db98d48bd
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 68aeee33dbbba67e2a24300db62bf6bcdc627ea5


---
# <a name="backup-stretch-enabled-databases"></a>Stretch 対応のデータベースをバックアップする
データベースをバックアップしておけば、さまざまな種類の障害、エラー、災害から復旧できます。  

* Stretch 対応 SQL Server データベースをバックアップする必要があります。  
* Microsoft Azure は、Stretch Database が SQL Server から Azure に移行したリモート データを自動的にバックアップします。  

> [!NOTE]
> バックアップは、高い可用性と事業継続性を実現するための完全ソリューションの一部に過ぎません。 高可用性の詳細については、「 [High Availability Solutions](https://msdn.microsoft.com/library/ms190202.aspx)」 (高可用性ソリューション) を参照してください。
> 
> 

## <a name="back-up-your-sql-server-data"></a>SQL Server データをバックアップする
Stretch 対応 SQL Server データベースをバックアップするとき、現在使用している SQL Server のバックアップ方法を引き続き使用できます。 詳細については、「 [SQL Server データベースのバックアップと復元](https://msdn.microsoft.com/library/ms187048.aspx)」を参照してください。

Stretch 対応 SQL Server のバックアップには、ローカル データとバックアップの実行時点で移行対象となるデータのみが含まれます。 \(移行対象データとは、まだ移行されておらず、テーブルの移行構成に基づき、Azure に移行されるデータのことです\) これは **shallow** backup (浅いバックアップ) と呼ばれています。Azure に既に移行されているデータは含まれません。  

## <a name="back-up-your-remote-azure-data"></a>リモート Azure データをバックアップする
Microsoft Azure は、Stretch Database が SQL Server から Azure に移行したリモート データを自動的にバックアップします。  

### <a name="azure-reduces-the-risk-of-data-loss-with-automatic-backup"></a>Azure は、自動バックアップでデータ消失のリスクを軽減します
Azure の SQL Server Stretch Database サービスは、少なくとも 8 時間ごとに自動ストレージ スナップショットでリモート データベースを保護します。 スナップショットは 7 日間保存され、広範囲の復旧ポイントが提供されます。  

### <a name="azure-reduces-the-risk-of-data-loss-with-geo-redundancy"></a>Azure は、地理冗長でデータ消失のリスクを軽減します
Azure のデータベース バックアップは地理冗長性のある Azure Storage (RA\-GRS) に保存されるため、既定で地理冗長になります。 地理冗長ストレージでは、プライマリ リージョンから数百マイル離れたセカンダリ リージョンにデータがレプリケートされます。 プライマリとセカンダリの両方のリージョンで、データは別個のフォールト ドメインとアップグレード ドメインの間でそれぞれ 3 回レプリケートされます。 これにより、いずれかの Azure リージョンが使用できなくなるような完全な地域的障害や災害が発生した場合でも、データの永続性が確保されます。

### <a name="a-namestretchrpoastretch-database-reduces-the-risk-of-data-loss-for-your-azure-data-by-retaining-migrated-rows-temporarily"></a><a name="stretchRPO"></a>Stretch Database は、移行した行を一時的に保持し、Azure データの消失リスクを軽減します
Stretch Database は、SQL Server から Azure に移行対象行を移行した後、最低 8 時間ステージング テーブルでその行を保持します。 Azure データベースのバックアップを復元するとき、Stretch Database はステージング テーブルに保存されている行を利用し、SQL Server と Azure データベースを一致させます。

Azure データのバックアップを復元したら、ストアド プロシージャの [sys.sp_rda_reauthorize_db](https://msdn.microsoft.com/library/mt131016.aspx) を実行し、Stretch 対応 SQL Server データベースをリモート Azure データベースに再接続する必要があります。 **sys.sp_rda_reauthorize_db** を実行すると、Stretch Database は SQL Server と Azure データベースを自動的に一致させます。

Stretch Database が移行データをステージング テーブルに一時的に保持する時間を増やすには、ストアド プロシージャの [sys.sp_rda_set_rpo_duration](https://msdn.microsoft.com/library/mt707766.aspx) を実行し、8 より大きい時間数を指定します。 保持するデータの量を決めるとき、次の要素を考慮してください。

* (少なくとも 8 時間ごとに行われる) Azure の自動バックアップの頻度。
* 問題の発生後、問題を認識し、バックアップの復元を決定するために必要な時間。
* Azure 復元操作にかかる時間。

> [!NOTE]
> Stretch Database がステージング テーブルに一時的に保持するデータの量を増やすと、SQL Server で必要とされる領域が増えます。
> 
> 

Stretch Database が現在、ステージング テーブルに一時的に保持するデータの時間数を確認するには、ストアド プロシージャの [sys.sp_rda_get_rpo_duration](https://msdn.microsoft.com/library/mt707767.aspx) を実行します。

## <a name="see-also"></a>関連項目
[Stretch Database を管理し、問題を解決します。](sql-server-stretch-database-manage.md)

[sys.sp_rda_reauthorize_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx)

[SQL Server データベースのバックアップと復元](https://msdn.microsoft.com/library/ms187048.aspx)




<!--HONumber=Nov16_HO3-->


