---
title: "SQL Data Warehouse のサポート チケットを作成する方法 | Microsoft Docs"
description: "Azure SQL Data Warehouse でサポート チケットを作成する方法。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a91d1f53-03cb-464b-9d5b-4a9c1a194ed3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 889776269aa3d722115ea0632b7c3db2775924eb
ms.openlocfilehash: cc3bc8a0463842ba11b9cdcc996319e2d811ce8a
ms.lasthandoff: 02/13/2017


---
# <a name="how-to-create-a-support-ticket-for-sql-data-warehouse"></a>SQL Data Warehouse のサポート チケットを作成する方法
SQL Data Warehouse で問題が発生した場合は、サポート チケットを作成してください。エンジニアリング チームがサポートいたします。

> [!NOTE] 
> 2016 年 12 月 20 日時点では、Azure Portal のリソース正常性チェックが正確ではありません。 現在、この問題の解決に積極的に取り組んでいます。 


## <a name="create-a-support-ticket"></a>サポート チケットの作成
1. [Azure Portal][Azure portal] を開きます。
2. ホーム画面で **[ヘルプとサポート]** タイルをクリックします。
   
    ![[ヘルプとサポート]](./media/sql-data-warehouse-get-started-create-support-ticket/help-support.png)
3. [ヘルプとサポート] ブレードで **[サポート要求の作成]**をクリックします。
   
    ![新しいサポート要求](./media/sql-data-warehouse-get-started-create-support-ticket/create-support-request.png)
   
    <a name="request-quota-change"></a> 
4. **[要求の種類]**を選択します。
   
    ![[要求の種類]](./media/sql-data-warehouse-get-started-create-support-ticket/request-type.png)
   
   > [!NOTE]
   > 既定では、各 SQL Server (myserver.database.windows.net など) の **DTU クォータ** は 45,000 に設定されています。 このクォータは単に安全上の制限です。 サポート チケットを作成し、要求の種類として *[クォータ]* を選択すれば、クォータを引き上げることができます。 実際に必要な DTU を計算するには、必要とされる [DWU][DWU] の合計に 7.5 を掛けます。 たとえば、2 つの DW6000 を 1 つの SQL Server でホストする場合、90,000 の DTU クォータを要求する必要があります。  現在の DTU 消費量は、ポータルの [SQL Server] ブレードから確認できます。 DTU クォータには、一時停止しているデータベースと一時停止していないデータベースの両方が考慮されます。 
   > 
   > 
5. 報告する問題が発生しているデータベースをホストしている **[サブスクリプション]** を選択します。
   
    ![[サブスクリプション]](./media/sql-data-warehouse-get-started-create-support-ticket/subscription.png)
6. [リソース] として **[SQL Data Warehouse]** を選択します。
   
    ![リソース](./media/sql-data-warehouse-get-started-create-support-ticket/resource.png)
7. お客様の [Azure サポート プラン][Azure support plan]を選択します。
   
   * **課金、クォータ、サブスクリプション管理** のサポートは、すべてのサポート レベルでご利用いただけます。
   * **障害対応**のサポートは、[Developer][Developer] サポート、[Standard][Standard] サポート、[Professional Direct][Professional Direct] サポート、[Premier][Premier] サポートにより提供されます。 障害対応のサポートとは、Azure のご利用に際して発生した、マイクロソフトが原因と想定される問題への対応です。
   * **開発者支援**および**アドバイザリ サービス**は、[Professional Direct][Professional Direct] サポートと [Premier][Premier] サポートでご利用いただけます。 
     
     Premier サポート プランを持っている場合は、[Microsoft Premier オンライン ポータル][Microsoft Premier online portal]でも SQL Data Warehouse 関連の問題を連絡できます。  サポートの範囲、応答時間、価格など各プランの詳細については、[Azure サポート プラン][Azure support plan]に関するページを参照してください。Azure のサポートに関してよく寄せられる質問については、「[Azure サポートに関する FAQ][Azure support FAQs]」を参照してください。  
     
     ![サポート プラン](./media/sql-data-warehouse-get-started-create-support-ticket/support-plan.png)
8. **問題の種類**と**カテゴリ**を選択します。 この例では、問題の種類として [ツール]、カテゴリとして [クライアント ツール] を選択しました。 
   
    ![問題の種類のカテゴリ](./media/sql-data-warehouse-get-started-create-support-ticket/problem-type-category.png)
9. 問題を記述し、業務への影響のレベルを選択します。
   
    ![問題の説明](./media/sql-data-warehouse-get-started-create-support-ticket/problem-description.png)
10. このサポート チケットの **連絡先情報** は自動的に設定されます。 必要な場合は更新します。
    
    ![連絡先情報](./media/sql-data-warehouse-get-started-create-support-ticket/contact-info.png)
11. **[作成]** をクリックしてサポート要求を送信します。

## <a name="monitor-a-support-ticket"></a>サポート チケットの状態の確認
サポート要求を送信すると、Azure サポート チームから連絡があります。 要求の状態と詳細を確認するには、ダッシュボードで **[サポート要求の管理]** をクリックします。

![状態の確認](./media/sql-data-warehouse-get-started-create-support-ticket/check-status.png)

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


