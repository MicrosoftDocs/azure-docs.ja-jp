---
title: SQL Data Warehouse 用に Visual Studio と SSDT をインストールする | Microsoft Docs
description: Azure SQL Data Warehouse 用に Visual Studio と SQL Server Development Tools (SSDT) をインストールします
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: consume
ms.date: 04/05/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f67c2a4547ee923e5c1b49302c38693e9ffe87c4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59262212"
---
# <a name="install-visual-studio-and-ssdt-for-sql-data-warehouse"></a>SQL Data Warehouse 用に Visual Studio と SSDT をインストールする
SQL Data Warehouse のアプリケーションを開発するには、Visual Studio 2017 を使用します。 現在 Visual Studio 2019 SSDT は、SQL Data Warehouse ではサポートされていません。 

SSDT がインストールされている Visual Studio を使用すると、SQL Server オブジェクト エクスプローラーを使用して、SQL Data Warehouse のテーブル、ビュー、ストアド プロシージャ、その他の多くのオブジェクトを視覚的に調査したり、クエリを実行したりすることができます。

> [!NOTE]
> SQL Data Warehouse では、Visual Studio データベース プロジェクトがまだサポートされていません。 この機能の定期的な更新を受け取りたい場合、[UserVoice] で投票してください。
> 
> 

## <a name="step-1-install-visual-studio"></a>手順 1:Visual Studio のインストール
Visual Studio をダウンロードしてインストールするには、以下のリンクをクリックしてください。 既に Visual Studio 2013 以降がインストールされている場合は、手順 2. に進んで SSDT をインストールします。

1. [Visual Studio をダウンロードします][]。
2. MSDN の [Visual Studio のインストール][Installing Visual Studio] ガイドに従ってインストールし、既定の構成を選択します。

## <a name="step-2-install-ssdt"></a>手順 2:SSDT のインストール
SSDT for Visual Studio をインストールするには、まず、次の手順に従って Visual Studio 内から SSDT の更新プログラムをチェックします。

1. Visual Studio で、**[ツール]** / **[拡張機能と更新プログラム…]**  / **更新プログラム**をクリックします。
2. **[製品の更新プログラム]** を選択し、**[データベース ツール用の Microsoft SQL Server 更新プログラム]** を探します。

更新プログラムが見つからない場合は、最新のバージョンが既にインストールされています。  SSDT がインストールされていることを確認するには、**[ヘルプ]** / **[Microsoft Visual Studio のバージョン情報]** をクリックして表示される一覧の中から [SQL Server Data Tools] を探します。 インストールするオプションを Visual Studio から使用できない場合は、代わりに [SSDT のダウンロード][SSDT Download] ページにアクセスし、SSDT を手動でダウンロードしてインストールします。

## <a name="next-steps"></a>次の手順
これで、最新バージョンの SSDT がインストールされたので、SQL Data Warehouse に[接続][connect]できるようになりました。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Visual Studio をダウンロードします]: https://www.visualstudio.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
