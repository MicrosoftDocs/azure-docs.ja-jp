---
title: Azure Automation でのソース管理の統合
description: この記事では、Azure Automation での GitHub とのソース管理の統合について説明します。
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1ae61af2ad857068950e2b2cdb6f058a33f57186
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477568"
---
# <a name="source-control-integration-in-azure-automation"></a>Azure Automation でのソース管理の統合

ソース管理では、GitHub または Azure Repos のソース管理リポジトリ内のスクリプトを使用して、Automation アカウントの Runbook を最新の状態に維持することができます。 ソース管理により、チームとの共同作業、変更の追跡、Runbook の以前のバージョンへのロールバックを簡単に実行できるようになります。 たとえば、ソース管理を使用すると、開発、テスト、運用の Automation アカウントに対して、ソース管理内の異なるブランチを同期できます。 これにより、開発環境でテストされたコードを、運用の Automation アカウントに昇格することが容易になります。 Automation とソース管理の統合では、ソース管理リポジトリからの一方向の同期がサポートされます。

Azure Automation は、次の 3 種類のソース管理をサポートしています。

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>前提条件

* ソース管理リポジトリ (GitHub または Azure Repos)
* [実行アカウント](manage-runas-account.md)
* Automation アカウントに[最新の Azure モジュール](automation-update-azure-modules.md)があることを確認してください

> [!NOTE]
> ソース管理の同期ジョブは、ユーザーの Automation アカウントの下で実行され、その他の Automation ジョブと同じレートで課金されます。

## <a name="configure-source-control---azure-portal"></a>ソース管理を構成する - Azure portal

Automation アカウント内で、 **[ソース管理]** を選択し、 **[+ 追加]** をクリックします

![ソース管理の選択](./media/source-control-integration/select-source-control.png)

**[ソース管理の種類]** を選択し、 **[認証]** をクリックします。 ブラウザー ウィンドウが開き、サインインを求められます。プロンプトに従って認証を完了します。

**[Source Control Summary] (ソース管理の概要)** ページで情報を入力し、 **[保存]** をクリックします。 次の表に、指定できるフィールドの説明を示します。

|プロパティ  |説明  |
|---------|---------|
|ソース管理名     | ソース管理のためのフレンドリ名。 *この名前は、アルファベットと数字でのみ構成されている必要があります。*        |
|ソース管理の種類     | ソース管理のソースの種類。 使用できるオプションは次のとおりです。</br> GitHub</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
|リポジトリ     | リポジトリまたはプロジェクトの名前。 最初の 200 個のリポジトリが返されます。 リポジトリを検索するには、フィールドに名前を入力して、 **[Search on GitHub]\(GitHub で検索\)** をクリックします。|
|[Branch]\(ブランチ)     | ソース ファイルの抽出元のブランチ。 TFVC ソース管理の種類では、ブランチのターゲット設定は使用できません。          |
|フォルダー パス     | 同期する Runbook が含まれているフォルダー。例: /Runbooks </br>*指定されたフォルダー内の Runbook のみが同期されます。再帰はサポートされていません。*        |
|自動同期<sup>1</sup>     | ソース管理リポジトリでコミットが行われたときに、自動同期をオンまたはオフにします         |
|Runbook の発行     | **[オン]** に設定されている場合は、ソース管理から同期されたときに Runbook が自動的に発行されます。         |
|説明     | 詳細情報を入力するテキスト フィールド        |

<sup>1</sup> Azure Repos でソース管理の統合を構成する際に自動同期を有効にするには、プロジェクト管理者でなければなりません。

