---
title: Azure Automation アカウントで監視タスクを作成する
description: Azure Automation アカウントで監視タスクを作成してフォルダーに作成された新しいファイルを監視する方法を説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: bee414ada61e2cfcf7609b02ef1da7323a0fe0e3
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606924"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>ローカル マシンでのファイル変更を追跡する Azure Automation 監視タスクを作成する

Azure Automation では、PowerShell Runbook で監視タスクを使用して、イベントを監視し、アクションをトリガーします。 このチュートリアルでは、ディレクトリへの新しいファイルの追加を監視する監視タスクの作成手順について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 監視 Runbook をインポートする
> * Automation 変数を作成する
> * アクション Runbook を作成する
> * 監視タスクを作成する
> * 監視をトリガーする
> * 出力を確認する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* Azure のサブスクリプション。 まだお持ちでない場合は、[MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)か、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)にサインアップしてください。
* 監視およびアクションの Runbook と監視タスクを保持する、[Automation アカウント](automation-offering-get-started.md)。
* 監視タスクが実行される、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)。

> [!NOTE]
> Azure 中国では、監視タスクはサポートされていません。

## <a name="import-a-watcher-runbook"></a>監視 Runbook をインポートする

このチュートリアルでは、**Watch-NewFile** という名前の監視 Runbook を使用して、ディレクトリ内の新規ファイルを検索します。 この監視 Runbook は、フォルダー内のファイルに対する既知の最終書き込み時刻を取得し、この基準時刻より新しいファイルを監視します。 このステップでは、この Runbook を Automation アカウントにインポートします。

1. Automation アカウントを開き、**[Runbook]** ページをクリックします。
2. **[ギャラリーの参照]** ボタンをクリックします。
3. "監視 Runbook" を検索し、**[Watcher runbook that looks for new files in a directory]\(ディレクトリ内の新規ファイルを検索する監視 Runbook\)** を選択して **[インポート]** をクリックします。
  ![UI での Automation Runbook のインポート](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Runbook の名前と説明を入力して **[OK]** を選択し、Automation アカウントにこの Runbook をインポートします。
1. **[編集]** を選択し、**[公開]** をクリックします。 プロンプトが表示されたら、**[はい]** を選択して Runbook を公開します。

## <a name="create-an-automation-variable"></a>Automation 変数を作成する

[Automation 変数](automation-variables.md)を使用して、上述の Runbook が各ファイルから読み取って保存するタイムスタンプを格納します。

1. **[共有リソース]** の **[変数]** を選択し、**[+ 変数の追加]** を選びます。
1. 名前に「Watch-NewFileTimestamp」と入力します。
1. 型には DateTime を選択します。
1. **[作成]** ボタンをクリックします。 これで、Automation 変数が作成されます。

## <a name="create-an-action-runbook"></a>アクション Runbook を作成する

監視タスクで、アクション Runbook を使用して、監視 Runbook から渡されたデータを操作します。 PowerShell ワークフロー Runbook は監視タスクではサポートされておらず、PowerShell Runbook を使用する必要があります。 この手順では、"Process-NewFile" という名前の事前定義済みのアクション Runbook をインポートして更新します。

1. Automation アカウントに移動して、**[プロセスの自動化]** カテゴリで **[Runbook]** を選択します。
1. **[ギャラリーの参照]** ボタンをクリックします。
1. "監視アクション" を検索して **[Watcher action that processes events triggered by a watcher runbook]\(監視 Runbook でトリガーされたイベントを処理する監視アクション\)** を選択し、**[インポート]** をクリックします。
  ![UI でのアクション Runbook のインポート](media/automation-watchers-tutorial/importsourceaction.png)
1. Runbook の名前と説明を入力して **[OK]** を選択し、Automation アカウントにこの Runbook をインポートします。
1. **[編集]** を選択し、**[公開]** をクリックします。 プロンプトが表示されたら、**[はい]** を選択して Runbook を公開します。

## <a name="create-a-watcher-task"></a>監視タスクを作成する

監視タスクには、2 つの要素が含まれています。 監視とアクションです。 監視の実行は、監視タスクに定義された間隔で行われます。 監視 Runbook からのデータは、アクション Runbook に渡されます。 この手順では、上記の手順で定義した監視 Runbook とアクション Runbook を参照する監視タスクを構成します。

1. Automation アカウントに移動して、**[プロセスの自動化]** カテゴリで **[監視タスク]** を選択します。
1. [監視タスク] ページを選択して、**[+ 監視タスクの追加]** ボタンをクリックします。
1. 名前に「WatchMyFolder」と入力します。

1. **[監視を構成]** を選択し、**Watch-NewFile** Runbook を選びます。

1. 各パラメーターの値を次のように入力します。

   * **FOLDERPATH** - 新しいファイルが作成される、ハイブリッド ワーカー上のフォルダー。 d:\examplefiles
   * **EXTENSION** - すべてのファイル拡張子を処理するために、空白のままにします。
   * **RECURSE** - この値は既定値のままにします。
   * **RUN SETTINGS** - ハイブリッド ワーカーを選択します。

1. [OK] をクリックし、[監視] ページに戻るように選択します。
1. **[アクションを構成]** を選択し、“Process-NewFile” Runbook を選びます。
1. パラメーターに次の値を入力します。

   * **EVENTDATA** - 空白のままにします。 監視 Runbook からデータが渡されます。  
   * **Run Settings** - この Runbook は Automation サービスで実行するので、Azure のままにします。

1. **[OK]** をクリックし、[監視] ページに戻るように選択します。
1. **[OK]** をクリックして監視タスクを作成します。

![UI での監視アクションの構成](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>監視をトリガーする

監視が想定通りに動作するかテストするには、テスト ファイルを作成する必要があります。

ハイブリッド ワーカーにリモート接続します。 **PowerShell** を開き、テスト対象のフォルダーにテスト ファイルを作成します。
  
```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

次の例で、想定される出力を示します。

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>出力を確認する

1. Automation アカウントに移動して、**[プロセスの自動化]** カテゴリで **[監視タスク]** を選択します。
1. 監視タスク "WatchMyFolder" を選択します。
1. **[ストリーム]** の **[監視ストリームを表示]** をクリックして、監視が新しいファイルを検出し、アクション Runbook を開始したかを確認します。
1. **[監視アクション ジョブを表示]** をクリックして、アクション Runbook ジョブを表示します。 各ジョブを選択すると、ジョブの詳細を確認できます。

   ![UI 上の監視アクション ジョブ](media/automation-watchers-tutorial/WatcherActionJobs.png)

新しいファイルが検出された場合に想定される出力は、次の例のようになります。

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 監視 Runbook をインポートする
> * Automation 変数を作成する
> * アクション Runbook を作成する
> * 監視タスクを作成する
> * 監視をトリガーする
> * 出力を確認する

次のリンクから、独自の Runbook を作成する方法についてご覧ください。

> [!div class="nextstepaction"]
> [初めての PowerShell Runbook](automation-first-runbook-textual-powershell.md)

