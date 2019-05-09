---
title: Azure Data Factory でのビジュアルの作成 | Microsoft Docs
description: Azure Data Factory でビジュアルの作成機能を使用する方法について説明します
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: d2e51551643e154714b9d2368e63d7af9ebfa204
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526913"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure Data Factory でのビジュアルの作成
Azure Data Factory ユーザー インターフェイス エクスペリエンス (UX) を使用すると、コードを記述することなくデータ ファクトリ用リソースを視覚的に作成してデプロイできます。 パイプライン キャンバスにアクティビティをドラッグし、テストの実行を行い、反復的にデバッグを行い、パイプラインの実行を展開して監視することができます。 UX を使用してビジュアルの作成機能を実行するには 2 つの方法があります。

- Data Factory サービスにより直接作成します。
- コラボレーション、ソース管理、およびバージョン管理に Azure Repos Git 統合を使用した作成。

## <a name="author-directly-with-the-data-factory-service"></a>Data Factory サービスを使用して直接作成
Data Factory サービスによるビジュアルの作成は、Git 統合によるビジュアルの作成と次の 2 点で異なります。

- Data Factory サービスには、変更について JSON エンティティを格納するためのリポジトリが含まれていません。
- Data Factory サービスは、コラボレーションまたはバージョン管理用に最適化されていません。

![Data Factory サービスの構成](media/author-visually/configure-data-factory.png)

UX **作成キャンバス**を使用して Data Factory サービスで直接作成する場合、**すべて発行**モードを使用できます。 行った変更はすべて、Data Factory サービスに直接発行されます。

![発行モード](media/author-visually/data-factory-publish.png)

## <a name="author-with-azure-repos-git-integration"></a>Azure Repos Git 統合を使用した作成
Azure Repos Git 統合を使ったビジュアルの作成では、データ ファクトリ パイプラインでの作業時にソース管理とコラボレーションがサポートされます。 ソース管理やコラボレーション、バージョン管理などで､データ ファクトリを Azure Repos Git 統合リポジトリに関連付けることができます。 各 Azure Repos Git 組織は複数のレポジトリを持つことができますが､1つの Azure Repos Git リポジトリに関連付けられるデータ ファクトリの数は 1 つだけです｡ Azure Repos アカウントまたはリポジトリがない場合は、[こちらの手順](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)に従ってリソースを作成します。

> [!NOTE]
> スクリプトとデータ ファイルは Azure Repos Git リポジトリに格納できます。 ただし、Azure Storage に手動でファイルをアップロードする必要があります。 Data Factory パイプラインは、Azure Repos Git リポジトリに保存されているスクリプトまたはデータ ファイルを Azure Storage に自動的にアップロードしません。

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Azure Data Factory で Azure Repos Git リポジトリを構成する
データ ファクトリで Azure Repos Git リポジトリを構成する方法は 2 通りあります。

#### <a name="method1"></a> 構成方法 1 (Azure Repos Git リポジトリ):[Let's get started]\(始めましょう\) ページ

Azure Data Factory で **[始めましょう]** ページに移動します。 **[Configure Code Repository]\(コード リポジトリの構成\)** を選択します。

![Azure Repos コード リポジトリを構成する](media/author-visually/configure-repo.png)

**[リポジトリ設定]** 構成ウィンドウが表示されます。

![コード リポジトリ設定の構成](media/author-visually/repo-settings.png)

ウィンドウに次の Azure Repos コード リポジトリの設定が表示されます。