![ソース管理の概要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> ソース管理リポジトリのログインは、Azure portal のログインと異なる可能性があります。 ソース管理を構成する際は、ソース管理リポジトリの正しいアカウントでログインするようにしてください。 正しいかどうかが不明な場合、ブラウザーで新しいタブを開き、visualstudio.com または github.com からログアウトし、ソース管理にもう一度接続してみてください。

## <a name="configure-source-control---powershell"></a>ソース管理を構成する - PowerShell

PowerShell を使用して Azure Automation のソース管理を構成することもできます。 PowerShell コマンドレットを使用してソース管理を構成するには、個人用アクセス トークン (PAT) が必要です。 [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) を使用してソース管理の接続を作成します。 このコマンドレットでは、個人用アクセス トークンのセキュアな文字列が必要です。セキュアな文字列を作成する方法については、[ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6) に関するページをご覧ください。

### <a name="azure-repos-git"></a>Azure Repos (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>個人用アクセス トークンのアクセス許可

ソース管理には、個人用アクセス トークンに対するいくつかの最小限のアクセス許可が必要です。 次の表に、GitHub と Azure Repos で必要な最小限のアクセス許可を示します。

#### <a name="github"></a>GitHub

GitHub で個人用アクセス トークンを作成する方法について詳しくは、「[コマンドラインの個人用アクセス トークンの作成](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)」をご覧ください。

|Scope (スコープ)  |説明  |
|---------|---------|
|**レポジトリ**     |         |
|repo:status     | コミット状態へのアクセス         |
|repo_deployment      | デプロイ状態へのアクセス         |
|public_repo     | パブリック リポジトリへのアクセス         |
|**admin:repo_hook**     |         |
|write:repo_hook     | リポジトリ フックの書き込み         |
|read:repo_hook|リポジトリ フックの読み取り|

#### <a name="azure-repos"></a>Azure Repos

Azure Repos で個人用アクセス トークンを作成する方法について詳しくは、「[Authenticate access with personal access tokens (個人用アクセス トークンによるアクセスの認証)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)」をご覧ください。

|Scope (スコープ)  |
|---------|
|コード (読み取り)     |
|プロジェクトおよびチーム (読み取り)|
|ID (読み取り)      |
|ユーザー プロファイル (読み取り)     |
|作業項目 (読み取り)    |
|サービス接続 (読み取り、クエリ、管理)<sup>1</sup>    |

<sup>1</sup>サービス接続のアクセス許可は、自動同期を有効にした場合にのみ必要です。

## <a name="syncing"></a>同期中

**[ソース管理]** ページの表でソースを選択します。 **[Start Sync] (同期の開始)** をクリックして、同期プロセスを開始します。

**[Sync jobs] (同期ジョブ)** タブをクリックすると、現在の同期ジョブまたは以前の同期ジョブの状態を表示することができます。 **[ソース管理]** ドロップダウン リストで、ソース管理を選択します。

![同期状態](./media/source-control-integration/sync-status.png)

ジョブをクリックすると、ジョブの出力を表示することができます。 次に、ソース管理の同期ジョブからの出力例を示します。

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

**[ソース管理の同期ジョブの概要]** ページの **[すべてのログ]** を選択すると、追加のログを使用できます。 このような追加のログ エントリは、ソース管理を使用する際に発生する可能性がある問題のトラブルシューティングに役立ちます。

## <a name="disconnecting-source-control"></a>ソース管理の切断

ソース管理リポジトリから切断するには、Automation アカウントの **[アカウント設定]** の下の **[ソース管理]** を開きます。

削除するソース管理を選択します。 **Source Control Summary (ソース管理の概要)** ページで **削除** をクリックします。

## <a name="encoding"></a>エンコード

ソース管理リポジトリで複数のユーザーがさまざまなエディターを使用して Runbook を編集しているとき、エンコードの問題が発生する可能性があります。 このような状況では、正しくない文字が Runbook に含まれることがあります。 詳しくは、「[エンコード問題の一般的な原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)」をご覧ください。

## <a name="next-steps"></a>次の手順

Runbook の種類とそれらの利点や制限事項の詳細については、「 [Azure Automation の Runbook の種類](automation-runbook-types.md)
