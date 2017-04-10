---
title: "Azure SQL Data Warehouse のコンピューティング能力の管理 (Azure Portal ) | Microsoft Docs"
description: "コンピューティング能力を管理するための Azure ポータルのタスク。 DWU を調整することで、コンピューティング リソースをスケーリングします。 また、コストを節約するために、コンピューティング リソースを一時停止および再開します。"
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 233b0da5-4abd-4d1d-9586-4ccc5f50b071
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: f9814fc0011153a25489d60d696bb567edfcebc4
ms.openlocfilehash: a86b041424ce1757fd45c7a39350f4c4e66ee265
ms.lasthandoff: 01/20/2017


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-azure-portal"></a>Azure SQL Data Warehouse のコンピューティング能力の管理 (Azure ポータル)
> [!div class="op_single_selector"]
> * [概要](sql-data-warehouse-manage-compute-overview.md)
> * [ポータル](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST ()](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>


## <a name="scale-compute-power"></a>コンピューティング能力のスケーリング
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

コンピューティング リソースを変更するには、次の手順に従います。

1. [Azure Portal][Azure portal] を開き、データベースを開いて、**[スケール]** をクリックします。

    ![[スケール] をクリックします。][1]
2. [スケール] ブレードで、スライダーを左または右に移動して DWU 設定を変更します。

    ![スライダーの移動][2]
3. **[Save]**をクリックします。 確認メッセージが表示されます。 **[はい]** をクリックして確定します。キャンセルするには、**[いいえ]** をクリックします。

    ![[保存] をクリックします。][3]

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>コンピューティングの一時停止
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

データベースを一時停止するには、次の手順に従います。

1. [Azure Portal][Azure portal] を開き、データベースを開きます。 [状態] が **[オンライン]**になっていることがわかります。

    ![オンライン状態][6]
2. コンピューティング リソースとメモリ リソースを一時停止するには、**[一時停止]** をクリックします。確認メッセージが表示されます。 **[はい]** をクリックして確定します。キャンセルするには、**[いいえ]** をクリックします。

    ![一時停止の確認][7]
3. SQL Data Warehouse がデータベースを起動している間、状態が **[一時停止中]**になります。
4. 状態が **[一時停止]**になると、一時停止操作が完了し、DWU に対して課金されなくなります。

    ![一時停止状態][4]

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>コンピューティングの再開
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

データベースを再開するには、次の手順に従います。

1. [Azure Portal][Azure portal] を開き、データベースを開きます。 状態が **[一時停止]**になっていることがわかります。

    ![データベースの一時停止][4]
2. データベースを再開するには、**[開始]** をクリックします。確認メッセージが表示されます。 **[はい]** をクリックして確定します。キャンセルするには、**[いいえ]** をクリックします。

    ![再開の確認][5]
3. SQL Data Warehouse がデータベースを起動している間、状態が [再開中] になります。
4. 状態が **[オンライン]**になったら、データベースをいつでも使用できます。

    ![オンライン状態][6]

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>次のステップ
詳しくは、「[管理の概要][Management overview]」をご覧ください。

<!--Image references-->
[1]: ./media/sql-data-warehouse-manage-compute-portal/click-scale.png
[2]: ./media/sql-data-warehouse-manage-compute-portal/move-slider.png
[3]: ./media/sql-data-warehouse-manage-compute-portal/click-save.png
[4]: ./media/sql-data-warehouse-manage-compute-portal/resume-database.png
[5]: ./media/sql-data-warehouse-manage-compute-portal/resume-confirm.png
[6]: ./media/sql-data-warehouse-manage-compute-portal/pause-database.png
[7]: ./media/sql-data-warehouse-manage-compute-portal/pause-confirm.png

<!--Article references-->
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

