---
title: "Azure SQL Database におけるデータベース パフォーマンスの監視 | Microsoft Docs"
description: "Azure ツールと動的管理ビューを使用してデータベースを監視するためのオプションについて説明します。"
keywords: "データベースの監視, クラウドのデータベース パフォーマンス"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: a2e47475-c955-4a8d-a65c-cbef9a6d9b9f
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 01/10/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3ba16154857f8e7b59a1013b736d6131a4161185
ms.openlocfilehash: f4150149cea4fa31e7b991e15ffcbefa8a202ada


---
# <a name="monitoring-database-performance-in-azure-sql-database"></a>Azure SQL Database におけるデータベース パフォーマンスの監視
Azure での SQL データベースのパフォーマンスの監視は、選択したデータベース パフォーマンスのレベルに対するリソース使用率を監視することから始めます。 監視することで、データベースに余分な容量があるかどうかや、リソースが上限に達したことで問題が発生しているかどうかを判断できます。また、データベースのパフォーマンス レベルと[サービス レベル](sql-database-service-tiers.md)を調整する必要があるかどうかを判断することもできます。 データベースの監視には、[Azure Portal](https://portal.azure.com) のグラフィカル ツールや SQL の[動的管理ビュー](https://msdn.microsoft.com/library/ms188754.aspx)を使用できます。

## <a name="monitor-databases-using-the-azure-portal"></a>Azure ポータルを使用したデータベースの監視
[Azure Portal](https://portal.azure.com/) では、データベースを選択して **[監視]** グラフをクリックすると、単一のデータベースの使用率を監視することができます。 これにより、**[メトリック]** ウィンドウが表示されます。**[グラフの編集]** ボタンをクリックすると、内容を編集できます。 次のメトリックを追加します。

* CPU の割合
* DTU の割合
* データ IO の割合
* データベース サイズの割合

これらのメトリックを追加すると、**[監視]** グラフでこれらを引き続き確認しながら、**[メトリック]** ウインドウにさらに詳細な情報を表示することができます。 4 つのメトリックはいずれも、データベースの **DTU** を基準とする平均使用率を示しています。 DTU の詳細については、 [サービス階層](sql-database-service-tiers.md) に関する記事を参照してください。

![データベース パフォーマンスのサービス階層の監視](./media/sql-database-service-tiers/sqldb_service_tier_monitoring.png)

また、パフォーマンス メトリックに対してアラートを構成することができます。 **[メトリック]** ウィンドウの **[アラートの追加]** ボタンをクリックします。 ウィザードの指示に従ってアラートを構成します。 メトリックが特定のしきい値を超えた場合、またはメトリックが特定のしきい値を下回った場合に警告するオプションがあります。

たとえば、データベースのワークロードが増加する見込みの場合、データベースのいずれかのパフォーマンス メトリックが 80% に達すると電子メールのアラートを送信するように構成できます。 このアラートは、1 つ上位のパフォーマンス レベルに切り替えるタイミングを示す早期の警告として使用できます。

下位のパフォーマンス レベルにダウングレードできるかどうかを判断するために、パフォーマンス メトリックを利用することもできます。 たとえば、Standard S2 データベースを使用していて、すべてのパフォーマンス メトリックは、どの時点でもデータベースの平均的な使用率が 10% を超えないとします。 この場合、データベースは Standard S1 で快適に動作します。 ただし、下位のパフォーマンス レベルへの移行を決定する前に、急上昇や変動するワークロードに注意してください。

## <a name="monitor-databases-using-dmvs"></a>DMV を使用したデータベースの監視
Portal で公開されているものと同じメトリックを、システム ビューからも利用できます。それは、サーバーの論理 **master** データベースの [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) と、ユーザー データベースの [sys.dm_db_resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) です。 詳細度の低いデータをより長い期間で監視する必要がある場合は、**sys.resource_stats** を使用します。 詳細度の高いデータをより短い期間で監視する必要がある場合は、**sys.dm_db_resource_stats** を使用します。 詳細については、[Azure SQL Database のパフォーマンス ガイダンス](sql-database-performance-guidance.md#monitor-resource-use)に関する記事を参照してください。

> [!NOTE]
> **sys.dm_db_resource_stats** は、提供終了になった Web および Business Edition データベースで使用された場合、空の結果セットを返します。
>
>

エラスティック プールでは、このセクションで説明した手法を使用して、プール内の個々のデータベースを監視できます。 ただし、プールを全体として監視することもできます。 詳細については、[エラスティック プールの監視と管理](sql-database-elastic-pool-manage-portal.md)に関する記事を参照してください。



<!--HONumber=Dec16_HO3-->


