---
title: Synapse Studio でのソース管理
description: Azure Synapse Studio でソース管理を構成する方法について説明します
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 3564609d869bef090f0a3db5e6040ba0f5ad80b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98796969"
---
# <a name="source-control-in-azure-synapse-studio"></a>Azure Synapse Studio でのソース管理

既定では、Azure Synapse Studio は Synapse サービスに対して直接作成を行います。 Synapse Studio では、ソース管理に Git を使用したコラボレーションが必要である場合、ワークスペースを Git リポジトリ、Azure DevOps、または GitHub に関連付けることができます。 

この記事では、Git リポジトリが有効になっている Synapse ワークスペースを構成し、それで作業する方法について説明します。 また、いくつかのベスト プラクティスとトラブルシューティング ガイドも提供します。

> [!NOTE]
> Azure Synapse Studio Git 統合は、Azure Government クラウドでは利用できません。

## <a name="configure-git-repository-in-synapse-studio"></a>Synapse Studio で Git リポジトリを構成する 

Synapse Studio を起動した後、ワークスペースで Git リポジトリを構成できます。 Synapse Studio ワークスペースは、一度に 1 つの Git リポジトリにのみ関連付けることができます。 

### <a name="configuration-method-1-global-bar"></a>構成方法 1: グローバル バー

Synapse Studio グローバル バーで、 **[Synapse Live]\(Synapse ライブ\)** ドロップダウン メニューを選択し、 **[Set up code repository]\(コード リポジトリの設定\)** を選択します。

![コード リポジトリ設定を作成から構成する](media/configure-repo-1.png)

### <a name="configuration-method-2-manage-hub"></a>構成方法 2:管理ハブ

Synapse Studio の管理ハブに移動します。 **[ソース管理]** セクションで **[Git 構成]** を選択します。 接続されているリポジトリがない場合は、 **[構成]** をクリックします。

![コード リポジトリ設定を管理ハブから構成する](media/configure-repo-2.png)

> [!NOTE]
> ワークスペースの共同作業者、所有者、またはより高いレベルのロールが付与されたユーザーは、Azure Synapse Studio で Git リポジトリの構成、設定編集、および切断を行うことができます。 

ワークスペースで Azure DevOps または GitHub Git リポジトリに接続できます。

## <a name="connect-with-azure-devops-git"></a>Azure DevOps Git に接続する 

ソース管理、コラボレーション、バージョン管理などのために､Synapse ワークスペースを Azure DevOps リポジトリに関連付けることができます。 Azure DevOps リポジトリがない場合は、[こちらの指示](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)に従ってリポジトリ リソースを作成します。

### <a name="azure-devops-git-repository-settings"></a>Azure DevOps Git リポジトリの設定

Git リポジトリに接続するときに、最初に Azure DevOps Git としてリポジトリの種類を選択し、ドロップダウン リストから Azure AD テナントを 1 つ選択し、 **[続行]** をクリックします。

![コード リポジトリ設定の構成](media/connect-with-azuredevops-repo-selected.png)

構成ペインには、次の Azure DevOps Git の設定が表示されます。

| 設定 | 説明 | 値 |
|:--- |:--- |:--- |
| **リポジトリの種類** | Azure Repos コード リポジトリの種類。<br/> | Azure DevOps Git または GitHub |
| **Azure Active Directory** | Azure AD テナントの名前。 | `<your tenant name>` |
| **Azure DevOps アカウント** | Azure Repos 組織の名前｡ Azure Repos 組織名は`https://{organization name}.visualstudio.com`で確認することができます｡ [Azure Repos 組織にサインイン](https://www.visualstudio.com/team-services/git/)し、お使いの Visual Studio プロファイルにアクセスして、リポジトリとプロジェクトを確認してください。 | `<your organization name>` |
| **ProjectName** | Azure Repos プロジェクトの名前。 Azure Repos プロジェクトの名前は `https://{organization name}.visualstudio.com/{project name}` で確認することができます｡ | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos コード リポジトリの名前｡ Azure Repos プロジェクトには、プロジェクトの拡大に合わせてソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内に既にある既存のリポジトリを使用できます。 | `<your Azure Repos code repository name>` |
| **コラボレーション ブランチ** | 発行に使用する Azure Repos コラボレーション ブランチ。 既定では `master` です。 別のブランチからのリソースを発行する場合は、この設定を変更します。 既存のブランチを選択することも、新しいものを作成することもできます | `<your collaboration branch name>` |
| **ルート フォルダー** | Azure Repos コラボレーション ブランチのルート フォルダー。 | `<your root folder name>` |
| **[Import existing resources to repository]\(既存のリソースをリポジトリにインポートする\)** | 既存のリソースを Synapse Studio から Azure Repos Git リポジトリにインポートするかどうかを指定します。 このチェックボックスをオンにすると、ワークスペース リソース (プールを除く) が JSON 形式で関連する Git リポジトリにインポートされます。 この操作により、各リソースが個別にエクスポートされます。 このチェックボックスがオンになっていない場合、既存のリソースはインポートされません。 | オン (既定値) |
| **[Import resource into this branch]\(リソースをこのブランチにインポートする\)** | リソース (SQL スクリプト、ノートブック、Spark ジョブ定義、データセット、データフローなど) をどのブランチにインポートするかを選択します。 

また、リポジトリ リンクを使用して、接続する Git リポジトリをすばやく指定することもできます。 

### <a name="use-a-different-azure-active-directory-tenant"></a>別の Azure Active Directory テナントを使用する

別の Azure Active Directory テナントで Azure Repos Git リポジトリを作成できます。 別の Azure AD テナントを指定するには、使用している Azure サブスクリプションの管理者のアクセス許可が必要です。 詳細については、[サブスクリプション管理者の変更](../../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)に関する記事を参照してください。

> [!IMPORTANT]
> 別の Azure Active Directory に接続するには、ログインしているユーザーがその Active Directory の一部である必要があります。 

### <a name="use-your-personal-microsoft-account"></a>個人用の Microsoft アカウントを使用する

Git の統合に個人用の Microsoft アカウントを使用するには、Azure の個人用のリポジトリを組織の Active Directory にリンクできます。

1. 個人用の Microsoft アカウントを組織の Active Directory にゲストとして追加します。 詳しくは、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../../active-directory/external-identities/add-users-administrator.md)」をご覧ください。

