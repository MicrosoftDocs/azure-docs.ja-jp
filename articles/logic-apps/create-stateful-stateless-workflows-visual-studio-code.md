---
title: Visual Studio Code で Logic Apps プレビュー ワークフローを作成する
description: Azure Logic Apps (プレビュー) 拡張機能を使用して Visual Studio Code で自動化と統合のシナリオのためのワークフローを構築して実行します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: f7f8082cc9120345336610d5cb49741140d3b606
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557014"
---
# <a name="create-stateful-and-stateless-workflows-in-visual-studio-code-with-the-azure-logic-apps-preview-extension"></a>Azure Logic Apps (プレビュー) 拡張機能を使用して Visual Studio Code でステートフルおよびステートレスのワークフローを作成する

> [!IMPORTANT]
> この機能はパブリック プレビュー段階であり、サービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure Logic Apps (プレビュー)](logic-apps-overview-preview.md) を使用すると、アプリ、データ、クラウド サービス、システムの全体に自動化および統合のソリューションを構築できます。そのためには、Azure Logic Apps (プレビュー) 拡張機能を使用して、Visual Studio Code で ["*ステートフル*" および "*ステートレス*" のワークフロー](logic-apps-overview-preview.md#stateful-stateless)を含むロジック アプリを作成して実行します。 この新しいロジック アプリの種類を使用することにより、再設計された Azure Logic Apps プレビュー ランタイムを利用する複数のワークフローを構築できます。これにより、Azure だけでなく、Docker コンテナーを含むさまざまなホスティング環境でデプロイおよび実行するための移植性、より優れたパフォーマンス、および柔軟性が提供されます。 新しいロジック アプリの種類の詳細については、[Azure Logic Apps プレビューの概要](logic-apps-overview-preview.md)に関するページをご覧ください。

![Visual Studio Code、ロジック アプリ プロジェクト、ワークフローを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-logic-apps-overview.png)

Visual Studio Code でまず、Azure Logic Apps (プレビュー) 拡張機能を使用して、自分の開発環境で "*ローカルに*" ロジック アプリのワークフローをビルドして実行できるプロジェクトを作成できます。 また、最初に [Azure portal で新しい **Logic App (プレビュー)** リソースを作成する](create-stateful-stateless-workflows-azure-portal.md)こともできますが、どちらの方法でも、同じ種類のホスティング環境でロジック アプリをデプロイして実行することができます。

また、元のロジック アプリの種類を作成することもできます。 Visual Studio Code の開発エクスペリエンスは、元のロジック アプリの種類と新しいものでは異なりますが、Azure サブスクリプションには両方の種類を含めることができます。 Azure サブスクリプションでデプロイされているすべてのロジック アプリを表示してアクセスできますが、アプリはその独自のカテゴリとセクションにまとめられます。

この記事では、Azure Logic Apps (プレビュー) 拡張機能を使用し、だいたい次のようなタスクを実行することにより、Visual Studio Code でロジック アプリとワークフローを作成する方法について説明します。

* ロジック アプリとワークフロー用のプロジェクトを作成します。

* トリガーとアクションを追加します。

* ローカル環境で実行、テスト、デバッグを行い、実行履歴を確認します。

* ファイアウォール アクセス用のドメイン名の詳細を検索します。

* Azure にデプロイします。必要に応じて、Application Insights を有効にします。

* Visual Studio Code と Azure portal で、デプロイされたロジック アプリを管理します。

* ステートレス ワークフローの実行履歴を有効にします。

* デプロイの後で、Application Insights を有効にするか開きます。

* どこでも実行できる Docker コンテナーにデプロイします。

> [!NOTE]
> 現在の既知の問題の詳細については、[GitHub の Logic Apps パブリック プレビューでの既知の問題に関するページ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)を参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="access-and-connectivity"></a>アクセスと接続

* 要件をダウンロードしたり、Visual Studio Code から Azure アカウントに接続したり、Visual Studio Code から Azure、Docker コンテナー、またはその他の環境に発行したりするためのインターネットへのアクセス。

* Azure アカウントとサブスクリプション。 サブスクリプションをお持ちでない場合には、[無料の Azure アカウントにサインアップ](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

* この記事の同じロジック アプリの例をビルドするには、Microsoft の職場または学校アカウントを使用してサインインする Office 365 Outlook 電子メール アカウントが必要です。

  Outlook.com や [Gmail](../connectors/connectors-google-data-security-privacy-policy.md) など、[Azure Logic Apps でサポートされている別の電子メール コネクタ](/connectors/)を使用する場合でもこの例に従うことができ、全体的な手順は同じです。ただし、ユーザー インターフェイスとオプションは一部異なる場合があります。 たとえば、Outlook.com コネクタを使用する場合は、代わりに個人用 Microsoft アカウントを使用してサインインします。

<a name="storage-requirements"></a>

### <a name="storage-requirements"></a>ストレージの要件

#### <a name="windows"></a>Windows

Windows を使用している場合、Visual Studio Code でロジック アプリ プロジェクトをローカルでビルドして実行するには、次の手順に従って Azure ストレージ エミュレーターを設定してください。

1. [Azure ストレージ エミュレーター 5.10](https://go.microsoft.com/fwlink/p/?linkid=717179) をダウンロードしてインストールします。

1. まだローカル SQL DB (無料の [SQL Server 2019 Express Edition](https://go.microsoft.com/fwlink/p/?linkid=866658) など) をインストールしていない場合は、インストールして、エミュレーターを実行できるようにする必要があります。

   詳細については、「[開発とテストに Azure ストレージ エミュレーターを使用する](../storage/common/storage-use-emulator.md)」を参照してください。

1. プロジェクトを実行する前に、エミュレーターを起動していることを確認してください。

   ![実行中の Azure ストレージ エミュレーターを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/start-storage-emulator.png)

#### <a name="macos-and-linux"></a>macOS、Linux

macOS または Linux を使用している場合、Visual Studio Code でロジック アプリ プロジェクトをローカルでビルドして実行するには、次の手順に従って Azure ストレージ アカウントを作成および設定してください。

> [!NOTE]
> 現在、Visual Studio Code のデザイナーは Linux OS で動作しませんが、Logic Apps プレビュー ランタイムを使用するロジック アプリのビルド、実行、および Linux ベースの仮想マシンへのデプロイはできます。 現在のところ、Windows または macOS の Visual Studio Code でロジック アプリを構築してから、Linux ベースの仮想マシンにデプロイすることができます。

1. [Azure portal](https://portal.azure.com) にサインインし、[Azure ストレージ アカウントを作成](../storage/common/storage-account-create.md?tabs=azure-portal)します。これは、[Azure Functions の前提条件](../azure-functions/storage-considerations.md)です。

1. ストレージ アカウント メニューの **[設定]** で、 **[アクセス キー]** を選択します。

1. **[アクセス キー]** ペインで、ストレージ アカウントの接続文字列を探してコピーします。接続文字列は、次の例のようなものです。

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Azure portal およびコピーされたストレージ アカウント アクセス キーと接続文字列が示されているスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   詳細については、[ストレージ アカウント キーの管理](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)に関するページをご確認ください。

1. 接続文字列を安全な場所に保存します。 Visual Studio Code でロジック アプリ プロジェクトを作成した後、プロジェクトのルート レベル フォルダーにある **local.settings.json** ファイルにこの文字列を追加する必要があります。

   > [!IMPORTANT]
   > Docker コンテナーへのデプロイを計画している場合は、デプロイに使用する Docker ファイルにもこの接続文字列を追加する必要があります。

### <a name="tools"></a>ツール

* [Visual Studio Code 1.30.1 (2019 年 1 月) 以降](https://code.visualstudio.com/) (無料)。 また、Visual Studio Code 用の次のツールをダウンロードしてインストールします (まだインストールしていない場合)。

  * [Azure アカウント拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。これにより、Visual Studio Code 内の他のすべての Azure 拡張機能に対して、共通する単一の Azure サインインとサブスクリプションのフィルター処理が可能になります。

  * [Visual Studio Code 用の C# 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。これにより、F5 機能でロジック アプリを実行できるようになります。

  * [Azure Functions Core Tools 3.0.3245 以降](https://github.com/Azure/azure-functions-core-tools/releases/tag/3.0.3245) (Microsoft インストーラー (MSI) バージョン (`func-cli-3.0.3245-x*.msi`) を使用)。

    これらのツールには、Azure Functions ランタイムで利用されるものと同じランタイムのバージョンが含まれており、それはプレビュー拡張機能によって Visual Studio Code で使用されます。

    > [!IMPORTANT]
    > これらのバージョンより古いバージョンをインストールしている場合は、最初にそのバージョンをアンインストールするか、ダウンロードしてインストールしたバージョンが PATH 環境変数で参照されていることを確認します。

  * [Visual Studio Code 用 Azure Logic Apps (プレビュー) 拡張機能](https://go.microsoft.com/fwlink/p/?linkid=2143167)。 この拡張機能により、ロジック アプリを作成するための機能が提供されます。それを使用して、Visual Studio Code でローカルに実行されるステートフルとステートレスのワークフローを作成し、それらのロジック アプリを Azure または Docker コンテナーに直接デプロイできます。

    現時点では、元の Azure Logic Apps 拡張機能とパブリック プレビュー拡張機能の両方を、Visual Studio Code に同時にインストールすることができます。 開発エクスペリエンスは拡張機能によっていくつか異なる点がありますが、Azure サブスクリプションには拡張機能を使用して作成する両方のロジック アプリの種類を含めることができます。 Visual Studio Code には、Azure サブスクリプションでデプロイされたすべてのロジック アプリが表示されますが、拡張機能名、**Logic Apps**、**Azure Logic Apps (プレビュー)** ごとに異なるセクションにまとめられています。

    > [!IMPORTANT]
    > 以前のプライベート プレビューの拡張機能を使用してロジック アプリ プロジェクトを作成した場合、それらのプロジェクトはパブリック プレビューの拡張機能では動作しません。 ただし、プライベート プレビューの拡張機能をアンインストールし、関連付けられているファイルを削除して、パブリック プレビューの拡張機能をインストールした後、これらのプロジェクトを移行することができます。 その後、Visual Studio Code で新しいプロジェクトを作成し、前に作成したロジック アプリの **workflow.definition** ファイルを新しいプロジェクトにコピーできます。 詳細については、「[プライベート プレビューの拡張機能から移行する](#migrate-private-preview)」を参照してください。
    > 
    > 以前のパブリック プレビュー拡張機能を使用してロジック アプリ プロジェクトを作成した場合は、移行手順を実行せずに、これらのプロジェクトを引き続き使用できます。

    ****Azure Logic Apps (プレビュー)** 拡張機能をインストールするには、次の手順に従います。**

    1. Visual Studio Code の左側のツールバーで、 **[拡張機能]** を選択します。

    1. 拡張機能の検索ボックスに、「`azure logic apps preview`」と入力します。 結果リストから、 **[Azure Logic Apps (プレビュー)]** **>** **[インストール]** の順に選択します。

       インストールが完了すると、プレビューの拡張機能は **[拡張機能:インストール済み]** リストに表示されます。

       ![Visual Studio Code にインストールされている拡張機能の一覧で下線が付いた "Azure Logic Apps (プレビュー)" 拡張機能を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-extension-installed.png)

       > [!TIP]
       > インストール済みの一覧に拡張機能が表示されない場合は、Visual Studio Code を再起動してみてください。

* JavaScript を実行する[インライン コード操作アクション](../logic-apps/logic-apps-add-run-inline-code.md)を使用するには、[Node.js バージョン 10.x.x、11.x.x、または 12.x.x](https://nodejs.org/en/download/releases/) をインストールします。

  > [!TIP] 
  > Windows の場合は、MSI バージョンをダウンロードします。 代わりに ZIP バージョンを使用する場合は、オペレーティング システムの PATH 環境変数を使用して、手動で Node.js を使用できるようにする必要があります。

* [組み込みの HTTP Webhook トリガー](../connectors/connectors-native-webhook.md)など、Webhook ベースのトリガーとアクションを Visual Studio Code でローカルに実行するには、[コールバック URL への転送を設定する](#webhook-setup)必要があります。

* この記事で作成したロジック アプリの例をテストするには、Request トリガーに呼び出しを送信できるツールが必要です。これは、ロジック アプリの例の最初の手順です。 このようなツールがない場合は、[Postman](https://www.postman.com/downloads/) をダウンロードしてインストールし、使用することができます。

* [Application Insights](../azure-monitor/app/app-insights-overview.md) の使用をサポートする設定で、ロジックアプリを作成してデプロイする場合は、必要に応じて、ロジック アプリの診断ログとトレースを有効にすることができます。 ロジック アプリを Visual Studio Code からデプロイするとき、またはデプロイした後で、それを行うことができます。 Application Insights インスタンスを用意する必要がありますが、このリソースは、[事前に](../azure-monitor/app/create-workspace-resource.md)、ロジック アプリをデプロイするときに、またはデプロイ後に、作成することができます。

<a name="migrate-private-preview"></a>

## <a name="migrate-from-private-preview-extension"></a>プライベート プレビューの拡張機能から移行する

**Azure Logic Apps (プライベート プレビュー)** 拡張機能を使用して作成したロジック アプリ プロジェクトは、パブリック プレビューの拡張機能では動作しません。 ただし、次の手順に従って、これらのプロジェクトを新しいプロジェクトに移行できます。

1. プライベート プレビューの拡張機能をアンインストールします。

1. 次の場所にある関連する拡張機能バンドルと NuGet パッケージのフォルダーをすべて削除します。

   * **Microsoft.Azure.Functions.ExtensionBundle.Workflows** フォルダー。このフォルダーには以前の拡張機能バンドルが含まれ、次のいずれかのパスに配置されています。

     * `C:\Users\{userName}\AppData\Local\Temp\Functions\ExtensionBundles`

     * `C:\Users\{userName}\.azure-functions-core-tools\Functions\ExtensionBundles`

   * **microsoft.azure.workflows.webjobs.extension** フォルダー。このフォルダーはプライベート プレビュー拡張機能の [NuGet](/nuget/what-is-nuget) キャッシュであり、次のパスに配置されています。

     `C:\Users\{userName}\.nuget\packages`

1. **Azure Logic Apps (プレビュー)** 拡張機能をインストールします。

1. Visual Studio Code で新しいプロジェクトを作成します。

1. 前に作成したロジック アプリの **workflow.definition** ファイルを、新しいプロジェクトにコピーします。

<a name="set-up"></a>

## <a name="set-up-visual-studio-code"></a>Visual Studio Code を設定する

1. すべての拡張機能が正しくインストールされていることを確認するには、Visual Studio Code を再読み込みまたは再起動します。

1. プレビューの拡張機能が最新の更新プログラムを取得できるように、Visual Studio Code によって拡張機能の更新プログラムが自動的に検出されてインストールされるようになっていることを確認します。 このようにしないと、古いバージョンのアンインストールと最新バージョンのインストールを、手動で行う必要があります。

   1. **[ファイル]** メニューで、 **[基本設定]** **>** **[設定]** の順に選択します。

   1. **[ユーザー]** タブで、 **[機能]** **>** **[拡張機能]** の順に選択します。

   1. **[更新プログラムの自動チェック]** と **[自動更新]** が選択されていることを確認します。

また、既定では次の設定が有効になっており、Logic Apps のプレビュー拡張機能用に設定されています。

* **[Azure Logic Apps V2:Project Runtime]\(Azure Logic Apps V2: プロジェクト ランタイム\)** で、バージョンが **3 前後** に設定されています

  > [!NOTE]
  > [インライン コード操作アクション](../logic-apps/logic-apps-add-run-inline-code.md)を使用するには、このバージョンが必要です。

* **[Azure Logic Apps V2: Experimental View Manager]\(Azure Logic Apps V2: 試験ビュー マネージャー\)** で、Visual Studio Code の最新のデザイナーが有効になっています。 デザイナーで、項目のドラッグ アンド ドロップなどで問題が発生する場合は、この設定をオフにします。

これらの設定を見つけて確認するには、次の手順に従います。

1. **[ファイル]** メニューで、 **[基本設定]** **>** **[設定]** の順に選択します。

1. **[ユーザー]** タブで、 **>** **[拡張機能]** **>** **[Azure Logic Apps (プレビュー)]** の順に選択します。

   たとえば、ここで **[Azure Logic Apps V2: Project Runtime]\(Azure Logic Apps V2: プロジェクト ランタイム\)** の設定を見つけたり、検索ボックスを使用して他の設定を探したりすることができます。

   !["Azure Logic Apps (プレビュー)" 拡張機能の Visual Studio Code 設定を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/azure-logic-apps-preview-settings.png)

<a name="connect-azure-account"></a>

## <a name="connect-to-your-azure-account"></a>Azure アカウントに接続する

1. Visual Studio Code のアクティビティ バーで、Azure アイコンを選択します。

   ![Visual Studio Code のアクティビティ バーと選択された Azure アイコンを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-azure-icon.png)

1. Azure ウィンドウの **[Azure:Logic Apps (プレビュー)]** で、 **[Azure へのサインイン]** を選択します。 Visual Studio Code 認証ページが表示されたら、Azure アカウントでサインインします。

   ![Azure ウィンドウと Azure にサインインするために選択されたリンクを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/sign-in-azure-subscription.png)

   サインインすると、Azure ウィンドウに Azure アカウントのサブスクリプションが表示されます。 パブリックにリリースされた拡張機能もある場合は、その拡張機能を使用して作成したロジック アプリを、 **[ロジック アプリ (プレビュー)]** セクションではなく、 **[Logic Apps]** セクションで見つけることができます。
   
   期待されるサブスクリプションが表示されない場合、または特定のサブスクリプションのみが表示されるようにする場合は、次の手順を実行します。

   1. サブスクリプションの一覧で、最初のサブスクリプションの横にあるポインターを、 **[サブスクリプションの選択]** ボタン (フィルター アイコン) が表示されるまで移動します。 フィルター アイコンを選択します。

      ![Azure ウィンドウと選択されたフィルター アイコンを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/filter-subscription-list.png)

      または、Visual Studio Code のステータス バーで Azure アカウントを選択します。 

   1. 別のサブスクリプションの一覧が表示されたら、目的のサブスクリプションを選択し、 **[OK]** を必ず選択してください。

<a name="create-project"></a>

## <a name="create-a-local-project"></a>ローカル プロジェクトを作成する

ロジック アプリを作成する前に、Visual Studio Code からロジック アプリを管理、実行、およびデプロイできるように、ローカル プロジェクトを作成します。 基になるプロジェクトは、Azure Functions プロジェクト (関数アプリ プロジェクトとも呼ばれます) に似ています。 ただし、これらの種類のプロジェクトは相互に分離されているので、ロジック アプリと関数アプリを同じプロジェクトで使用することはできません。

1. コンピューター上に、Visual Studio Code で後で作成するプロジェクトに使用する "*空の*" ローカル フォルダーを作成します。

1. Visual Studio Code で、開いているすべてのフォルダーを閉じます。

1. Azure ウィンドウで、 **[Azure:Logic Apps (プレビュー)]** の横にある **[新しいプロジェクトの作成]** (フォルダーと稲妻が表示されたアイコン) を選択します。

   ![[新しいプロジェクトの作成] が選択されている Azure ウィンドウのツールバーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/create-new-project-folder.png)

1. Windows Defender ファイアウォールによって `Code.exe` (Visual Studio Code) および `func.exe` (Azure Functions Core Tools) にネットワーク アクセスを許可するように求められた場合は、 **[プライベート ネットワーク (ホーム ネットワークや社内ネットワークなど)]** **>** **[アクセスを許可]** の順に選択します。

1. プロジェクト フォルダーを作成した場所を参照し、そのフォルダーを選択して続行します。

   ![新しく作成されたプロジェクト フォルダーと [選択] ボタンが選択された [フォルダーの選択] ダイアログ ボックスを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-project-folder.png)

1. 表示されたテンプレートの一覧で、 **[ステートフル ワークフロー]** または **[ステートレス ワークフロー]** を選択します。 この例では **[ステートフル ワークフロー]** を選択します。

   ![[ステートフル ワークフロー] が選択されているワークフロー テンプレートの一覧を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-stateful-stateless-workflow.png)

1. ワークフローの名前を指定して、Enter キーを押します。 この例では、`Fabrikam-Stateful-Workflow` という名前を使用しています。

   ![[Create new Stateful Workflow (3/4)]\(新しいステートフル ワークフローの作成 (3/4)\) ボックスとワークフロー名 "Fabrikam-Stateful-Workflow" が示されているスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/name-your-workflow.png)

   Visual Studio Code によるプロジェクトの作成が完了し、ワークフローの **workflow.json** ファイルがコード エディターで開かれます。

   > [!NOTE]
   > プロジェクトを開く方法の選択を求めるメッセージが表示されたら、現在の Visual Studio Code ウィンドウでプロジェクトを開く場合は、 **[現在のウィンドウで開く]** を選択します。 Visual Studio Code の新しいインスタンスを開く場合は、 **[新しいウィンドウで開く]** を選択します。

1. Visual Studio のツール バーで、[エクスプローラー] ウィンドウを開きます (まだ開いていない場合)。

   [エクスプローラー] ペインにプロジェクトが表示されます。そこには、自動的に生成されたプロジェクト ファイルが含まれています。 たとえば、プロジェクトには、ワークフローの名前を示すフォルダーがあります。 このフォルダーにある **workflow.json** ファイルには、ワークフローの基になる JSON 定義が含まれています。

   ![プロジェクト フォルダー、ワークフロー フォルダー、"workflow. json" ファイルが表示されている [エクスプローラー] ペインのスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/local-project-created.png)

1. macOS または Linux を使用している場合は、次の手順に従って、ストレージ アカウントへのアクセスを設定します。この操作は、プロジェクトをローカルで実行するために必要です。

   1. プロジェクトのルート フォルダーで、**local.settings.json** ファイルを開きます。

      ![[エクスプローラー] ペインとプロジェクトの "local.settings.json" ファイルが示されているスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/local-settings-json-files.png)

   1. `AzureWebJobsStorage` プロパティの値を、前に保存したストレージ アカウントの接続文字列に置き換えます。次に例を示します。

      次の処理の前

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "UseDevelopmentStorage=true",
            "FUNCTIONS_WORKER_RUNTIME": "dotnet"
          }
      }
      ```

      次の処理の後

      ```json
      {
         "IsEncrypted": false,
         "Values": {
            "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net",
           "FUNCTIONS_WORKER_RUNTIME": "dotnet"
         }
      }
      ```

   1. 完了したら、変更を保存してください。

<a name="enable-built-in-connector-authoring"></a>

## <a name="enable-built-in-connector-authoring"></a>組み込みコネクタの作成を有効にする

[プレビュー リリースの機能拡張フレームワーク](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)を使用して、必要なサービス用の独自の組み込みコネクタを作成できます。 Azure Service Bus や SQL Server などの組み込みコネクタと同様に、これらのコネクタは、より高いスループット、短い待機時間、ローカル接続を実現し、プレビュー ランタイムと同じプロセスでネイティブに実行されます。

作成機能は現在 Visual Studio Code でのみ使用できますが、既定では有効になっていません。 これらのコネクタを作成するには、まず、プロジェクトを拡張バンドルベース (Node.js) から NuGet パッケージベース (.NET) に変換する必要があります。

> [!IMPORTANT]
> この操作は一方向の操作であり、元に戻すことはできません。

1. [エクスプローラー] ウィンドウのプロジェクトのルートで、他のすべてのファイルとフォルダーの下にある空白の領域の上にマウス ポインターを移動し、ショートカット メニューを開いて、 **[Nuget ベースのロジック アプリ プロジェクトに変換]** を選択します。

   ![[エクスプローラー] ウィンドウに、プロジェクト ウィンドウの空白の領域から開いたプロジェクトのショートカット メニューが表示されているスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/convert-logic-app-project.png)

1. プロンプトが表示されたら、プロジェクトの変換を確認します。

1. 続行するには、[あらゆる場所で実行される Azure Logic Apps - 組み込みコネクタの拡張性](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)に関する記事の手順を確認して実行してください。

<a name="open-workflow-definition-designer"></a>

## <a name="open-the-workflow-definition-file-in-the-designer"></a>デザイナーでワークフロー定義ファイルを開く

1. 次のコマンドを実行して、コンピューターにインストールされているバージョンを確認します。

   `..\Users\{yourUserName}\dotnet --list-sdks`

   .NET Core SDK 5.x バージョンの場合は、ロジック アプリの基になるワークフロー定義をデザイナーで開くことができないおそれがあります。 このバージョンをアンインストールするのではなく、プロジェクトのルート フォルダーで、存在している 3.1.201 より後の .NET Core ランタイム 3.x バージョンを参照する **global.json** ファイルを作成します。次に例を示します。

   ```json
   {
      "sdk": {
         "version": "3.1.8",
         "rollForward": "disable"
      }
   }
   ```

   > [!IMPORTANT]
   > Visual Studio Code 内からプロジェクトのルート フォルダーに **global.json** ファイルを明示的に追加します。 そうしないと、デザイナーは開きません。

1. ワークフローのプロジェクト フォルダーを展開します。 **workflow.json** ファイルのショートカット メニューを開き、 **[デザイナーで開く]** を選択します。

   ![エクスプローラー ウィンドウが表示され、workflow.json ファイルのショートカット ウィンドウで [デザイナーで開く] が選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/open-definition-file-in-designer.png)

1. **[Azure でコネクタを有効にする]** の一覧で、 **[Azure のコネクタを使用する]** を選択します。これは、Azure サービスのコネクタだけでなく、Azure portal で利用可能かつデプロイされているすべてのマネージド コネクタに適用されます。

   ![[Azure でコネクタを有効にする] の一覧が開き、[Azure のコネクタを使用する] が選択されているエクスプローラー ウィンドウを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/use-connectors-from-azure.png)

   > [!NOTE]
   > ステートレスなワークフローでは、現在、トリガーではなく Azure にデプロイされている [マネージド コネクタ](../connectors/apis-list.md#managed-api-connectors)に対する *アクション* のみがサポートされています。 ステートレスなワークフローにおいて Azure のコネクタを有効にすることもできますが、デザイナーには選択できるマネージド コネクタ トリガーは表示されません。

1. **[サブスクリプションの選択]** の一覧で、ロジック アプリ プロジェクトに使用する Azure サブスクリプションを選択します。

   ![[サブスクリプションの選択] ボックスと選択されたサブスクリプションが表示されている [エクスプローラー] ペインを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-subscription.png)

1. リソース グループの一覧で、 **[新しいリソース グループの作成]** を選択します。

   ![リソース グループの一覧と選択された [新しいリソース グループの作成] が表示されている [エクスプローラー] ペインを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/create-select-resource-group.png)

1. リソース グループの名前を指定し、Enter キーを押します。 この例では、`Fabrikam-Workflows-RG` を使用します。

   ![エクスプローラー ウィンドウとリソース グループ名のボックスを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-name-for-resource-group.png)

1. 場所の一覧で、リソース グループとリソースの作成に使用する Azure リージョンを見つけて選択します。 この例では、 **[米国中西部]** を使用します。

   ![エクスプローラー ウィンドウと場所の一覧が表示され、[米国中西部] が選択されているスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-azure-region.png)

   このステップを実行すると、Visual Studio Code のワークフロー デザイナーが開きます。

   > [!NOTE]
   > Visual Studio Code でワークフロー デザイン時 API が開始されると、起動に数秒かかる場合があるというメッセージが表示されことがあります。 このメッセージは無視することも **[OK]** を選択することもできます。
   >
   > デザイナーが開かれない場合は、トラブルシューティングのセクションの「[デザイナーが開けない](#designer-fails-to-open)」を確認してください。

   デザイナーが表示された後、 **[操作を選択してください]** というプロンプトがデザイナーに表示されて既定で選択され、 **[アクションの追加]** ペインが表示されます。

   ![ワークフロー デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-designer.png)

1. 次に、ワークフローに[トリガーとアクションを追加します](#add-trigger-actions)。

<a name="add-trigger-actions"></a>

## <a name="add-a-trigger-and-actions"></a>トリガーとアクションを追加する

デザイナーを開くと、 **[操作を選択してください]** というプロンプトがデザイナーに表示されて、既定で選択されます。 トリガーとアクションを追加して、ワークフローの作成を開始できるようになりました。

この例のワークフローでは、次のトリガーとアクションを使用します。

* 組み込みの [Request トリガー](../connectors/connectors-native-reqres.md) **[HTTP 要求の受信時]** 。受信呼び出しまたは要求を受信し、他のサービスまたはロジック アプリが呼び出すことができるエンドポイントを作成します。

* [Office 365 Outlook アクション](../connectors/connectors-create-api-office365-outlook.md) **[メールの送信]** 。

* 組み込みの[応答アクション](../connectors/connectors-native-reqres.md)。応答を送信してデータを呼び出し元に返すために使用します。

### <a name="add-the-request-trigger"></a>要求トリガーを追加する

1. ネイティブに実行されるトリガーを選択できるように、デザイナーの横にある **[トリガーの追加]** ウィンドウの **[操作の選択]** 検索ボックスで **[組み込み]** が選択されていることを確認します。

1. **[操作の選択]** 検索ボックスに「`when a http request`」と入力し、 **[HTTP 要求の受信時]** という名前の組み込みの Request トリガーを選択します。

   ![ワークフロー デザイナーと、[HTTP 要求の受信時] トリガーが選択された [トリガーの追加] ペインを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/add-request-trigger.png)

   トリガーがデザイナーに表示されると、トリガーの詳細ウィンドウが開き、トリガーのプロパティ、設定、およびその他のアクションが表示されます。

   ![[HTTP 要求の受信時] トリガーが選択されたワークフロー デザイナーが表示され、トリガー詳細ペインが開いたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/request-trigger-added-to-designer.png)

   > [!TIP]
   > 詳細ウィンドウが表示されない場合は、デザイナーでトリガーが選択されていることを確認します。

1. デザイナーから項目を削除する必要がある場合は、[デザイナーからの項目の削除に関するこちらの手順のようにします](#delete-from-designer)。

### <a name="add-the-office-365-outlook-action"></a>Office 365 Outlook アクションを追加する

1. デザイナーで、追加したトリガーの下の **[新しいステップ]** を選択します。

   デザイナーに **[操作を選択してください]** というプロンプトが表示され、次のアクションを選択できるように **[アクションの追加]** ペインが再び表示されます。

1. Azure にデプロイされているマネージド コネクタのアクションを見つけて選択できるように、 **[アクションの追加]** ウィンドウの **[操作の選択]** 検索ボックスで **[Azure]** を選択します。

   この例では、Office 365 Outlook アクション **[メールの送信 (V2)]** を選択して使用します。

   ![ワークフロー デザイナーが表示され、[アクションの追加] ペインで Office 365 Outlook の [メールの送信] アクションが選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/add-send-email-action.png)

1. アクションの詳細ウィンドウで **[サインイン]** を選択して、電子メール アカウントへの接続を作成できるようにします。

   ![ワークフロー デザイナーと、[サインイン] が選択された [メールの送信 (V2)] ペインを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-sign-in.png)

1. Visual Studio Code で電子メール アカウントへのアクセスに同意するかどうかを確認するメッセージが表示されたら、 **[開く]** を選択します。

   ![アクセス許可を確認する Visual Studio Code プロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/visual-studio-code-open-external-website.png)

   > [!TIP]
   > 今後プロンプトが表示されないようにするには、認証ページを信頼されたドメインとして追加できるように、 **[信頼されているドメインの構成]** を選択します。

1. 後続のプロンプトに従ってサインインし、アクセスを許可して、Visual Studio Code に戻ることを許可します。

   > [!NOTE]
   > プロンプトを完了するまでの時間が長すぎると、認証プロセスがタイムアウトして失敗します。 この場合は、デザイナーに戻り、サインインし直して接続を作成します。

1. Azure Logic Apps (プレビュー) 拡張機能でメール アカウントへのアクセスの同意を求められたら、 **[開く]** を選択します。 後続のプロンプトに従って、アクセスを許可します。

   ![アクセス許可を確認するプレビュー拡張機能のプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/allow-preview-extension-open-uri.png)

   > [!TIP]
   > 今後プロンプトが表示されないようにするには、 **[この拡張機能を再度表示しません]** を選択します。

   Visual Studio Code によって接続が作成された後、一部のコネクタで `The connection will be valid for {n} days only` (接続は n 日だけ有効です) というメッセージが表示されます。 この制限時間は、Visual Studio Code でロジック アプリを作成している期間にのみ適用されます。 デプロイ後は、自動的に有効にされる[システム割り当てマネージド ID](../logic-apps/create-managed-service-identity.md) を使用して実行時にロジック アプリを認証できるため、この制限は適用されなくなります。 このマネージド ID は、接続の作成時に使用する認証資格情報または接続文字列とは異なります。 このシステム割り当てマネージド ID を無効にした場合、接続は実行時に機能しません。

1. デザイナーで **[メールの送信]** アクションが選択されていない場合は、そのアクションを選択します。

1. アクションの詳細ウィンドウの **[パラメーター]** タブで、アクションに必要な情報を指定します。次に例を示します。

   ![Office 365 Outlook の [メールの送信] アクションの詳細が表示されたワークフロー デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/send-email-action-details.png)

   | プロパティ | 必須 | 値 | 説明 |
   |----------|----------|-------|-------------|
   | **To** | はい | <*your-email-address*> | 電子メールの受信者。テストのためにご自分のメール アドレスを指定できます。 この例では、架空のメール アドレス `sophiaowen@fabrikam.com` を使用しています。 |
   | **件名** | はい | `An email from your example workflow` | メールの件名 |
   | **本文** | はい | `Hello from your example workflow!` | メールの本文の内容 |
   ||||

   > [!NOTE]
   > **[設定]** タブ、 **[静的な結果]** タブ、または **[実行までの期間]** タブの詳細ペインで変更を加える場合は、タブを切り替えたり、デザイナーにフォーカスを変更したりする前に、必ず **[完了]** を選択して変更をコミットしてください。 それ以外の場合、Visual Studio Code では変更が保持されません。 詳細については、[GitHub の Logic Apps パブリック プレビューでの既知の問題に関するページ](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)をご確認ください。

1. デザイナーで、 **[保存]** を選択します。

> [!IMPORTANT]
> Webhook ベースのトリガーまたはアクションを使用するワークフローをローカル環境で実行するには ([組み込みの HTTP Webhook トリガーやアクション](../connectors/connectors-native-webhook.md)など)、[Webhook のコールバック URL への転送を設定する](#webhook-setup)ことで、この機能を有効にする必要があります。

<a name="webhook-setup"></a>

## <a name="enable-locally-running-webhooks"></a>ローカルで実行されている Webhook を有効にする

Azure で実行されているロジック アプリで **HTTP Webhook** などの Webhook ベースのトリガーまたはアクションを使用するときは、Logic Apps ランタイムにより、コールバック URL が生成されてサービス エンドポイントに登録されることにより、そのエンドポイントがサブスクライブされます。 その後、トリガーまたはアクションは、サービス エンドポイントで URL が呼び出されるまで待機します。 ただし、Visual Studio Code で作業している場合は、生成されたコールバック URL は `http://localhost:7071/...` で開始します。 この URL は localhost サーバー用であり、サービス エンドポイントでこの URL を呼び出すことができないようにプライベートです。

Visual Studio Code で Webhook ベースのトリガーとアクションをローカルに実行するには、localhost サーバーを公開し、サービス エンドポイントからの呼び出しを Webhook コールバック URL に安全に転送するパブリック URL を、設定する必要があります。 転送サービスと、localhost ポートへの HTTP トンネルが開かれる [**ngrok**](https://ngrok.com/) などのツールを使用するか、または独自のツールを使用することができます。

#### <a name="set-up-call-forwarding-using-ngrok"></a>**ngrok** を使用して呼び出しの転送を設定する

1. 持っていない場合は、[**ngrok** アカウントにサインアップ](https://dashboard.ngrok.com/signup)します。 それ以外の場合は、[自分のアカウントにサインイン](https://dashboard.ngrok.com/login)します。

1. 個人用の認証トークンを取得します。これは、**ngrok** クライアントから、お使いのアカウントに接続してアクセスの認証を行う場合に必要です。

   1. [認証トークン ページ](https://dashboard.ngrok.com/auth/your-authtoken)を表示するには、アカウントのダッシュボード メニューで、 **[Authentication]\(認証\)** を展開し、 **[Your Authtoken]\(自分の認証トークン\)** を選択します。

   1. **[Your Authtoken]\(自分の認証トークン\)** ボックスから安全な場所にトークンをコピーします。

1. [**ngrok** のダウンロード ページ](https://ngrok.com/download)または [自分のアカウントのダッシュボード](https://dashboard.ngrok.com/get-started/setup)から、目的の **ngrok** のバージョンをダウンロードし、.zip ファイルを抽出します。 詳細については、「[手順 1: 解凍してインストールする](https://ngrok.com/download)」を参照してください。

1. 自分のコンピューターで、コマンド プロンプト ツールを開きます。 **ngrok.exe** ファイルがある場所に移動します。

1. 次のコマンドを実行して、**ngrok** クライアントを自分の **ngrok** アカウントに接続します。 詳細については、「[手順 2: アカウントを接続する](https://ngrok.com/download)」を参照してください。

   `ngrok authtoken <your_auth_token>`

1. 次のコマンドを実行して、localhost のポート 7071 への HTTP トンネルを開きます。 詳細については、「[手順 3: 起動する](https://ngrok.com/download)」を参照してください。

   `ngrok http 7071`

1. 出力から、次の行を見つけます。

   `http://<domain>.ngrok.io -> http://localhost:7071`

1. `http://<domain>.ngrok.io` という形式になっている URL をコピーして保存します

#### <a name="set-up-the-forwarding-url-in-your-app-settings"></a>アプリの設定で転送 URL を設定する

1. Visual Studio Code のデザイナーで、 **[HTTP + Webhook]** トリガーまたはアクションを追加します。

1. ホスト エンドポイントの場所についてのプロンプトが表示されたら、前に作成した転送 (リダイレクト) URL を入力します。

   > [!NOTE]
   > プロンプトを無視すると、転送 URL を指定する必要があることを示す警告が表示されます。その場合は、 **[構成]** を選択し、URL を入力します。 この手順を完了すると、後で追加する Webhook トリガーまたはアクションに対してプロンプトが表示されなくなります。
   >
   > プロンプトが再び表示されるようにするには、プロジェクトのルート レベルで **local.settings.json** ファイルのショートカット メニューを開き、 **[Configure Webhook Redirect Endpoint]\(Webhook リダイレクト エンドポイントの構成\)** を選択します。 これでプロンプトが表示されるので、転送 URL を指定できるようになります。

   Visual Studio Code によって、転送 URL がプロジェクトのルート フォルダー内の **local.settings.json** ファイルに追加されます。 `Values` オブジェクトに `Workflows.WebhookRedirectHostUri` という名前のプロパティが表示されるようになり、転送 URL に設定されます。たとえば、次のようになります。
   
   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "node",
         "FUNCTIONS_V2_COMPATIBILITY_MODE": "true",
         <...>
         "Workflows.WebhookRedirectHostUri": "http://xxxXXXXxxxXXX.ngrok.io",
         <...>
      }
   }
   ```

初めてローカル デバッグ セッションを開始するとき、またはデバッグなしでワークフローを実行するときに、Logic Apps ランタイムによってワークフローがサービス エンドポイントに登録され、そのエンドポイントで Webhook 操作の通知がサブスクライブされます。 次にワークフローが実行されるときには、サブスクリプションの登録がローカル ストレージに既に存在するため、ランタイムで登録または再登録は行われません。

ローカルに実行される Webhook ベースのトリガーまたはアクションを使用するワークフロー実行のデバッグ セッションを停止しても、既存のサブスクリプション登録は削除されません。 登録を解除するには、サブスクリプション登録を手動で除去または削除する必要があります。

> [!NOTE]
> ワークフローの実行が開始された後、ターミナル ウィンドウに次の例のようなエラーが表示されることがあります。
>
> `message='Http request failed with unhandled exception of type 'InvalidOperationException' and message: 'System.InvalidOperationException: Synchronous operations are disallowed. Call ReadAsync or set AllowSynchronousIO to true instead.`
>
> この場合は、プロジェクトのルート フォルダーにある **local.settings.json** ファイルを開き、プロパティが `true` に設定されていることを確認します。
>
> `"FUNCTIONS_V2_COMPATIBILITY_MODE": "true"`

<a name="manage-breakpoints"></a>

## <a name="manage-breakpoints-for-debugging"></a>デバッグ用のブレークポイントを管理する

デバッグ セッションを開始してロジック アプリのワークフローを実行およびテストする前に、各ワークフローの **workflow.json** ファイル内に [ブレークポイント](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)を設定できます。 他の設定は必要ありません。 

現時点では、ブレークポイントはアクションについてのみサポートされており、トリガーではサポートされていません。 各アクション定義には、次のブレークポイントの場所があります。

* アクションの名前が記述されている行に、開始ブレークポイントを設定します。 デバッグ セッションの間にこのブレークポイントにヒットすると、アクションの入力を評価前に確認できます。

* アクションの終了中かっこ ( **}** ) が記述されている行に、終了ブレークポイントを設定します。 デバッグ セッションの間にこのブレークポイントにヒットすると、アクションの実行が完了する前に、アクションの結果を確認できます。

ブレークポイントを追加するには、次の手順のようにします。

1. デバッグするワークフローの **workflow.json** ファイルを開きます。

1. ブレークポイントを設定する行で、左側の列の内側を選択します。 ブレークポイントを削除するには、そのブレークポイントを選択します。

   デバッグ セッションを開始すると、コード ウィンドウの左側に [実行] ビューが表示され、上部の近くに [デバッグ] ツール バーが表示されます。

   > [!NOTE]
   > [実行] ビューが自動的に表示されない場合は、Ctrl + Shift + D キーを押します。

1. ブレークポイントにヒットしたときに使用可能な情報を確認するには、[実行] ビューで、 **[変数]** ペインを調べます。

1. ワークフローの実行を続けるには、[デバッグ] ツール バーの **[続行]** (再生ボタン) を選択します。

ブレークポイントは、ワークフローの実行中いつでも追加および削除できます。 ただし、実行の開始後に **workflow.json** ファイルを更新した場合、ブレークポイントは自動的に更新されません。 ブレークポイントを更新するには、ロジック アプリを再起動します。

一般的な情報については、[Visual Studio Code のブレークポイント](https://code.visualstudio.com/docs/editor/debugging#_breakpoints)に関するページを参照してください。

<a name="run-test-debug-locally"></a>

## <a name="run-test-and-debug-locally"></a>ローカル環境で実行、テスト、デバッグする

ロジック アプリをテストするには、次の手順のようにして、デバッグ セッションを開始し、Request トリガーによって作成されたエンドポイントの URL を検索します。 この URL は、後でそのエンドポイントに要求を送信できるようにするために必要です。

1. ステートレス ワークフローをより簡単にデバッグするには、[そのワークフローの実行履歴を有効にする](#enable-run-history-stateless)ことができます。

1. Visual Studio Code のアクティビティ バーで **[実行]** メニューを開き、 **[デバッグの開始]** (F5) を選択します。

   **[ターミナル]** ウィンドウが開き、デバッグ セッションを確認できます。

   > [!NOTE]
   > **"Error exists after running preLaunchTask 'generateDebugSymbols' (preLaunchTask 'generateDebugSymbols' の実行後にエラーがあります)"** というエラーが発生する場合は、トラブルシューティング セクションの「[デバッグ セッションが開始できない](#debugging-fails-to-start)」を参照してください。

1. 次に、Request トリガーでエンドポイントのコールバック URL を検索します。

   1. エクスプローラー ウィンドウを再度開き、プロジェクトを表示できるようにします。

   1. **workflow.json** ファイルのショートカット メニューから、 **[概要]** を選択します。

      ![エクスプローラー ウィンドウが表示され、workflow.json ファイルのショートカット ウィンドウで [概要] が選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/open-workflow-overview.png)

   1. **[コールバック URL]** の値を探します。この値は、Request トリガーの例として次の URL のようになります。

      `http://localhost:7071/api/<workflow-name>/triggers/manual/invoke?api-version=2020-05-01-preview&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<shared-access-signature>`

      ![ワークフローの [概要] ページに [コールバック URL] が表示されているスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/find-callback-url.png)

1. ロジック アプリ ワークフローをトリガーすることによってコールバック URL をテストするには、[Postman](https://www.postman.com/downloads/) を開くか、要求を作成および送信するための任意のツールを開きます。

   この例では、Postman を使用して続行します。 詳細については、[Postman の概要](https://learning.postman.com/docs/getting-started/introduction/)に関するページを参照してください。

   1. Postman のツールバーで、 **[新規]** を選択します。

      ![[新規] ボタンが選択された Postman を示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/postman-create-request.png)

   1. **[新規作成]** ウィンドウの **[構成要素]** で、 **[要求]** を選択します。

   1. **[要求の保存]** ウィンドウの **[要求名]** で、要求の名前を指定します。たとえば、`Test workflow trigger` のように指定します。

   1. **[保存先のコレクションまたはフォルダーの選択]** で、 **[コレクションの作成]** を選択します。

   1. **[すべてのコレクション]** で、要求を整理するために作成するコレクションの名前を指定して Enter キーを押し、 **[<*コレクション名*> に保存]** を選択します。 この例では、`Logic Apps requests` というコレクション名を使用しています。

      Postman の要求ウィンドウが開き、Request トリガーのコールバック URL に要求を送信できるようになります。

      ![要求ウィンドウを開いた Postman を示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/postman-request-pane.png)

   1. Visual Studio Code に戻ります。 ワークフローの [概要] ページから、 **[コールバック URL]** プロパティ値をコピーします。

   1. Postman に戻ります。 [要求] ウィンドウで、既定の要求メソッドとして **[GET]** が表示されているメソッドの一覧の横にあるアドレス ボックスに、先ほどコピーしたコールバック URL を貼り付け、 **[送信]** を選択します。

      ![アドレス ボックスにコールバック URL が入力され、[送信] ボタンが選択されている Postman を示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/postman-test-call-back-url.png)

      ロジック アプリ ワークフローの例では、次の例のような電子メールが送信されます。

      ![例に記載されている Outlook の電子メールを示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-app-result-email.png)

1. Visual Studio Code で、ワークフローの [概要] ページに戻ります。

   ステートフルなワークフローを作成した場合、送信した要求がワークフローをトリガーすると、[概要] ページにワークフローの実行の状態と履歴が表示されます。

   > [!TIP]
   > 実行の状態が表示されない場合は、 **[更新]** を選択して [概要] ページを更新してみてください。 条件が満たされなかったか、データが見つからなかったためにスキップされたトリガーに対しては、実行は行われません。

   ![ワークフローの [概要] ページに実行の状態と履歴が表示されているスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/post-trigger-call.png)

   | 実行の状態 | 説明 |
   |------------|-------------|
   | **Aborted** | 外部に問題が発生したため、実行が停止したか、または完了しませんでした。たとえば、システムの停止や Azure サブスクリプションの中断などです。 |
   | **取り消し済み** | 実行がトリガーされ、開始されましたが、キャンセル要求を受け取りました。 |
   | **Failed** | 実行中に少なくとも 1 つのアクションが失敗しました。 ワークフローの後続のアクションは、エラーを処理するように設定されていません。 |
   | **実行中** | 実行がトリガーされ、進行中です。ただし、この状態は、[アクション制限](logic-apps-limits-and-config.md)または[現在の料金プラン](https://azure.microsoft.com/pricing/details/logic-apps/)によって制限されている実行に対しても表示されます。 <p><p>**ヒント**:[診断ログ](monitor-logic-apps-log-analytics.md)を設定すると、発生するスロットル イベントに関する情報を取得することができます。 |
   | **Succeeded** | 実行は成功しました。 いずれかのアクションが失敗した場合、ワークフロー内の後続のアクションによってそのエラーが処理されます。 |
   | **タイムアウト** | 現在の継続時間が実行継続時間の制限を超えたため、実行がタイムアウトしました。これは、[ **[実行履歴の保持期間 (日数)]** 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)によって制御されます。 実行の継続時間は、実行の開始時刻とその開始時刻での実行継続時間の制限を使用して計算されます。 <p><p>**注**:実行の継続時間が現在の *"実行履歴の保持期間の制限"* も超えている場合は、毎日のクリーンアップ ジョブによって実行履歴から実行が消去されます。これも、[ **[実行履歴の保持期間 (日数)]** 設定](logic-apps-limits-and-config.md#run-duration-retention-limits)によって制御されます。 実行がタイムアウトしたか完了したかにかかわらず、保持期間は常に、実行の開始時刻と "*現在の*" 保持期間の制限を使用して計算されます。 そのため、処理中の実行の継続時間制限を短くすると、実行はタイムアウトします。ただし、実行が実行履歴に残るか消去されるかは、実行の継続時間が保持期間の制限を超えているかどうかに基づいて決まります。 |
   | **待機中** | たとえば、その前のワークフロー インスタンスがまだ実行中である等の理由で、実行が開始されていないか、または一時停止しています。 |
   |||

1. 特定の実行の各ステップの状態とそのステップの入出力を確認するには、その実行の省略記号 ( **...** ) ボタンを選択し、 **[実行の表示]** を選択します。

   ![ワークフローの実行履歴行に省略記号ボタンが表示され、[実行の表示] が選択されているスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/show-run-history.png)

   Visual Studio Code で [モニター] ビューが開き、実行の各ステップの状態が表示されます。

   ![ワークフロー実行の各ステップとその状態を示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-action-status.png)

   > [!NOTE]
   > 実行が失敗し、モニター ビューのステップに `400 Bad Request` エラーが表示された場合、この問題は、長いトリガー名またはアクション名が原因で、基になる URI (Uniform Resource Identifier) が既定の文字制限を超えたために発生している可能性があります。 詳細については、「[400 無効な要求です](#400-bad-request)」を参照してください。

   ワークフローの各ステップに付けられる状態は次のとおりです。

   | アクションの状態 | アイコン | 説明 |
   |---------------|------|-------------|
   | **Aborted** | !["中止" 状態のアクションのアイコン][aborted-icon] | 外部に問題が発生したため、アクションが停止したか、または完了しませんでした。たとえば、システムの停止や Azure サブスクリプションの中断などです。 |
   | **取り消し済み** | !["キャンセル" 状態のアクションのアイコン][cancelled-icon] | アクションは実行中でしたが、取り消す要求を受け取りました。 |
   | **失敗** | !["失敗" 状態のアクションのアイコン][failed-icon] | アクションに失敗しました。 |
   | **実行中** | !["実行中" 状態のアクションのアイコン][running-icon] | アクションは現在実行中です。 |
   | **Skipped** | !["スキップ" 状態のアクションのアイコン][skipped-icon] | 直前のアクションが失敗したため、アクションはスキップされました。 アクションには、現在のアクションを実行する前に、前のアクションが正常に完了している必要がある `runAfter` 条件が含まれています。 |
   | **Succeeded** | !["成功" 状態のアクションのアイコン][succeeded-icon] | アクションに成功しました。 |
   | **再試行により成功** | !["再試行により成功" 状態のアクションのアイコン][succeeded-with-retries-icon] | アクションは成功しましたが、1 回以上の再試行が行われました。 再試行履歴を確認するには、実行履歴の詳細ビューでその操作を選択し、入力と出力を表示します。 |
   | **タイムアウト** | !["タイムアウト" 状態のアクションのアイコン][timed-out-icon] | アクションの設定によって指定されたタイムアウト制限により、アクションが停止しました。 |
   | **待機中** | !["待機中" 状態のアクションのアイコン][waiting-icon] | 呼び出し元からの受信要求を待機している Webhook アクションに適用されます。 |
   ||||

   [aborted-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/aborted.png
   [cancelled-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/cancelled.png
   [failed-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/failed.png
   [running-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/running.png
   [skipped-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/skipped.png
   [succeeded-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded.png
   [succeeded-with-retries-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/succeeded-with-retries.png
   [timed-out-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/timed-out.png
   [waiting-icon]: ./media/create-stateful-stateless-workflows-visual-studio-code/waiting.png

1. 各ステップの入出力を確認するには、検査するステップを選択します。

   ![ワークフロー内の各ステップの状態に加えて、展開された [メールの送信] アクションの入出力を示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details.png)

1. そのステップの未加工の入出力をさらに確認するには、 **[未加工入力の表示]** または **[未加工出力の表示]** を選択します。

1. デバッグ セッションを停止するには、 **[実行]** メニューで、 **[デバッグの停止]** (Shift + F5) を選択します。

<a name="return-response"></a>

## <a name="return-a-response"></a>応答を返す

ロジック アプリに要求を送信した呼び出し元に応答を返すには、Request トリガーで開始したワークフローに対して、組み込みの [Response アクション](../connectors/connectors-native-reqres.md)を使用できます。

1. ワークフロー デザイナーで、 **[メールの送信]** アクションの下の **[新しいステップ]** を選択します。

   デザイナーに **[操作の選択]** プロンプトが表示され、次のアクションを選択できるように **[アクションの追加]** ウィンドウが再び表示されます。

1. **[アクションの追加]** ウィンドウで、 **[アクションの選択]** 検索ボックスの下にある **[組み込み]** が選択されていることを確認します。 検索ボックスに「`response`」と入力し、 **[応答]** アクションを選択します。

   ![応答アクションが選択されたワークフロー デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/add-response-action.png)

   **[応答]** アクションがデザイナーに表示されると、アクションの詳細ウィンドウが自動的に開きます。

   ![[応答] アクションの詳細ウィンドウが開き、[本文] プロパティが [メールの送信] アクションの [本文] プロパティ値に設定されているワークフロー デザイナーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details.png)

1. **[パラメーター]** タブで、呼び出す関数に必要な情報を指定します。

   この例では、 **[メールの送信]** アクションから出力された **[本文]** プロパティ値が返されます。

   1. **[本文]** プロパティ ボックス内をクリックすると、動的コンテンツ リストが表示され、先行するトリガーとワークフロー内のアクションから使用可能な出力値が表示されます。

      ![動的コンテンツ リストが表示されるように、[本文] プロパティ内にマウス ポインターを置いた [応答] アクションの詳細ウィンドウを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/open-dynamic-content-list.png)

   1. 動的コンテンツ リストの **[メールの送信]** から **[本文]** を選択します。

      ![開いた動的コンテンツ リストを示すスクリーンショット。 リストの [メールの送信] ヘッダーの下で、[本文] 出力値が選択されています。](./media/create-stateful-stateless-workflows-visual-studio-code/select-send-email-action-body-output-value.png)

      完了すると、[応答] アクションの **[本文]** プロパティが、 **[メールの送信]** アクションの **[本文]** 出力値に設定されます。

      ![ワークフロー内の各ステップの状態に加えて、展開された [応答] アクションの入出力を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/response-action-details-body-property.png)

1. デザイナーで、 **[保存]** を選択します。

<a name="retest-workflow"></a>

## <a name="retest-your-logic-app"></a>ロジック アプリを再テストする

ロジック アプリを更新した後、Visual Studio でデバッガーを再実行し、更新したロジック アプリをトリガーする別の要求を送信することで、別のテストを実行できます。手順は「[ローカル環境で実行、テスト、デバッグする](#run-test-debug-locally)」と同じです。

1. Visual Studio Code のアクティビティ バーで **[実行]** メニューを開き、 **[デバッグの開始]** (F5) を選択します。

1. Postman またはツールで要求を作成して送信するには、別の要求を送信してワークフローをトリガーします。

1. ステートフルなワークフローを作成した場合は、ワークフローの [概要] ページで最新の実行の状態を確認します。 その実行の各ステップの状態、入力、および出力を表示するには、その実行の省略記号 ( **...** ) ボタンを選択し、 **[実行の表示]** を選択します。

   例として、サンプル ワークフローが [応答] アクションを使用して更新された後の実行のステップ バイ ステップの状態を次に示します。

   ![更新されたワークフロー内の各ステップの状態に加えて、展開された [応答] アクションの入出力を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/run-history-details-rerun.png)

1. デバッグ セッションを停止するには、 **[実行]** メニューで、 **[デバッグの停止]** (Shift + F5) を選択します。

<a name="firewall-setup"></a>

##  <a name="find-domain-names-for-firewall-access"></a>ファイアウォール アクセス用のドメイン名を検索する

ロジック アプリのワークフローをデプロイして Azure portal で実行する前に、環境内にトラフィックを制限する厳しいネットワーク要件またはファイアウォールがある場合は、ワークフロー内に存在するすべてのトリガーまたはアクション接続に対するアクセス許可を設定する必要があります。

これらの接続の完全修飾ドメイン名 (FQDN) を検索するには、次の手順を実行します。

1. ロジック アプリ プロジェクトで、**connections.js** ファイル (最初の接続ベースのトリガーまたはアクションをワークフローに追加した後に作成されます) を開いて、`managedApiConnections` オブジェクトを検索します。

1. 作成した接続ごとに、`connectionRuntimeUrl` プロパティ値を見つけてコピーし、安全な場所に保存して、この情報を使用してファイアウォールを設定できるようにします。

   この例の **connections.js** ファイルには、これらの `connectionRuntimeUrl` 値を含む 2 つの接続 (AS2 接続と Office 365 接続) が含まれています。

   * AS2: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba`

   * Office 365: `"connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f`

   ```json
   {
      "managedApiConnections": {
         "as2": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/as2"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/as2/11d3fec26c87435a80737460c85f42ba,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         },
         "office365": {
            "api": {
               "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/office365"
            },
            "connection": {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Web/connections/{connection-resource-name}"
            },
            "connectionRuntimeUrl": https://9d51d1ffc9f77572.00.common.logic-{Azure-region}.azure-apihub.net/apim/office365/668073340efe481192096ac27e7d467f,
            "authentication": {
               "type":"ManagedServiceIdentity"
            }
         }
      }
   }
   ```

<a name="deploy-azure"></a>

## <a name="deploy-to-azure"></a>Azure にデプロイ

Visual Studio Code からは、プロジェクトを Azure に直接発行することができます。これにより、新しい **ロジック アプリ (プレビュー)** リソースの種類を使用して、ロジック アプリがデプロイされます。 Azure Functions の関数アプリ リソースと同様に、この新しいリソースの種類のデプロイでは、デプロイ時に設定できる[ホスティング プランと価格レベル](../app-service/overview-hosting-plans.md)を選択する必要があります。 ホスティング プランと価格の詳細については、次のトピックを参照してください。

* [Azure App Service でスケールアップする](../app-service/manage-scale-up.md)
* [Azure Functions のスケールとホスティング](../azure-functions/functions-scale.md)

ロジック アプリは新しいリソースとして発行できます。これにより、[関数アプリの要件と同様に、Azure ストレージ アカウントなど](../azure-functions/storage-considerations.md)、必要なリソースが自動的に作成されます。 または、以前にデプロイされた **ロジック アプリ (プレビュー)** リソースにロジック アプリを発行することもできます。これにより、そのロジック アプリは上書きされます。

### <a name="publish-to-a-new-logic-app-preview-resource"></a>新しいロジック アプリ (プレビュー) リソースに発行する

1. Visual Studio Code のアクティビティ バーで、Azure アイコンを選択します。

1. **[Azure:Logic Apps (プレビュー)]** ウィンドウのツールバーで、 **[ロジック アプリにデプロイする]** を選択します。

   ![[Azure:Logic Apps (プレビュー)] ウィンドウと [ロジック アプリにデプロイする] が選択されたウィンドウのツールバーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/deploy-to-logic-app.png)

1. プロンプトが表示される場合は、ロジック アプリのデプロイに使用する Azure サブスクリプションを選択します。

1. Visual Studio Code で開いたリストで、次のオプションから選択します。

   * **[Azure で新しいロジック アプリ (プレビュー) を作成する]** (クイック)
   * **[Azure Advanced で新しいロジック アプリ (プレビュー) を作成する]**
   * 以前にデプロイされた **[ロジック アプリ (プレビュー)]** リソース (存在する場合)

   この例では、 **[Azure Advanced で新しいロジック アプリ (プレビュー) を作成する]** を選択して続行します。

   ![[Azure:Logic Apps (プレビュー)] ウィンドウと、[Azure で新しいロジック アプリ (プレビュー) を作成する] が選択されたリストを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-create-logic-app-options.png)

1. 新しい **[ロジック アプリ (プレビュー)]** リソースを作成するには、次の手順を実行します。

   1. 新しいロジック アプリのグローバルに一意の名前を指定します。これは、 **[ロジック アプリ (プレビュー)]** リソースに使用する名前です。 この例では、`Fabrikam-Workflows-App` を使用します。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、作成する新しいロジック アプリの名前を入力するプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/enter-logic-app-name.png)

   1. 新しいロジック アプリの [ホスティング プラン](../app-service/overview-hosting-plans.md)として、[ **[App Service プラン]** (Dedicated)](../azure-functions/dedicated-plan.md) または [ **[Premium]**](../azure-functions/functions-premium-plan.md) を選択します。

      > [!IMPORTANT]
      > このリソースの種類には、従量課金プランはサポートされておらず、使用できません。 選択したプランは、後で利用できる機能と価格レベルに影響します。 詳細については、次のトピックをご覧ください。 
      >
      > * [Azure Functions のスケールとホスティング](../azure-functions/functions-scale.md)
      > * [App Service の価格の詳細](https://azure.microsoft.com/pricing/details/app-service/)
      >
      > たとえば、Premium プランでは、ロジック アプリを作成してデプロイする場合の Azure Functions と同様に、Azure 仮想ネットワークとのプライベートな接続や統合などのネットワーク機能にアクセスできます。 
      > 詳細については、次のトピックをご覧ください。
      > 
      > * [Azure Functions のネットワーク オプション](../azure-functions/functions-networking-options.md)
      > * [あらゆる場所で実行される Azure Logic Apps - Azure Logic Apps プレビューによるネットワークの可能性](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

      この例では、 **[App Service プラン]** を使用します。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、[App Service プラン] または [Premium] を選択するように求めるプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-hosting-plan.png)

   1. 新しい App Service プランを作成するか、既存のプランを選択します。 この例では、 **[新しい App Service プランを作成する]** を選択します。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、[新しい App Service プランを作成する] または既存の App Service プランを選択するように求めるプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/create-app-service-plan.png)

   1. App Service プランの名前を入力して、プランの[価格レベル](../app-service/overview-hosting-plans.md)を選択します。 この例では、 **[F1 Free]** プランを選択します。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、価格レベルを選択するように求めるプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/select-pricing-tier.png)

   1. 最適なパフォーマンスを得るには、デプロイ用のプロジェクトと同じリソース グループを見つけて選択します。

      > [!NOTE]
      > 別のリソース グループを作成したり、使用したりすることはできますが、パフォーマンスに影響を与える可能性があります。 別のリソース グループを作成または選択した場合、確認プロンプトが表示された後にキャンセルすると、デプロイも取り消されます。

   1. ステートフルなワークフローの場合は、 **[新しいストレージ アカウントを作成する]** または既存のストレージ アカウントを選択します。

      ![[Azure:Logic Apps (プレビュー)] ウィンドウと、ストレージ アカウントを作成または選択するように求めるプロンプトを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/create-storage-account.png)

   1. ロジック アプリの作成とデプロイの設定で [Application Insights](../azure-monitor/app/app-insights-overview.md) の使用がサポートされている場合は、必要に応じて、ロジック アプリの診断ログとトレースを有効にすることができます。 ロジック アプリを Visual Studio Code からデプロイするとき、またはデプロイした後で、それを行うことができます。 Application Insights インスタンスを用意する必要がありますが、このリソースは、[事前に](../azure-monitor/app/create-workspace-resource.md)、ロジック アプリをデプロイするときに、またはデプロイ後に、作成することができます。

      ここでログとトレースを有効にするには、次の手順のようにします。

      1. 既存の Application Insights リソースを選択するか、**新しい Application Insights リソースを作成** します。

      1. [Azure portal](https://portal.azure.com) で、お使いの Application Insights リソースに移動します。

      1. リソース メニューで **[概要]** を選択します。 **[インストルメンテーション キー]** の値を見つけてコピーします。

      1. Visual Studio Code で、プロジェクトのルート フォルダーにある **local.settings.json** ファイルを開きます。

      1. `Values` オブジェクトで `APPINSIGHTS_INSTRUMENTATIONKEY` プロパティを追加し、値にインストルメンテーション キーを設定します。次に例を示します。

         ```json
         {
            "IsEncrypted": false,
            "Values": {
               "AzureWebJobsStorage": "UseDevelopmentStorage=true",
               "FUNCTIONS_WORKER_RUNTIME": "dotnet",
               "APPINSIGHTS_INSTRUMENTATIONKEY": <instrumentation-key>
            }
         }
         ```

         > [!TIP]
         > トリガーとアクションの名前が Application Insights インスタンスに正しく表示されているかどうかを確認できます。
         >
         > 1. Azure portal で、お使いの Application Insights リソースに移動します。
         >
         > 2. リソース メニューの **[調査]** で、 **[アプリケーション マップ]** を選択します。
         >
         > 3. マップに表示される操作の名前を確認します。
         >
         > 組み込みトリガーからの一部の受信要求は、アプリケーション マップに重複して表示される場合があります。 
         > これらの重複の場合、`WorkflowName.ActionName` 形式ではなく、ワークフロー名が操作名として使用され、Azure Functions ホストから生成されます。

      1. 次に、ロジック アプリによって収集されて Application Insights インスタンスに送信されるトレース データの重大度レベルを、必要に応じて調整できます。

         ワークフローがトリガーされたときや、アクションが実行されたときなど、ワークフロー関連のイベントが発生するたびに、ランタイムによってさまざまなトレースが出力されます。 これらのトレースによってワークフローの有効期間がカバーされ、次のイベントの種類が含まれます (これらだけではありません)。

         * サービスのサービス (開始、停止、エラーなど)。
         * ジョブとディスパッチャーのアクティビティ。
         * ワークフローのアクティビティ (トリガー、アクション、実行など)。
         * ストレージ要求のアクティビティ (成功や失敗など)。
         * HTTP 要求のアクティビティ (受信、送信、成功、失敗など)。
         * すべての開発トレース (デバッグ メッセージなど)。

         各イベントの種類は、重大度レベルに割り当てられています。 たとえば、`Trace` レベルでは最も詳細なメッセージがキャプチャされる一方、`Information` レベルでは、ロジック アプリ、ワークフロー、トリガー、アクションが開始および停止したときなど、ワークフローの一般的なアクティビティがキャプチャされます。 次の表では、重大度レベルとそのトレースの種類について説明します。

         | 重大度レベル | トレースの種類 |
         |----------------|------------|
         | Critical | ロジック アプリで回復不能なエラーを示すログ。 |
         | デバッグ | 開発中の調査に使用できるログ。たとえば、受信と送信の HTTP 呼び出し。 |
         | エラー | ワークフローの実行の失敗だが、ロジック アプリでの一般的なエラーではないものを示すログ。 |
         | Information | ロジック アプリまたはワークフローでの一般的なアクティビティを追跡するログ。次のようなもの。 <p><p>- トリガー、アクション、または実行が開始および終了したとき。 <br>- ロジック アプリが開始または終了したとき。 |
         | Trace | 最も詳細なメッセージが含まれるログ。たとえば、ストレージ要求やディスパッチャーのアクティビティに加えて、ワークフロー実行アクティビティに関連するすべてのメッセージ。 |
         | 警告 | ロジック アプリでの異常な状態だが、実行を妨げはしないものを強調して示すログ。 |
         |||

         重大度レベルを設定するには、プロジェクトのルート レベルで **host.json** ファイルを開き、`logging` オブジェクトを見つけます。 このオブジェクトにより、ロジック アプリ内のすべてのワークフローのログ フィルター処理が制御され、[ログの種類のフィルター処理に対する ASP.NET Core のレイアウト](/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1&preserve-view=true#log-filtering)に従います。

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               }
            }
         }
         ```

         `logging` オブジェクトに、`Host.Triggers.Workflow` プロパティを含む `logLevel` オブジェクトが含まれていない場合は、それらの項目を追加します。 プロパティを、必要なトレースの種類の重大度レベルに設定します。次に例を示します。

         ```json
         {
            "version": "2.0",
            "logging": {
               "applicationInsights": {
                  "samplingExcludedTypes": "Request",
                  "samplingSettings": {
                     "isEnabled": true
                  }
               },
               "logLevel": {
                  "Host.Triggers.Workflow": "Information"
               }
            }
         }
         ```

   デプロイ手順を完了すると、Visual Studio Code によりロジック アプリの発行に必要なリソースの作成とデプロイが開始されます。

1. デプロイ プロセスを確認および監視するには、 **[表示]** メニューで **[出力]** を選択します。 [出力] ウィンドウのツールバーの一覧で、 **[Azure Logic Apps]** を選択します。

   ![ツールバーの一覧で [Azure Logic Apps] が選択された [出力] ウィンドウと、デプロイの進行状況と状態を示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-deployment-output-window.png)

   Visual Studio Code による Azure へのロジック アプリのデプロイが完了すると、次のメッセージが表示されます。

   ![Azure へのデプロイが正常に完了したというメッセージを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/deployment-to-azure-completed.png)

   これで、ロジック アプリが Azure で稼働し、既定で有効になりました。

次に、以下のタスクを実行する方法を学習できます。

* [空のワークフローをプロジェクトに追加する](#add-workflow-existing-project)。

* [Visual Studio Code](#manage-deployed-apps-vs-code) または [Azure portal](#manage-deployed-apps-portal) を使用して、デプロイされたロジック アプリを管理する。

* [ステートレス ワークフローで実行履歴を有効にする](#enable-run-history-stateless)。

* [Azure portal でデプロイされたロジック アプリの監視ビューを有効にする](#enable-monitoring)。

<a name="add-workflow-existing-project"></a>

## <a name="add-blank-workflow-to-project"></a>空のワークフローをプロジェクトに追加する

ロジック アプリ プロジェクトには、複数のワークフローを含めることができます。 空のワークフローをプロジェクトに追加するには、次の手順のようにします。

1. Visual Studio Code のアクティビティ バーで、Azure アイコンを選択します。

1. Azure ウィンドウで、 **[Azure:ロジック アプリ (プレビュー)]** の横の **[ワークフローの作成]** (Azure Logic Apps のアイコン) を選択します。

1. 追加するワークフローの種類として、 **[ステートフル]** または **[ステートレス]** を選択します

1. ワークフローの名前を指定します。

終わると、新しいワークフロー フォルダーとワークフロー定義の **workflow.json** ファイルが、プロジェクトに表示されます。

<a name="manage-deployed-apps-vs-code"></a>

## <a name="manage-deployed-logic-apps-in-visual-studio-code"></a>デプロイされたロジック アプリを Visual Studio Code で管理する

Visual Studio Code では、リソースの種類が元の **[ロジック アプリ]** であるか **[ロジック アプリ (プレビュー)]** であるかに関係なく、Azure サブスクリプションでデプロイされたすべてのロジック アプリを表示できます。また、これらのロジック アプリの管理に役立つタスクを選択できます。 ただし、両方のリソースの種類にアクセスするには、Visual Studio Code 用の **Azure Logic Apps** と **Azure Logic Apps (プレビュー)** の両方の拡張機能が必要です。

1. 左側のツールバーで、Azure アイコンを選択します。 **[Azure: Logic Apps (プレビュー)]** ウィンドウで、サブスクリプションを展開します。これによって、そのサブスクリプションにデプロイされているすべてのロジック アプリが表示されます。

1. 管理するロジック アプリを開きます。 ロジック アプリのショートカット メニューで、実行するタスクを選択します。

   たとえば、デプロイしたロジック アプリの停止、開始、再起動、削除などのタスクを選択できます。

   ![Visual Studio Code と、開いている "Azure Logic Apps (プレビュー)" 拡張機能ウィンドウ、デプロイされたワークフローを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-visual-studio-code.png)

1. ロジック アプリのすべてのワークフローを表示するには、ロジック アプリを展開した後、 **[ワークフロー]** ノードを展開します。

1. 特定のワークフローを表示するには、ワークフローのショートカット メニューを開き、 **[デザイナーで開く]** を選択します。これにより、ワークフローが読み取り専用モードで開きます。

   ワークフローを編集するには、次のオプションがあります。

   * Visual Studio Code のワークフロー デザイナーでプロジェクトの **workflow.json** ファイルを開き、編集を行って、ロジック アプリを Azure に再デプロイします。

   * Azure portal で、[ご利用のロジック アプリを検索して開きます](#manage-deployed-apps-portal)。 ワークフローを検索、編集、保存します。

1. デプロイされたロジック アプリを Azure portal で開くには、ロジック アプリのショートカット メニューを開き、 **[ポータルで開く]** を選択します。

   ブラウザーで Azure portal が開き、Visual Studio Code にサインインしている場合はポータルに自動的にサインインし、ロジック アプリが表示されます。

   ![Visual Studio Code でのロジック アプリのための Azure portal ページを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

   Azure portal に個別にサインインし、ポータルの検索ボックスを使用してロジック アプリを検索し、結果の一覧からロジック アプリを選択することもできます。

   ![Azure portal と、デプロイされているロジック アプリの検索結果が表示された検索バーを示すスクリーンショット。選択された状態で表示されます。](./media/create-stateful-stateless-workflows-visual-studio-code/find-deployed-workflow-azure-portal.png)

<a name="manage-deployed-apps-portal"></a>

## <a name="manage-deployed-logic-apps-in-the-portal"></a>デプロイされたロジック アプリをポータルで管理する

Azure portal では、リソースの種類が元の **[ロジック アプリ]** であるか **[ロジック アプリ (プレビュー)]** であるかに関係なく、Azure サブスクリプションでデプロイされたすべてのロジック アプリを表示できます。 現時点では、各リソースの種類は Azure で個別のカテゴリとして編成および管理されています。 **[ロジック アプリ (プレビュー)]** リソースの種類を持つロジック アプリを検索するには、次の手順を実行します。

1. Azure portal の検索ボックスに、「`logic app preview`」と入力します。 結果の一覧が表示されたら、 **[サービス]** で、 **[ロジック アプリ (プレビュー)]** を選択します。

   ![検索テキスト "logic app preview" が入力された Azure portal の検索ボックスを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/portal-find-logic-app-preview-resource.png)

1. **[ロジック アプリ (プレビュー)]** ウィンドウで、Visual Studio Code からデプロイしたロジック アプリを探して選択します。

   ![Azure portal と、Azure でデプロイされた [ロジック アプリ (プレビュー)] リソースを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/logic-app-preview-resources-pane.png)

   Azure portal で、選択したロジック アプリの個別のリソース ページが開きます。

   ![Azure portal でのロジック アプリ ワークフローのリソース ページを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-workflow-azure-portal.png)

1. このロジック アプリのワークフローを表示するには、ロジック アプリのメニューで **[ワークフロー]** を選択します。

   **[ワークフロー]** ウィンドウには、現在のロジック アプリのすべてのワークフローが表示されます。 この例では、Visual Studio Code で作成したワークフローを示しています。

   ![[ワークフロー] ウィンドウが開き、デプロイされたワークフローを含む [ロジック アプリ (プレビュー)] リソース ページを示すスクリーンショット](./media/create-stateful-stateless-workflows-visual-studio-code/deployed-logic-app-workflows-pane.png)

1. ワークフローを表示するには、 **[ワークフロー]** ウィンドウで、そのワークフローを選択します。

   [ワークフロー] ウィンドウが開き、詳細情報とそのワークフローに対して実行できるタスクが表示されます。

   たとえば、ワークフローのステップを表示するには、 **[デザイナー]** を選択します。

   ![選択されたワークフローの [概要] ウィンドウを示すスクリーンショット。[ワークフロー] メニューには、選択した [デザイナー] コマンドが表示されています。](./media/create-stateful-stateless-workflows-visual-studio-code/workflow-overview-pane-select-designer.png)

   ワークフロー デザイナーが開き、Visual Studio Code で作成したワークフローが表示されます。 これで、Azure portal でこのワークフローに変更を加えることができます。

   ![Visual Studio Code からデプロイされたワークフロー デザイナーとワークフローを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-workflow-designer.png)

<a name="add-workflow-portal"></a>

## <a name="add-another-workflow-in-the-portal"></a>ポータルで別のワークフローを追加する

Azure portal を使用すると、Visual Studio Code からデプロイした **ロジック アプリ (プレビュー)** リソースに空のワークフローを追加し、それらのワークフローを Azure portal でビルドできます。

1. [Azure portal](https://portal.azure.com) で、デプロイされた **[ロジック アプリ (プレビュー)]** リソースを見つけて選択します。

1. ロジック アプリのメニューで、 **[ワークフロー]** を選択します。 **[ワークフロー]** ウィンドウで、 **[追加]** を選択します。

   ![選択されたロジック アプリの [ワークフロー] ウィンドウと、[追加] コマンドが選択されたツールバーを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/add-new-workflow.png)

1. **[新しいワークフロー]** ウィンドウで、ワークフローの名前を指定します。 **[ステートフル]** または **[ステートレス]** **>** **[作成]** の順に選択します。

   Azure で新しいワークフローがデプロイされた後、それが **[ワークフロー]** ペインに表示されたら、そのワークフローを選択し、デザイナーやコード ビューを開くなどして、他のタスクを管理および実行できます。

   ![選択されたワークフローを管理およびレビューのオプションと共に示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/view-new-workflow.png)

   たとえば、新しいワークフローのデザイナーを開くと、空のキャンバスが表示されます。 このワークフローを Azure portal でビルドできるようになりました。

   ![ワークフロー デザイナーと空のワークフローを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/opened-blank-workflow-designer.png)

<a name="enable-run-history-stateless"></a>

## <a name="enable-run-history-for-stateless-workflows"></a>ステートレス ワークフローの実行履歴を有効にする

ステートレス ワークフローをさらに簡単にデバッグするには、そのワークフローの実行履歴を有効にした後、完了したら実行履歴を無効にすることができます。 Visual Studio Code の場合は、以下の手順のようにします。Azure portal で作業している場合は、[Azure portal でのステートフル ワークフローとステートレス ワークフローの作成](create-stateful-stateless-workflows-azure-portal.md#enable-run-history-stateless)に関するページを参照してください。

1. Visual Studio Code プロジェクトで、**workflow-designtime** フォルダーを展開し、**local.settings.json** ファイルを開きます。

1. `Workflows.{yourWorkflowName}.operationOptions` プロパティを追加し、値を `WithStatelessRunHistory` に設定します。次に例を示します。

   **Windows**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "UseDevelopmentStorage=true",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

   **macOS または Linux**

   ```json
   {
      "IsEncrypted": false,
      "Values": {
         "AzureWebJobsStorage": "DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct; \
             AccountKey=<access-key>;EndpointSuffix=core.windows.net",
         "FUNCTIONS_WORKER_RUNTIME": "dotnet",
         "Workflows.{yourWorkflowName}.OperationOptions": "WithStatelessRunHistory"
      }
   }
   ```

1. 完了時に実行履歴を無効にするには、`Workflows.{yourWorkflowName}.OperationOptions` プロパティを `None` に設定するか、プロパティとその値を削除します。

<a name="enable-monitoring"></a>

## <a name="enable-monitoring-view-in-the-azure-portal"></a>Azure portal で監視ビューを有効にする

Visual Studio Code から Azure に **ロジック アプリ (プレビュー)** リソースをデプロイした後は、Azure portal とそのワークフローの **[監視]** を使用して、そのリソース内のワークフローの利用可能な実行履歴と詳細を確認できます。 ただし、最初に、そのロジック アプリ リソースで **監視** ビュー機能を有効にする必要があります。

1. [Azure portal](https://portal.azure.com) で、デプロイされた **[ロジック アプリ (プレビュー)]** リソースを見つけて選択します。

1. そのリソースのメニューで、 **[API]** の下にある **[CORS]** を選択します。

1. **[CORS]** ウィンドウの **[許可されたオリジン]** で、ワイルドカード文字 (*) を追加します。

1. 操作が完了したら、 **[CORS]** のツールバーで、 **[保存]** を選択します。

   ![Azure portal とデプロイされたロジック アプリ (プレビュー) リソースを示すスクリーンショット。 リソース メニューで [CORS] が選択され、[許可されたオリジン] の新しいエントリがワイルドカード文字 (*) に設定されています。](./media/create-stateful-stateless-workflows-visual-studio-code/enable-run-history-deployed-logic-app.png)

<a name="enable-open-application-insights"></a>

## <a name="enable-or-open-application-insights-after-deployment"></a>デプロイの後で Application Insights を有効にするか開く

ワークフローの実行中に、ロジック アプリによって他のイベントと共にテレメトリが出力されます。 このテレメトリを使用して、ワークフローの実行状況や、Logic Apps ランタイムのさまざまな方法での動作を、より明確に把握することができます。 [Application Insights](../azure-monitor/app/app-insights-overview.md) を使用してワークフローを監視でき、ほぼリアルタイムのテレメトリ (ライブ メトリック) が提供されます。 この機能を使用すると、このデータを使用して問題の診断、アラートの設定、グラフの作成を行うときに、エラーやパフォーマンスの問題をより簡単に調査できます。

ロジック アプリの作成とデプロイの設定で [Application Insights](../azure-monitor/app/app-insights-overview.md) の使用がサポートされている場合は、必要に応じて、ロジック アプリの診断ログとトレースを有効にすることができます。 ロジック アプリを Visual Studio Code からデプロイするとき、またはデプロイした後で、それを行うことができます。 Application Insights インスタンスを用意する必要がありますが、このリソースは、[事前に](../azure-monitor/app/create-workspace-resource.md)、ロジック アプリをデプロイするときに、またはデプロイ後に、作成することができます。

デプロイされたロジック アプリで Application Insights を有効にするには、または既に有効になっている場合に Application Insights データを確認するには、次の手順のようにします。

1. Azure portal で、デプロイされたロジック アプリを見つけます。

1. ロジック アプリのメニューの **[設定]** で、 **[Application Insights]** を選択します。

1. Application Insights が有効になっていない場合は、 **[Application Insights]** ペインで、 **[Application Insights を有効にする]** を選択します。 ペインが更新されたら、下部にある **[適用]** を選択します。

   Application Insights が有効になっている場合は、 **[Application Insights]** ペインで、 **[Application Insights データの表示]** を選択します。

Application Insights が開いたら、ロジック アプリのさまざまなメトリックを確認できます。 詳細については、次のトピックをご覧ください。

* [あらゆる場所で実行される Azure Logic Apps - Application Insights で監視する - パート 1](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/1877849)
* [あらゆる場所で実行される Azure Logic Apps - Application Insights で監視する - パート 2](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-monitor-with-application/ba-p/2003332)

<a name="deploy-docker"></a>

## <a name="deploy-to-docker"></a>Docker にデプロイする

[.NET CLI](/dotnet/core/tools/) を使用することで、ホスト環境としての [Docker コンテナー](/visualstudio/docker/tutorials/docker-tutorial#what-is-a-container)にロジック アプリをデプロイできます。 これらのコマンドを使用すると、ロジック アプリのプロジェクトをビルドして発行できます。 その後、ロジック アプリのデプロイ先として Docker コンテナーを作成して実行できます。

Docker に慣れていない場合は、次のトピックをご確認ください。

* [Docker とは](/dotnet/architecture/microservices/container-docker-introduction/docker-defined)
* [コンテナーと Docker の概要](/dotnet/architecture/microservices/container-docker-introduction/)
* [.NET および Docker の概要](/dotnet/core/docker/introduction)
* [Docker のコンテナー、イメージ、およびレジストリ](/dotnet/architecture/microservices/container-docker-introduction/docker-containers-images-registries)
* [チュートリアル: Docker を使ってみる (Visual Studio Code)](/visualstudio/docker/tutorials/docker-tutorial)

### <a name="requirements"></a>必要条件

* ロジック アプリでデプロイに使用される Azure ストレージ アカウント

* Docker コンテナーを構築するときに使用するワークフロー用の Docker ファイル

  たとえば、このサンプル Docker ファイルを使用すると、ロジック アプリがデプロイされます。 Azure portal へのロジック アプリの発行に使用された Azure ストレージ アカウントのアクセス キーを含む接続文字列が指定されます。 この文字列を見つけるには、「[ストレージ アカウントの接続文字列を取得する](#find-storage-account-connection-string)」を参照してください。

   ```text
   FROM mcr.microsoft.com/azure-functions/node:3.0

   ENV AzureWebJobsStorage <storage-account-connection-string>
   ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
       AzureFunctionsJobHost__Logging__Console__IsEnabled=true \
       FUNCTIONS_V2_COMPATIBILITY_MODE=true

   COPY . /home/site/wwwroot

   RUN cd /home/site/wwwroot
   ```

   詳細については、「[Docker ファイルの記述に関するベスト プラクティス](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)」を参照してください

<a name="find-storage-account-connection-string"></a>

### <a name="get-storage-account-connection-string"></a>ストレージ アカウントの接続文字列を取得する

Docker コンテナー イメージをビルドして実行する前に、ストレージ アカウントへのアクセス キーが含まれる接続文字列を取得する必要があります。 以前は、macOS または Linux で拡張機能を使用するとき、またはロジック アプリを Azure portal にデプロイしたときに、このストレージ アカウントを作成しました。

この接続文字列を見つけてコピーするには、次の手順に従います。

1. Azure portal のストレージ アカウント メニューの **[設定]** で、 **[アクセス キー]** を選択します。 

1. **[アクセス キー]** ペインで、ストレージ アカウントの接続文字列を探してコピーします。接続文字列は、次の例のようなものです。

   `DefaultEndpointsProtocol=https;AccountName=fabrikamstorageacct;AccountKey=<access-key>;EndpointSuffix=core.windows.net`

   ![Azure portal およびコピーされたストレージ アカウント アクセス キーと接続文字列が示されているスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/find-storage-account-connection-string.png)

   詳細については、[ストレージ アカウント キーの管理](../storage/common/storage-account-keys-manage.md?tabs=azure-portal#view-account-access-keys)に関するページをご確認ください。

1. デプロイに使用する Docker ファイルにこの文字列を追加できるように、接続文字列を安全な場所に保存します。 

<a name="find-storage-account-master-key"></a>

### <a name="find-master-key-for-storage-account"></a>ストレージ アカウントのマスター キーを見つける

ワークフローに Request トリガーが含まれている場合は、Docker コンテナー イメージをビルドして実行した後で、[トリガーのコールバック URL を取得](#get-callback-url-request-trigger)する必要があります。 このタスクでは、デプロイに使用するストレージ アカウントのマスター キーの値も指定する必要があります。

1. このマスター キーを見つけるには、プロジェクトで、**azure-webjobs-secrets/{deployment-name}/host.json** ファイルを開きます。

1. `AzureWebJobsStorage` プロパティを見つけて、このセクションのキー値をコピーします。

   ```json
   {
      <...>
      "masterKey": {
         "name": "master",
         "value": "<master-key>",
         "encrypted": false
      },
      <...>
   }
   ```

1. このキー値は、後で使用できるように、安全な場所に保存しておいてください。

<a name="build-run-docker-container-image"></a>

### <a name="build-and-run-your-docker-container-image"></a>Docker コンテナー イメージをビルドして実行する

1. Docker ファイルを使用し、次のコマンドを実行して、Docker コンテナー イメージをビルドします。

   `docker build --tag local/workflowcontainer .`

   詳細については、[docker build](https://docs.docker.com/engine/reference/commandline/build/) に関するページを参照してください。

1. 次のコマンドを使用して、コンテナーをローカルで実行します。

   `docker run -e WEBSITE_HOSTNAME=localhost -p 8080:80 local/workflowcontainer`

   詳細については、[docker run](https://docs.docker.com/engine/reference/commandline/run/) に関するページを参照してください。

<a name="get-callback-url-request-trigger"></a>

### <a name="get-callback-url-for-request-trigger"></a>Request トリガーのコールバック URL を取得する

Request トリガーを使用するワークフローの場合は、この要求を送信して、トリガーのコールバック URL を取得します。

`POST /runtime/webhooks/workflow/api/management/workflows/{workflow-name}/triggers/{trigger-name}/listCallbackUrl?api-version=2020-05-01-preview&code={master-key}`

`{trigger-name}` 値は、ワークフローの JSON 定義内に現れる Request トリガーの名前です。 `{master-key}` 値は、**azure-webjobs-secrets/{deployment-name}/host.json** ファイル内の `AzureWebJobsStorage` プロパティに設定した Azure ストレージ アカウントで定義されています。 詳細については、「[ストレージ アカウントのマスター キーを見つける](#find-storage-account-master-key)」を参照してください。

<a name="delete-from-designer"></a>

## <a name="delete-items-from-the-designer"></a>デザイナーから項目を削除する

デザイナーからワークフロー内の項目を削除するには、次のいずれかの手順のようにします。

* 項目を選択し、項目のショートカット メニューを開いて (Shift + F10)、 **[削除]** を選択します。 **[OK]** を選択して確認します。

* 項目を選択して、Delete キーを押します。 **[OK]** を選択して確認します。

* 項目を選択すると、その項目の詳細ペインが開きます。 ペインの右上隅で、省略記号 **[...]** メニューを開き、 **[削除]** を選択します。 **[OK]** を選択して確認します。

  ![詳細ペインが開いてデザイナーに選択した項目が表示され、省略記号ボタンと [削除] コマンドが選択されたスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/delete-item-from-designer.png)

  > [!TIP]
  > 省略記号メニューが表示されない場合は、右上隅にある省略記号 **[...]** ボタンが詳細ペインに表示されるように、Visual Studio Code のウィンドウを十分に広く展開します。

<a name="troubleshooting"></a>

## <a name="troubleshoot-errors-and-problems"></a>エラーと問題のトラブルシューティング

<a name="designer-fails-to-open"></a>

### <a name="designer-fails-to-open"></a>デザイナーが開けない

デザイナーを開こうとすると、 **"Workflow design time could not be started (ワークフロー デザイン時を開始できませんでした)"** というエラーが発生します。 以前にデザイナーを開こうとした後、プロジェクトを中止または削除した場合、拡張機能のバンドルがダウンロードされない可能性があります この理由が問題になっているかどうかを確認するには、次の手順に従います。

  1. Visual Studio Code で、[出力] ウィンドウを開きます。 **[表示]** メニューの **[出力]** を選択します。

  1. [出力] ウィンドウのタイトル バーの一覧から **[Azure Logic Apps (プレビュー)]** を選択して、拡張機能からの出力を確認できるようにします。次に例を示します。

     ![[Azure Logic Apps] が選択されている [出力] ウィンドウを示すスクリーンショット。](./media/create-stateful-stateless-workflows-visual-studio-code/check-outout-window-azure-logic-apps.png)

  1. 出力を確認し、次のエラー メッセージが表示されているかどうかを調べます。

     ```text
     A host error has occurred during startup operation '{operationID}'.
     System.Private.CoreLib: The file 'C:\Users\{userName}\AppData\Local\Temp\Functions\
     ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows\1.1.7\bin\
     DurableTask.AzureStorage.dll' already exists.
     Value cannot be null. (Parameter 'provider')
     Application is shutting down...
     Initialization cancellation requested by runtime.
     Stopping host...
     Host shutdown completed.
     ```

   このエラーを解決するには、 **...\Users\{your-username}\AppData\Local\Temp\Functions\ExtensionBundles** の位置にある **ExtensionBundles** フォルダーを削除し、デザイナーで **workflow.json** ファイルをもう一度開いてみてください。

<a name="missing-triggers-actions"></a>

### <a name="new-triggers-and-actions-are-missing-from-the-designer-picker-for-previously-created-workflows"></a>前に作成したワークフローのデザイナー ピッカーに新しいトリガーとアクションがない

Azure Logic Apps プレビューでは、 **[XML の検証]** や **[XML の変換]** など、Azure 関数の操作、Liquid の操作、XML の操作に対する組み込みアクションがサポートされています。 ただし、以前に作成したロジック アプリでは、Visual Studio Code で古いバージョンの拡張機能バンドル `Microsoft.Azure.Functions.ExtensionBundle.Workflows` が使用されている場合、これらの操作がデザイナー ピッカーに表示されないことがあります。

また、ロジック アプリを作成するときに **[Use connectors from Azure]\(Azure からのコネクタを使用する\)** を有効にするか、選択していない限り、 **[Azure Function Operations]\(Azure 関数操作\)** コネクタとアクションはデザイナー ピッカーに表示されません。 アプリの作成時に Azure によってデプロイされるコネクタを有効にしなかった場合は、Visual Studio Code でプロジェクトからそれらを有効にすることができます。 **workflow.json** のショートカット メニューを開き、 **[Use Connectors from Azure]\(Azure からのコネクタを使用する\)** を選択します。

古くなったバンドルを修正するには、次の手順に従って古いバンドルを削除します。そうすると、Visual Studio Code により拡張機能バンドルが最新バージョンに自動的に更新されます。

> [!NOTE]
> この解決方法は、Visual Studio Code と Azure Logic Apps (プレビュー) 拡張機能を使用して作成およびデプロイしたロジック アプリにのみ適用され、Azure portal を使用して作成したロジック アプリには適用されません。 [サポートされているトリガーとアクションが Azure portal のデザイナーに表示されない場合](create-stateful-stateless-workflows-azure-portal.md#missing-triggers-actions)に関するページを参照してください。

1. 失いたくない作業をすべて保存し、Visual Studio を閉じます。

1. コンピューターで、次のフォルダーに移動します。このフォルダーには、既存のバンドルのバージョン管理されたフォルダーが含まれています。

   `...\Users\{your-username}\.azure-functions-core-tools\Functions\ExtensionBundles\Microsoft.Azure.Functions.ExtensionBundle.Workflows`

1. 以前のバンドルのバージョン フォルダーを削除します。たとえば、バージョン 1.1.3 のフォルダーがある場合は、そのフォルダーを削除します。

1. 次に、必要な NuGet パッケージのバージョン管理されたフォルダーが含まれる次のフォルダーを参照します。

   `...\Users\{your-username}\.nuget\packages\microsoft.azure.workflows.webjobs.extension`

1. 以前のパッケージのバージョン フォルダーを削除します。たとえば、バージョン 1.0.0.8-preview のフォルダーがある場合は、そのフォルダーを削除します。

1. Visual Studio Code とプロジェクト、およびデザイナーで **workflow.json** ファイルを、再度開きます。

不足していたトリガーとアクションが、デザイナーに表示されるようになります。

<a name="400-bad-request"></a>

### <a name="400-bad-request-appears-on-a-trigger-or-action"></a>"400 要求が正しくありません" がトリガーまたはアクションで表示される

実行が失敗し、モニター ビューで実行を検査すると、このエラーが長い名前のトリガーまたはアクションで表示されることがあります。これが原因で、基になる URI (Uniform Resource Identifier) が既定の文字制限を超えてしまいます。

この問題を解決し、長い URI に合わせて調整するには、次の手順に従って、コンピューターの `UrlSegmentMaxCount` と `UrlSegmentMaxLength` のレジストリ キーを編集します。 これらのキーの既定値については、「[Windows 用のレジストリ設定の Http.sys](/troubleshoot/iis/httpsys-registry-windows)」のトピックに記載されています。

> [!IMPORTANT]
> 開始する前に、作業内容が保存されていることを確認してください。 この解決策では、変更を有効にするために対処後にコンピューターを再起動する必要があります。

1. コンピューターで、 **[ファイル名を指定して実行]** ウィンドウを開き、`regedit` コマンドを実行します。これにより、レジストリ エディターが開きます。

1. **[ユーザー アカウント制御]** ボックスで、 **[はい]** を選択して、コンピューターへの変更を許可します。

1. 左側のウィンドウで、 **[コンピューター]** の下のパス **HKEY_LOCAL_MACHINE \SYSTEM\CurrentControlSet\Services\HTTP\Parameters** に沿ってノードを展開し、 **[Parameters]** を選択します。

1. 右側のペインで、`UrlSegmentMaxCount` と `UrlSegmentMaxLength` のレジストリ キーを見つけます。

1. 使用する名前が URI に収容されるように、これらのキーの値を十分に増やします。 これらのキーが存在しない場合は、次の手順に従って **Parameters** フォルダーに追加します。

   1. **[Parameters]** のショートカット メニューから、 **[新規]**  >  **[DWORD (32 ビット) 値]** を選択します。

   1. 表示された編集ボックスに、新しいキー名として `UrlSegmentMaxCount` を入力します。

   1. 新しいキーのショートカット メニューを開き、 **[修正]** を選択します。

   1. 表示された **[文字列の編集]** ボックスで、 **[値のデータ]** に 16 進数形式または 10 進数形式のキー値を入力します。 たとえば、16 進数の `400` は 10 進数の `1024` に相当します。

   1. `UrlSegmentMaxLength` のキー値を追加するには、これらの手順を繰り返します。

   これらのキー値を増やすか、追加すると、レジストリ エディターは次の例のようになります。

   ![レジストリ エディターを示すスクリーンショット。](media/create-stateful-stateless-workflows-visual-studio-code/edit-registry-settings-uri-length.png)

1. 準備ができたら、コンピューターを再起動して変更を有効にします。

<a name="debugging-fails-to-start"></a>

### <a name="debugging-session-fails-to-start"></a>デバッグ セッションが開始できない

デバッグ セッションを開始しようとすると、 **"Error exists after running preLaunchTask 'generateDebugSymbols' (preLaunchTask 'generateDebugSymbols' の実行後にエラーがあります)"** というエラーが発生します。 この問題を解決するには、プロジェクト内の **tasks.json** ファイルを編集して、シンボルの生成をスキップします。

1. プロジェクト内で **.vscode** フォルダーを展開し、**tasks.json** ファイルを開きます。

1. 次のタスクで、`"dependsOn: "generateDebugSymbols"` という行と、前の行の末尾のコンマを削除し、次のようにします。

   次の処理の前

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true,
      "dependsOn": "generateDebugSymbols"
    }
   ```

   次の処理の後

   ```json
    {
      "type": "func",
      "command": "host start",
      "problemMatcher": "$func-watch",
      "isBackground": true
    }
   ```

## <a name="next-steps"></a>次のステップ

Azure Logic Apps (プレビュー) 拡張機能でのエクスペリエンスについて、ご意見をお聞かせください。

* バグまたは問題については、[GitHub で問題を作成してください](https://github.com/Azure/logicapps/issues)。
* 質問、要望、コメント、その他のフィードバックについては、[このフィードバック フォームを使用してください](https://aka.ms/lafeedback)。
