---
title: Azure Automation 監視タスクを使用して更新されたファイルを追跡する
description: この記事では、Azure Automation アカウントで監視タスクを作成してフォルダーに作成された新しいファイルを監視する方法を説明します。
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 38963a8e1bfdbde50439ed871aa33e9aaa830d35
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185655"
---
# <a name="track-updated-files-with-a-watcher-task"></a>監視タスクによる更新されたファイルの追跡

Azure Automation では、PowerShell Runbook で監視タスクを使用して、イベントを検索し、アクションをトリガーします。 監視タスクには、監視とアクションという 2 つの部分が含まれています。 監視 Runbook は、監視タスクで定義された間隔で実行され、データをアクション Runbook に出力します。 

> [!NOTE]
> Azure China Vianet 21 では、監視タスクはサポートされていません。

> [!IMPORTANT]
> 2020 年 5 月から、イベントの監視、定期的なタスクのスケジュール、およびアクションのトリガーを行う方法として Azure Logic Apps の使用がサポートされるようになりました。 「[Azure Logic Apps で自動化された定期的なタスク、プロセス、ワークフローのスケジュールを設定して実行する](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)」を参照してください。

このチュートリアルでは、ディレクトリへの新しいファイルの追加を監視する監視タスクの作成手順について説明します。 学習内容は次のとおりです。

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
* 監視およびアクションの Runbook と監視タスクを保持する、[Automation アカウント](./index.yml)。
* 監視タスクが実行される、[Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)。
* PowerShell Runbook。 PowerShell ワークフロー Runbook は、監視タスクではサポートされていません。

## <a name="import-a-watcher-runbook"></a>監視 Runbook をインポートする

このチュートリアルでは、**Watch-NewFile** という名前の監視 Runbook を使用して、ディレクトリ内の新規ファイルを検索します。 この監視 Runbook は、フォルダー内のファイルに対する既知の最終書き込み時刻を取得し、この基準時刻より新しいファイルを監視します。