2. 個人用の Microsoft アカウントを使用して、Azure portal にログインします。 その後組織の Active Directory に切り替えます。

3. Azure DevOps セクションに移動すると、個人用のリポジトリがあることを確認できます。 リポジトリを選択し、Active Directory に接続します。

これらの構成手順の後、Synapse Studio で Git 統合を設定すると、個人用のリポジトリを使用できます。

Azure Repos を組織の Active Directory に接続する方法について詳しくは、「[Azure Active Directory に組織を接続する](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)」を参照してください。

## <a name="connect-with-github"></a>GitHub に接続する 

 ソース管理、コラボレーション、バージョン管理のために、ワークスペースを GitHub リポジトリに関連付けることができます。 GitHub アカウントまたはリポジトリがない場合は、[こちらの手順](https://github.com/join)に従ってリソースを作成します。

Synapse Studio と GitHub の統合では、パブリック GitHub (つまり [https://github.com](https://github.com)) と GitHub Enterprise の両方がサポートされます。 GitHub のリポジトリの読み取りおよび書き込みアクセス許可があれば、パブリックおよびプライベートの両方の GitHub リポジトリを使用できます。

### <a name="github-settings"></a>GitHub の設定

Git リポジトリに接続する場合、最初に GitHub としてリポジトリの種類を選択し、次に GitHub アカウントまたは GitHub Enterprise Server の URL (GitHub Enterprise Server を使用する場合) を指定し、 **[続行]** をクリックします。

![GitHub リポジトリの設定](media/connect-with-github-repo-1.png)

[構成] ウィンドウには、次の GitHub リポジトリの設定が表示されます。

| **設定** | **説明**  | **Value**  |
|:--- |:--- |:--- |
| **リポジトリの種類** | Azure Repos コード リポジトリの種類。 | GitHub |
| **GitHub Enterprise の使用** | GitHub Enterprise を選択するチェックボックス | オフ (既定値) |
| **GitHub Enterprise の URL** | GitHub Enterprise ルート URL (ローカル GitHub Enterprise サーバーの場合は HTTPS である必要があります)。 (例: `https://github.mydomain.com`)。 **[Use GitHub Enterprise]\(GitHub Enterprise を使用する\)** がオンの場合にのみ必要です | `<your GitHub enterprise url>` |                                                           
| **GitHub アカウント** | GitHub アカウント名。 この名前は、https:\//github.com/{アカウント名}/{リポジトリ名} で確認できます。 このページに移動すると、お使いの GitHub アカウントへの GitHub OAuth の資格情報を入力するよう求められます。 | `<your GitHub account name>` |
| **リポジトリ名**  | GitHub コード リポジトリ名。 GitHub アカウントには、ソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内の既存のリポジトリを使用できます。 | `<your repository name>` |
| **コラボレーション ブランチ** | 発行に使用される GitHub コラボレーション ブランチ。 既定では、そのマスターです。 別のブランチからのリソースを発行する場合は、この設定を変更します。 | `<your collaboration branch>` |
| **ルート フォルダー** | GitHub コラボレーション ブランチのルート フォルダー。 |`<your root folder name>` |
| **[Import existing resources to repository]\(既存のリソースをリポジトリにインポートする\)** | 既存のリソースを Synapse Studio から Git リポジトリにインポートするかどうかを指定します。 このチェックボックスをオンにすると、ワークスペース リソース (プールを除く) が JSON 形式で関連する Git リポジトリにインポートされます。 この操作により、各リソースが個別にエクスポートされます。 このチェックボックスがオンになっていない場合、既存のリソースはインポートされません。 | 選択済み (既定値) |
| **[Import resource into this branch]\(リソースをこのブランチにインポートする\)** | リソース (SQL スクリプト、ノートブック、Spark ジョブ定義、データセット、データフローなど) をどのブランチにインポートするかを選択します。 

### <a name="github-organizations"></a>GitHub 組織

GitHub 組織に接続するには、組織が Synapse Studio にアクセス許可を付与する必要があります。 組織の管理者アクセス許可を持つユーザーは、下の手順を実行する必要があります。

#### <a name="connecting-to-github-for-the-first-time"></a>初めて GitHub に接続する

初めて Synapse Studio から GitHub に接続する場合は、これらの手順に従って GitHub 組織に接続します。

1. [Git 構成] ウィンドウの *[GitHub アカウント]* フィールドに組織名を入力します。 GitHub にログインするためのプロンプトが表示されます。 

1. 資格情報を使用してログインします。

1. Synapse を *Azure Synapse* という名前のアプリケーションとして承認するように求められます。 この画面には、Synapse が組織にアクセスするためのアクセス許可を付与するオプションが表示されます。 アクセス許可を付与するオプションが表示されない場合は、GitHub でアクセス許可を手動で付与するように管理者に依頼してください。

これらの手順に従うと、ワークスペースは組織内のパブリックとプライベートの両方のリポジトリに接続できるようになります。 接続できない場合は、ブラウザーのキャッシュをクリアして再試行してください。

#### <a name="already-connected-to-github-using-a-personal-account"></a>個人アカウントを使用して既に GitHub に接続している

既に GitHub に接続していて、個人アカウントへのアクセス許可のみが付与されている場合は、以下の手順に従って、組織にアクセス許可を付与します。

1. GitHub に移動して **[設定]** を開きます。

    ![GitHub の設定を開く](media/github-settings.png)

1. **[アプリケーション]** を選択します。 **[Authorized OAuth Apps]\(認可済み OAuth アプリ\)** タブに *Azure Synapse* が表示されます。

    ![OAuth アプリを承認する](media/authorize-app.png)

1. *Azure Synapse* を選択し、自分の組織へのアクセス権を付与します。

    ![組織へのアクセス許可を付与する](media/grant-organization-permission.png)

これらの手順を完了すると、ワークスペースは組織内のパブリックとプライベートの両方のリポジトリに接続できるようになります。

## <a name="version-control"></a>バージョン コントロール

開発者は、バージョン管理システム (別名、_ソース管理_) を使用して、コードに対して共同作業を行うことや、変更を追跡することができます。ソース管理は、複数の開発者で行うプロジェクトに不可欠なツールです。

### <a name="creating-feature-branches"></a>機能ブランチの作成

Synapse Studio に関連付けられた各 Git リポジトリには、コラボレーション ブランチがあります (`main` や `master` は、既定のコラボレーション ブランチです)。 ユーザーは、ブランチのドロップダウンで **[+ New Branch]\(新しいブランチ\)** をクリックして機能分岐を作成することもできます。 新しいブランチのウィンドウが表示されたら、機能ブランチの名前を入力します。

![新しいブランチを作成する](media/create-new-branch.png)

機能ブランチの変更をコラボレーション ブランチにマージする準備ができたら、ブランチのドロップダウンをクリックし、 **[Create pull request]\(pull request の作成\)** を選択します。 この操作を行うと、Git プロバイダーに移動します。ここで、pull request の発行、コードのレビュー、コラボレーション ブランチへの変更のマージを行うことができます。 コラボレーション ブランチからは、Synapse サービスへの発行のみ許可されます。 

![新しい pull request を作成する](media/create-pull-request.png)

### <a name="configure-publishing-settings"></a>発行の設定を構成する

既定では、Synapse Studio はワークスペース テンプレートを生成し、それらを `workspace_publish` という名前のブランチに保存します。 カスタムの公開ブランチを構成するには、コラボレーション ブランチのルート フォルダーに `publish_config.json` ファイルを追加します。 発行時、Synapse Studio はこのファイルを読み込み、フィールド `publishBranch` を探し、ワークスペース テンプレート ファイルを指定の場所に保存します。 ブランチが存在しない場合は、Synapse Studio によって自動的に作成されます。 このファイルの例を次に示します。

```json
{
    "publishBranch": "workspace_publish"
}
```

Azure Synapse Studio では、一度に 1 つの発行ブランチのみ使用できます。 新しい発行ブランチを指定しても、以前の発行ブランチは削除されません｡ 以前の発行ブランチを削除する場合は、それを手動で削除します。


### <a name="publish-code-changes"></a>コード変更の発行

変更をコラボレーション ブランチにマージした後、 **[発行]** をクリックして、コラボレーション ブランチでのコードの変更を Synapse サービスに手動で発行します。

![変更を発行する](media/gitmode-publish.png)

サイド ウィンドウが開き、発行ブランチと保留中の変更が正しいことを確認できます。 変更を確認したら、 **[OK]** をクリックして発行を確定します。

![発行ブランチが正しいことを確認する](media/publish-change.png)

> [!IMPORTANT]
> コラボレーション ブランチは、サービスにデプロイされているものを表すものではありません。 コラボレーション ブランチでの変更は、サービスに手動で発行する "*必要*" があります。

## <a name="switch-to-a-different-git-repository"></a>別の Git リポジトリに切り替える

別の Git リポジトリに切り替えるには、 **[ソース管理]** の管理ハブにある [Git 構成] ページに移動します。 **[切断]** を選択します。 

![Git アイコン](media/remove-repository.png)

ワークスペース名を入力し、 **[切断]** をクリックして、ワークスペースに関連付けられている Git リポジトリを削除します。

現在のリポジトリとの関連付けを削除すると、別のリポジトリを使用するように Git 設定を構成してから、既存のリソースを新しいリポジトリにインポートできるようになります。

> [!IMPORTANT]
> ワークスペースから Git 構成を削除しても、リポジトリからは何も削除されません。 Synapse ワークスペースには、発行されたすべてのリソースが含まれます。 引き続き、サービスに対してワークスペースを直接編集できます。

## <a name="best-practices-for-git-integration"></a>Git 統合のベスト プラクティス

-   **権限**: Git リポジトリをワークスペースに接続すると、ワークスペースのいずれかのロールで Git リポジトリにアクセスできるすべてのユーザーが SQL スクリプト、ノートブック、Spark ジョブ定義、データセット、データフロー、パイプラインなどの成果物を Git モードで更新できるようになります。 通常、ワークスペースを更新するためのアクセス許可は、すべてのチーム メンバーには付与しません。 Git リポジトリのアクセス許可は、Synapse ワークスペース成果物の作成者のみに付与します。 
-   **コラボレーション**: コラボレーション ブランチへの直接チェックインは許可しないことをお勧めします。 この制限は、すべてのチェックインが「[機能ブランチの作成](source-control.md#creating-feature-branches)」に記載されている pull request のレビュー プロセスを通過するため、バグを防ぐのに役立ちます。
-   **Synapse ライブ モード**: Git モードでの発行後、すべての変更が Synapse ライブ モードで反映されます。 Synapse ライブ モードでは、発行は無効になります。 また、適切なアクセス許可が付与されている場合は、ライブ モードで成果物を表示したり、実行したりすることができます。 
-   **Studio での成果物の編集**: Synapse Studio は、ワークスペースのソース管理を有効にし、変更を Git に自動的に同期できる唯一の場所です。 SDK、PowerShell による変更は、Git には同期されません。 Git が有効になっている場合は、常に Studio で成果物を編集することをお勧めします。

## <a name="troubleshooting-git-integration"></a>Git 統合のトラブルシューティング

### <a name="access-to-git-mode"></a>Git モードへのアクセス 

ワークスペースにリンクされている GitHub Git リポジトリへのアクセス許可が付与されていても、Git モードにアクセスできない場合は、次のようにします。 

1. キャッシュをクリアし、ページを更新します。 

1. GitHub アカウントにログインします。

### <a name="stale-publish-branch"></a>古い発行ブランチ

発行ブランチがコラボレーション ブランチと同期しておらず、最新の発行があっても古いリソースが含まれる場合は、これらの手順を試してください。

1. 現在の Git リポジトリを削除します

1. 同じ設定で Git を再構成します。ただし、 **[Import existing resources to repository]\(既存のリソースをリポジトリにインポートする\)** がオンになっていることを確認し、同じブランチを選択してください。  

1. 変更をコラボレーション ブランチにマージする pull request を作成します 

## <a name="unsupported-features"></a>サポートされていない機能

- Synapse Studio では、コミットのチェリーピックやリソースの選択的発行は許可されません。 
- Synapse Studio では、コミット メッセージのカスタマイズはサポートされません。
- 設計上、Studio での削除操作は Git に直接コミットされます。

## <a name="next-steps"></a>次の手順

* 継続的インテグレーションとデプロイを実装するには、[継続的インテグレーションと配信 (CI/CD)](continuous-integration-deployment.md) に関する記事を参照してください。