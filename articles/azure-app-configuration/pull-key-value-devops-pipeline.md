---
title: Azure Pipelines を使用して App Configuration から設定をプルする
description: Azure Pipelines を使用して App Configuration ストアへのキー値をプルする方法について説明します
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: how-to
ms.date: 11/17/2020
ms.author: drewbat
ms.openlocfilehash: 7bd163781203a277f4c9d6866a156c11e4d5d520
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979574"
---
# <a name="pull-settings-to-app-configuration-with-azure-pipelines"></a>Azure Pipelines を使用して App Configuration に設定をプルする

[Azure App Configuration](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task) タスクは、App Configuration ストアからキー値をプルし、それを Azure パイプライン変数として設定します。この変数は後続のタスクで使用できます。 このタスクは、キー値を構成ファイルから App Configuration ストアにプッシュする [Azure App Configuration Push](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task-push) タスクを補足するものです。 詳細については、「[Azure Pipelines を使用して App Configuration に設定をプッシュする](push-kv-devops-pipeline.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- App Configuration ストア - [Azure portal](https://portal.azure.com) で無料リソースを作成する
- Azure DevOps プロジェクト - [無料プロジェクトを作成する](https://go.microsoft.com/fwlink/?LinkId=2014881)
- Azure App Configuration タスク - [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AzureAppConfiguration.azure-app-configuration-task#:~:text=Navigate%20to%20the%20Tasks%20tab,the%20Azure%20App%20Configuration%20instance.) から無料でダウンロードする  

## <a name="create-a-service-connection"></a>サービス接続を作成する

[サービス接続](/azure/devops/pipelines/library/service-endpoints)により、Azure DevOps プロジェクトから Azure サブスクリプション内のリソースにアクセスできます。

1. Azure DevOps で、ターゲットのパイプラインを含むプロジェクトに移動し、左下にある **[プロジェクトの設定]** を開きます。
1. **[パイプライン]** の下にある **[サービス接続]** を選択します。
1. 既存のサービス接続がない場合は、画面の中央にある **[サービス接続の作成]** ボタンをクリックします。 あるいは、ページの右上にある **[新しいサービス接続]** をクリックします。
1. **[Azure Resource Manager]** を選択します。
1. **[サービス プリンシパル (自動)]** を選択します。
1. サブスクリプションとリソースを入力します。 サービス接続に名前を付けます。

サービス接続が作成されたので、それに割り当てるサービス プリンシパルの名前を見つけます。 次の手順では、このサービス プリンシパルに新しいロールの割り当てを追加します。

1. **[プロジェクトの設定]**  >  **[サービス接続]** の順に移動します。
1. 前のセクションで作成したサービス接続を選択します。
1. **[サービス プリンシパルの管理]** を選択します。
1. 一覧表示された **[表示名]** をメモします。

## <a name="add-role-assignment"></a>ロールの割り当ての追加

タスクで App Configuration ストアにアクセスできるように、タスク内で使用するサービス接続に適切な App Configuration のロールを割り当てます。

1. ターゲットの App Configuration ストアに移動します。 App Configuration ストアの設定のチュートリアルについては、Azure App Configuration のクイックスタートのいずれかで「[App Configuration ストアを作成する](./quickstart-dotnet-core-app.md#create-an-app-configuration-store)」を参照してください。
1. 左側で、 **[アクセス制御 (IAM)]** を選択します。
1. 上部にある **[+ 追加]** を選択し、 **[ロールの割り当ての追加]** を選択します。
1. **[ロール]** の中から、**App Configuration データ リーダー** を選択します。 このロールを使用すると、タスクで App Configuration ストアから読み取ることができます。 
1. 前のセクションで作成したサービス接続に関連付けるサービス プリンシパルを選択します。

> [!NOTE]
> App Configuration 内の Azure Key Vault 参照を解決するには、参照先の Azure Key vault のシークレットを読み取るためのアクセス許可もサービス接続に付与する必要があります。
  
## <a name="use-in-builds"></a>ビルドでの使用

このセクションでは、Azure DevOps ビルド パイプラインで Azure App Configuration タスクを使用する方法について説明します。

1. **[パイプライン]**  >  **[パイプライン]** の順にクリックして、ビルド パイプラインのページに移動します。 ビルド パイプラインのドキュメントについては、「[最初のパイプラインの作成](/azure/devops/pipelines/create-first-pipeline?tabs=net%2Ctfs-2018-2%2Cbrowser)」を参照してください。
      - 新しいビルド パイプラインを作成する場合は、 **[新しいパイプライン]** をクリックし、パイプラインのリポジトリを選択します。 パイプラインの右側にある **[アシスタントを表示する]** を選択し、**Azure App Configuration** タスクを検索します。
      - 既存のビルド パイプラインを使用する場合は、 **[編集]** を選択してパイプラインを編集します。 **[タスク]** タブで **Azure App Configuration** タスクを検索します。
1. キー値を App Configuration ストアからプルするために必要なタスクのパラメーターを構成します。 パラメーターの説明は、以下の「**パラメーター**」セクション、および各パラメーターの横にあるヒントを参照してください。
      - **[Azure サブスクリプション]** パラメーターを前の手順で作成したサービス接続の名前に設定します。
      - **[App Configuration 名]** を App Configuration ストアのリソース名に設定します。
      - 残りのパラメーターは既定値のままにします。
1. ビルドを保存してキューに登録します。 ビルド ログには、タスクの実行中に発生したすべてのエラーが表示されます。

## <a name="use-in-releases"></a>リリースでの使用

このセクションでは、Azure DevOps リリース パイプラインで Azure App Configuration タスクを使用する方法について説明します。

1. **[パイプライン]**  >  **[リリース]** の順に選択して、リリース パイプラインのページに移動します。 リリース パイプラインのドキュメントについては、「[リリース パイプライン](/azure/devops/pipelines/release)」を参照してください。
1. 既存のリリース パイプラインを選択します。 既存のリリース パイプラインがない場合は、 **[新しいパイプライン]** をクリックして新規作成します。
1. 右上隅にある **[編集]** ボタンを選択して、リリース パイプラインを編集します。
1. **[ステージ]** を選択して、タスクを追加します。 ステージの詳細については、「[Add stages, dependencies, & conditions](/azure/devops/pipelines/release/environments)」(ステージ、依存関係、条件を追加する) を参照してください。
1. [エージェントで実行] の **[+]** をクリックし、 **[追加のタスク]** タブで **Azure App Configuration** タスクを追加します。
1. キー値を App Configuration ストアからプルするために必要なタスク内のパラメーターを構成します。 パラメーターの説明は、以下の「**パラメーター**」セクション、および各パラメーターの横にあるヒントを参照してください。
      - **[Azure サブスクリプション]** パラメーターを前の手順で作成したサービス接続の名前に設定します。
      - **[App Configuration 名]** を App Configuration ストアのリソース名に設定します。
      - 残りのパラメーターは既定値のままにします。
1. リリースを保存してキューに登録します。 リリース ログに、タスクの実行中に発生したすべてのエラーが表示されます。

## <a name="parameters"></a>パラメーター

Azure App Configuration タスクによって、次のパラメーターが使用されます。

- **Azure サブスクリプション**:利用可能な Azure サービス接続を含むドロップダウン。 利用可能な Azure サービス接続の一覧を最新の情報に更新するには、テキストボックスの右側にある **[Azure サブスクリプションの更新]** ボタンを押します。
- **App Configuration の名前**: 選択したサブスクリプションで利用可能な Configuration ストアが読み込まれるドロップダウン。 利用可能な Configuration ストアの一覧を最新の情報に更新するには、テキストボックスの右側にある **[App Configuration 名の更新]** ボタンを押します。
- **キー フィルター**:このフィルターを使用して、Azure App Configuration から要求されるキー値を選択できます。 "*" の値を指定すると、すべてのキー値が選択されます。 詳細については、「[キー値の照会](concept-key-value.md#query-key-values)」を参照してください。
- **ラベル**:App Configuration ストアからキー値を選択するときに使用するラベルを指定します。 ラベルが指定されていない場合は、ラベルのないキー値が取得されます。 ","、"*" の文字は入力できません。
- **キー プレフィックスのトリミング**:変数として設定する前に、App Configuration キーからトリミングする必要がある 1 つ以上のプレフィックスを指定します。 複数のプレフィックスは、改行文字で区切ることができます。

## <a name="use-key-values-in-subsequent-tasks"></a>後続のタスクでキー値を使用する

App Configuration からフェッチされるキー値は、環境変数としてアクセスできるパイプライン変数として設定されます。 環境変数のキーは、プレフィックスをトリミングした後に (指定されている場合)、App Configuration から取得されるキー値のキーです。

たとえば、後続のタスクが PowerShell スクリプトを実行する場合、次のようにキー 'myBuildSetting' を持つキー値を使用できます。
```powershell
echo "$env:myBuildSetting"
```
値がコンソールに出力されます。

> [!NOTE]
> App Configuration 内の Azure Key Vault 参照が解決され、[シークレット変数](/azure/devops/pipelines/process/variables#secret-variables)として設定されます。 Azure Pipelines では、シークレット変数はログからマスクされます。 これらは環境変数としてタスクに渡されないので、代わりに入力として渡す必要があります。 

## <a name="troubleshooting"></a>トラブルシューティング

予期しないエラーが発生した場合、パイプライン変数 `system.debug` を `true`に設置すると、デバッグ ログを有効にすることができます。

## <a name="faq"></a>よく寄せられる質問

**複数のキーとラベルはどのように構成したらよいですか?**

複数のラベル (default と dev など) を構成する必要がある場合もあります。 このシナリオを実装するために、1 つのパイプラインで複数の App Configuration タスクを使用できます。 後の手順でタスクによってフェッチされるキー値は、前の手順の値よりも優先されます。 前述の例では、1 つ目のタスクを使用して default のラベルを持つキー値を選択し、2 番目のタスクで dev ラベルを持つキー値を選択できます。 dev ラベルを持つキーは、default ラベルを持つ同じキーよりも優先されます。
