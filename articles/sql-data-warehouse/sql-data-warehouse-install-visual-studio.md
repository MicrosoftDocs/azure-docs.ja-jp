---
title: SQL Data Warehouse 用に Visual Studio 2019 をインストールする | Microsoft Docs
description: Azure SQL Data Warehouse 用に Visual Studio と SQL Server Development Tools (SSDT) をインストールします
services: sql-data-warehouse
ms.custom: vs-azure
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e08d0a52903fb488359356707ecedfd81d49084a
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165069"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>SQL Data Warehouse 用の Visual Studio 2019 を始める
Visual Studio **2019** SQL Server Data Tools (SSDT) は次のことを可能にする単一ツールです。

- SQL Data Warehouse 用のアプリケーションを接続、クエリ実行、開発する 
- オブジェクト エクスプローラーを活用し、テーブル、ビュー、ストアド プロシージャなど、データ モデル内のあらゆるオブジェクトを視覚的に調べます。
- オブジェクトの T-SQL データ定義言語 (DDL) スクリプトを生成する
- 状態に基づく手法と SSDT データベース プロジェクトを利用し、データ ウェアハウスを開発する
- Git と Azure DevOps Repos のように、データベース プロジェクトとソース システムを統合する
- Azure DevOps [近日公開予定] など、自動化サーバーを利用し、継続的インテグレーション/デプロイのパイプラインを設定する

> [!NOTE]
> 現在、Visual Studio SSDT データベース プロジェクトはプレビュー段階にあります。 この機能の定期的な更新を受け取りたい場合、[UserVoice] で投票してください。

## <a name="install-visual-studio-2019-preview"></a>Visual Studio 2019 Preview をインストールする
Visual Studio をダウンロードしてインストールする場合は、[Visual Studio 2019 Preview をダウンロードする][]に関するページを参照してください。 インストール中、データ ストレージと処理ワークロードを選択します。 スタンドアロン SSDT インストールは Visual Studio 2019 では必須でなくなりました。

## <a name="reporting-issues-with-ssdt-visual-studio-2019-preview"></a>SSDT Visual Studio 2019 の問題を報告する (プレビュー)

SQL Data Warehouse で SSDT を使用しているときに発生した問題を報告するには、電子メール配信リスト <sqldwssdtpreview@service.microsoft.com> に電子メールで送信します。

## <a name="next-steps"></a>次の手順

これで、最新バージョンの SSDT がインストールされたので、SQL Data Warehouse に[接続][connect]できるようになりました。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Visual Studio 2019 Preview をダウンロードする]: https://visualstudio.microsoft.com/vs/preview/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
