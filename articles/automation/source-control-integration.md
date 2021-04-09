---
title: Azure Automation でソース管理の統合を使用する
description: この記事では、Azure Automation のソース管理を他のリポジトリと同期させる方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 281da27ce95649e85dae5d0795bb743f21fdb578
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102631746"
---
# <a name="use-source-control-integration"></a>ソース管理の統合を使用する

 Azure Automation でのソース管理の統合により、ソース管理リポジトリからの一方向の同期がサポートされます。 ソース管理では、GitHub または Azure Repos のソース管理リポジトリ内のスクリプトを使用して、Automation アカウントの Runbook を最新の状態に維持することができます。 この機能により、開発環境でテストされたコードを、運用の Automation アカウントに昇格することが容易になります。

 ソース管理の統合によって、チームとの共同作業、変更の追跡、Runbook の以前のバージョンへのロールバックを容易に行えるようになります。 たとえば、ソース管理を使用すると、開発、テスト、運用の Automation アカウントに対して、ソース管理内の異なるブランチを同期できます。

## <a name="source-control-types"></a>ソース管理の種類

Azure Automation は、次の 3 種類のソース管理をサポートしています。

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>前提条件

* ソース管理リポジトリ (GitHub または Azure Repos)
* [実行アカウント](automation-security-overview.md#run-as-accounts)
* [`AzureRM.Profile` モジュール](/powershell/module/azurerm.profile/)を Automation アカウントにインポートする必要があります。 対応する Az モジュール (`Az.Accounts`) は、Automation ソース管理では動作しないことに注意してください。

> [!NOTE]
> ソース管理の同期ジョブは、ユーザーの Automation アカウントのもとで実行され、その他の Automation ジョブと同じレートで課金されます。

## <a name="configure-source-control"></a>ソース管理を構成する

このセクションでは、Automation アカウントのソース管理を構成する方法について説明します。 Azure portal または PowerShell のいずれかを使用できます。

### <a name="configure-source-control-in-azure-portal"></a>Azure portal でソース管理を構成する

Azure portal を使用してソース管理を構成するには、次の手順に従います。

1. Automation アカウント内で、 **[ソース管理]** を選択し、 **[追加]** をクリックします。

    ![ソース管理の選択](./media/source-control-integration/select-source-control.png)

2. **[ソース管理の種類]** を選択し、 **[認証]** をクリックします。

3. ブラウザー ウィンドウが開き、サインインが求められます。 指示に従って認証を完了します。

4. [ソース管理の概要] ページのフィールドを使用して、以下で定義されているソース管理プロパティを入力します。 完了したら、 **[保存]** をクリックします。

    |プロパティ  |説明  |
    |---------|---------|
    |ソース管理名     | ソース管理のためのフレンドリ名。 この名前は、アルファベットと数字でのみ構成されている必要があります。        |
    |ソース管理の種類     | ソース管理機構の種類。 使用できるオプションは次のとおりです。</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |リポジトリ     | リポジトリまたはプロジェクトの名前。 最初の 200 個のリポジトリが取得されます。 リポジトリを検索するには、フィールドに名前を入力して、 **[Search on GitHub]\(GitHub で検索\)** をクリックします。|
    |[Branch]\(ブランチ)     | ソース ファイルの抽出元のブランチ。 TFVC ソース管理の種類では、ブランチのターゲット設定は使用できません。          |
    |フォルダー パス     | 同期する Runbook を含むフォルダー ( **/Runbooks** など)。 指定されたフォルダー内の Runbook のみが同期されます。 再帰はサポートされていません。        |
    |自動同期<sup>1</sup>     | ソース管理リポジトリでコミットが行われたときに、自動同期をオンまたはオフにする設定。        |
    |Runbook の発行     | Runbook がソース管理からの同期後に自動的に発行される場合はオン、それ以外の場合はオフの設定。           |
    |説明     | ソース管理に関する追加の詳細を指定するテキスト。        |

    <sup>1</sup> Azure Repos でソース管理の統合を構成する際に自動同期を有効にするには、プロジェクト管理者でなければなりません。

   ![ソース管理の概要](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> ソース管理リポジトリのログインは、Azure portal でのログインと異なる場合があります。 ソース管理を構成する際は、ソース管理リポジトリの正しいアカウントでログインするようにしてください。 不明点がある場合は、ブラウザーで新しいタブを開き、**dev.azure.com**、**visualstudio.com**、または **github.com** からログアウトし、ソース管理への再接続を試みてください。

### <a name="configure-source-control-in-powershell"></a>PowerShell でソース管理を構成する

PowerShell を使用して Azure Automation のソース管理を構成することもできます。 この操作に PowerShell コマンドレットを使用するには、個人用アクセス トークン (PAT) が必要です。 [New-AzAutomationSourceControl](/powershell/module/az.automation/new-azautomationsourcecontrol) コマンドレットを使用してソース管理接続を作成します。 このコマンドレットでは、PAT に対するセキュリティで保護された文字列が必要です。 セキュリティで保護された文字列を作成する方法については、「[ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring)」を参照してください。

次のサブセクションは、PowerShell による GitHub、Azure Repos (Git)、および Azure Repos (TFVC) のソース管理接続の作成について説明しています。

#### <a name="create-source-control-connection-for-github"></a>GitHub のソース管理接続を作成する

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Azure Repos (Git) のソース管理接続を作成する

> [!NOTE]
> Azure Repos (Git) では、以前の形式で使用されていた **visualstudio.com** ではなく、**dev.azure.com** にアクセスする URL を使用します。 古い URL 形式の `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` は非推奨ですが、まだサポートされています。 新しい形式が推奨されています。


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Azure Repos (TFVC) のソース管理接続を作成する

> [!NOTE]
> Azure Repos (TFVC) では、以前の形式で使用されていた **visualstudio.com** ではなく、**dev.azure.com** にアクセスする URL を使用します。 古い URL 形式の `https://<accountname>.visualstudio.com/<projectname>/_versionControl` は非推奨ですが、まだサポートされています。 新しい形式が推奨されています。

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>個人用アクセス トークン (PAT) のアクセス許可

ソース管理には、PAT に対するいくつかの最小限のアクセス許可が必要です。 次のサブセクションは、GitHub と Azure Repos で必要な最小限のアクセス許可を示します。

##### <a name="minimum-pat-permissions-for-github"></a>GitHub の PAT に対する最小限のアクセス許可

次の表は、GitHub で必要な PAT の最小限のアクセス許可を定義したものです。 GitHub で PAT を作成する方法について詳しくは、「[コマンドライン用の個人アクセス トークンを作成する](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)」をご覧ください。

|Scope  |説明  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | コミット状態へのアクセス         |
|`repo_deployment`      | デプロイ状態へのアクセス         |
|`public_repo`     | パブリック リポジトリへのアクセス         |
|`repo:invite` | リポジトリの招待へのアクセス |
|`security_events` | セキュリティ イベントの読み取りと書き込み |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | リポジトリ フックの書き込み         |
|`read:repo_hook`|リポジトリ フックの読み取り|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Azure Repos の PAT に対する最小限のアクセス許可

次のリストは、Azure Repos で必要な PAT の最小限のアクセス許可を定義したものです。 Azure Repos で PAT を作成する方法について詳しくは、「[個人用アクセス トークンによるアクセスの認証](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)」をご覧ください。

| Scope  |  アクセスの種類  |
|---------| ----------|
| `Code`      | Read  |
| `Project and team` | Read |
| `Identity` | Read     |
| `User profile` | Read     |
| `Work items` | Read    |
| `Service connections` | 読み取り、クエリの実行、および管理<sup>1</sup>    |

<sup>1</sup> `Service connections` アクセス許可が必要なのは、自動同期を有効にした場合のみです。

## <a name="synchronize-with-source-control"></a>ソース管理と同期させる

以下の手順に従って、ソース管理と同期します。

1. [ソース管理] ページの表から、ソースを選択します。

2. **[Start Sync] (同期の開始)** をクリックして、同期プロセスを開始します。

3. **[同期ジョブ]** タブをクリックして、現在の同期ジョブまたは以前の同期ジョブの状態を表示します。

4. **[ソース管理]** ドロップダウン メニューで、ソース管理機構を選択します。

    ![同期状態](./media/source-control-integration/sync-status.png)

5. ジョブをクリックすると、ジョブの出力を表示することができます。 次に、ソース管理の同期ジョブからの出力例を示します。

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzEnvironment.

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

    ==================================================================

    ```

6. [ソース管理の同期ジョブの概要] ページの **[すべてのログ]** を選択すると、追加のログを使用できます。 このような追加のログ エントリは、ソース管理を使用する際に発生する可能性がある問題のトラブルシューティングに役立ちます。

## <a name="disconnect-source-control"></a>ソース管理を切断する

ソース管理リポジトリから切断するには:

1. Automation アカウントの **[アカウント設定]** で、 **[ソース管理]** を開きます。

2. 削除するソース管理機構を選択します。

3. [ソース管理の概要] ページで **[削除]** をクリックします。

## <a name="handle-encoding-issues"></a>エンコードに関する問題を処理する

ソース管理リポジトリで複数のユーザーがさまざまなエディターを使用して Runbook を編集していると、エンコードの問題が発生する可能性があります。 この状況について詳しくは、「[エンコード問題の一般的な原因](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)」をご覧ください。

## <a name="update-the-pat"></a>PAT を更新する

現時点では、ソース管理の PAT を更新するために Azure portal を使用できません。 PAT が有効期限切れになったり取り消されたりしたら、以下のいずれかの方法で、新しいアクセス トークンを使用してソース管理を更新できます。

* [REST API](/rest/api/automation/sourcecontrol/update) を使用する。
* [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) コマンドレットを使用する。

## <a name="next-steps"></a>次のステップ

* Azure Automation におけるソース管理の統合については、[Azure Automation: Azure Automation でのソース管理の統合](https://azure.microsoft.com/blog/azure-automation-source-control-13/)に関する記事を参照してください。  
* Visual Studio Codespaces を使用した Runbook ソース管理の統合方法については、次の記事を参照してください。[Azure Automation:
