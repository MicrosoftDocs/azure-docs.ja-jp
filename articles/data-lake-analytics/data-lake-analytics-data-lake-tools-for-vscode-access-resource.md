---
title: Data Lake Tools を使用したリソースへのアクセス
description: Azure Data Lake Tools を使用して Azure Data Lake Analytics リソースにアクセスする方法について説明します。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 02/09/2018
ms.openlocfilehash: d04f108b45070b27c4ff9ed833e8fb77b74cd597
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96754533"
---
# <a name="accessing-resources-with-azure-data-lake-tools"></a>Azure Data Lake Tools を使用したリソースへのアクセス

VS Code 内で Azure Data Tools のコマンドまたはアクションを使用して Azure Data Lake Analytics リソースに簡単にアクセスできます。

## <a name="integrate-with-azure-data-lake-analytics-through-a-command"></a>コマンドを使用して Azure Data Lake Analytics と統合する

Azure Data Lake Analytics リソースにアクセスして、アカウントの一覧表示、メタデータへのアクセス、分析ジョブの表示を実行できます。

### <a name="to-list-the-azure-data-lake-analytics-accounts-under-your-azure-subscription"></a>Azure サブスクリプション内の Azure Data Lake Analytics アカウントを一覧表示するには

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**ADL:List Accounts**」と入力します。 **[出力]** ウィンドウにアカウントが表示されます。

### <a name="to-access-azure-data-lake-analytics-metadata"></a>Azure Data Lake Analytics メタデータにアクセスするには

1. Ctrl + Shift + P キーを押し、「**ADL:List Tables**」と入力します。
2. いずれかの Data Lake Analytics アカウントを選択します。
3. いずれかの Data Lake Analytics データベースを選択します。
4. いずれかのスキーマを選択します。 テーブルの一覧が表示されます。

### <a name="to-view-azure-data-lake-analytics-jobs"></a>Azure Data Lake Analytics ジョブを表示するには

1. コマンド パレットを開いて (Ctrl + Shift + P)、 **[ADL:Show Job]** を選択します。
2. Data Lake Analytics アカウントまたはローカル アカウントを選択します。
3. アカウントのジョブ一覧が表示されるまで待ちます。
4. ジョブ一覧からジョブを選択します。 Data Lake Tools により右側のウィンドウにジョブ ビューが開かれ、VS Code 出力情報を表示します。

    ![Job 一覧](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-show-job.png)

## <a name="integrate-with-azure-data-lake-store-through-a-command"></a>コマンドを使用して Azure Data Lake Store と統合する

Azure Data Lake Store 関連コマンドを使用して次のことを実行できます：

