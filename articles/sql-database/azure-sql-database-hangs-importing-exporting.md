---
title: Azure SQL Database Import/Export サービスでのデータベースのインポートまたはエクスポートに時間がかかる | Microsoft Docs
description: Azure SQL Database Import/Export サービスでのデータベースのインポートまたはエクスポートに時間がかかる
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: ed5f3d1cd505270eb91c9cfbd6fb5c38b908f33d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71975370"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database Import/Export サービスでのデータベースのインポートまたはエクスポートに時間がかかる

Azure SQL Database Import/Export サービスを使用しているときに、プロセスの完了までに時間がかかる場合があります。 この記事では、こうした遅延の考えられる原因に関する追加情報と、これらの問題を回避するために使用できる代替方法について説明します。

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database Import/Export サービス

Azure SQL Database Import/Export サービスは、すべての Microsoft Azure データセンターで実行される REST ベースの Web サービスです。 これは、[[データベースのインポート]](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) オプションまたは [[エクスポート]](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) オプションを使用して Microsoft Azure portal で SQL データベースを移動するときに呼び出されるサービスです。 このサービスでは、Microsoft Azure SQL データベースから Microsoft Azure バイナリ ラージ オブジェクト (BLOB) ストレージへのインポートとエクスポートを実行する無料の要求キュー サービスと無料のコンピューティング サービスが提供されます。

インポート操作とエクスポート操作は、従来の物理データベース バックアップではなく、特別な BACPAC 形式を使用するデータベースの論理バックアップです。 この論理 BACPAC 形式を使用すると、SQL Server と SQL Database のバージョンごとに異なる可能性がある物理形式を使用する必要がなくなります。 そのため、これを使用して、データベースを SQL データベースおよび SQL Server データベースに安全に復元できます。

## <a name="what-causes-the-process-to-take-a-long-time"></a>プロセスに長い時間がかかる原因

Azure SQL Database Import/Export サービスでは、インポート操作とエクスポート操作を処理するために、リージョンごとに制限された数のコンピューティング仮想マシン (VM) が提供されます。 インポートまたはエクスポートにより複数リージョンにわたる帯域幅の遅延と課金が生じるのを回避するために、コンピューティング VM はリージョンごとにホストされています。 そのため、同じリージョンで同時に行われる要求が多すぎると、それらの操作の処理で大幅な遅延が発生します。 要求を完了するために必要な時間は、数秒から長時間までさまざまです。

> [!NOTE]
> 要求が 4 日以内に処理されない場合、サービスによって自動的に要求がキャンセルされます。

## <a name="recommended-solutions"></a>推奨されるソリューション

データベースのエクスポートが偶発的なデータ削除からの復旧のみに使用されている場合、Azure SQL Server データベースのすべてのエディションには、システムによって生成されるバックアップからのセルフサービス復元機能が用意されています。 ただし、その他の理由でこれらのエクスポートが必要な場合や、一貫して高速または予測可能なインポートまたはエクスポートのパフォーマンスが必要な場合は、次のオプションを検討してください。

* [SQLPackage ユーティリティを使用して BACPAC ファイルにエクスポートする](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)
* [SQL Server Management Studio (SSMS) を使用して BACPAC ファイルにエクスポートする](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)
* Microsoft® SQL Server®Data-Tier Application Framework (DacFx) API を使用して、BACPAC のインポートまたはエクスポートをコード内で直接実行する。 追加情報については、次をご覧ください。
  * [データ層アプリケーションのエクスポート](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft.SqlServer.Dac 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx のダウンロード](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="considerations-when-exporting-or-importing-an-azure-sql-database"></a>Azure SQL データベースをエクスポートまたはインポートする際の考慮事項

* この記事で説明されているすべての方法で DTU クォータが消費されるため、その結果、Azure SQLDB サービスによる調整が行われます。 [Azure portal でデータベースの DTU 統計を表示](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance)できます。 データベースがリソース制限に達している場合は、[サービス レベルをアップグレードして](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)さらにリソースを追加します。
* クライアント アプリケーション (sqlpackage ユーティリティやカスタム DAC アプリケーションなど) は、SQL データベースと同じリージョンの仮想マシン (VM) から実行することをお勧めします。そうしないと、ネットワーク待機時間によるパフォーマンスの問題が発生する可能性があります。
* クラスター化インデックスを使用せずに大きなテーブルをエクスポートすると、非常に時間がかかったり、エラーが発生したりする可能性があります。 これは、テーブルを分割して並列でエクスポートすることはできず、1 つのトランザクションでエクスポートしなければならないため、特に大きなテーブルの場合、エクスポート中にパフォーマンスが低下したりエラーが発生したりする可能性があるためです。 


## <a name="related-documents"></a>関連ドキュメント

[Azure SQL データベースをエクスポートする際の考慮事項](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
