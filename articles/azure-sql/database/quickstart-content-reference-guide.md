---
title: 単一データベースのクイックスタートに関するコンテンツ リファレンス
description: Azure SQL Database の単一データベースをすばやく開始するのに役立つすべてのクイックスタートのコンテンツ リファレンスを確認できます。
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: guide
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 07/29/2019
ms.openlocfilehash: 392f361f6fe487a15bdd63c2f3281ccc78b2f86d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184418"
---
# <a name="getting-started-with-single-databases-in-azure-sql-database"></a>Azure SQL Database の単一データベースの概要
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[単一データベース](../index.yml)は、フル マネージド PaaS (サービスとしてのプラットフォーム) のサービスとしてのデータベース (DbaaS) であり、最新のクラウド生まれのアプリケーションに最適なストレージ エンジンです。 このセクションでは、Azure SQL Database で単一データベースの構成と作成をすばやく行う方法について説明します。

## <a name="quickstart-overview"></a>クイック スタートの概要

このセクションでは、単一データベースの基本的な使用方法を理解できる記事の概要を紹介します。 以下の各クイック スタートを使用すると、単一データベースをすばやく作成し、サーバーレベルのファイアウォール規則を構成し、`.bacpac` ファイルを使用して新しい単一データベースにデータベースをインポートできます。

- [Azure portal を使用して単一データベースを作成](single-database-create-quickstart.md)します。
- データベースの作成後は、[ファイアウォール規則を構成してデータベースをセキュリティで保護する](firewall-create-server-level-portal-quickstart.md)必要があります。
- Azure SQL Database に移行したい既存のデータベースが SQL Server 上にある場合は、SQL Server 上のデータベースを分析し、移行を妨げる可能性のある問題を検出する [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595) をインストールしてください。 問題がない場合は、データベースを `.bacpac` ファイルとしてエクスポートし、[Azure portal または SqlPackage を使用してインポートします](database-import.md)。


## <a name="automating-management-operations"></a>管理操作の自動化

データベースの作成、構成、およびスケーリングには、PowerShell または Azure CLI を使用できます。

- [PowerShell](scripts/create-and-configure-database-powershell.md) または [Azure CLI](scripts/create-and-configure-database-cli.md) を使用して単一データベースを作成および構成する
- [PowerShell](scripts/monitor-and-scale-database-powershell.md) または [Azure CLI](scripts/monitor-and-scale-database-cli.md) を使用して単一データベースを更新し、リソースをスケーリングする

## <a name="migrating-to-a-single-database-with-minimal-downtime"></a>最小限のダウンタイムで単一データベースに移行する

これらのクイック スタートを使用すると、`.bacpac` でデータベースを簡単に作成し、Azure にインポートできます。 ただし、`.bacpac` および `.dacpac` ファイルは、SQL Server のさまざまなバージョン間および Azure SQL 内でデータベースをすばやく移動したり、DevOps パイプラインで継続的インテグレーションを実装したりするように設計されています。 しかし、この方法は、最小限のダウンタイムで運用データベースを移行するためには設計されていません。新しいデータの追加を中止し、`.bacpac` ファイルへのソース データベースのエクスポートが完了するのを待機し、さらに Azure SQL Database へのインポートが完了するのを待機する必要があるためです。 このような待機の結果、特に大規模なデータベースでは、アプリケーションにダウンタイムが発生します。 運用データベースを移動する場合は、最小限のダウンタイムが保証される、より優れた移行方法が必要です。 そのためには、[Database Migration Service (DMS)](../../dms/tutorial-sql-server-to-azure-sql.md?toc=%2fazure%2fsql-database%2ftoc.json) を使用して、最小限のダウンタイムでデータベースを移行します。 DMS は、ソース データベースで行われた変更を、復元中の単一データベースに増分的にプッシュすることで、これを実現します。 この方法であれば、最小限のダウンタイムでアプリケーションをソース データベースからターゲット データベースにすばやく切り替えることができます。

## <a name="hands-on-learning-modules"></a>ハンズオン ラーニング モジュール

次の Microsoft Learn モジュールは、Azure SQL Database について無料で学習するのに役立ちます。

- [アプリケーション データを格納するために SQL Database にデータベースをプロビジョニングする](/learn/modules/provision-azure-sql-db/)
- [Azure SQL Database 内のデータベースのクエリを実行する ASP.NET アプリケーションを開発して構成する](/learn/modules/develop-app-that-queries-azure-sql/)
- [Azure SQL Database 内のデータベースをセキュリティで保護する](/learn/modules/secure-your-azure-sql-database/)

## <a name="next-steps"></a>次のステップ

- [Azure SQL Database でサポートされている機能の概要の一覧](features-comparison.md)を確認します。
- [データベースの安全性を高める](secure-database-tutorial.md)方法を学びます。
- 「[Azure SQL Database の単一データベースを使用する方法](how-to-content-reference-guide.md)」で、より高度な操作方法を参照します。
- [PowerShell](powershell-script-content-guide.md) と [Azure CLI](az-cli-script-samples-content-guide.md) で記述されたその他のサンプル スクリプトを確認します。
- データベースの構成に使用できる[管理 API](single-database-manage.md) について詳しく学びます。
- [オンプレミス データベースに適した Azure SQL Database または Azure SQL Managed Instance の SKU を確認します](/sql/dma/dma-sku-recommend-sql-db/)。