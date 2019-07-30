---
title: クイック スタート - Azure SQL Database の単一データベース | Microsoft Docs
description: Azure SQL Database の単一データベースの基本的な使用方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 7b52453bab661531461a2bec2f15f7659ec15a1c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441717"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Azure SQL Database の単一データベースの概要

[単一データベース](sql-database-single-index.yml)は、フル マネージド PaaS のサービスとしてのデータベース (DbaaS) であり、最新のクラウド生まれのアプリケーションに最適なストレージ エンジンです。 このセクションでは、SQL Database で単一データベースの構成と作成を簡単に行う方法について説明します。

## <a name="quickstart-overview"></a>クイック スタートの概要

このセクションでは、単一データベースの基本的な使用方法を理解できる記事の概要を紹介します。 以下の各クイック スタートを使用すると、単一データベースをすばやく作成し、データベース サーバーのファイアウォール規則を構成してから、`.bacpac` ファイルを使用して新しい単一データベースにデータベースをインポートできます。

- [Azure portal を使用して単一データベースを作成](sql-database-single-database-get-started.md)します。
- データベースの作成後は、[ファイアウォール規則を構成してデータベースをセキュリティで保護する](sql-database-server-level-firewall-rule.md)必要があります。
- Azure に移行したい既存のデータベースが SQL Server 上にある場合は、[Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) をインストールして SQL Server 上のデータベースを分析し、単一データベース デプロイ オプションへの移行を妨げる可能性のある問題を探します。 問題がない場合は、データベースを `.bacpac` ファイルとしてエクスポートし、[Azure portal または SqlPackage を使用してインポートします](sql-database-import.md)。

## <a name="automating-management-operations"></a>管理操作の自動化

データベースの作成、構成、およびスケーリングには、PowerShell または Azure CLI を使用できます。

- [PowerShell を使用して単一データベースを作成および構成する](scripts/sql-database-create-and-configure-database-powershell.md)
- [Azure CLI を使用して単一データベースを作成および構成する](scripts/sql-database-create-and-configure-database-cli.md)
- [PowerShell を使用して単一データベースを更新し、リソースをスケーリングする](scripts/sql-database-monitor-and-scale-database-powershell.md)
- [Azure CLI を使用して単一データベースを更新し、リソースをスケーリングする](scripts/sql-database-monitor-and-scale-database-cli.md)

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>最小限のダウンタイムで単一データベースに移行する

これらのクイック スタートを使用すると、`.bacpac` でデータベースを簡単に作成し、Azure にインポートできます。 ただし、`.bacpac` および `.dacpac` ファイルは、Azure SQL Database 内のさまざまなバージョンの SQL Server およびデプロイ オプション間でデータベースをすばやく移動したり、DevOps パイプラインで継続的な統合を実装したりするように設計されています。 しかし、この方法は、最小限のダウンタイムで運用データベースを移行するためには設計されていません。新しいデータの追加を中止し、`.bacpac` ファイルへのソース データベースのエクスポートが完了するのを待機し、さらに Azure SQL Database へのインポートが完了するのを待機する必要があるためです。 このような待機の結果、特に大規模なデータベースでは、アプリケーションにダウンタイムが発生します。 運用データベースを移動する場合は、最小限のダウンタイムが保証される、より優れた移行方法が必要です。 そのためには、[データ移行サービス (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql?toc=/azure/sql-database/toc.json) を使用して、最小限のダウンタイムでデータベースを移行します。 DMS は、ソース データベースで行われた変更を、復元中の単一データベースに増分的にプッシュすることで、これを実現します。 この方法であれば、最小限のダウンタイムでアプリケーションをソース データベースからターゲット データベースにすばやく切り替えることができます。

## <a name="next-steps"></a>次の手順

- [Azure SQL Database でサポートされている機能の概要の一覧](sql-database-features.md)を確認します。
- [データベースの安全性を高める](sql-database-security-tutorial.md)方法を学びます。
- 「[Azure SQL Database の単一データベースを使用する方法](sql-database-howto-single-database.md)」で、より高度な操作方法を参照します。
- [PowerShell](sql-database-powershell-samples.md) と [Azure CLI](sql-database-cli-samples.md) で記述されたその他のサンプル スクリプトを確認します。
- データベースの構成に使用できる[管理 API](sql-database-single-databases-manage.md) について詳しく学びます。
- [オンプレミス データベースに適した Azure SQL Database/Managed Instance SKU を特定します](/sql/dma/dma-sku-recommend-sql-db/)。