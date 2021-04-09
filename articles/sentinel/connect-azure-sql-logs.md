---
title: Azure SQL データベースの診断と監査のログを Azure Sentinel に接続する
description: Azure SQL データベースの診断ログとセキュリティ監査ログを Azure Sentinel に接続する方法について説明します。
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 01/06/2021
ms.author: yelevin
ms.openlocfilehash: a3a09ceffc75e2d396d7bd7aeedd97b7f2b6ec2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99807735"
---
# <a name="connect-azure-sql-database-diagnostics-and-auditing-logs"></a>Azure SQL データベースの診断と監査のログを接続する

Azure SQL は、アップグレード、修正プログラムの適用、バックアップ、監視などのほとんどのデータベース管理機能をユーザーの介入なしで処理する、フル マネージドの PaaS (サービスとしてのプラットフォーム) データベース エンジンです。 

Azure SQL データベース コネクタを使用すると、データベースの監査と診断のログを Azure Sentinel にストリーミングできます。これにより、すべてのインスタンスのアクティビティを継続的に監視できます。

- 診断ログを接続すると、さまざまなデータ型のデータベース診断ログを Azure Sentinel ワークスペースに送信できます。

- 監査ログを接続すると、サーバー レベルですべての Azure SQL データベースからセキュリティ監査ログをストリーミングできます。

[Azure SQL データベースの監視](../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md)の詳細を確認してください。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- 監査ログを接続するには、Azure SQL Server の監査設定に対する読み取りと書き込みのアクセス許可が必要です。

## <a name="connect-to-azure-sql-database"></a>Azure SQL データベースに接続する
    
1. Azure Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーから **[Azure SQL Database]** を選択し、プレビュー ペインで **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで、接続できる 2 つのカテゴリのログを確認します。

### <a name="connect-diagnostics-logs"></a>診断ログを接続する

1. **[診断ログ]** で、 **[Enable diagnostics logs on each of your Azure SQL databases manually]\(各 Azure SQL データベースの診断ログを手動で有効にする\)** を展開します。

1. **[Open Azure SQL >]\(Azure SQL を開く >\)** リンクを選択して **[Azure SQL]** リソース ブレードを開きます。

1. **(省略可能)** データベース リソースを簡単に検索するには、上部にあるフィルター バーで **[フィルターの追加]** を選択します。
    1. **[フィルター]** ドロップダウン リストで、 **[リソースの種類]** を選択します。
    1. **[値]** ドロップダウン リストで、 **[すべて選択]** を選択解除し、 **[SQL データベース]** を選択します。
    1. **[適用]** をクリックします。
    
1. Azure Sentinel に送信する診断ログがあるデータベース リソースを選択します。

    > [!NOTE]
    > 収集するログがあるデータベース リソースごとに、この手順からこのプロセスを繰り返す必要があります。

1. 選択したデータベースのリソース ページで、ナビゲーション メニューの **[監視]** の下にある **[診断設定]** を選択します。

    1. 表の下部にある **[+ 診断設定の追加]** リンクを選択します。

    1. **[診断設定]** 画面で、 **[診断設定の名前]** フィールドに名前を入力します。
    
    1. **[宛先の詳細]** 列で、 **[Log Analytics ワークスペースに送信する]** チェック ボックスをオンにします。 2 つの新しいフィールドが下に表示されます。 関連する **サブスクリプション** と **Log Analytics ワークスペース** (Azure Sentinel が存在する場所) を選択します。

    1. **[カテゴリの詳細]** 列で、取り込むログとメトリックの種類のチェック ボックスをオンにします。 **[ログ]** と **[メトリック]** の両方で、使用可能なすべての種類を選択することをお勧めします。

    1. ページの最上部で **[保存]** を選択します。

- または、提供されている **PowerShell スクリプト** を使用して診断ログを接続することもできます。
    1. **[診断ログ]** で **[Enable by PowerShell script]\(PowerShell スクリプトで有効にする\)** を展開します。

    1. コード ブロックをコピーし、PowerShell に貼り付けます。

### <a name="connect-audit-logs"></a>監査ログを接続する

1. **[Auditing logs (preview)]\(監査ログ (プレビュー)\)** で **[Enable auditing logs on all Azure SQL databases (at the server level)]\(すべての Azure SQL データベースの監査ログを有効にする (サーバー レベル)\)** を展開します。

1. **[Open Azure SQL >]\(Azure SQL を開く >\)** リンクを選択して **[SQL サーバー]** リソース ブレードを開きます。

1. Azure Sentinel に送信する監査ログがある SQL サーバーを選択します。

    > [!NOTE]
    > 収集するログがあるサーバー リソースごとに、この手順からこのプロセスを繰り返す必要があります。

1. 選択したサーバーのリソース ページで、ナビゲーション メニューの **[セキュリティ]** の下にある **[監査]** を選択します。

    1. **[Enable Azure SQL Auditing]\(Azure SQL の監査を有効にする\)** トグルを **[オン]** に切り替えます。

    1. **[監査ログの保存先]** で **[Log Analytics (プレビュー)]** を選択します。
    
    1. 表示されるワークスペースの一覧から、お使いのワークスペース (Azure Sentinel が存在する場所) を選択します。

    1. ページの最上部で **[保存]** を選択します。

- または、提供されている **PowerShell スクリプト** を使用して診断ログを接続することもできます。
    1. **[監査ログ]** で **[Enable by PowerShell script]\(PowerShell スクリプトで有効にする\)** を展開します。

    1. コード ブロックをコピーし、PowerShell に貼り付けます。


> [!NOTE]
>
> この特定のデータ コネクタを使用すると、過去 2 週間のある時点でデータが取り込まれた場合にのみ、接続状態インジケーター (データ コネクタ ギャラリーのカラー ストライプとデータ型の名前の横にある接続アイコン) が "*接続済み*" (緑) と表示されます。 データ インジェストがないまま 2 週間が経過すると、コネクタは切断済みと表示されます。 さらに多くのデータが入ってくると、"*接続済み*" 状態が返されます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure SQL データベースの診断と監査のログを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。