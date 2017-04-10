---
title: "移行: Data Warehouse 移行ユーティリティ |Microsoft Docs"
description: "SQL Data Warehouse に移行します。"
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 4d7ad981-ef31-4513-b337-50bdc4709c09
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: migrate
ms.date: 10/31/2016
ms.author: jrj;barbkess
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 61adb7ae8fddc3cf423ee4558308eb9ded11fea3
ms.lasthandoff: 12/08/2016


---
# <a name="data-warehouse-migration-utility-preview"></a>Data Warehouse 移行ユーティリティ (プレビュー)
> [!div class="op_single_selector"]
> * [移行ユーティリティのダウンロード][Download Migration Utility]
> 
> 

Data Warehouse 移行ユーティリティは、SQL Server と Azure SQL Database から Azure SQL Data Warehouse にスキーマとデータを移行するように設計されたツールです。 スキーマの移行中、このツールによって、対応するスキーマが移行元から移行先へ自動的にマップされます。 スキーマの移行後、自動的に生成されたスクリプトによってデータを移動するオプションが用意されています。

このツールを使用すると、スキーマとデータの移行だけでなく、互換性レポートを生成することもできます。このレポートでは、合理化された移行の妨げとなる移行元インスタンスと移行先インスタンス間の非互換性がまとめられています。

## <a name="get-started"></a>作業開始
インストールの前提条件として、移行用のスクリプトを実行するための BCP コマンド ライン ユーティリティと、互換性レポートを表示するための Office が必要です。 ダウンロードした実行可能ファイルを起動すると、ツールをインストールする前に、標準的な使用許諾契約書への同意が求められます。

さらに、移行ユーティリティを実行するには、移行するデータベースに対して、データベースの作成、任意のデータベースの変更、任意の定義の表示のいずれかのアクセス許可が必要です。

### <a name="launching-the-tool-and-connecting"></a>ツールの起動と接続
ツールを起動するには、インストール後に表示されるデスクトップ アイコンをクリックします。 このツールを開くと、接続ページが表示され、移行ツールに対して移行元と移行先を選択するよう求められます。 現時点では、移行元として SQL Server と Azure SQL Database、移行先として SQL Data Warehouse がサポートされています。 これを選択すると、移行元サーバーに接続するよう求められます。これには、サーバー名を入力して認証し、[Connect] をクリックします。

認証後、接続先サーバーに存在するデータベースの一覧が表示されます。 移行を開始するには、移行するデータベースを選択し、[Migrate selected] \(選択した項目を移行) をクリックします。

## <a name="migration-report"></a>移行レポート
ツールで [Check Database Compatibility] \(データベースの互換性をチェック) を選択すると、移行するように要求されたデータベースに含まれる、すべてのオブジェクトの非互換性をまとめたレポートが生成されます。 SQL Data Warehouse に存在しない SQL Server 機能をまとめたより広範な一覧については、[移行に関するドキュメント][migration documentation]をご覧ください。 生成されたレポートは保存して、Excel で開くことができます。

移行スキーマを生成すると、"Object" として識別されるほとんどの結果がそのデータを即座に移行できるように調整されます。 スキーマを適用する前に、追加の調整が必要ないかどうかを確認するために、変更を確認してください。

## <a name="migrate-schema"></a>スキーマの移行
接続後に [Migrate Schema] を選択すると、選択したテーブルのスキーマ移行スクリプトが生成されます。 このスクリプトにより、テーブルの構造が移植され、互換性のないデータ型がより互換性のある形式にマップされます。さらに、ユーザーが移行の設定でセキュリティ資格情報とスキーマを指定した場合は、それらが作成されます。 このコードは、移行先となる SQL Data Warehouse インスタンスに対して実行できます。また、ファイルへの保存、クリップボードへのコピー、追加操作の実行前のインラインでの編集が可能です。  

既に説明したように、スキーマを移行する際は、ツールによって行われた移行の変更を十分に理解できるようにその変更を確認してください。  

## <a name="migrate-data"></a>データの移行
[Migrate Data] オプションをクリックすると、BCP スクリプトを生成できます。このスクリプトにより、データは、まずサーバー上のフラット ファイルに移動された後、SQL Data Warehouse に直接移動されます。 このプロセスは、少量のデータを移動する場合にお勧めします。ネットワーク接続が切断されると、再試行機能が組み込まれていないため、エラーが発生する場合があります。 このプロセスを実行するには、BCP コマンド ライン ユーティリティをインストールしておく必要があります。また、そのデータのスキーマが既に作成されている必要もあります。

上記のパラメーターを入力したら、[run migration] をクリックするだけです。2 つのパッケージのセットが指定した場所に生成されます。 移行元からフラット ファイルにデータをエクスポートするためにエクスポート ファイルを実行し、SQL Data Warehouse にデータをインポートするためにインポート ファイルを実行します。

## <a name="next-steps"></a>次のステップ
これで、一部のデータが移行されました。次に、[開発][develop]方法を確認します。

<!--Image references-->

<!--Article references-->
[migration documentation]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md

<!--Other Web references--> 
[Download Migration Utility]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip

