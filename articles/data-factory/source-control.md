---
title: ソース管理
description: Azure Data Factory でソース管理を構成する方法について説明します
ms.service: data-factory
author: dcstwh
ms.author: weetok
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2021
ms.openlocfilehash: 7691c285bcc1c490878f5055468b0a57b6248679
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719374"
---
# <a name="source-control-in-azure-data-factory"></a>Azure Data Factory のソース管理
[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

既定の Azure Data Factory ユーザー インターフェイス エクスペリエンス (UX) では、データ ファクトリに対して直接作成されます。 このエクスペリエンスには次のような制限があります。

- Data Factory サービスには、変更について JSON エンティティを格納するためのリポジトリが含まれていません。 変更を保存する唯一の方法は **[すべて公開]** ボタンを使用することであり、変更内容はすべて、Data Factory サービスに直接公開されます。
- Data Factory サービスは、コラボレーションとバージョン管理で最適化されていません。
- Data Factory 自体をデプロイするために必要な Azure Resource Manager テンプレートは含まれていません。

効率的に作成できるよう、Azure Data Factory では、Azure Repos または GitHub のいずれかで Git リポジトリを構成することができます。 Git はバージョン管理システムであり、変更追跡や共同作業を簡単にします。 この記事では、Git リポジトリを構成し、そこで作業する方法を簡単に説明し、ベスト プラクティスとトラブルシューティング ガイドを提示します。

> [!NOTE]
> Azure Government クラウドの場合、GitHub Enterprise のみを使用できます。

Azure Data Factory を Git と統合する方法の詳細については、次の 15 分のチュートリアル ビデオをご覧ください。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4GNKv]

## <a name="advantages-of-git-integration"></a>Git 統合の利点

Git 統合が作成作業にもたらす利点をいくつか下の一覧にまとめています。

-   **ソース管理:** データ ファクトリのワークロードの重要性が高まると、ソース管理に関して以下に示すような優れた機能を利用するために、そのファクトリと Git を統合する必要性を感じることも出てくると思われます。
    -   変更の追跡と監査の機能
    -   バグの原因となっている変更を元に戻す機能
-   **途中保存:** データ ファクトリ サービスに対して作成するとき、変更内容を下書きとして保存することはできません。公開内容はすべて、データ ファクトリの妥当性確認に合格する必要があります。 パイプラインが完了していない場合、あるいは単純に、コンピューターがクラッシュして変更内容が失われては困るときに、Git 統合によって、データ ファクトリ リソースの状態に関係なく、データ ファクトリ リソースを増分方式で変更することができます。 Git リポジトリを構成すると、変更内容を保存できます。変更内容を満足できるまでテストしてから公開できます。
-   **コラボレーションと統制:** 同じファクトリに多数のチーム メンバーが貢献している場合には、コード レビューのプロセスを通じてチームメイトが相互に協力して作業できるようにしたいと思うこともあるかもしれません。 共同作成者ごとにアクセス許可を変えるようにファクトリを設定することもできます。 一部のチーム メンバーに Git 経由での変更のみを許可し、チーム内の特定の人間にファクトリの変更を公開することを許可します。
-   **CI/CD の改善:** [継続的デリバリー プロセス](continuous-integration-deployment.md)で複数の環境にデプロイしている場合、Git 統合で特定のアクションが簡単になります。 そうしたアクションの例を次に示します。
    -   "開発" ファクトリに何か変更があった時点ですぐに自動でトリガーされるようにリリース パイプラインを構成します。
    -   Resource Manager テンプレートのパラメーターとして利用可能なプロパティをファクトリ内でカスタマイズします。 これは、必要なプロパティのみをパラメーターにして、残りをすべてハード コーディングする場合に便利です。
-   **パフォーマンスの向上:** Git 統合の平均的なファクトリでは、データ ファクトリ サービスに対し、作成が 1 つの場合に比べ、読み込み速度が 10 倍になります。 このパフォーマンスの向上は、リソースが Git 経由でダウンロードされることに起因します。

> [!NOTE]
> Git リポジトリが構成されている場合、Azure Data Factory UX では Data Factory サービスを使用した直接作成は無効になります。 PowerShell または SDK を使用して行われた変更は、Data Factory サービスに直接発行され、Git には入力されません。

## <a name="connect-to-a-git-repository"></a>Git リポジトリに接続する

Azure Repos と GitHub の両方で Git リポジトリをデータ ファクトリに接続するには、4 つの方法があります。 Git リポジトリに接続した後、[管理ハブ](author-management-hub.md)の **[ソース管理]** セクションにある **[Git 構成]** で構成を表示し、管理することができます。

### <a name="configuration-method-1-home-page"></a>構成方法 1:ホーム ページ

Azure Data Factory のホームページで、 **[Set up code repository]\(コード リポジトリの設定\)** を選択します。

![ホーム ページからコード リポジトリを構成する](media/author-visually/configure-repo.png)

### <a name="configuration-method-2-authoring-canvas"></a>構成方法 2:作成キャンバス

Azure Data Factory UX 作成キャンバスで、 **[Data Factory]** ドロップダウン メニューを選択し、 **[Set up code repository]\(コード リポジトリの設定\)** を選択します。

![コード リポジトリ設定を作成から構成する](media/author-visually/configure-repo-2.png)

### <a name="configuration-method-3-management-hub"></a>構成方法 3:管理ハブ

ADF UX で管理ハブに移動します。 **[ソース管理]** セクションで **[Git 構成]** を選択します。 接続されているリポジトリがない場合は、 **[構成]** をクリックします。

![コード リポジトリ設定を管理ハブから構成する](media/author-visually/configure-repo-3.png)

### <a name="configuration-method-4-during-factory-creation"></a>構成方法 4:ファクトリの作成時

Azure portal で新しいデータ ファクトリを作成するときに、 **[Git 構成]** タブで Git リポジトリの情報を構成できます。

> [!NOTE]
> Azure portal で git を構成する場合は、プロジェクト名やリポジトリ名などの設定を、ドロップダウン リストの一部としてではなく、手動で入力する必要があります。

![コード リポジトリ設定を Azure portal から構成する](media/author-visually/configure-repo-4.png)

## <a name="author-with-azure-repos-git-integration"></a>Azure Repos Git 統合を使用した作成

Azure Repos Git 統合を使ったビジュアルの作成では、データ ファクトリ パイプラインでの作業時にソース管理とコラボレーションがサポートされます。 ソース管理やコラボレーション、バージョン管理などで､データ ファクトリを Azure Repos Git 統合リポジトリに関連付けることができます。 各 Azure Repos Git 組織は複数のレポジトリを持つことができますが､1つの Azure Repos Git リポジトリに関連付けられるデータ ファクトリの数は 1 つだけです｡ Azure Repos アカウントまたはリポジトリがない場合は、[こちらの手順](/azure/devops/organizations/accounts/create-organization-msa-or-work-student)に従ってリソースを作成します。

> [!NOTE]
> スクリプトとデータ ファイルは Azure Repos Git リポジトリに格納できます。 ただし、Azure Storage に手動でファイルをアップロードする必要があります。 データ ファクトリ パイプラインは、Azure Repos Git リポジトリに保存されているスクリプトまたはデータ ファイルを Azure Storage に自動的にアップロードしません。

### <a name="azure-repos-settings"></a>Azure Repos の設定

![コード リポジトリ設定の構成](media/author-visually/repo-settings.png)

構成ウィンドウに次の Azure Repos コード リポジトリの設定が表示されます。

