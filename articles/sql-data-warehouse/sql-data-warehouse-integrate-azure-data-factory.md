---
title: "SQL Data Warehouse での Azure Data Factory の使用 | Microsoft Docs"
description: "ソリューション開発のための、Azure SQL Data Warehouse での Azure Data Factory (ADF) の使用に関するヒント。"
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: 492de762-c7a2-4cdb-943f-3135230e94f1
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: integrate
ms.date: 10/31/2016
ms.author: elbutter;barbkess
ms.openlocfilehash: 6adfa1264c9d196d6c6e57f1d108710b9ee73265
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-data-factory-with-sql-data-warehouse"></a>SQL Data Warehouse での Azure Data Factory の使用
Azure Data Factory では、SQL Data Warehouse におけるデータ転送とストアド プロシージャの実行を、完全に管理された方法で調整できます。  これにより、複雑な抽出、変換、および読み込み (ETL) プロシージャを、SQL Data Warehouse を使用して簡単に設定およびスケジュールできます。 Azure Data Factory の概要については、[Azure Data Factory のドキュメント][Azure Data Factory documentation]を参照してください。

## <a name="data-movement"></a>データの移動
Azure Data Factory により、オンプレミス ソースとさまざまな Azure サービスの両方の間でデータを移動できます。  Azure Data Factory との現在の包括的統合では、次の場所との間でのデータ移動がサポートされています。

* Azure BLOB ストレージ
* Azure SQL データベース
* オンプレミスの SQL Server
* IaaS 上の SQL Server

データのコピー アクティビティの設定方法については、「[コピー アクティビティを使用したデータの移動][Copy data with Azure Data Factory]」を参照してください。

## <a name="stored-procedures"></a>ストアド プロシージャ
 Azure Data Factory を使用すると、データ転送のスケジュールを設定できますが、それと同様に、ストアド プロシージャの実行を調整する際に使用することもできます。  これにより、さらに複雑なパイプラインを作成でき、SQL Data Warehouse の演算性能を利用する Azure Data Factory の機能も拡張されます。

## <a name="next-steps"></a>次のステップ
統合の概要については、「[SQL Data Warehouse の設計上の決定と コーディング技法][SQL Data Warehouse integration overview]」を参照してください。
開発に関するその他のヒントについては、[SQL Data Warehouse の開発の概要][SQL Data Warehouse development overview]に関する記事をご覧ください。

<!--Image references-->

<!--Article references-->

[Copy data with Azure Data Factory]: ../data-factory/copy-activity-overview.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integrate.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory documentation]:https://azure.microsoft.com/documentation/services/data-factory/

