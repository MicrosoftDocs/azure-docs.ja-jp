---
title: サードパーティ アプリケーションおよび Azure Synapse Analytics との互換性の問題
description: サードパーティ アプリケーションによって Azure Synapse で検出される可能性がある既知の問題について説明します
services: synapse-analytics
author: periclesrocha
ms.service: synapse-analytics
ms.topic: troubleshooting
ms.subservice: ''
ms.date: 11/18/2020
ms.author: procha
ms.reviewer: jrasnick
ms.openlocfilehash: a1031656eaa5125d07ae078773379270b26625e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98121381"
---
# <a name="compatibility-issues-with-third-party-applications-and-azure-synapse-analytics"></a>サードパーティ アプリケーションおよび Azure Synapse Analytics との互換性の問題

SQL Server 用に構築されたアプリケーションは、Azure Synapse 専用 SQL プールとシームレスに連携します。 ただし、SQL Server で一般的に使用されている機能と言語要素が Azure Synapse で使用できなかったり、異なる動作をしたりする場合があります。

この記事では、Azure Synapse Analytics でサードパーティ アプリケーションを使用する場合に発生する可能性のある既知の問題を示します。 

## <a name="tableau-error-an-attempt-to-complete-a-transaction-has-failed-no-corresponding-transaction-found"></a>Tableau エラー:"トランザクションを完了しようとして失敗しました。 対応するトランザクションが見つかりませんでした"

Azure Synapse 専用 SQL プール バージョン 10.0.11038.0 以降では、ストアド プロシージャ呼び出しを行う一部の Tableau クエリで、次のようなエラー メッセージが表示されて失敗することがあります。" **[Microsoft][ODBC Driver 17 for SQL Server][SQL Server]111214;トランザクションを完了しようとして失敗しました。対応するトランザクションが見つかりませんでした。** "

### <a name="cause"></a>原因

これは、ODBC および JDBC ドライバーによって自動的に呼び出される新しいシステム ストアド プロシージャが導入されたことに起因する Azure Synapse 専用 SQL プールの問題です。 これらのシステム ストアド プロシージャの 1 つが実行に失敗した場合、開いているトランザクションが中止される可能性があります。 この問題は、クライアント アプリケーション ロジックによって発生する可能性があります。

### <a name="solution"></a>解決策
Azure Synapse 専用 SQL プールに接続された Tableau を使用するときにこの特定の問題が発生しているお客様は、SQL 接続で FMTONLY を YES に設定する必要があります。 Tableau Desktop と Tableau Server では、Tableau Data source Customization (TDC) ファイルを使用して、Tableau がこのパラメーターをドライバーに確実に渡すようにする必要があります。  

> [!NOTE] 
> Microsoft では、サード パーティ ツールに対するサポートは提供していません。 この解決策が Tableau Desktop 2020.3.2 で動作することはテストされていますが、この回避策はご自身の技量で使用してください。
>

* [Tableau Desktop で TDC ファイルを使用してグローバルなカスタマイズを行う方法については、Tableau Desktop のドキュメントを参照してください。](https://help.tableau.com/current/pro/desktop/en-us/odbc_customize.htm)
* [Tableau Server で TDC ファイルを使用してグローバルなカスタマイズを行う方法については、「Tableau Server での .TDC ファイルの使用」を参照してください。](https://kb.tableau.com/articles/howto/using-a-tdc-file-with-tableau-server)

下の例は、FMTONLY=YES パラメーターを SQL 接続文字列に渡す Tableau TDC ファイルを示しています。

```json
<connection-customization class='azure_sql_dw' enabled='true' version='18.1'>
    <vendor name='azure_sql_dw' />
    <driver name='azure_sql_dw' />
    <customizations>        
        <customization name='odbc-connect-string-extras' value='UseFMTONLY=yes' />
    </customizations>
</connection-customization>
```
TDC ファイルの使用方法の詳細については、Tableau サポートにお問い合わせください。 

## <a name="see-also"></a>関連項目

* [Azure Synapse Analytics の専用 SQL プールの T-SQL 言語要素。](./sql-data-warehouse-reference-tsql-language-elements.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
* [Azure Synapse Analytics の専用 SQL プール用にサポートされている T-SQL ステートメント。](./sql-data-warehouse-reference-tsql-statements.md)