このインポート プロセスは [PowerShell ギャラリー](https://www.powershellgallery.com)で実行できます。

1. [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd) のギャラリー ページに移動します。
2. **[Azure Automation]** タブで、 **[Deploy to Azure Automation]\(Azure Automation にデプロイ\)** をクリックします。

次の手順を使用して、この Runbook をポータルから Automation アカウントにインポートすることもできます。

1. Automation アカウントを開き、[Runbook] ページをクリックします。
2. **[ギャラリーの参照]** をクリックします。
3. **監視 Runbook** を検索し、 **[Watcher runbook that looks for new files in a directory]\(ディレクトリ内の新規ファイルを検索する監視 Runbook\)** を選択して **[インポート]** をクリックします。
  ![UI での Automation Runbook のインポート](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Runbook の名前と説明を入力して **[OK]** をクリックし、Automation アカウントにこの Runbook をインポートします。
5. **[編集]** を選択し、 **[公開]** をクリックします。 プロンプトが表示されたら、 **[はい]** をクリックして Runbook を公開します。

## <a name="create-an-automation-variable"></a>Automation 変数を作成する

[Automation 変数](./shared-resources/variables.md)を使用して、上述の Runbook が各ファイルから読み取って保存するタイムスタンプを格納します。

1. **[共有リソース]** の **[変数]** を選択し、 **[+ 変数の追加]** をクリックします。
1. 名前に「Watch-NewFileTimestamp」と入力します。
1. 型には DateTime を選択します。
1. **[作成]** をクリックして Automation 変数を作成します。

## <a name="create-an-action-runbook"></a>アクション Runbook を作成する

監視タスクで、アクション Runbook を使用して、監視 Runbook から渡されたデータを操作します。 **Process-NewFile** という名前の事前定義済みのアクション Runbook を [PowerShell ギャラリー](https://www.powershellgallery.com)からインポートする必要があります。 

アクション Runbook を作成するには、次のようにします。

1. [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf) のギャラリー ページに移動します。
2. **[Azure Automation]** タブで、 **[Deploy to Azure Automation]\(Azure Automation にデプロイ\)** をクリックします。

この Runbook を Azure portal から Automation アカウントにインポートすることもできます。

1. Automation アカウントに移動して、 **[プロセスの自動化]** で **[Runbook]** を選択します。
1. **[ギャラリーの参照]** をクリックします。
1. **監視アクション**を検索して **[Watcher action that processes events triggered by a watcher runbook]\(監視 Runbook でトリガーされたイベントを処理する監視アクション\)** を選択し、 **[インポート]** をクリックします。
  ![UI でのアクション Runbook のインポート](media/automation-watchers-tutorial/importsourceaction.png)
1. Runbook の名前と説明を入力して **[OK]** をクリックし、Automation アカウントにこの Runbook をインポートします。
1. **[編集]** を選択し、 **[公開]** をクリックします。 プロンプトが表示されたら、 **[はい]** をクリックして Runbook を公開します。

## <a name="create-a-watcher-task"></a>監視タスクを作成する

この手順では、前述のセクションで定義した監視 Runbook とアクション Runbook を参照する監視タスクを構成します。

1. Automation アカウントに移動して、 **[プロセスの自動化]** で **[監視タスク]** を選択します。
1. [監視タスク] ページを選択して、 **[+ 監視タスクの追加]** をクリックします。
1. 名前として「**WatchMyFolder**」を入力します。

1. **[監視を構成]** を選択し、**Watch-NewFile** Runbook を選びます。

1. 各パラメーターの値を次のように入力します。

   * **FOLDERPATH** - 新しいファイルが作成される、ハイブリッド Runbook ワーカー上のフォルダー (たとえば、**d:\examplefiles**)。
   * **EXTENSION** - 構成の拡張機能。 すべてのファイル拡張子を処理するために、空白のままにします。
   * **RECURSE** - 再帰的操作。 この値は既定値のままにします。
   * **RUN SETTINGS** - Runbook を実行するための設定。 ハイブリッド ワーカーを選択します。

1. **[OK]** をクリックし、[監視] ページに戻るように**選択**します。
1. **[アクションを構成]** を選択し、**Process-NewFile** Runbook を選びます。
1. 各パラメーターの値を次のように入力します。

   * **EVENTDATA** - イベント データ。 空白のままにします。 監視 Runbook からデータが渡されます。
   * **Run Settings** - Runbook を実行するための設定。 この Runbook は Azure Automation で実行するので、Azure のままにします。

1. **[OK]** をクリックし、[監視] ページに戻るように**選択**します。
1. **[OK]** をクリックして監視タスクを作成します。

![UI での監視アクションの構成](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>監視をトリガーする

監視タスクが期待どおりに動作することを確実にするために、次の説明に従ってテストを実行する必要があります。 

1. ハイブリッド Runbook ワーカーにリモート接続します。 
2. **PowerShell** を開き、テスト対象のフォルダーにテスト ファイルを作成します。

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

1. Automation アカウントに移動して、 **[プロセスの自動化]** で **[監視タスク]** を選択します。
1. 監視タスク **WatchMyFolder** を選択します。
1. **[ストリーム]** の **[監視ストリームを表示]** をクリックして、監視によって新しいファイルが検出され、アクション Runbook が開始されたことを確認します。
1. アクション Runbook ジョブを表示するために、 **[監視アクション ジョブを表示]** をクリックします。 各ジョブを選択すると、ジョブの詳細を確認できます。

   ![UI 上の監視アクション ジョブ](media/automation-watchers-tutorial/WatcherActionJobs.png)

新しいファイルが検出された場合に想定される出力は、次の例のようになります。

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>次のステップ

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
> [PowerShell Runbook を作成する](learn/automation-tutorial-runbook-textual-powershell.md)
