---
title: ソース管理
description: Azure Data Factory でソース管理を構成する方法について説明します
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: ''
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/09/2019
ms.openlocfilehash: 1f31bb78fced6db2a26559cbd098407823161dfc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928764"
---
# <a name="source-control-in-azure-data-factory"></a>Azure Data Factory のソース管理

Azure Data Factory のユーザー インターフェイス エクスペリエンス (UX) には、ビジュアルの作成に使用できる 2 つのエクスペリエンスがあります。

- Data Factory サービスを使用して直接作成
- Azure Repos Git または GitHub 統合を使用して作成

> [!NOTE]
> Azure Government Cloud では、Data Factory サービスの直接作成のみがサポートされています。

## <a name="author-directly-with-the-data-factory-service"></a>Data Factory サービスを使用して直接作成

Data Factory サービスを使用して直接作成する場合、変更を保存するには、 **[すべて公開]** ボタンを使用する必要があります。 クリックすると、行ったすべての変更が Data Factory サービスに直接発行されます。 

![発行モード](media/author-visually/data-factory-publish.png)

Data Factory サービスを使用した直接作成には、次の制限があります。

- Data Factory サービスには、変更について JSON エンティティを格納するためのリポジトリが含まれていません。
- Data Factory サービスは、コラボレーションまたはバージョン管理用に最適化されていません。

> [!NOTE]
> Git リポジトリが構成されている場合、Azure Data Factory UX では Data Factory サービスを使用した直接作成は無効になります。 PowerShell または SDK を使用して、サービスに直接変更を加えることができます。

## <a name="author-with-azure-repos-git-integration"></a>Azure Repos Git 統合を使用した作成

Azure Repos Git 統合を使ったビジュアルの作成では、データ ファクトリ パイプラインでの作業時にソース管理とコラボレーションがサポートされます。 ソース管理やコラボレーション、バージョン管理などで､データ ファクトリを Azure Repos Git 統合リポジトリに関連付けることができます。 各 Azure Repos Git 組織は複数のレポジトリを持つことができますが､1つの Azure Repos Git リポジトリに関連付けられるデータ ファクトリの数は 1 つだけです｡ Azure Repos アカウントまたはリポジトリがない場合は、[こちらの手順](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)に従ってリソースを作成します。

> [!NOTE]
> スクリプトとデータ ファイルは Azure Repos Git リポジトリに格納できます。 ただし、Azure Storage に手動でファイルをアップロードする必要があります。 Data Factory パイプラインは、Azure Repos Git リポジトリに保存されているスクリプトまたはデータ ファイルを Azure Storage に自動的にアップロードしません。

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Azure Data Factory で Azure Repos Git リポジトリを構成する

データ ファクトリで Azure Repos Git リポジトリを構成する方法は 2 通りあります。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>構成方法 1:Azure Data Factory のホーム ページ

Azure Data Factory のホームページで、 **[Set up Code Repository]\(コード リポジトリの設定\)** を選択します。