| Setting | 説明 | 値 |
|:--- |:--- |:--- |
| **リポジトリの種類** | Azure Repos コード リポジトリの種類。<br/>**メモ**:GitHub は現在サポートされていません。 | Azure Repos Git |
| **Azure Active Directory** | Azure AD テナントの名前。 | `<your tenant name>` |
| **Azure Repos 組織** | Azure Repos 組織の名前｡ Azure Repos 組織名は`https://{organization name}.visualstudio.com`で確認することができます｡ [Azure Repos 組織にサインイン](https://www.visualstudio.com/team-services/git/)し、お使いの Visual Studio プロファイルにアクセスして、リポジトリとプロジェクトを確認してください。 | `<your organization name>` |
| **ProjectName** | Azure Repos プロジェクトの名前。 Azure Repos プロジェクトの名前は `https://{organization name}.visualstudio.com/{project name}` で確認することができます｡ | `<your Azure Repos project name>` |
| **RepositoryName** | Azure Repos コード リポジトリの名前｡ Azure Repos プロジェクトには、プロジェクトの拡大に合わせてソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内に既にある既存のリポジトリを使用できます。 | `<your Azure Repos code repository name>` |
| **コラボレーション ブランチ** | 発行に使用する Azure Repos コラボレーション ブランチ。 既定では `master` です。 別のブランチからのリソースを発行する場合は、この設定を変更します。 | `<your collaboration branch name>` |
| **ルート フォルダー** | Azure Repos コラボレーション ブランチのルート フォルダー。 | `<your root folder name>` |
| **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** | UX **作成キャンバス**からの既存のデータ ファクトリ リソースを Azure Repos Git リボジトリにインポートするかどうかを指定します。 オンにすると、JSON 形式でデータ ファクトリ リソースを関連付けられている Git リポジトリにインポートします。 このアクションでは、各リソースが個別にエクスポートされます (つまり、リンクされたサービスとデータセットは、異なる JSON にエクスポートされます)。 このボックスを選択しなかった場合、既存のリソースはインポートされません。 | 選択済み (既定値) |

#### <a name="configuration-method-2-azure-repos-git-repo-ux-authoring-canvas"></a>構成方法 2 (Azure Repos Git リポジトリ):UX 作成キャンバス
Azure Data Factory UX **作成キャンバス**でデータ ファクトリを見つけます。 **[Data Factory]** ドロップダウン メニューを選択して、**[Configure Code Repository]\(コード リポジトリの構成\)** を選択します。

構成ウィンドウが表示されます。 構成設定について詳しくは、「<a href="#method1">構成方法 1</a>」の説明をご覧ください。

![UX 作成のコード リポジトリ設定の構成](media/author-visually/configure-repo-2.png)

### <a name="use-a-different-azure-active-directory-tenant"></a>別の Azure Active Directory テナントを使用する

別の Azure Active Directory テナントで Azure Repos Git リポジトリを作成できます。 別の Azure AD テナントを指定するには、使用している Azure サブスクリプションの管理者のアクセス許可が必要です。

### <a name="use-your-personal-microsoft-account"></a>個人用の Microsoft アカウントを使用する

Git の統合に個人用の Microsoft アカウントを使用するには、Azure の個人用のリポジトリを組織の Active Directory にリンクできます。

1. 個人用の Microsoft アカウントを組織の Active Directory にゲストとして追加します。 詳しくは、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../active-directory/b2b/add-users-administrator.md)」をご覧ください。

2. 個人用の Microsoft アカウントを使用して、Azure portal にログインします。 その後組織の Active Directory に切り替えます。

3. Azure DevOps セクションに移動すると、個人用のリポジトリがあることを確認できます。 リポジトリを選択し、Active Directory に接続します。

これらの構成手順を実行すると、Data Factory UI で Git 統合を設定するときに個人用のリポジトリを使用できます。

Azure Repos を組織の Active Directoryに接続する方法について詳しくは、[Azure DevOps の組織を Azure Active Directory に接続する方法](/azure/devops/organizations/accounts/connect-organization-to-azure-ad)に関するページをご覧ください。

### <a name="switch-to-a-different-git-repo"></a>別の Git リポジトリに切り替える

別の Git リポジトリに切り替えるには、次のスクリーンショットに示すように、Data Factory の概要ページの右上隅にあるアイコンを探します。 アイコンが表示されていない場合は、ローカル ブラウザーのキャッシュをクリアします。 アイコンを選択して現在のリポジトリとの関連付けを削除します。

現在のリポジトリとの関連付けを削除した後で、別のリポジトリを使用するように Git の設定を構成できます。 その後、既存の Data Factory リソースを新しいリポジトリにインポートできます。

![現在の Git リポジトリとの関連付けを削除する](media/author-visually/remove-repo.png)

### <a name="use-version-control"></a>バージョン コントロールの使用
開発者は、バージョン コントロール (_ソース管理_ とも呼ばれます) システムを使うことで、コードの共同作業を行い、コード ベースに対して行われた変更を追跡することができます。 ソース管理は、複数の開発者で行うプロジェクトに不可欠なツールです。

データ ファクトリに関連付けられている各 Azure Repos Git リポジトリには、コラボレーション ブランチが存在します。 (`master` は既定のコラボレーション ブランチです)。 ユーザーは、**[+ 新しいブランチ]** をクリックして機能ブランチを作成し、その機能ブランチで開発を行うこともできます。

![同期または発行してコードを変更](media/author-visually/sync-publish.png)

機能ブランチでの機能開発によって準備ができたら、**[pull request の作成]** をクリックできます。 このアクションで Azure Repos Git に移動します。Azure Repos Git では、pull request の発行、コード レビューの実行、およびコラボレーション ブランチへの変更のマージを行うことができます。 (`master` が既定値です)。 コラボレーション ブランチからは、Data Factory サービスにのみ発行することができます。 

![新しい pull request を作成する](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>発行の設定を構成する

発行ブランチを構成するには､すなわち､Resource Manager テンプレートが保存されているブランチを構成するには､コラボレーション ブランチのルート フォルダーに `publish_config.json` ファイルを追加します｡ Data Factory は、このファイルを読み取り、フィールド `publishBranch` を探して､新しいブランチが存在していない場合は､指定されている値を使ってブランチを作成します。 以降､Resource Manager のテンプレートはすべて､その指定した場所に保存されます｡ 例: 

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Git モードからの発行では､次のスクリーン ショットに示すように､Data Factory が適切な発行ブランチを使用しているかどうか確認することができます。

![発行ブランチが正しいことを確認する](media/author-visually/configure-publish-branch.png)

新しい発行ブランチが指定されたとき､Data Factory は前回の発行ブランチを削除しません｡ 前回の発行ブランチを削除したい場合は､手動で削除します｡

Data Factory はファクトリを読み取るときに `publish_config.json` ファイルを読み取るだけです｡ ポータルにファクトリをすでに読み込んでいる場合は､ブラウザを最新の情報に更新することで､変更が有効になっていることを確認することができます｡

### <a name="publish-code-changes"></a>コード変更の発行
コラボレーション ブランチ (`master` が既定値) に変更をマージしたら、**[発行]** を選択して、マスター ブランチ内のコード変更を Data Factory サービスに手動で発行します。

![Data Factory サービスに変更を発行する](media/author-visually/publish-changes.png)

> [!IMPORTANT]
> マスター ブランチは Data Factory サービスに展開されているものを代表しているわけではありません。 マスター ブランチを Data Factory サービスに手動で発行する "*必要があります*"。

### <a name="advantages-of-git-integration"></a>Git 統合の利点

-   **ソース管理**。 データ ファクトリのワークロードの重要性が高まると、ソース管理に関して以下に示すような優れた機能を利用するために、そのファクトリと Git を統合する必要性を感じることも出てくると思われます。
    -   変更の追跡と監査の機能
    -   バグの原因となっている変更を元に戻す機能
-   **途中保存**。 ファクトリで多数の変更を実施していると、通常の LIVE モードでは (準備が整っていないので) 変更点をドラフトとして保存できないことや、コンピューターがクラッシュしても変更が失われることのないようにする必要があることに気付かされます。 Git を統合すれば、変更を差分の形で保存していき、準備が整った時点でファクトリに公開することができます。 変更点を満足のいくまでテストしている間、Git が作業のステージングの場としての役割を果たしてくれるのです。
-   **コラボレーションと統制**。 同じファクトリに多数のチーム メンバーが参加している場合には、コード レビューのプロセスを通じてチームメイトが相互に協力して作業できるようにしたいと思うこともあるかもしれません。 また、ファクトリにデプロイするためのアクセス許可をファクトリの共同作成者全員には与えないような設定も可能です。 チーム メンバーであれば Git 経由で変更ができるようにしつつ、変更をファクトリに "公開" する操作は一部のメンバーにのみに許可するわけです。
-   **差分の表示**。 Git モードでは、ファクトリへの公開が直前に迫っているペイロードの差分をわかりやすい形で確認できます。 この差分では、前回ファクトリに公開した時点から変更、追加、削除のあったリソースやエンティティがすべて表示されます。 この差分に基づいて、公開を続行するか、戻って変更点を確認してからもう一度やり直すかを判断できます。
-   **CI/CD の改善**。 Git モードを使用している場合には、開発用ファクトリに何か変更があった時点ですぐに自動でトリガーされるようにリリース パイプラインを構成できます。 また、Resource Manager テンプレートのパラメーターとして利用可能なプロパティをファクトリ内でカスタマイズすることもできるようになります。 これは、必要なプロパティのみをパラメーターにして、残りをすべてハード コーディングする場合に便利です。
-   **パフォーマンスの向上**。 平均的なファクトリの場合、Git モードではリソースが Git 経由でダウンロードされるので、読み込み速度が通常の LIVE モードの 10 倍になります。

### <a name="best-practices-for-git-integration"></a>Git 統合のベスト プラクティス

-   **アクセス許可**。 通常は、チーム メンバー全員にファクトリの更新が可能なアクセス許可を与える必要はありません。
    -   データ ファクトリに対する読み取りアクセス許可は、チーム メンバー全員に必要です。
    -   ファクトリへの公開については、一定のグループの人にのみ許可するのが好ましいと言えます。そのためには、そのようなグループがファクトリ上で "データ ファクトリ共同作成者" ロールの一部になっている必要があります。
    -   ソース管理の優れた方法としては、ほかにもコラボレーション ブランチへの直接チェックインを許可しないことが挙げられます。 このようにすれば、あらゆるチェックインが pull request のプロセスを経由することになるため、バグの発生を防ぐことができます。
-   **モードの切り替え**。
    -    いったん Git モードに切り替えたら、LIVE モードとの間で何度もモードを切り替えないようにすることをお勧めします。これは、LIVE モードで何か変更を加えた後に Git モードに戻った場合に、その変更点が表示されないというのが主な理由です。 変更は Git モードで実施し、UI を使って公開するようにしてください。
    -   同じように、Data Factory の PowerShell コマンドレットも使用しないようにしてください。PowerShell コマンドレットを使うと、ライブのファクトリに変更を直接適用することになるので、同じような結果を招きます。
-   **Azure Key Vault からのパスワードの利用**。
    -   Data Factory のリンクされたサービスへの接続文字列またはパスワードの保存場所には、Azure Key Vault の利用を強くお勧めします。
    -   Microsoft では、(セキュリティ上の理由により) そのような秘密情報を Git に保管していません。そのため、リンクされたサービスに変更を加えた場合には、すぐにライブのファクトリに公開されます。 変更をこのように即座に公開することは、テストを経ていない状態の変更が公開される可能性が出てくるという点で Git の目的にそぐわないので、時として好ましくありません。
    -   その結果、そのような秘密情報はいずれも、Azure Key Vault をベースに使用するリンクされたサービスからフェッチする必要があります。
    -   Key Vault を使用するメリットとしてはほかにも、Resource Manager テンプレートのデプロイ中に秘密情報を提供せずに済むようになり、CI/CD が容易になるという点が挙げられます。

## <a name="author-with-github-integration"></a>GitHub 統合での作成

GitHub 統合を使用したビジュアルの作成では、データ ファクトリ パイプラインの使用にあたりソース管理とコラボレーションがサポートされています。 データ ファクトリを GitHub アカウント リポジトリと関連付けて、ソース管理、コラボレーション、バージョン管理などを行うことができます。 1 つの GitHub アカウントで複数のリポジトリを使用できますが、1 つの GitHub リポジトリに関連付けられるのは 1 つのデータ ファクトリのみです。 GitHub アカウントまたはリポジトリがない場合は、 [こちらの手順](https://github.com/join) に従ってリソースを作成します。

Data Factory と GitHub の統合では、パブリック GitHub (つまり [https://github.com](https://github.com)) と GitHub Enterprise の両方がサポートされます。 GitHub のリポジトリの読み取りおよび書き込みアクセス許可があれば、パブリックおよびプライベートの両方の GitHub リポジトリを Data Factory で使用できます。

GitHub リポジトリを構成するには、使用している Azure サブスクリプションの管理者のアクセス許可が必要です。

この機能の概要とデモンストレーションについては、以下の 9 分間の動画を視聴してください。

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="limitations"></a>制限事項

- スクリプトとデータ ファイルは GitHub リポジトリに格納できます。 ただし、Azure Storage に手動でファイルをアップロードする必要があります。 Data Factory パイプラインでは、GitHub リポジトリに保存されているスクリプトまたはデータ ファイルが Azure Storage に自動的にアップロードされません。

- GitHub Enterprise 2.14.0 より前のバージョンは Microsoft Edge ブラウザーで動作しません。

- Data Factory ビジュアル作成ツールと GitHub の統合は、一般的に利用できるバージョンの Data Factory でのみ機能します。

### <a name="configure-a-public-github-repository-with-azure-data-factory"></a>Azure Data Factory で パブリック GitHub リポジトリを構成する

データ ファクトリで GitHub リポジトリを構成するには 2 つの方法があります。

**構成方法 1 (パブリック リポジトリ):[Let's get started]\(始めましょう\) ページ**

Azure Data Factory で  **[始めましょう]**  ページに移動します。 [ **Configure Code Repository**]\(コード リポジトリの構成) を選択します。

![Data Factory の [始めましょう] ページ](media/author-visually/github-integration-image1.png)

[ **リポジトリ設定**] 構成ウィンドウが表示されます。

![GitHub リポジトリの設定](media/author-visually/github-integration-image2.png)

ウィンドウに次の Azure Repos コード リポジトリの設定が表示されます。

| **設定**                                              | **説明**                                                                                                                                                                                                                                                                                                                                                                                                                   | **値**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **リポジトリの種類**                                      | Azure Repos コード リポジトリの種類。                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub アカウント**                                       | GitHub アカウント名。 この名前は、https:\//github.com/{アカウント名}/{リポジトリ名} で確認できます。 このページに移動すると、お使いの GitHub アカウントへの GitHub OAuth の資格情報を入力するよう求められます。                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | GitHub コード リポジトリ名。 GitHub アカウントには、ソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内の既存のリポジトリを使用できます。                                                                                                                                                                                                                              |                    |
| **コラボレーション ブランチ**                                 | 発行に使用される GitHub コラボレーション ブランチ。 既定は master です。 別のブランチからのリソースを発行する場合は、この設定を変更します。                                                                                                                                                                                                                                                               |                    |
| **ルート フォルダー**                                          | GitHub コラボレーション ブランチのルート フォルダー。                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** | UX  **作成キャンバス** からの既存のデータ ファクトリ リソースを GitHub リボジトリにインポートするかどうかを指定します。 オンにすると、JSON 形式でデータ ファクトリ リソースを関連付けられている Git リポジトリにインポートします。 このアクションでは、各リソースが個別にエクスポートされます (つまり、リンクされたサービスとデータセットは、異なる JSON にエクスポートされます)。 このボックスを選択しなかった場合、既存のリソースはインポートされません。 | 選択済み (既定値) |
| **ソースをインポートするブランチ**                       | データ ファクトリのリソース (パイプライン、データセット、リンクされたサービスなど) をインポートするブランチを指定します。 次のブランチのいずれかにリソースをインポートできます。a.  コラボレーション b.  新規作成 c.  既存のものを使用                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-public-repo-ux-authoring-canvas"></a>構成方法 2 (パブリック リポジトリ):UX 作成キャンバス

Azure Data Factory UX  **作成キャンバス**でデータ ファクトリを見つけます。 [ **Data Factory**] ドロップダウン メニューを選択して、[ **Configure Code Repository**]\(コード リポジトリの構成) を選択します。

構成ウィンドウが表示されます。 構成設定について詳しくは、前述の「 *構成方法 1*」の説明をご覧ください。

### <a name="configure-a-github-enterprise-repository-with-azure-data-factory"></a>Azure Data Factory で GitHub Enterprise リポジトリを構成する

データ ファクトリで GitHub Enterprise リポジトリを構成するには 2 つの方法があります。

#### <a name="configuration-method-1-enterprise-repo-lets-get-started-page"></a>構成方法 1 (Enterprise リポジトリ):[Let's get started]\(始めましょう\) ページ

Azure Data Factory で  **[始めましょう]**  ページに移動します。 [ **Configure Code Repository**]\(コード リポジトリの構成) を選択します。

![Data Factory の [始めましょう] ページ](media/author-visually/github-integration-image1.png)

[ **リポジトリ設定**] 構成ウィンドウが表示されます。

![GitHub リポジトリの設定](media/author-visually/github-integration-image3.png)

ウィンドウに次の Azure Repos コード リポジトリの設定が表示されます。

| **設定**                                              | **説明**                                                                                                                                                                                                                                                                                                                                                                                                                   | **値**          |
|----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------|
| **リポジトリの種類**                                      | Azure Repos コード リポジトリの種類。                                                                                                                                                                                                                                                                                                                                                                                             | GitHub             |
| **GitHub Enterprise の使用**                                | GitHub Enterprise を選択するチェックボックス                                                                                                                                                                                                                                                                                                                                                                                              |                    |
| **GitHub Enterprise の URL**                                | GitHub Enterprise のルート URL。 次に例を示します。 https://github.mydomain.com                                                                                                                                                                                                                                                                                                                                                          |                    |
| **GitHub アカウント**                                       | GitHub アカウント名。 この名前は、https:\//github.com/{アカウント名}/{リポジトリ名} で確認できます。 このページに移動すると、お使いの GitHub アカウントへの GitHub OAuth の資格情報を入力するよう求められます。                                                                                                                                                                                                                                               |                    |
| **RepositoryName**                                       | GitHub コード リポジトリ名。 GitHub アカウントには、ソース コードを管理するための Git リポジトリが含まれます。 新しいリポジトリを作成するか、プロジェクト内の既存のリポジトリを使用できます。                                                                                                                                                                                                                              |                    |
| **コラボレーション ブランチ**                                 | 発行に使用される GitHub コラボレーション ブランチ。 既定は master です。 別のブランチからのリソースを発行する場合は、この設定を変更します。                                                                                                                                                                                                                                                               |                    |
| **ルート フォルダー**                                          | GitHub コラボレーション ブランチのルート フォルダー。                                                                                                                                                                                                                                                                                                                                                                             |                    |
| **[Import existing Data Factory resources to repository]\(既存の Data Factory リソースをリポジトリにインポートする\)** | UX  **作成キャンバス** からの既存のデータ ファクトリ リソースを GitHub リボジトリにインポートするかどうかを指定します。 オンにすると、JSON 形式でデータ ファクトリ リソースを関連付けられている Git リポジトリにインポートします。 このアクションでは、各リソースが個別にエクスポートされます (つまり、リンクされたサービスとデータセットは、異なる JSON にエクスポートされます)。 このボックスを選択しなかった場合、既存のリソースはインポートされません。 | 選択済み (既定値) |
| **ソースをインポートするブランチ**                       | データ ファクトリのリソース (パイプライン、データセット、リンクされたサービスなど) をインポートするブランチを指定します。 次のブランチのいずれかにリソースをインポートできます。a.  コラボレーション b.  新規作成 c.  既存のものを使用                                                                                                                                                                                                     |                    |

#### <a name="configuration-method-2-enterprise-repo-ux-authoring-canvas"></a>構成方法 2 (Enterprise リポジトリ):UX 作成キャンバス

Azure Data Factory UX  **作成キャンバス**でデータ ファクトリを見つけます。 [ **Data Factory**] ドロップダウン メニューを選択して、[ **Configure Code Repository**]\(コード リポジトリの構成) を選択します。

構成ウィンドウが表示されます。 構成設定について詳しくは、前述の「 *構成方法 1*」の説明をご覧ください。

## <a name="use-the-expression-language"></a>式言語の使用
プロパティ値の式を指定するには、Azure Data Factory でサポートされる式言語を使用します。

**[動的なコンテンツの追加]** を選択して、プロパティ値の式を指定します。

![式言語の使用](media/author-visually/dynamic-content-1.png)

## <a name="use-functions-and-parameters"></a>関数とパラメーターを使用する

関数を使用することも、Data Factory の**式ビルダー**でパイプラインやデータセットのパラメーターを指定することもできます。

サポートされている式について詳しくは、「[Azure Data Factory の式と関数](control-flow-expression-language-functions.md)」をご覧ください。

![動的なコンテンツの追加](media/author-visually/dynamic-content-2.png)

## <a name="provide-feedback"></a>フィードバックの提供
機能に関するコメントを送信するには、またはツールの問題について Microsoft に通知するには、**[フィードバック]** を選択します。

![フィードバック](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>次の手順
パイプラインの監視と管理について詳しくは、[プログラムでのパイプラインの監視と管理](monitor-programmatically.md)に関する記事をご覧ください。
