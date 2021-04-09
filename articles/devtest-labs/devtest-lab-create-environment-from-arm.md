---
title: テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する
description: Azure Resource Manager テンプレートから Azure DevTest Labs で複数 VM の環境と PaaS リソースを作成する方法について説明します
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: 97659d4ab95fdbe75460161d0ceed71a1cb5cf82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88182410"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する

Azure DevTest Labs 環境を使用すると、ユーザーはラボの範囲内において一貫した方法で複雑なインフラストラクチャをすぐにデプロイすることができます。 [Azure Resource Manager テンプレート](../azure-resource-manager/templates/template-syntax.md)を使用すると、DevTest Labs に一連のリソースを含む環境を作成できます。 これらの環境には、Resource Manager テンプレートで作成できるすべての Azure リソースを含めることができます。

[Azure portal](https://portal.azure.com) を使用すると、ラボに[一度に 1 つの仮想マシン (VM) を簡単に追加](devtest-lab-add-vm.md)できます。 ただし、多層 Web アプリや SharePoint ファームのようなシナリオでは、1 つの手順で複数の VM を作成するメカニズムが必要です。 Azure Resource Manager テンプレートを使用することで、Azure のソリューションのインフラストラクチャと構成を定義して、複数の VM を一貫した状態で繰り返しデプロイできます。

Azure Resource Manager テンプレートには、次の利点もあります。

- Azure Resource Manager テンプレートは、GitHub または Azure Repos ソース管理リポジトリから直接読み込まれます。
- ユーザーは、他の種類の [VM ベース](devtest-lab-comparing-vm-base-image-types.md)を使用する場合と同様に、Azure portal から構成済みの Azure Resource Manager テンプレートを選択して環境を作成できます。
- Azure PaaS リソースだけでなく、環境内の IaaS VM を Azure Resource Manager テンプレートからプロビジョニングすることもできます。
- 環境のコストを、他の種類のベースによって作成された個々の VM だけでなくラボで追跡できます。 PaaS リソースが作成され、コストの追跡に表示されます。 ただし、VM の自動シャットダウンは、PaaS のリソースには適用されません。

Resource Manager テンプレートを使用して 1 回の操作で多数のラボ リソースをデプロイ、更新、または削除する利点の詳細については、[Resource Manager テンプレートを使用する利点](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager)に関する記事を参照してください。

> [!NOTE]
> ラボ VM を作成するベースとして Resource Manager テンプレートを使用する場合、複数の VM を作成する場合と 1 つの VM を作成する場合とでいくつかの違いがあります。 詳細については、[仮想マシンの Azure Resource Manager テンプレートの使用](devtest-lab-use-resource-manager-template.md)に関する記事を参照してください。
>

## <a name="use-devtest-labs-public-environments"></a>DevTest Labs のパブリック環境を使用する
Azure DevTest Labs には、[Azure Resource Manager テンプレートのパブリック リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments)が装備されています。このテンプレートを使用すると、自分で外部 GitHub ソースに接続しなくても、環境を作成できます。 このパブリック リポジトリは、ラボを作成するごとに Azure portal で使用できるようになるアーティファクトのパブリック リポジトリと似ています。 環境リポジトリを使用すると、入力パラメーターが少ない、事前に作成された環境テンプレートをすぐに使い始めることができます。 これらのテンプレートを使用すると、ラボ内で PaaS リソースの使い始める作業がスムーズになります。

パブリック リポジトリでは、DevTest Labs チームなどが、Azure Web Apps、Service Fabric クラスター、開発用 SharePoint ファーム環境といった使用頻度の高いテンプレートを作成し、共有しています。 これらのテンプレートは、直接使用するか、ニーズに合わせてカスタマイズすることができます。 詳細については、「[Azure DevTest Labs でのパブリックな環境の構成と使用](devtest-lab-configure-use-public-environments.md)」を参照してください。 独自のテンプレートを作成したら、それをこのリポジトリに保存して他のユーザーと共有したり、独自の Git リポジトリを設定したりすることができます。

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>独自のテンプレート リポジトリを作成する

コードとしてのインフラストラクチャとコードとしての構成のベスト プラクティスの 1 つとして、環境テンプレートをソース管理で管理する必要があります。 Azure DevTest Labs では、このプラクティスに従って、GitHub または Azure Repos のリポジトリからすべての Azure Resource Manager テンプレートが直接読み込まれます。 その結果、Resource Manager のテンプレートは、テスト環境から運用環境まで、リリース サイクル全体で使用することができます。

この場合、リポジトリ内で Azure Resource Manager テンプレートを整理するために従わなければならないルールがいくつかあります。

- マスター テンプレート ファイルの名前は *azuredeploy.json* にする必要があります。

- パラメーター ファイルで定義したパラメーター値を使用する場合、パラメーター ファイルの名前は *azuredeploy.parameters.json* にする必要があります。

  `_artifactsLocation` と `_artifactsLocationSasToken` のパラメーターを使用すると、parametersLink URI の値を構築できるため、DevTest Labs で入れ子にされたテンプレートを自動的に管理できるようになります。 詳細については、「[テスト環境向けに入れ子になったAzure Resource Manager テンプレートをデプロイする](deploy-nested-template-environments.md)」を参照してください。

- 次のように、メタデータを定義して、*metadata.json* という名前のファイルにテンプレートの表示名と説明を指定できます。

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![主要な Azure Resource Manager テンプレート ファイル](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>ラボにテンプレートのリポジトリを追加する

リポジトリを作成して構成したら、Azure portal を使用してそれをラボに追加できます。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧から、目的のラボを選択します。
1. ラボの **[概要]** ウィンドウで、 **[構成とポリシー]** を選択します。

   ![構成とポリシー](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. **[構成とポリシー]** の設定一覧で、 **[リポジトリ]** を選択します。 **Public Artifact Repo** リポジトリはすべてのラボに対して自動的に生成され、[DevTest Labs パブリック GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)に接続されます。

1. Azure Resource Manager テンプレートのリポジトリを追加するには、 **[追加]** を選択します。

   ![Public Repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. **[リポジトリ]** ウィンドウで、次の情報を入力します。

   - **[Name]/(名前)/**:ラボで使用するリポジトリ名を入力します。
   - **[Git clone URL]**/ :GitHub または Azure Repos から Git HTTPS クローン URL を入力します。
   - **[Branch]**/ (省略可能):Azure Resource Manager テンプレートの定義にアクセスするためのブランチ名を入力します。
   - **[Personal access token]**/ :リポジトリに安全にアクセスするために使用される個人用アクセス トークンを入力します。
     - Azure Repos からトークンを取得するには、自分のプロファイルで **[ユーザー設定]**  >  **[セキュリティ]**  >  **[個人用アクセス トークン]** を選択します。
     - GitHub からトークンを取得するには、自分のプロファイルで、 **[Settings]\(設定\)**  >  **[Developer Settings]\(開発者の設定\)**  >  **[Personal access tokens]\(個人用アクセス トークン\)** を選択します。
   - **[Folder paths]**/ :アーティファクトの定義または Azure Resource Manager テンプレートの定義の Git クローン URI に対する相対フォルダー パスを入力します。

1. **[保存]** を選択します。

   ![新しいリポジトリを追加する](./media/devtest-lab-create-environment-from-arm/repo-values.png)

ラボに Azure Resource Manager テンプレートを追加すると、ラボ ユーザーはテンプレートを使用して環境を作成できるようになります。

## <a name="configure-access-rights-for-lab-users"></a>ラボ ユーザーのアクセス権を構成する

ラボ ユーザーは既定で **閲覧者** ロールを持っているので、環境リソース グループ内のリソースを変更できません。 たとえば、リソースを停止または開始できません。

環境内のリソースを編集できるようにラボ ユーザーに **共同作成者** ロールを付与するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) のラボの **[概要]** ウィンドウで、 **[構成とポリシー]** を選択し、 **[ラボの設定]** を選択します。

1. **[ラボの設定]** ウィンドウで、 **[共同作成者]** を選択し、 **[保存]** を選択してラボ ユーザーに書き込みアクセス許可を付与します。

   ![ラボ ユーザーのアクセス権の構成](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

次のセクションでは、Azure Resource Manager テンプレートから環境を作成する手順を説明します。

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Azure portal でテンプレートから環境を作成する

ラボに Azure Resource Manager テンプレートを追加すると、ラボ ユーザーは次の手順に従って Azure portal で環境を作成できるようになります。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧から、目的のラボを選択します。

1. ラボのページで、 **[追加]** を選択します。

1. **[ベースの選択]** ウィンドウに、使用できるベース画像が表示されます。Azure Resource Manager テンプレートがその先頭に表示されます。 目的の Azure Resource Manager テンプレートを選択します。

   ![ベースの選択](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. **[追加]** ウィンドウで、環境ユーザーに表示する **[環境名]** の値を入力します。

   Azure Resource Manager テンプレートで、入力フィールドの残りの部分を定義します。 テンプレート *azuredeploy.parameter.json* ファイルに既定値が定義されている場合は、入力フィールドにそれらの値が表示されます。

   *セキュリティで保護された文字列* 型のパラメーターの場合、Azure キー コンテナーのシークレットを使用することができます。 キー コンテナーにシークレットを格納し、ラボ リソースの作成時に使用する方法については、[Azure Key Vault にシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)方法に関する記事を参照してください。  

   ![[追加] ウィンドウ](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > 次のパラメーター値は、テンプレートで指定されている場合でも、入力フィールドには表示されません。 このフォームには、環境の作成時にラボ ユーザーが値を入力する必要がある空白の入力フィールドが表示されます。
   >
   > - GEN-UNIQUE
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KEY
   > - GEN-PASSWORD

1. **[追加]** を選択して環境を作成します。

   環境で即座にプロビジョニングが開始され、 **[仮想マシン]** の一覧に状態が表示されます。 ラボでは、Azure Resource Manager テンプレートに定義されているすべてのリソースをプロビジョニングする新しいリソース グループが自動的に作成されます。

1. 環境が作成されたら、 **[仮想マシン]** の一覧で環境を選択してリソース グループ ウィンドウを開き、環境がプロビジョニングされているすべてのリソースを参照します。

   ![環境リソース](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   環境を展開して、環境がプロビジョニングされている VM の一覧のみを表示することもできます。

   ![[仮想マシン] の一覧](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 任意の環境を選択して、アーティファクトの適用、データ ディスクのアタッチ、自動シャットダウン時刻の変更など、使用可能なアクションを参照します。

   ![環境のアクション](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>PowerShell を使用して環境の作成を自動化する

Azure portal を使用してラボに 1 つの環境を追加することはできますが、開発やテストのシナリオで複数の環境を作成する必要がある場合は、自動化されたデプロイの方が優れています。

次に進む前に、作成するリソースを定義する Azure Resource Manager テンプレートを用意します。 [Git リポジトリでテンプレートを追加して構成し](#configure-your-own-template-repositories)、[そのリポジトリをラボに追加します](#add-template-repositories-to-the-lab)。

次のサンプル スクリプトで、ラボに環境を作成します。 コメントは、スクリプトの理解を深めるために役立ちます。

1. 次のサンプル PowerShell スクリプトを、*deployenv.ps1* という名前でハード ドライブに保存します。

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. 次のように、SubscriptionId、LabName、ResourceGroupName、RepositoryName、TemplateName (Git リポジトリ内のフォルダー)、および EnvironmentName に特定の値を使用して、スクリプトを実行します。

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

また、Azure CLI を使用して、Resource Manager テンプレートでリソースをデプロイすることもできます。 詳細については、「[Deploy resources with Resource Manager templates and Azure CLI](../azure-resource-manager/templates/deploy-cli.md)」 (Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ) を参照してください。

> [!NOTE]
> ラボ所有者権限を持つユーザーだけが、Azure PowerShell を使用して Resource Manager テンプレートから VM を作成できます。 Resource Manager テンプレートを使用して VM の作成を自動化する場合でユーザー権限しか持っていない場合は、CLI コマンド [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create) を使用します。

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>DevTest Labs での Resource Manager テンプレートの制限事項

DevTest Labs で Resource Manager テンプレートを使用する場合は、次の制限事項を考慮してください。

- Resource Manager テンプレートから作成されたラボ VM から数式やカスタム画像を作成することはできません。

- Resource Manager テンプレートをデプロイするときに、ほとんどのポリシーは評価されません。

たとえば、ユーザーが 5 個の VM しか作成できないラボ ポリシーがあるとします。 ただし、ユーザーは、数十台の VM を作成する Resource Manager テンプレートをデプロイできます。 次のようなポリシーは評価されません。

  - ユーザーあたりの VM 数

  - ラボ ユーザーあたりの Premium VM 数

  - ラボ ユーザーあたりの Premium ディスク数

## <a name="next-steps"></a>次のステップ
- VM を作成したら、その VM の管理ウィンドウで **[接続]** を選択して VM に接続できます。
- ラボの **[仮想マシン]** 一覧で環境を選択して、環境内のリソースを表示および管理します。
- [Azure クイックスタート テンプレート ギャラリーから Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates)を検索します。