![Azure Repos コード リポジトリを構成する](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>構成方法 2:UX 作成キャンバス
Azure Data Factory UX 作成キャンバスで、 **[Data Factory]** ドロップダウン メニューを選択し、 **[Set up Code Repository]\(コード リポジトリの設定\)** を選択します。

![UX 作成のコード リポジトリ設定の構成](media/author-visually/configure-repo-2.png)

どちらの方法でも、リポジトリ設定の構成ウィンドウが開きます。

![コード リポジトリ設定の構成](media/author-visually/repo-settings.png)

構成ウィンドウに次の Azure Repos コード リポジトリの設定が表示されます。

| Setting | 説明 | 値 |
|:--- |:--- |:--- |
| **リポジトリの種類** | Azure Repos コード リポジトリの種類。<br/> | Azure DevOps Git または GitHub |
| **Azure Active Directory** | Azure AD テナントの名前。 | `<your tenant name>` |
| **Azure Repos 組織** | Azure Repos 組織の名前｡ Azure Repos 組織名は`https://{organization name}.visualstudio.com`で確認することができます｡ [Azure Repos 組織にサインイン](https://www.visualstudio.com/team-services/git/)し、お使いの Visual Studio プロファイルにアクセスして、リポジトリとプロジェクトを確認してください。 | `<your organization name>` |
| **ProjectName** | Azure Repos プロジェクトの名前。 Azure Repos プロジェクトの名前は `https://{organization name}.visualstudio.com/{project name}` で確認することができます｡ | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos コード リポジトリの名前｡ Azure Repos プロジェクトには、プロジェクトの拡大に合わせてソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内に既にある既存のリポジトリを使用できます。 | `<your Azure Repos code repository name>` |
| **コラボレーション ブランチ** | 発行に使用する Azure Repos コラボレーション ブランチ。 既定では、これは `master` です。 別のブランチからのリソースを発行する場合は、この設定を変更します。 | `<your collaboration branch name>` |
| **ルート フォルダー** | Azure Repos コラボレーション ブランチのルート フォルダー。 | `<your root folder name>` |
| **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** | UX **作成キャンバス**からの既存のデータ ファクトリ リソースを Azure Repos Git リボジトリにインポートするかどうかを指定します。 オンにすると、JSON 形式でデータ ファクトリ リソースを関連付けられている Git リポジトリにインポートします。 このアクションでは、各リソースが個別にエクスポートされます (つまり、リンクされたサービスとデータセットは、異なる JSON にエクスポートされます)。 このボックスを選択しなかった場合、既存のリソースはインポートされません。 | 選択済み (既定値) |
| **ソースをインポートするブランチ** | データ ファクトリのリソース (パイプライン、データセット、リンクされたサービスなど) をインポートするブランチを指定します。 次のブランチのいずれかにリソースをインポートできます。a. コラボレーション b. 新規作成 c. 既存のものを使用 |  |

> [!NOTE]
> Microsoft Edge を使用していて、Azure DevOps アカウントのドロップダウンに値が表示されない場合は、 https://*.visualstudio.com を信頼済みサイト一覧に追加します。

### <a name="use-a-different-azure-active-directory-tenant"></a>別の Azure Active Directory テナントを使用する

別の Azure Active Directory テナントで Azure Repos Git リポジトリを作成できます。 別の Azure AD テナントを指定するには、使用している Azure サブスクリプションの管理者のアクセス許可が必要です。

### <a name="use-your-personal-microsoft-account"></a>個人用の Microsoft アカウントを使用する

Git の統合に個人用の Microsoft アカウントを使用するには、Azure の個人用のリポジトリを組織の Active Directory にリンクできます。

1. 個人用の Microsoft アカウントを組織の Active Directory にゲストとして追加します。 詳しくは、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/b2b/add-users-administrator.md)」をご覧ください。

2. 個人用の Microsoft アカウントを使用して、Azure portal にログインします。 その後組織の Active Directory に切り替えます。

3. Azure DevOps セクションに移動すると、個人用のリポジトリがあることを確認できます。 リポジトリを選択し、Active Directory に接続します。

これらの構成手順を実行すると、Data Factory UI で Git 統合を設定するときに個人用のリポジトリを使用できます。

Azure Repos を組織の Active Directoryに接続する方法について詳しくは、[Azure DevOps の組織を Azure Active Directory に接続する方法](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)に関するページをご覧ください。

## <a name="author-with-github-integration"></a>GitHub 統合での作成

GitHub 統合を使用したビジュアルの作成では、データ ファクトリ パイプラインの使用にあたりソース管理とコラボレーションがサポートされています。 データ ファクトリを GitHub アカウント リポジトリと関連付けて、ソース管理、コラボレーション、バージョン管理などを行うことができます。 1 つの GitHub アカウントで複数のリポジトリを使用できますが、1 つの GitHub リポジトリに関連付けられるのは 1 つのデータ ファクトリのみです。 GitHub アカウントまたはリポジトリがない場合は、 [こちらの手順](https://github.com/join) に従ってリソースを作成します。

Data Factory と GitHub の統合では、パブリック GitHub (つまり [https://github.com](https://github.com)) と GitHub Enterprise の両方がサポートされます。 GitHub のリポジトリの読み取りおよび書き込みアクセス許可があれば、パブリックおよびプライベートの両方の GitHub リポジトリを Data Factory で使用できます。

GitHub リポジトリを構成するには、使用している Azure サブスクリプションの管理者のアクセス許可が必要です。

この機能の概要とデモンストレーションについては、以下の 9 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Azure Data Factory で GitHub リポジトリを構成する

データ ファクトリで GitHub リポジトリを構成するには 2 つの方法があります。

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>構成方法 1:Azure Data Factory のホーム ページ

Azure Data Factory のホームページで、 **[Set up Code Repository]\(コード リポジトリの設定\)** を選択します。

![Azure Repos コード リポジトリを構成する](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>構成方法 2:UX 作成キャンバス

Azure Data Factory UX 作成キャンバスで、 **[Data Factory]** ドロップダウン メニューを選択し、 **[Set up Code Repository]\(コード リポジトリの設定\)** を選択します。

![UX 作成のコード リポジトリ設定の構成](media/author-visually/configure-repo-2.png)

どちらの方法でも、リポジトリ設定の構成ウィンドウが開きます。

![GitHub リポジトリの設定](media/author-visually/github-integration-image2.png)

[構成] ウィンドウには、次の GitHub リポジトリの設定が表示されます。

| **設定** | **説明**  | **値**  |
|:--- |:--- |:--- |
| **リポジトリの種類** | Azure Repos コード リポジトリの種類。 | GitHub |
| **GitHub Enterprise の使用** | GitHub Enterprise を選択するチェックボックス | オフ (既定値) |
| **GitHub Enterprise の URL** | GitHub Enterprise のルート URL。 (例: https://github.mydomain.com )。 **[Use GitHub Enterprise]\(GitHub Enterprise を使用する\)** がオンの場合にのみ必要です | `<your GitHub enterprise url>` |                                                           
| **GitHub アカウント** | GitHub アカウント名。 この名前は、https:\//github.com/{アカウント名}/{リポジトリ名} で確認できます。 このページに移動すると、お使いの GitHub アカウントへの GitHub OAuth の資格情報を入力するよう求められます。 | `<your GitHub account name>` |
| **リポジトリ名**  | GitHub コード リポジトリ名。 GitHub アカウントには、ソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内の既存のリポジトリを使用できます。 | `<your repository name>` |
| **コラボレーション ブランチ** | 発行に使用される GitHub コラボレーション ブランチ。 既定は master です。 別のブランチからのリソースを発行する場合は、この設定を変更します。 | `<your collaboration branch>` |
| **ルート フォルダー** | GitHub コラボレーション ブランチのルート フォルダー。 |`<your root folder name>` |
| **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** | UX 作成キャンバスからの既存のデータ ファクトリ リソースを GitHub リボジトリにインポートするかどうかを指定します。 オンにすると、JSON 形式でデータ ファクトリ リソースを関連付けられている Git リポジトリにインポートします。 このアクションでは、各リソースが個別にエクスポートされます (つまり、リンクされたサービスとデータセットは、異なる JSON にエクスポートされます)。 このボックスを選択しなかった場合、既存のリソースはインポートされません。 | 選択済み (既定値) |
| **ソースをインポートするブランチ** | データ ファクトリのリソース (パイプライン、データセット、リンクされたサービスなど) をインポートするブランチを指定します。 次のブランチのいずれかにリソースをインポートできます。a. コラボレーション b. 新規作成 c. 既存のものを使用 |  |

### <a name="known-github-limitations"></a>GitHub の既知の制限事項

- スクリプトとデータ ファイルは GitHub リポジトリに格納できます。 ただし、Azure Storage に手動でファイルをアップロードする必要があります。 Data Factory パイプラインでは、GitHub リポジトリに保存されているスクリプトまたはデータ ファイルが Azure Storage に自動的にアップロードされません。

- GitHub Enterprise 2.14.0 より前のバージョンは Microsoft Edge ブラウザーで動作しません。

- Data Factory ビジュアル作成ツールと GitHub の統合は、一般的に利用できるバージョンの Data Factory でのみ機能します。

## <a name="switch-to-a-different-git-repo"></a>別の Git リポジトリに切り替える

別の Git リポジトリに切り替えるには、[Data Factory overview]\(Data Factory の概要\) ページの右上にある **[Git Repo Settings]\(Git リポジトリの設定\)** アイコンをクリックします。 アイコンが表示されていない場合は、ローカル ブラウザーのキャッシュをクリアします。 アイコンを選択して現在のリポジトリとの関連付けを削除します。

![Git アイコン](media/author-visually/remove-repo.png)

[Repository Settings]\(リポジトリの設定\) ウィンドウが表示されたら、 **[Remove Git]\(Git の削除\)** を選択します。 データ ファクトリ名を入力し、 **[confirm]\(確認\)** をクリックして、データ ファクトリに関連付けられている Git リポジトリを削除します。

![現在の Git リポジトリとの関連付けを削除する](media/author-visually/remove-repo2.png)

現在のリポジトリとの関連付けを削除すると、別のリポジトリを使用するように Git 設定を構成してから、既存の Data Factory リソースを新しいリポジトリにインポートできるようになります。 

## <a name="version-control"></a>バージョン コントロール

開発者は、バージョン コントロール (_ソース管理_ とも呼ばれます) システムを使うことで、コードの共同作業を行い、コード ベースに対して行われた変更を追跡することができます。 ソース管理は、複数の開発者で行うプロジェクトに不可欠なツールです。

### <a name="creating-feature-branches"></a>機能ブランチの作成

データ ファクトリに関連付けられている各 Azure Repos Git リポジトリには、コラボレーション ブランチが存在します。 (`master` は既定のコラボレーション ブランチです)。 ユーザーは、ブランチのドロップダウンで **[+ New Branch]\(新しいブランチ\)** をクリックして機能分岐を作成することもできます。 新しいブランチのウィンドウが表示されたら、機能ブランチの名前を入力します。

![新しいブランチを作成する](media/author-visually/new-branch.png)

機能ブランチの変更をコラボレーション ブランチにマージする準備ができたら、ブランチのドロップダウンをクリックし、 **[Create pull request]\(プル要求の作成\)** を選択します。 このアクションで Azure Repos Git に移動します。Azure Repos Git では、pull request の発行、コード レビューの実行、およびコラボレーション ブランチへの変更のマージを行うことができます。 (`master` が既定値です)。 コラボレーション ブランチからは、Data Factory サービスにのみ発行することができます。 

![新しい pull request を作成する](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>発行の設定を構成する

発行ブランチを構成するには､すなわち､Resource Manager テンプレートが保存されているブランチを構成するには､コラボレーション ブランチのルート フォルダーに `publish_config.json` ファイルを追加します｡ Data Factory は、このファイルを読み取り、フィールド `publishBranch` を探して､新しいブランチが存在していない場合は､指定されている値を使ってブランチを作成します。 以降､Resource Manager のテンプレートはすべて､その指定した場所に保存されます｡ 例:

```json
{
    "publishBranch": "factory/adf_publish"
}
```

新しい発行ブランチが指定されたとき､Data Factory は前回の発行ブランチを削除しません｡ 以前の発行ブランチを削除する場合は、それを手動で削除します。

> [!NOTE]
> Data Factory はファクトリを読み取るときに `publish_config.json` ファイルを読み取るだけです｡ ポータルにファクトリをすでに読み込んでいる場合は､ブラウザを最新の情報に更新することで､変更が有効になっていることを確認することができます｡

### <a name="publish-code-changes"></a>コード変更の発行

コラボレーション ブランチ (`master` が既定値) に変更をマージしたら、 **[発行]** をクリックして、マスター ブランチ内のコード変更を Data Factory サービスに手動で発行します。

![Data Factory サービスに変更を発行する](media/author-visually/publish-changes.png)

サイド ウィンドウが開き、発行ブランチと保留中の変更が正しいことを確認できます。 変更を確認したら、 **[OK]** をクリックして発行を確定します。

![発行ブランチが正しいことを確認する](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> マスター ブランチは Data Factory サービスに展開されているものを代表しているわけではありません。 マスター ブランチを Data Factory サービスに手動で発行する "*必要があります*"。

## <a name="advantages-of-git-integration"></a>Git 統合の利点

-   **ソース管理**。 データ ファクトリのワークロードの重要性が高まると、ソース管理に関して以下に示すような優れた機能を利用するために、そのファクトリと Git を統合する必要性を感じることも出てくると思われます。
    -   変更の追跡と監査の機能
    -   バグの原因となっている変更を元に戻す機能
-   **途中保存**。 ファクトリで多数の変更を実施していると、通常の LIVE モードでは (準備が整っていないので) 変更点をドラフトとして保存できないことや、コンピューターがクラッシュしても変更が失われることのないようにする必要があることに気付かされます。 Git を統合すれば、変更を差分の形で保存していき、準備が整った時点でファクトリに公開することができます。 変更点を満足のいくまでテストしている間、Git が作業のステージングの場としての役割を果たしてくれるのです。
-   **コラボレーションと統制**。 同じファクトリに多数のチーム メンバーが参加している場合には、コード レビューのプロセスを通じてチームメイトが相互に協力して作業できるようにしたいと思うこともあるかもしれません。 また、ファクトリにデプロイするためのアクセス許可をファクトリの共同作成者全員には与えないような設定も可能です。 チーム メンバーであれば Git 経由で変更ができるようにしつつ、変更をファクトリに "公開" する操作は一部のメンバーにのみに許可するわけです。
-   **差分の表示**。 Git モードでは、ファクトリへの公開が直前に迫っているペイロードの差分をわかりやすい形で確認できます。 この差分では、前回ファクトリに公開した時点から変更、追加、削除のあったリソースやエンティティがすべて表示されます。 この差分に基づいて、公開を続行するか、戻って変更点を確認してからもう一度やり直すかを判断できます。
-   **CI/CD の改善**。 Git モードを使用している場合には、開発用ファクトリに何か変更があった時点ですぐに自動でトリガーされるようにリリース パイプラインを構成できます。 また、Resource Manager テンプレートのパラメーターとして利用可能なプロパティをファクトリ内でカスタマイズすることもできるようになります。 これは、必要なプロパティのみをパラメーターにして、残りをすべてハード コーディングする場合に便利です。
-   **パフォーマンスの向上**。 平均的なファクトリの場合、Git モードではリソースが Git 経由でダウンロードされるので、読み込み速度が通常の LIVE モードの 10 倍になります。

## <a name="best-practices-for-git-integration"></a>Git 統合のベスト プラクティス

### <a name="permissions"></a>アクセス許可

通常は、すべてのチーム メンバーにファクトリを更新するアクセス許可を付与しないようにします。 次のアクセス許可の設定をお勧めします。

*   データ ファクトリに対する読み取りアクセス許可は、チーム メンバー全員に必要です。
*   ファクトリへの発行は、一部のメンバーにのみ許可するようにします。 そのためには、ファクトリに **Data Factory 共同作成者**ロールが必要です。 アクセス許可の詳細については、「[Azure Data Factory のロールとアクセス許可](concepts-roles-permissions.md)」を参照してください。
   
コラボレーション ブランチへの直接チェックインは許可しないことをお勧めします。 この制限は、すべてのチェックインが「[機能ブランチの作成](source-control.md#creating-feature-branches)」に記載されているプル要求のレビュー プロセスを通過するため、バグを防ぐのに役立ちます。

### <a name="using-passwords-from-azure-key-vault"></a>Azure Key Vault からのパスワードの使用

Data Factory のリンクされたサービスのすべての接続文字列またはパスワードを格納するために Azure Key Vault を使用することをお勧めします。 セキュリティ上の理由から、このようなシークレット情報は Git に保存しないため、リンクされたサービスに対する変更は、直ちに Azure Data Factory サービスに発行されます。

また Key Vault を使用すると、Resource Manager テンプレートのデプロイ時にこれらのシークレットを指定する必要がなくなるため、継続的インテグレーションとデプロイが容易になります。

## <a name="troubleshooting-git-integration"></a>Git 統合のトラブルシューティング

### <a name="stale-publish-branch"></a>古い発行ブランチ

発行ブランチがマスター ブランチと同期しておらず、最新の発行があっても古いリソースが含まれている場合は、次の手順を行います。

1. 現在の Git リポジトリを削除します
1. 同じ設定で Git を再構成しますが、 **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** がオンになっていることを確認し、 **[New branch]\(新しいブランチ\)** を選択します
1. コラボレーション ブランチからすべてのリソースを削除します
1. 変更をコラボレーション ブランチにマージするプル要求を作成します 

## <a name="provide-feedback"></a>フィードバックの提供
機能に関するコメントを送信するには、またはツールの問題について Microsoft に通知するには、 **[フィードバック]** を選択します。

![フィードバック](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>次の手順

* パイプラインの監視と管理について詳しくは、[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)に関する記事をご覧ください。
* 継続的インテグレーションとデプロイを実装するには、「[Azure Data Factory における継続的インテグレーションと継続的デリバリー (CI/CD)](continuous-integration-deployment.md)」を参照してください。
