---
title: Azure SQL Database Machine Learning Services (プレビュー) の主な違い
description: このトピックでは、Azure SQL Database Machine Learning Services と R および SQL Server Machine Learning Services の主な違いについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: ee92b598625b1346cf87c661d1867cc1cb012b60
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486006"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>SQL Server の Machine Learning Services と Azure SQL Database の Machine Learning Services (プレビュー) の主な違い

Azure SQL Database Machine Learning Services と R の機能 (プレビュー) は、[SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) と似ています。 主な違いをいくつか以下に示します。

> [!IMPORTANT]
> Azure SQL Database の Machine Learning Services は、現在パブリック プレビュー期間です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="language-support"></a>言語のサポート

SQL Server では、[拡張性フレームワーク](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)を介して、R と Python がサポートされます。 SQL Database では、両方の言語がサポートされません。 主な違いは次のとおりです。

- SQL Database でサポートされている言語は R のみです。 現時点では、Python はサポートされていません。
- R のバージョンは 3.4.4 です。
- `sp_configure` で `external scripts enabled` を構成する必要はありません。 [サインアップ](sql-database-machine-learning-services-overview.md#signup)したら、SQL データベースに対して機械学習が有効になります。

## <a name="package-management"></a>パッケージの管理

R パッケージの管理とインストールの動作は、SQL Database と SQL Server で異なります。 これらの違いは次のとおりです。

- R パッケージは、[sqlmlutils](https://github.com/Microsoft/sqlmlutils) または [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql) を使用してインストールされます。
- パッケージでアウトバウンド ネットワークを呼び出すことはできません。 この制限は、SQL Server での [Machine Learning Services の既定のファイアウォール規則](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration)に似ていますが、SQL Database で変更することはできません。
- 外部ランタイム (Java など) に依存するか、インストールまたは使用のための OS API へのアクセスが必要なパッケージはサポートされません。

## <a name="writing-to-a-temporary-table"></a>一時テーブルへの書き込み

Azure SQL Database で RODBC を使用している場合、一時テーブルは `sp_execute_external_script` セッションの内部でも外部でも作成できません。 対処法として、[RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) と [ rxDataStep ](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (overwrite=FALSE、append="rows" に設定) を使用して、`sp_execute_external_script` クエリの前に作成されたグローバル一時テーブルに書き込みます。

## <a name="resource-governance"></a>リソース ガバナンス

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) と外部リソース プールを使用して、R リソースを制限することはできません。

パブリック プレビューの期間中は、R リソースは最大で SQL Database リソースの 20% に設定され、選択するサービス レベルによって異なります。 詳細については、「[Azure SQL Database purchasing models](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)」 (Azure SQL Database の購入モデル) を参照してください。
### <a name="insufficient-memory-error"></a>メモリ不足エラー

R に使用できるメモリが不足している場合は、エラー メッセージが表示されます。 一般的なエラー メッセージは次のとおりです。

- 'R' スクリプトのランタイムと通信できませんでした。要求 ID: *******。 'R' ランタイムの要件を確認してください
- 'sp_execute_external_script' に HRESULT 0x80004004 を指定して実行中に、'R' スクリプト エラーが発生しました。 ...外部スクリプト エラーが発生しました: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"
- 外部スクリプト エラーが発生しました:Error: cannot allocate vector of size.

メモリ使用量は、R スクリプトでの使用量と、実行されている並列クエリの数によって決まります。 上記のエラーが発生した場合は、問題を解決するために、データベースを上位のサービス レベルにスケーリングすることができます。

## <a name="next-steps"></a>次の手順

- 「[Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md) 」(R 付き Azure SQL Database 機械学習サービスと R (プレビュー)) の概要を参照してください。
- Azure SQL データベース機械学習サービス (プレビュー) を R で照会する方法について、「[クイック スタート ガイド](sql-database-connect-query-r.md)」を参照してください。
- シンプルな R スクリプトから始めるには、「[Azure SQL Database Machine Learning Services (プレビュー) で簡単な R スクリプトを作成して実行する](sql-database-quickstart-r-create-script.md)」を参照してください。
