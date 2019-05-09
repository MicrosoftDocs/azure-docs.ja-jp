---
title: Azure SQL Data Warehouse のサポート チケットを作成する方法 | Microsoft Docs
description: Azure SQL Data Warehouse でサポート チケットを作成する方法。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b3ffc9794967f44708d8330d4dc5d5de11b794d6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58079181"
---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>SQL Data Warehouse のサポート チケットを作成する方法
SQL Data Warehouse で問題が発生した場合は、サポート チケットを作成してください。エンジニアリング チームがサポートいたします。

## <a name="create-a-support-ticket"></a>サポート チケットの作成
1. [Azure Portal][Azure portal] を開きます。
2. ホーム画面で **[ヘルプとサポート]** タブをクリックします。
   
    ![[ヘルプとサポート]](./media/sql-data-warehouse-get-started-create-support-ticket/MainPage.PNG)
3. [ヘルプとサポート] ブレードで **[新しいサポート要求]** をクリックし、**[基本]** ブレードに入力します。

   お客様の [Azure サポート プラン][Azure support plan]を選択します。
   
   * **課金、クォータ、サブスクリプション管理**のサポートは、すべてのサポート レベルでご利用いただけます。
   * **障害対応**のサポートは、[Developer][Developer] サポート、[Standard][Standard] サポート、[Professional Direct][Professional Direct] サポート、[Premier][Premier] サポートにより提供されます。 障害対応のサポートとは、Azure のご利用に際して発生した、マイクロソフトが原因と想定される問題への対応です。
   * **開発者支援**および**アドバイザリ サービス**は、[Professional Direct][Professional Direct] サポートと [Premier][Premier] サポートでご利用いただけます。 
     
     Premier サポート プランを持っている場合は、[Microsoft Premier オンライン ポータル][Microsoft Premier online portal]でも SQL Data Warehouse 関連の問題を連絡できます。  サポートの範囲、応答時間、価格など各プランの詳細については、[Azure サポート プラン][Azure support plan]に関するページを参照してください。Azure のサポートに関してよく寄せられる質問については、「[Azure サポートに関する FAQ][Azure support FAQs]」を参照してください。  
        
     ![[基本] ブレード](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_1.PNG)
     ![[基本] ブレード 1](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_2.PNG)
4. **[問題]** ブレードに入力します。
    ![Problem_blade](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_3.PNG)
   
   > [!NOTE]
   > 既定では、各 SQL Server (myserver.database.windows.net など) の **DTU クォータ** は 45,000 に設定されています。 このクォータは単に安全上の制限です。 サポート チケットを作成し、要求の種類として *[クォータ]* を選択すれば、クォータを引き上げることができます。 実際に必要な DTU を計算するには、必要とされる [DWU][DWU] の合計に 7.5 を掛けます。 たとえば、2 つの DW6000 を 1 つの SQL Server でホストする場合、90,000 の DTU クォータを要求する必要があります。  現在の DTU 消費量は、ポータルの [SQL Server] ブレードから確認できます。 DTU クォータには、一時停止しているデータベースと一時停止していないデータベースの両方が考慮されます。 
   > 
   > 
   
5. **連絡先情報**を入力します。
   ![Contact_information](./media/sql-data-warehouse-get-started-create-support-ticket/Create_ticket_4.PNG)

    
6. **[作成]** をクリックしてサポート要求を送信します。

## <a name="monitor-a-support-ticket"></a>サポート チケットの状態の確認
サポート要求を送信すると、Azure サポート チームから連絡があります。 要求の状態と詳細を確認するには、ダッシュボードで **[すべてのサポート要求]** をクリックします。

![状態の確認](./media/sql-data-warehouse-get-started-create-support-ticket/Monitor_ticket.PNG)

## <a name="other-resources"></a>その他のリソース
さらに、[Stack Overflow][Stack Overflow] または [Azure SQL Data Warehouse MSDN フォーラム][Azure SQL Data Warehouse MSDN forum]で SQL Data Warehouse のコミュニティと交流することができます。

<!--Image references--> 

<!--Article references--> 
[DWU]: ./sql-data-warehouse-overview-what-is.md

<!--MSDN references--> 

<!--Other web references--> 
[Azure portal]: https://portal.azure.com/
[Azure support plan]: https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/  
[Developer]: https://azure.microsoft.com/support/plans/developer/  
[Standard]: https://azure.microsoft.com/support/plans/standard/  
[Professional Direct]: https://azure.microsoft.com/support/plans/prodirect/  
[Premier]: https://azure.microsoft.com/support/plans/premier/  
[Azure support FAQs]: https://azure.microsoft.com/support/faq/
[Microsoft Premier online portal]: https://premier.microsoft.com/
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw/
[Azure SQL Data Warehouse MSDN forum]: https://social.msdn.microsoft.com/Forums/home?forum=AzureSQLDataWarehouse/

