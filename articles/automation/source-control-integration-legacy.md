---
title: Azure Automation でソース管理の統合を使用する - 従来
description: この記事では、ソース管理の統合を使用する方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4dedbcf58e76b8c969f8607db6922e87a85f08e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97591875"
---
# <a name="use-source-control-integration-in-azure-automation---legacy"></a>Azure Automation でソース管理の統合を使用する - 従来

> [!NOTE]
> ソース管理の新しいエクスペリエンスがあります。 新しいエクスペリエンスの詳細については、[ソース管理 (プレビュー)](source-control-integration.md) に関するページを参照してください。

ソース管理の統合により、Automation アカウントの Runbook を GitHub のソース管理リポジトリに関連付けることができます。 ソース管理により、チームとの共同作業、変更の追跡、Runbook の以前のバージョンへのロールバックを簡単に実行できるようになります。 たとえば、開発、テスト、または運用の Automation アカウントに異なるブランチをソース管理で同期できるようになり、開発環境内でテストされたコードを運用の Automation アカウントに昇格することが容易になります。

ソース管理することで、Azure Automation からソース管理にコードをプッシュしたり、ソース管理から Azure Automation に Runbook をプルしたりできます。 この記事では、Azure Automation 環境内でソース管理を設定する方法について説明します。 まず、Azure Automation を GitHub リポジトリにアクセスするよう構成し、ソース管理の統合を使用することで実行できるさまざまな操作について、順を追って説明します。 