| 設定 | 説明 | 値 |
|:--- |:--- |:--- |
| **リポジトリの種類** | Azure Repos コード リポジトリの種類。<br/> | Azure DevOps Git または GitHub |
| **Azure Active Directory** | Azure AD テナントの名前。 | `<your tenant name>` |
| **Azure Repos Organization** | Azure Repos 組織の名前｡ Azure Repos 組織名は`https://{organization name}.visualstudio.com`で確認することができます｡ [Azure Repos 組織にサインイン](https://www.visualstudio.com/team-services/git/)し、お使いの Visual Studio プロファイルにアクセスして、リポジトリとプロジェクトを確認してください。 | `<your organization name>` |
| **ProjectName** | Azure Repos プロジェクトの名前。 Azure Repos プロジェクトの名前は `https://{organization name}.visualstudio.com/{project name}` で確認することができます｡ | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos コード リポジトリの名前｡ Azure Repos プロジェクトには、プロジェクトの拡大に合わせてソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内に既にある既存のリポジトリを使用できます。 | `<your Azure Repos code repository name>` |
| **コラボレーション ブランチ** | 発行に使用する Azure Repos コラボレーション ブランチ。 既定では、これは `main` です。 別のブランチからのリソースを発行する場合は、この設定を変更します。 | `<your collaboration branch name>` |
| **ルート フォルダー** | Azure Repos コラボレーション ブランチのルート フォルダー。 | `<your root folder name>` |
| **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** | UX **作成キャンバス** からの既存のデータ ファクトリ リソースを Azure Repos Git リボジトリにインポートするかどうかを指定します。 オンにすると、JSON 形式でデータ ファクトリ リソースを関連付けられている Git リポジトリにインポートします。 このアクションでは、各リソースが個別にエクスポートされます (つまり、リンクされたサービスとデータセットは、異なる JSON にエクスポートされます)。 このボックスを選択しなかった場合、既存のリソースはインポートされません。 | 選択済み (既定値) |
| **Branch to import resource into** | データ ファクトリのリソース (パイプライン、データセット、リンクされたサービスなど) をインポートするブランチを指定します。 次のブランチのいずれかにリソースをインポートできます。a. コラボレーション b. 新規作成 c. 既存のものを使用 |  |

> [!NOTE]
> Microsoft Edge を使用していて、Azure DevOps アカウントのドロップダウンに値が表示されない場合は、 https://*.visualstudio.com を信頼済みサイト一覧に追加します。

### <a name="use-a-different-azure-active-directory-tenant"></a>別の Azure Active Directory テナントを使用する

別の Azure Active Directory テナントで Azure Repos Git リポジトリを作成できます。 別の Azure AD テナントを指定するには、使用している Azure サブスクリプションの管理者のアクセス許可が必要です。 詳細については、[サブスクリプション管理者の変更](../cost-management-billing/manage/add-change-subscription-administrator.md#to-assign-a-user-as-an-administrator)に関する記事を参照してください。

> [!IMPORTANT]
> 別の Azure Active Directory に接続するには、ログインしているユーザーがその Active Directory の一部である必要があります。 

### <a name="use-your-personal-microsoft-account"></a>個人用の Microsoft アカウントを使用する

Git の統合に個人用の Microsoft アカウントを使用するには、Azure の個人用のリポジトリを組織の Active Directory にリンクできます。

1. 個人用の Microsoft アカウントを組織の Active Directory にゲストとして追加します。 詳しくは、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/external-identities/add-users-administrator.md)」をご覧ください。

2. 個人用の Microsoft アカウントを使用して、Azure portal にログインします。 その後組織の Active Directory に切り替えます。

3. Azure DevOps セクションに移動すると、個人用のリポジトリがあることを確認できます。 リポジトリを選択し、Active Directory に接続します。

これらの構成手順を実行すると、Data Factory UI で Git 統合を設定するときに個人用のリポジトリを使用できます。

Azure Repos を組織の Active Directoryに接続する方法について詳しくは、[Azure DevOps の組織を Azure Active Directory に接続する方法](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)に関するページをご覧ください。

## <a name="author-with-github-integration"></a>GitHub 統合での作成

GitHub 統合を使用したビジュアルの作成では、データ ファクトリ パイプラインの使用にあたりソース管理とコラボレーションがサポートされています。 データ ファクトリを GitHub account リポジトリと関連付けて、ソース管理、コラボレーション、バージョン管理などを行うことができます。 1 つの GitHub アカウントで複数のリポジトリを使用できますが、1 つの GitHub リポジトリに関連付けられるのは 1 つのデータ ファクトリのみです。 GitHub アカウントまたはリポジトリがない場合は、[こちらの手順](https://github.com/join)に従ってリソースを作成します。

Data Factory と GitHub の統合では、パブリック GitHub (つまり [https://github.com](https://github.com)) と GitHub Enterprise の両方がサポートされます。 GitHub のリポジトリの読み取りおよび書き込みアクセス許可があれば、パブリックおよびプライベートの両方の GitHub リポジトリを Data Factory で使用できます。

GitHub リポジトリを構成するには、使用している Azure サブスクリプションの管理者のアクセス許可が必要です。

### <a name="github-settings"></a>GitHub の設定

![GitHub リポジトリの設定](media/author-visually/github-integration-image2.png)

[構成] ウィンドウには、次の GitHub リポジトリの設定が表示されます。

| **設定** | **説明**  | **Value**  |
|:--- |:--- |:--- |
| **リポジトリの種類** | Azure Repos コード リポジトリの種類。 | GitHub |
| **GitHub Enterprise の使用** | GitHub Enterprise を選択するチェックボックス | オフ (既定値) |
| **GitHub Enterprise の URL** | GitHub Enterprise ルート URL (ローカル GitHub Enterprise サーバーの場合は HTTPS である必要があります)。 (例: `https://github.mydomain.com`)。 **[Use GitHub Enterprise]\(GitHub Enterprise を使用する\)** がオンの場合にのみ必要です | `<your GitHub enterprise url>` |                                                           
| **GitHub アカウント** | GitHub アカウント名。 この名前は、https:\//github.com/{アカウント名}/{リポジトリ名} で確認できます。 このページに移動すると、お使いの GitHub アカウントへの GitHub OAuth の資格情報を入力するよう求められます。 | `<your GitHub account name>` |
| **リポジトリ名**  | GitHub コード リポジトリ名。 GitHub アカウントには、ソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内の既存のリポジトリを使用できます。 | `<your repository name>` |
| **コラボレーション ブランチ** | 発行に使用される GitHub コラボレーション ブランチ。 既定ではメインです。 別のブランチからのリソースを発行する場合は、この設定を変更します。 | `<your collaboration branch>` |
| **ルート フォルダー** | GitHub コラボレーション ブランチのルート フォルダー。 |`<your root folder name>` |
| **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** | UX 作成キャンバスからの既存のデータ ファクトリ リソースを GitHub リボジトリにインポートするかどうかを指定します。 オンにすると、JSON 形式でデータ ファクトリ リソースを関連付けられている Git リポジトリにインポートします。 このアクションでは、各リソースが個別にエクスポートされます (つまり、リンクされたサービスとデータセットは、異なる JSON にエクスポートされます)。 このボックスを選択しなかった場合、既存のリソースはインポートされません。 | 選択済み (既定値) |
| **Branch to import resource into** | データ ファクトリのリソース (パイプライン、データセット、リンクされたサービスなど) をインポートするブランチを指定します。 次のブランチのいずれかにリソースをインポートできます。a. コラボレーション b. 新規作成 c. 既存のものを使用 |  |

### <a name="github-organizations"></a>GitHub 組織

GitHub 組織に接続するには、Azure Data Factory のアクセス許可を組織に付与する必要があります。 組織に対する管理者アクセス許可を持つユーザーは、以下の手順を実行して、データ ファクトリに接続できるようにする必要があります。

#### <a name="connecting-to-github-for-the-first-time-in-azure-data-factory"></a>Azure Data Factory で初めて GitHub に接続する

初めて Azure Data Factory から GitHub に接続している場合は、これらの手順に従って GitHub 組織に接続します。

1. [Git 構成] ウィンドウの *[GitHub アカウント]* フィールドに組織名を入力します。 GitHub にログインするためのプロンプトが表示されます。 
1. 資格情報を使用してログインします。
1. Azure Data Factory を *AzureDataFactory* と呼ばれるアプリケーションとして認可するように求められます。 この画面には、ADF が組織にアクセスするためにアクセス許可を付与するためのオプションが表示されます。 アクセス許可を付与するオプションが表示されない場合は、GitHub でアクセス許可を手動で付与するように管理者に依頼してください。

これらの手順に従うと、ファクトリは組織内のパブリックとプライベートの両方のリポジトリに接続できるようになります。 接続できない場合は、ブラウザーのキャッシュをクリアして再試行してください。

#### <a name="already-connected-to-github-using-a-personal-account"></a>個人アカウントを使用して既に GitHub に接続している

既に GitHub に接続していて、個人アカウントへのアクセス許可のみが付与されている場合は、以下の手順に従って、組織にアクセス許可を付与します。 

1. GitHub に移動して **[設定]** を開きます。

    ![GitHub の設定を開く](media/author-visually/github-settings.png)

1. **[アプリケーション]** を選択します。 **[Authorized OAuth Apps]\(認可済み OAuth アプリ\)** タブに *AzureDataFactory* が表示されます。

    ![OAuth アプリの選択](media/author-visually/github-organization-select-application.png)

1. アプリケーションを選択し、アプリケーションに組織へのアクセス権を付与します。

    ![アクセス権の付与](media/author-visually/github-organization-grant.png)

これらの手順に従うと、ファクトリは組織内のパブリックとプライベートの両方のリポジトリに接続できるようになります。 

### <a name="known-github-limitations"></a>GitHub の既知の制限事項

- スクリプトとデータ ファイルは GitHub リポジトリに格納できます。 ただし、Azure Storage に手動でファイルをアップロードする必要があります。 Data Factory パイプラインでは、GitHub リポジトリに保存されているスクリプトまたはデータ ファイルが Azure Storage に自動的にアップロードされません。

- GitHub Enterprise 2.14.0 より前のバージョンは Microsoft Edge ブラウザーで動作しません。

- Data Factory ビジュアル作成ツールと GitHub の統合は、一般的に利用できるバージョンの Data Factory でのみ機能します。


- 1 つの GitHub ブランチからは、リソースの種類ごとに最大 1,000 のエンティティ (パイプライン、データセットなど) をフェッチできます。 この制限に達した場合は、リソースを個別のファクトリに分割することをお勧めします。 Azure DevOps Git にはこのような制限はありません。

## <a name="version-control"></a>バージョン コントロール

開発者は、バージョン コントロール (_ソース管理_ とも呼ばれます) システムを使うことで、コードの共同作業を行い、コード ベースに対して行われた変更を追跡することができます。 ソース管理は、複数の開発者で行うプロジェクトに不可欠なツールです。

### <a name="creating-feature-branches"></a>機能ブランチの作成

データ ファクトリに関連付けられている各 Azure Repos Git リポジトリには、コラボレーション ブランチが存在します。 (`main`) は既定のコラボレーション ブランチです)。 ユーザーは、ブランチのドロップダウンで **[+ New Branch]\(新しいブランチ\)** をクリックして機能分岐を作成することもできます。 新しいブランチのウィンドウが表示されたら、機能ブランチの名前を入力します。

![新しいブランチを作成する](media/author-visually/new-branch.png)

機能ブランチの変更をコラボレーション ブランチにマージする準備ができたら、ブランチのドロップダウンをクリックし、 **[Create pull request]\(pull request の作成\)** を選択します。 このアクションで Azure Repos Git に移動します。Azure Repos Git では、pull request の発行、コード レビューの実行、およびコラボレーション ブランチへの変更のマージを行うことができます。 (`main` が既定値です)。 コラボレーション ブランチからは、Data Factory サービスにのみ発行することができます。 

![新しい pull request を作成する](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>発行の設定を構成する

既定のデータ ファクトリでは、公開されたファクトリの Resource Manager テンプレートが生成され、`adf_publish` という名前のブランチにそれが保存されます。 カスタムの公開ブランチを構成するには、コラボレーション ブランチのルート フォルダーに `publish_config.json` ファイルを追加します。 公開時、ADF ではこのファイルを読み込み、フィールド `publishBranch` を探し、Resource Manager テンプレートをすべて、指定の場所に保存します。 ブランチが存在しない場合は、データ ファクトリによって自動的に作成されます。 このファイルの例を次に示します。

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Azure Data Factory に指定できる公開ブランチは一度に 1 つとなっています。 新しい発行ブランチが指定されたとき､Data Factory は前回の発行ブランチを削除しません｡ 以前の発行ブランチを削除する場合は、それを手動で削除します。

> [!NOTE]
> Data Factory はファクトリを読み取るときに `publish_config.json` ファイルを読み取るだけです｡ ポータルにファクトリをすでに読み込んでいる場合は､ブラウザを最新の情報に更新することで､変更が有効になっていることを確認することができます｡

### <a name="publish-code-changes"></a>コード変更の発行

コラボレーション ブランチ (`main` が既定値) に変更をマージしたら、 **[発行]** をクリックして、メイン ブランチ内のコード変更を Data Factory サービスに手動で発行します。

![Data Factory サービスに変更を発行する](media/author-visually/publish-changes.png)

サイド ウィンドウが開き、発行ブランチと保留中の変更が正しいことを確認できます。 変更を確認したら、 **[OK]** をクリックして発行を確定します。

![発行ブランチが正しいことを確認する](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> メイン ブランチは Data Factory サービスに展開されているものを代表しているわけではありません。 メイン ブランチを Data Factory サービスに手動で発行する "*必要があります*"。

## <a name="best-practices-for-git-integration"></a>Git 統合のベスト プラクティス

### <a name="permissions"></a>アクセス許可

通常、Data Factory を更新するアクセス許可をすべてのチーム メンバーには付与することはありません。 次のアクセス許可の設定をお勧めします。

*   Data Factory に対する読み取りアクセス許可は、チーム メンバー全員に必要です。
*   Data Factory への発行は、一部の選ばれたメンバーにのみ許可するようにします。 そのためには、Data Factory がある **リソース グループ** で、**Data Factory 共同作成者** ロールを持つ必要があります。 アクセス許可の詳細については、「[Azure Data Factory のロールとアクセス許可](concepts-roles-permissions.md)」を参照してください。

コラボレーション ブランチへの直接チェックインは許可しないことをお勧めします。 この制限は、すべてのチェックインが「[機能ブランチの作成](source-control.md#creating-feature-branches)」に記載されている pull request のレビュー プロセスを通過するため、バグを防ぐのに役立ちます。

### <a name="using-passwords-from-azure-key-vault"></a>Azure Key Vault からのパスワードの使用

Data Factory のリンクされたサービスのすべての接続文字列またはパスワードまたはマネージド ID の認証を格納するために Azure Key Vault を使用することをお勧めします。 セキュリティ上の理由から、データ ファクトリでは、シークレットが Git に格納されません。 パスワードなどのシークレットを含むリンクされたサービスが変更されると、直後に、Azure Data Factory サービスに変更内容が発行されます。

また、Key Vault または MSI 認証を使用すると、Resource Manager テンプレートのデプロイ時にこれらのシークレットを指定する必要がなくなるため、継続的インテグレーションとデプロイが容易になります。

## <a name="troubleshooting-git-integration"></a>Git 統合のトラブルシューティング

### <a name="stale-publish-branch"></a>古い発行ブランチ

発行ブランチがメイン ブランチと同期しておらず、最新の発行があっても古いリソースが含まれている場合は、次の手順を行います。

1. 現在の Git リポジトリを削除します
1. 同じ設定で Git を再構成しますが、 **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** がオンになっていることを確認し、 **[New branch]\(新しいブランチ\)** を選択します
1. 変更をコラボレーション ブランチにマージする pull request を作成します 

以下に、古い発行ブランチが発生する可能性がある状況の例をいくつか示します。
- ユーザーに複数のブランチがある。 ユーザーが 1 つの機能ブランチで、AKV 関連ではないリンクされたサービスを削除し (非 AKV のリンクされたサービスは、Git にあるかどうかに関係なく、直ちに発行されます)、機能ブランチをコラボレーション ブランチにマージしませんでした。
- ユーザーが SDK または PowerShell を使用してデータ ファクトリを変更した
- ユーザーがすべてのリソースを新しいブランチに移動し、初めて発行を試みた。 リンクされたサービスは、リソースをインポートするときに手動で作成する必要があります。
- ユーザーが、非 AKV のリンクされたサービスまたは Integration Runtime JSON を手動でアップロードする。 それらは、データセット、リンクされたサービス、パイプラインなどの別のリソースからそのリソースを参照します。 UX を使用して作成された非 AKV のリンクされたサービスは、資格情報を暗号化する必要があるため、直ちに発行されます。 そのリンクされたサービスを参照しているデータセットをアップロードして発行しようとすると、git 環境に存在するために UX はそれを許可します。 これは、データ ファクトリ サービスに存在しないため、発行時に拒否されます。

## <a name="switch-to-a-different-git-repository"></a>別の Git リポジトリに切り替える

別の Git リポジトリに切り替えるには、 **[ソース管理]** の管理ハブにある [Git 構成] ページに移動します。 **[切断]** を選択します。 

![Git アイコン](media/author-visually/remove-repository.png)

データ ファクトリ名を入力し、 **[confirm]\(確認\)** をクリックして、データ ファクトリに関連付けられている Git リポジトリを削除します。

![現在の Git リポジトリとの関連付けを削除する](media/author-visually/remove-repository-2.png)

現在のリポジトリとの関連付けを削除すると、別のリポジトリを使用するように Git 設定を構成してから、既存の Data Factory リソースを新しいリポジトリにインポートできるようになります。

> [!IMPORTANT]
> データ ファクトリから Git 構成を削除しても、リポジトリからは何も削除されません。 ファクトリには、公開されたすべてのリソースが含まれます。 引き続きサービスに対して直接、ファクトリを編集できます。

## <a name="next-steps"></a>次のステップ

* パイプラインの監視と管理について詳しくは、[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)に関する記事をご覧ください。
* 継続的インテグレーションとデプロイを実装するには、「[Azure Data Factory における継続的インテグレーションと継続的デリバリー (CI/CD)](continuous-integration-deployment.md)」を参照してください。