- [Azure Data Lake Store のリソースを閲覧する](#list-the-storage-path)
- [Azure Data Lake Store ファイルをプレビューする](#preview-the-storage-file)
- VS Code で Azure Data Lake Store に直接ファイルをアップロードする
- VS Code で Azure Data Lake Store から直接ファイルをダウンロードする

### <a name="list-the-storage-path"></a>ストレージ パスの一覧表示

### <a name="to-list-the-storage-path-through-the-command-palette"></a>コマンド パレットを使用してストレージ パスを一覧表示するには

1. スクリプト エディターを右クリックし、 **[ADL:List Path]** を選択します。
2. 一覧でフォルダーを選択するか、 **[Enter a path]** または **[Browse from root path]** をクリックします。 (ここでは、例として **[Enter a path]** を使用しています。)
3. Data Lake Analytics アカウントを選択します。
4. ストレージ フォルダーのパスを参照もしくは入力します (/output/ など)。  

コマンド パレットに入力に基づくパス情報の一覧が表示されます。

![Storage パスの結果](./media/data-lake-analytics-data-lake-tools-for-vscode/list-storage-path.png)

相対パスの一覧を表示するさらに便利な方法は、ショートカット メニューを使用することです。

### <a name="to-list-the-storage-path-through-the-shortcut-menu"></a>ショートカット メニューを使用してストレージ パスを一覧表示するには

パス文字列を右クリックし、 **[List Path]** を選択します。

![ショートカット メニューの「パスの一覧を表示する」](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-right-click-path.png)

### <a name="preview-the-storage-file"></a>ストレージ ファイルのプレビュー

1. スクリプト エディターを右クリックし、 **[ADL:Preview File]** を選択します。
2. Data Lake Analytics アカウントを選択します。
3. Azure Storage ファイルのパス (/output/SearchLog.txt など) を入力します。

このファイルは、VS Code で開きます。

![ストレージファイルのプレビュー手順と結果](./media/data-lake-analytics-data-lake-tools-for-vscode/preview-storage-file.png)

ファイルをプレビューする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスの右クリック メニューを使用する方法があります。

### <a name="upload-a-file-or-folder"></a>ファイルまたはフォルダーをアップロード

1. スクリプト エディターを右クリックし、 **[ファイルのアップロード]** または **[フォルダーをアップロード]** を選択します。
2. 1 つ以上のファイル ( **[ファイルのアップロード]** を選択した場合) またはフォルダー全体 ( **[フォルダーをアップロード]** を選択した場合) を選択します。 **[アップロード]** を選択します。
3. 一覧でストレージフォルダーを選択するか、 **[Enter a path]** または **[Browse from root path]** をクリックします。 (ここでは、例として **[Enter a path]** を使用しています。)
4. Data Lake Analytics アカウントを選択します。
5. ストレージ フォルダーのパスを参照もしくは入力します (/output/ など)。
6. **[Choose Current Folder]** をクリックして、アップロード先を指定します。

![ファイルまたはフォルダーをアップロードする手順と結果](./media/data-lake-analytics-data-lake-tools-for-vscode/upload-file.png)

ファイルをストレージにアップロードする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスのショートカット メニューを使用する方法があります。

[アップロードの状態の監視](#check-storage-tasks-status)ができます。

### <a name="download-a-file"></a>ファイルをダウンロードする

ファイルは、コマンド **ADL:Download File** または **ADL:Download File (Advanced)** を使用してダウンロードできます。

### <a name="to-download-a-file-through-the-adl-download-file-advanced-command"></a>ADL:Download File (Advanced) コマンドを使用してファイルをダウンロードするには

1. スクリプト エディターを右クリックし、 **[Download File (Advanced)]\(ファイルのダウンロード (詳細)\)** を選択します。
2. VS Code に、JSON ファイルが表示されます。 ファイルのパスを入力して、複数のファイルを同時にダウンロードできます。 手順が **[出力]** ウィンドウに表示されます。 ファイルのダウンロードを続行するには、Ctrl + S キーを押して JSON ファイルを保存します。

    ![ファイルのダウンロード用のパスつき JSON ファイル](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-files.png)

**[出力]** ウィンドウに、ファイル ダウンロードの状態が表示されます。

![ダウンロードの状態が表示された出力ウィンドウ](./media/data-lake-analytics-data-lake-tools-for-vscode/download-multi-file-result.png)

[ダウンロードの状態の監視](#check-storage-tasks-status)ができます。

### <a name="to-download-a-file-through-the-adl-download-file-command"></a>ADL:Download File コマンドを使用してファイルをダウンロードするには

1. スクリプト エディターを右クリックし、 **[ファイルのダウンロード]** を選択してから、 **[フォルダーの選択]** ダイアログ ボックスでダウンロード先フォルダーを選択します。

1. 一覧でフォルダーを選択するか、 **[Enter a path]** または **[Browse from root path]** をクリックします。 (ここでは、例として **[Enter a path]** を使用しています。)

1. Data Lake Analytics アカウントを選択します。

1. ストレージ フォルダーのパス (/output/ など) を参照または入力し、 ダウンロードするファイルを選択します。

![ファイルをダウンロードする手順と結果](./media/data-lake-analytics-data-lake-tools-for-vscode/download-file.png)

ストレージファイルをダウンロードする別の方法として、スクリプト エディターでファイルの完全なパスまたは相対パスのショートカット メニューを使用する方法があります。

[ダウンロードの状態の監視](#check-storage-tasks-status)ができます。

### <a name="check-storage-tasks-status"></a>ストレージ タスクの状態の確認

アップロードとダウンロードの状態は、ステータス バーに表示されます。 ステータス バーを選択すると、**出力** タブに状態が表示されます。

![ステータス バーと出力の詳細](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-status.png)

## <a name="integrate-with-azure-data-lake-analytics-from-the-explorer"></a>エクスプローラーから Azure Data Lake Analytics と統合する

ログインすると、 **[AZURE DATALAKE]** の下の左側のウインドウに、現在の Azure アカウントに含まれるサブスクリプションがすべて一覧表示されます。

![Data Lake エクスプローラー](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer.png)

### <a name="data-lake-analytics-metadata-navigation"></a>Data Lake Analytics メタデータへのナビゲーション

Azure サブスクリプションの展開。 **U-SQL データベース** ノードの下で、U SQL データベースを参照することができ、**スキーマ**、**資格情報**、**アセンブリ**、**テーブル**、および **インデックス** などのフォルダーが表示されます。

### <a name="data-lake-analytics-metadata-entity-management"></a>Azure Data Lake Analytics メタデータのエンティティ

**U-SQL データベース** を展開します。 データベース、スキーマ、テーブル、テーブル型、インデックス、または統計を作成するには、対応するノードを右クリックして、ショートカット メニューの **作成するスクリプト** を選択します。 開いているスクリプトページで、必要に応じてスクリプトを編集します。 その後、これを右クリックし、 **[ADL:Submit Job]** を選択して、ジョブを送信します。

アイテムの作成が完了したら、ノードを右クリックし、**最新の情報に更新** を選択してアイテムを表示します。 右クリックして **[削除]** を選択して、アイテムを削除することもできます。

![Data Lake エクスプローラーのショートカットメニューの「作成するスクリプト」コマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create.png)

![新しい項目のスクリプトページ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-code-explorer-script-create-snippet.png)

### <a name="data-lake-analytics-assembly-registration"></a>Data Lake Analytics アセンブリの登録

**[アセンブリ]** ノードを右クリックして、 **[アセンブリを登録]** をクリックしてアセンブリを関連するデータベースに登録できます。

![アセンブリノードのショートカット メニューの「アセンブリの登録」コマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/datalake-explorer-register-assembly.png)

## <a name="integrate-with-azure-data-lake-store-from-the-explorer"></a>エクスプローラーから Azure Data Lake Store と統合する

**Data Lake Store** を参照する：

- フォルダー ノードを右クリックし、ショートカット メニューの **[更新]** 、 **[削除]** 、 **[アップロード]** 、 **[フォルダーをアップロード]** 、 **[相対パスのコピー]** および **[完全パスのコピー]** コマンドを実行できます。

   ![Data Lake エクスプ ローラーのフォルダー ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-folder-menu.png)

- ファイル ノードを右クリックし、ショートカット メニューのコマンド **[プレビュー]** 、 **[ダウンロード]** 、 **[削除]** 、 **[EXTRACT スクリプトの作成]** (CSV、TSV、および TXT ファイルでのみ使用可能)、 **[相対パスのコピー]** および **[完全パスのコピー]** を実行できます。

   ![Data Lake エクスプ ローラーのファイル ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/storage-account-extract.png)

## <a name="integrate-with-azure-blob-storage-from-the-explorer"></a>エクスプローラーから Azure BLOB ストレージ と統合する

BLOB ストレージの参照:

- BLOB コンテナー ノードを右クリックし、ショートカット メニューのコマンド **[最新の情報に更新]** 、 **[BLOB コンテナーの削除]** 、および **[BLOB のアップロード]** を実行できます。

   ![Blob ストレージの blob コンテナー ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-blob-container-node.png)

- フォルダー ノードを右クリックし、ショートカット メニューのコマンド **[最新の情報に更新]** および **[BLOB のアップロード]** を実行できます。

   ![BLOB ストレージの フォルダー ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/blob-storage-folder-node.png)

- ファイル ノードを右クリックし、ショートカット メニューのコマンド **[プレビュー/編集]** 、 **[ダウンロード]** 、 **[削除]** 、 **[EXTRACT スクリプトの作成]** (CSV、TSV、および TXT ファイルでのみ使用可能)、 **[相対パスのコピー]** および **[完全パスのコピー]** を実行できます。

    ![BLOB ストレージの ファイル ノードのショートカット メニューのコマンド](./media/data-lake-analytics-data-lake-tools-for-vscode/create-extract-script-from-context-menu-2.png)

## <a name="open-the-data-lake-explorer-in-the-portal"></a>ポータルで、Data Lake エクスプ ローラーを開く

1. Ctrl + Shift + P キーを押してコマンド パレットを開きます。
2. 「**Open Web Azure Storage Explorer**」と入力するか、スクリプト エディターで相対パスまたは完全なパスを右クリックして、 **[Open Web Azure Storage Explorer]** を選択します。
3. Data Lake Analytics アカウントを選択します。

Data Lake Tools によって、Azure Portal に Azure Storage パスが表示されます。 そのパスを見つけて Web からファイルをプレビューすることができます。

## <a name="additional-features"></a>その他の機能

Data Lake Tools for VS Code では、以下の各機能がサポートされています。

- **IntelliSense オートコンプリート**:キーワード、メソッド、変数などの項目の付近にポップアップ ウィンドウで修正候補が表示されます。 オブジェクトの種類ごとに異なるアイコンで表されます：

  - Scala データ型
  - 複合データ型
  - 組み込みの UDT
  - .NET コレクションおよびクラス
  - C# 式
  - 組み込みの C# UDF、UDO、UDAAG
  - U-SQL 関数
  - U-SQL ウィンドウ化関数

    ![IntelliSense オブジェクトの種類](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-objects.png)

- **Data Lake Analytics メタデータでの IntelliSense オートコンプリート**:Data Lake Tools によって Data Lake Analytics メタデータ情報がローカルにダウンロードされます。 IntelliSense 機能は、Data Lake Analytics メタデータからオブジェクトを自動的に作成します。 これらのオブジェクトには、データベース、スキーマ、テーブル、ビュー、テーブル値関数、プロシージャ、および C# アセンブリが含まれます。

  ![IntelliSense のメタデータ](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-auto-complete-metastore.png)

- **IntelliSense エラー マーカー**:Data Lake Tools によって U-SQL と C# の編集エラーが下線で示されます。
- **構文の強調表示**:Data Lake Tools では色を使用して、変数、キーワード、データ型、関数などの項目を区別します。

    ![さまざまな色で表示された構文](./media/data-lake-analytics-data-lake-tools-for-vscode/data-lake-tools-for-vscode-syntax-highlights.png)

> [!NOTE]
> Azure Data Lake Tools for Visual Studio バージョン 2.3.3000.4 以降にアップグレードすることをお勧めしています。 以前のバージョンはダウンロードできなくなり、非推奨になっています。  

## <a name="next-steps"></a>次のステップ

- [VS Code で Azure Data Lake Analytics の Python、R、および C　Sharp を使用した U-SQL の開発](data-lake-analytics-u-sql-develop-with-python-r-csharp-in-vscode.md)
- [Visual Studio Code を使用した U-SQL ローカル実行とローカル デバッグ](data-lake-tools-for-vscode-local-run-and-debug.md)
- [チュートリアル:Azure Data Lake Analytics の使用を開始する](data-lake-analytics-get-started-portal.md)
- [チュートリアル:Data Lake Tools for Visual Studio を使用して U-SQL スクリプトを開発する](data-lake-analytics-data-lake-tools-get-started.md)