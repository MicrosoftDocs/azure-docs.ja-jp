---
title: Machine Learning Services (プレビュー) の主な違い
description: このトピックでは、Azure SQL Managed Instance の Machine Learning Services と SQL Server Machine Learning Services の主な違いについて説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: carlrab, davidph
manager: cgronlun
ms.date: 05/27/2020
ms.openlocfilehash: f267b155fe21e5dee1a7c488c999fe194ed38eb1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86504120"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL Managed Instance と SQL Server の Machine Learning Services の主な違い

[Azure SQL Managed Instance (プレビュー) の Machine Learning Services](machine-learning-services-overview.md) の機能は、[SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) とほぼ同じです。 次に主な違いをいくつか示します。

> [!IMPORTANT]
> Azure SQL Managed Instance の Machine Learning Services は現在パブリック プレビュー段階です。 サインアップするには、「[プレビューのサインアップ](machine-learning-services-overview.md#signup)」を参照してください。

## <a name="preview-limitations"></a>プレビューの制限事項

プレビュー段階では、サービスには次の制限があります。

- ループバック接続が機能しません (「[Python または R スクリプトからの SQL Server へのループバック接続](/sql/machine-learning/connect/loopback-connection)」を参照してください)。
- 外部リソース プールはサポートされていません。
- Python と R のみがサポートされています。 Java などの外部言語を追加することはできません。
- [メッセージ パッシング インターフェイス (MPI)](https://docs.microsoft.com/message-passing-interface/microsoft-mpi) を使用するシナリオはサポートされていません。

サービス レベル目標 (SLO) の更新の場合は、SLO を更新し、サポート チケットを作成し、R および Python の専用のリソース制限を再び有効にしてください。

## <a name="language-support"></a>言語のサポート

SQL Managed Instance と SQL Server の Machine Learning Services は、Python と R の両方の[拡張機能フレームワーク](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)をサポートしています。 主な違いは次のとおりです。

- Python と R の初期バージョンは、SQL Managed Instance と SQL Server の Machine Learning Services で異なります。

  | システム               | Python | R     |
  |----------------------|--------|-------|
  | SQL Managed Instance | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- `sp_configure` で `external scripts enabled` を構成する必要はありません。 プレビューに[サインアップ](machine-learning-services-overview.md#signup)すると、Azure SQL Managed Instance に対する機械学習が有効になります。

## <a name="packages"></a>パッケージ

Python および R のパッケージ管理は、SQL Managed Instance と SQL Server で動作が異なります。 これらの違いは次のとおりです。

- パッケージでアウトバウンド ネットワークを呼び出すことはできません。 この制限は、SQL Server での [Machine Learning Services の既定のファイアウォール規則](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration)に似ていますが、SQL Managed Instance で変更することはできません。
- 外部ランタイム (Java など) に依存するか、インストールまたは使用のための OS API へのアクセスが必要なパッケージはサポートされません。

Python および R パッケージの管理の詳細については、以下を参照してください。

- [Python パッケージ情報の取得](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)
- [R パッケージ情報の取得](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=azure/sql-database/context/ml-context&view=sql-server-ver15)

## <a name="resource-governance"></a>リソース管理

[Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) と外部リソース プールを使用して、R リソースを制限することはできません。

パブリック プレビューの期間中は、R リソースは最大で SQL Managed Instance リソースの 20% に設定され、選択するサービス レベルによって異なります。 詳細については、「[Azure SQL Database purchasing models](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)」 (Azure SQL Database の購入モデル) を参照してください。

### <a name="insufficient-memory-error"></a>メモリ不足エラー

R に使用できるメモリが不足している場合は、エラー メッセージが表示されます。 一般的なエラー メッセージは次のとおりです。

- 'R' スクリプトのランタイムと通信できませんでした。要求 ID: *******。 'R' ランタイムの要件を確認してください
- 'sp_execute_external_script' に HRESULT 0x80004004 を指定して実行中に、'R' スクリプト エラーが発生しました。 ...外部スクリプト エラーが発生しました: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"
- 外部スクリプト エラーが発生しました:Error: cannot allocate vector of size.

メモリ使用量は、R スクリプトでの使用量と、実行されている並列クエリの数によって決まります。 上記のエラーが発生した場合は、問題を解決するために、データベースを上位のサービス レベルにスケーリングすることができます。

## <a name="next-steps"></a>次のステップ

- 詳細については、「[Azure SQL Managed Instance の Machine Learning Services](machine-learning-services-overview.md)」を参照してください。
- Machine Learning Services で Python を使用する方法については、[Python スクリプトの実行](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)に関するページを参照してください。
- Machine Learning Services で R を使用する方法については、[R スクリプトの実行](https://docs.microsoft.com/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=sql-server-ver15)に関するページを参照してください。