> [!NOTE]
> ソース管理では、[PowerShell ワークフロー Runbook](automation-runbook-types.md#powershell-workflow-runbooks) および [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) のプル操作とプッシュ操作がサポートされていますが、 [グラフィカル Runbook](automation-runbook-types.md#graphical-runbooks) はサポートされません。

## <a name="configure-source-control"></a>ソース管理を構成する

Automation アカウントのソース管理を構成するには、2 つの簡単な手順が必要です。ただし、既に GitHub アカウントを持っている場合、必要な手順は 1 つだけです。 

### <a name="create-a-github-repository"></a>GitHub リポジトリを作成する

GitHub アカウントと、Azure Automation にリンクするリポジトリが既にある場合は、既存のアカウントにサインインし、次の手順 2. から開始してください。 それ以外の場合は、[GitHub](https://github.com/) に移動し、新しいアカウントにサインアップして、[新しいリポジトリを作成](https://help.github.com/articles/create-a-repo/)します。

### <a name="set-up-source-control"></a>ソース管理を設定する

1. Azure Portal の [Automation アカウント] ページの **[アカウント設定]** で、 **[ソース管理]** をクリックします。

2. [ソース管理] ページが開きます。ここで、GitHub アカウントの詳細を構成できます。 構成するパラメーターを次に示します。  

   | **パラメーター** | **説明** |
   |:--- |:--- |
   | ソースの選択 |ソースを選択します。 現時点では、 **GitHub** のみがサポートされています。 |
   | 承認 |**[承認する]** ボタンをクリックすると、GitHub リポジトリへのアクセスが Azure Automation に許可されます。 既に別のウィンドウで GitHub アカウントにログインしている場合は、そのアカウントの資格情報が使用されます。 承認が成功すると、ページの **[承認プロパティ]** の下にご利用の GitHub ユーザー名が表示されます。 |
   | リポジトリの選択 |使用可能なリポジトリの一覧から GitHub リポジトリを選択します。 |
   | ブランチの選択 |使用可能なブランチの一覧からブランチを選択します。 ブランチを作成したことがない場合は、**メイン** ブランチのみが表示されます。 |
   | Runbook フォルダーのパス |Runbook フォルダーのパスは、コードをプッシュまたはプルする GitHub リポジトリのパスを指定します。 これは、 **/foldername/subfoldername** 形式で指定する必要があります。 Runbook フォルダーのパスにある Runbook のみが Automation アカウントと同期されます。 Runbook フォルダーのパスのサブフォルダーにある Runbook は同期 **されません** 。 リポジトリですべての Runbook を同期するには、 **/** を使います。 |
3. たとえば、**PowerShellScripts** という名前のリポジトリに **RootFolder** という名前のフォルダーがあり、このフォルダー内に **SubFolder** という名前のフォルダーがあるとします。 次の文字列を使用して、各フォルダー レベルで同期できます。

   1. **リポジトリ** から Runbook を同期するには、Runbook フォルダーのパスは **/** となります。
   2. **RootFolder** から Runbook を同期するには、Runbook フォルダーのパスは **/RootFolder** となります。
   3. **SubFolder** から Runbook を同期するには、Runbook フォルダーのパスは **/RootFolder/SubFolder** となります。
4. 構成したパラメーターは、[ソース管理の設定] ページに表示されます。  

    ![設定が表示される [ソース管理] ページ](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. **[OK]** をクリックすると、ソース管理の統合は、お使いの Automation アカウント用に構成されるため、GitHub 情報を使用して更新する必要があります。 この部分をクリックすると、ソース管理の同期ジョブの履歴がすべて表示されます。  

    ![現在構成されているソース管理構成の値](media/source-control-integration-legacy/automation-RepoValues.png)
6. ソース管理の設定後、Automation アカウントに 2 つの[変数アセット](./shared-resources/variables.md)が作成されます。 加えて、承認されたアプリケーションが GitHub アカウントに追加されます。

   * 次に示すように、 **Microsoft.Azure.Automation.SourceControl.Connection** 変数には、接続文字列の値が格納されます。  

     | **パラメーター** | **Value** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |String |
     | `Value` |{"Branch":\<*Your branch name*>,"RunbookFolderPath":\<*Runbook folder path*>,"ProviderType":\<*has a value 1 for GitHub*>,"Repository":\<*Name of your repository*>,"Username":\<*Your GitHub user name*>} |

   * **Microsoft.Azure.Automation.SourceControl.OauthToken** 変数には、OAuthToken のセキュリティで保護され暗号化された値が格納されます。  

     |**パラメーター**            |**Value** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![ソース管理の変数を表示するウィンドウ](media/source-control-integration-legacy/automation-Variables.png)  

   * **Automation ソース管理** は、承認済みのアプリケーションとして GitHub アカウントに追加されます。 アプリケーションを表示するには、GitHub のホーム ページから **[プロファイル]**  > 、 **[設定]**  > 、 **[アプリケーション]** の順に移動します。 このアプリケーションにより、Azure Automation は GitHub リポジトリを Automation アカウントに同期できます。  

     ![GitHub のアプリケーション設定](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="use-source-control-in-automation"></a>Automation でソース管理を使用する

Runbook をチェックインすると、Azure Automation で Runbook に対して行った変更をソース管理リポジトリにプッシュできます。 Runbook のチェックイン手順は次のとおりです。

1. Automation アカウントから、[テキスト形式の Runbook を新しく作成](./learn/automation-tutorial-runbook-textual.md)するか、[テキスト形式の既存の Runbook を編集](automation-edit-textual-runbook.md)します。 この Runbook は、PowerShell ワークフローまたは PowerShell スクリプト Runbook のどちらでもかまいません。  
2. ご利用の Runbook を編集して保存したら、[編集] ページの **[チェックイン]** をクリックします。  

    ![GitHub へのチェックイン ボタンを表示するウィンドウ](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Azure Automation からチェックインすると、ソース管理に現在存在しているコードが上書きされます。 チェックインするための対応する Git コマンド ライン命令は、**git add + git commit + git push** です。  

3. **[チェックイン]** をクリックすると、確認メッセージが表示されるので、 **[はい]** をクリックして続行します。  

    ![ソース コード管理へのチェックインを確認するダイアログ ボックス](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. チェックインにより、ソース管理 Runbook の **Sync-MicrosoftAzureAutomationAccountToGitHubV1** が開始されます。 この Runbook は、GitHub に接続し、変更を Azure Automation からリポジトリにプッシュします。 チェックインしたジョブの履歴を表示するには、 **[ソース管理の統合]** タブに戻り、[リポジトリの同期] ページをクリックして開きます。 このページには、すべてのソース管理ジョブが表示されます。 表示するジョブを選択し、クリックして詳細を表示します。  

    ![同期ジョブの結果を表示するウィンドウ](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > ソース管理 Runbook は、特別な Automation Runbook であり、表示または編集することはできません。 これらは Runbook の一覧に表示されませんが、ジョブの一覧には同期ジョブが表示されます。

5. 変更された Runbook の名前は、入力パラメーターとしてチェックイン Runbook に送信されます。 [リポジトリの同期] ページで Runbook を展開すると、[ジョブの詳細を表示](automation-runbook-execution.md#job-statuses)できます。  

    ![同期ジョブの入力を表示するウィンドウ](media/source-control-integration-legacy/automation-CheckinInput.png)
6. ジョブが完了したら GitHub リポジトリを更新して、変更を表示します。  リポジトリにコミットすると、"**Updated *Runbook Name* in Azure Automation** (Azure Automation で <Runbook 名> が更新されました)" というコミット メッセージが表示されます。  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>ソース管理から Azure Automation に Runbook を同期する

[リポジトリの同期] ページの [同期] ボタンを使用すると、リポジトリの Runbook フォルダーのパスにあるすべての Runbook を Automation アカウントにプルできます。 同一リポジトリを複数の Automation アカウントに同期できます。 Runbook の同期手順は次のとおりです。

1. ソース管理を設定した Automation アカウントで、[ソース管理の統合] の [リポジトリの同期] ページを開き、 **[同期]** をクリックします。確認メッセージが表示されるので、 **[はい]** をクリックして続行します。  

    ![すべての Runbook が同期されることを伝えるメッセージと [同期] ボタン](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. 同期により、**Sync-MicrosoftAzureAutomationAccountFromGitHubV1** Runbook が開始されます。これは、GitHub に接続し、変更をリポジトリから Azure Automation にプルします。 [リポジトリ同期] ページにこのアクション向けの新しいジョブが表示されます。 同期ジョブの詳細を表示するには、[ジョブの詳細] ページをクリックして開きます。  

    ![GitHub リポジトリの同期ジョブの結果を表示するウィンドウ](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > ソース管理から同期すると、ソース管理に現在含まれている **すべての** Runbook のうち、Automation アカウントに現在存在するドラフト バージョンの Runbook が上書きされます。 同期するための対応する Git コマンド ライン命令は、**git pull** です。

![中断されたソース管理同期ジョブのすべてのログを表示するウィンドウ](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnect-source-control"></a>ソース管理を切断する

GitHub アカウントから切断するには、[リポジトリの同期] ページを開き、 **[切断]** をクリックします。 ソース管理を切断すると、それまでに同期された Runbook はそのまま Automation アカウントに保持されますが、[リポジトリの同期] ページは有効になりません。  

  ![ソース管理を切断するための [切断] ボタンを表示するウィンドウ](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>次のステップ

* Azure Automation におけるソース管理の統合については、[Azure Automation: Azure Automation でのソース管理の統合](https://azure.microsoft.com/blog/azure-automation-source-control-13/)に関する記事を参照してください。  
* Runbook のソース管理を Visual Studio Online と統合する方法については、[Azure Automation: Visual Studio Online を使用した Runbook ソース管理の統合](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)に関する記事を参照してください。  
