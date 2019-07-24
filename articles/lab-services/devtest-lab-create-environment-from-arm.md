---
title: Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する | Microsoft Docs
description: Azure Resource Manager テンプレートから Azure DevTest Labs で複数 VM の環境と PaaS リソースを作成する方法について説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: spelluru
ms.openlocfilehash: 96e3a24b0c9f9ab21652ffcd1b29deeb512581e5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59796935"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用してマルチ VM 環境と PaaS リソースを作成する

[Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) を使用すると、簡単に [1 度に 1 台の VM をラボに追加する](https://docs.microsoft.com/azure/devtest-lab/devtest-lab-add-vm)ことができます。 ところが、環境に複数の VM が含まれる場合は、各 VM を個別に作成する必要があります。 多層 Web アプリや SharePoint ファームのようなシナリオでは、シングル ステップで複数の VM を作成できるメカニズムが必要です。 Azure Resource Manager テンプレートを使用することで、Azure のソリューションのインフラストラクチャと構成を定義して、複数の VM を一貫した状態で繰り返しデプロイできるようになりました。 この機能には次のような利点があります。

- Azure Resource Manager テンプレートが、ソース管理リポジトリ (GitHub や Azure DevOps Services Git) から直接読み込まれます。
- 構成したら、ユーザーは、他の種類の [VM ベース](./devtest-lab-comparing-vm-base-image-types.md)を使用する場合と同様に、Azure Portal から Azure Resource Manager テンプレートを選択して環境を作成できます。
- Azure PaaS のリソースを、IaaS VM だけでなく Azure Resource Manager テンプレートから環境にプロビジョニングできます。
- 環境のコストを、他の種類のベースによって作成された個々の VM だけでなくラボで追跡できます。
- PaaS のリソースが作成され、コスト管理に表示されます。ただし、VM の自動シャットダウンは PaaS リソースに適用されません。

Resource Manager テンプレートを使用してラボのすべてのリソースを単一の操作でデプロイ、更新、または削除することには多くの利点があります。詳細については、[こちらで](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#the-benefits-of-using-resource-manager)ご確認ください。

> [!NOTE]
> より多くのラボの VM を作成するためのベースとして Resource Manager テンプレートを使用する場合、マルチ VM を作成するか、単一 VM を作成するかでいくつかの違いがあることに注意してください。 その違いについては、[仮想マシンの Azure Resource Manager テンプレートの使用](devtest-lab-use-resource-manager-template.md)に関するページで詳しく説明しています。
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="devtest-labs-public-environments"></a>DevTest Labs のパブリック環境
Azure DevTest Labs には、[Azure Resource Manager テンプレートのパブリック リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments)が装備されています。このテンプレートを使用すると、自分で外部 GitHub ソースに接続しなくても、環境を作成できます。 このリポジトリには、Azure Web Apps、Service Fabric クラスター、開発の SharePoint ファーム環境など、頻繁に使用されるテンプレートが含まれています。 この機能は、作成するすべてのラボに含まれている成果物のパブリック リポジトリに似ています。 環境リポジトリを使用すると、事前に作成されている環境テンプレートに最低限の入力パラメーターを指定して、すばやく作業を開始できます。こうして、ラボ内の PaaS リソースでスムーズに作業を開始できます。 詳細については、「[Azure DevTest Labs でのパブリックな環境の構成と使用](devtest-lab-configure-use-public-environments.md)」を参照してください。

## <a name="configure-your-own-template-repositories"></a>独自のテンプレート リポジトリを構成する
コードとしてのインフラストラクチャとコードとしての構成のベスト プラクティスの 1 つとして、環境テンプレートをソース管理で管理する必要があります。 Azure DevTest Labs は、このベスト プラクティスに従って、GitHub または Azure DevOps Services Git リポジトリから直接すべての Azure Resource Manager テンプレートを読み込みます。 その結果、Resource Manager のテンプレートは、テスト環境から運用環境まで、リリース サイクル全体で使用することができます。

DevTest Labs チームが[パブリック GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments)内に作成したテンプレートをチェックアウトします。 このパブリック リポジトリでは、他のユーザーによって共有されているテンプレートを表示し、必要に応じてそのまま使用したり、カスタマイズしたりすることができます。 テンプレートを作成した後、それを他のユーザーと共有するには、このリポジトリに格納します。 クラウド内に環境をセットアップするために使用できるテンプレートを使用して、独自の Git リポジトリを設定することもできます。 

この場合、リポジトリ内で Azure Resource Manager テンプレートを整理するために従わなければならないルールがいくつかあります。

- マスター テンプレート ファイルの名前を `azuredeploy.json` にする必要があります。 

    ![主要な Azure Resource Manager テンプレート ファイル](./media/devtest-lab-create-environment-from-arm/master-template.png)

- パラメーター ファイルで定義したパラメーター値を使用する場合は、パラメーター ファイルの名前を `azuredeploy.parameters.json` にする必要があります。
- `_artifactsLocation` と `_artifactsLocationSasToken` のパラメーターを使用すると、parametersLink URI の値を構築できるため、DevTest Labs で入れ子にされたテンプレートを自動的に管理できるようになります。 詳細については、「[How Azure DevTest Labs makes nested Resource Manager template deployments easier for testing environments](https://blogs.msdn.microsoft.com/devtestlab/2017/05/23/how-azure-devtest-labs-makes-nested-arm-template-deployments-easier-for-testing-environments/)」(Azure DevTest Labs を使用してテスト環境での入れ子にされた Resource Manager テンプレートのデプロイを簡単にする方法) を参照してください。
- メタデータを定義して、テンプレートの表示名と説明を指定することができます。 このメタデータは、`metadata.json` はという名前のファイルに格納する必要があります。 次のメタデータ ファイルの例で、表示名と説明を指定する方法を示します。 

    ```json
    { 
        "itemDisplayName": "<your template name>", 
        "description": "<description of the template>" 
    }
    ```

次の手順では、Azure Portal を使用してラボにリポジトリを追加する方法を説明します。 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。   
1. ラボの **[概要]** ウィンドウで、**[構成とポリシー]** を選択します。

    ![構成とポリシー](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. **[Configuration and Policies (構成とポリシー)]** の設定一覧で、**[リポジトリ]** を選択します。 **[リポジトリ]** ウィンドウには、ラボに追加されたリポジトリが一覧表示されます。 `Public Repo` という名前のリポジトリがすべてのラボに自動的に生成され、複数の VM アーティファクトを格納する [DevTest ラボ GitHub リポジトリ](https://github.com/Azure/azure-devtestlab)に接続され、使用されます。

    ![Public Repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. **[追加+]** を選択し、Azure Resource Manager テンプレートのリポジトリを追加します。
1. 2 つ目の **[リポジトリ]** ウィンドウが開いたら、必要な情報を次のように入力します。
    - **名前** - ラボで使用するリポジトリ名を入力します。
    - **Git クローン URL** - GitHub または Azure DevOps Services の GIT HTTPS クローン URL を入力します。  
    - **分岐** - Azure Resource Manager テンプレートの定義にアクセスするための分岐名を入力します。 
    - **個人用アクセス トークン** - 個人用アクセス トークンは、リポジトリに安全にアクセスするために使用されます。 Azure DevOps Services からトークンを取得するには、**&lt;[自分の名前] > [マイ プロファイル] > [セキュリティ] > [パブリック アクセス トークン]** の順に選択します。 GitHub からトークンを入手するには、アバターを選択した後に、**[設定]、[Public access token (パブリック アクセス トークン)]** の順に選択します。 
    - **フォルダー パス** - 2 つの入力フィールドのいずれかを使用して、アーティファクトの定義 (最初の入力フィールド) または Azure Resource Manager テンプレートの定義に、フォワード スラッシュ (/) で始まり、Git クローン URI に対して相対的なフォルダー パスを入力します。   
    
        ![Public Repo](./media/devtest-lab-create-environment-from-arm/repo-values.png)


1. すべての必須フィールドを入力し、検証に合格したら、**[保存]** を選択します。

次のセクションでは、Azure Resource Manager テンプレートから環境を作成する手順を説明します。

## <a name="create-an-environment-from-a-resource-manager-template-using-the-azure-portal"></a>Azure Portal を使用して Resource Manager テンプレートから環境を作成する

ラボで Azure Resource Manager テンプレート リポジトリが構成されると、ラボのユーザーは Azure Portal で次の手順を使用して環境を作成できます。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
1. ラボの一覧で目的のラボを選択します。   
1. ラボのウィンドウで、**[追加+]** を選択します。
1. **[ベースの選択]** ウィンドウに、使用できるベース イメージが表示されます。Azure Resource Manager テンプレートがその先頭に表示されます。 目的の Azure Resource Manager テンプレートを選択します。

    ![ベースの選択](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)
  
1. **[追加]** ウィンドウで、**[環境名]** に値を入力します。 環境名は、ラボでユーザーに表示される内容です。 その他の入力フィールドは、Azure Resource Manager テンプレートで定義します。 既定値がテンプレートで定義されている場合、または `azuredeploy.parameter.json` ファイルが存在する場合は、これらの入力フィールドに既定値が表示されます。 *セキュリティで保護された文字列*型のパラメーターの場合、Azure Key Vault に格納されているシークレットを使用することができます。 キー コンテナーにシークレットを保存し、ラボ リソースの作成時に使用する方法については、[Azure Key Vault にシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)方法に関する記事を参照してください。  

    ![[追加] ウィンドウ](./media/devtest-lab-create-environment-from-arm/add.png)

    > [!NOTE]
    > 値を指定した場合でも、空の値として表示されるパラメーター値がいくつかあります。 そのため、ユーザーが Azure Resource Manager テンプレートのパラメーターにそれらの値を割り当てた場合、DevTest Labs に値は表示されません。 代わりに、空白の入力フィールドが表示され、ラボ ユーザーは環境を作成するときに、ここに値に入力する必要があります。
    > 
    > - GEN-UNIQUE
    > - GEN-UNIQUE-[N]
    > - GEN-SSH-PUB-KEY
    > - GEN-PASSWORD 
 
1. **[追加]** を選択して環境を作成します。 環境でプロビジョニングが開始され、**[仮想マシン]** の一覧に状態が即座に表示されます。 Azure Resource Manager テンプレートで定義されているすべてのリソースをプロビジョニングするために、新しいリソース グループがラボによって自動的に作成されます。
1. 環境が作成されたら、**[仮想マシン]** の一覧で環境を選択してリソース グループ ウィンドウを開き、環境内にプロビジョニングされたすべてのリソースを参照します。
    
    ![[仮想マシン] の一覧](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)
   
   環境にプロビジョニングされている VM の一覧を単に表示するために環境を展開することもできます。
    
    ![[仮想マシン] の一覧](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. 任意の環境をクリックして、アーティファクトの適用、データ ディスクのアタッチ、自動シャット ダウン時刻の変更など、使用可能なアクションを参照します。

    ![環境のアクション](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

## <a name="automate-deployment-of-environments"></a>環境のデプロイを自動化する
Azure DevTest Labs には、[Azure Resource Management Manager テンプレート](../azure-resource-manager/resource-group-authoring-templates.md)を使用して、ラボ内のリソース セットで環境を作成する機能が用意されています。 これらの環境には、Resource Manager テンプレートを使用して作成できるすべての Azure リソースを含めることができます。 DevTest ラボ環境を使用すると、ユーザーはラボの範囲内において一貫した方法で複雑なインフラストラクチャをすぐにデプロイすることができます。 現時点では、1 回の作成であれば、Azure portal を使用してラボに環境を追加することは可能ですが、複製の作成が行われるデプロイやテストの状態では、自動デプロイによりエクスペリエンスの改善が可能になります。

先に進む前に、「[独自のテンプレート リポジトリを構成する](#configure-your-own-template-repositories)」セクションで以下の手順を実行してください。 

1. 作成するリソースを定義する Resource Manager テンプレートを作成します。 
2. Resource Manager テンプレートを Git リポジトリに設定します。 
3. Git リポジトリをラボに接続します。 

### <a name="powershell-script-to-deploy-the-resource-manager-template"></a>Resource Manager テンプレートをデプロイするための PowerShell スクリプト
次のセクションの PowerShell スクリプトをハード ディスク (例: deployenv.ps1) に保存し、SubscriptionId、ResourceGroupName、LabName、RepositoryName、Git リポジトリ内の TemplateName (フォルダー)、EnvironmentName の値を指定した後にスクリプトを実行します。

```powershell
./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"  
```

#### <a name="sample-script"></a>サンプル スクリプト
以下は、ラボ内に環境を作成するためのサンプル スクリプトです。 スクリプト内のコメントを利用すると、スクリプトをより良く理解することができます。 

```powershell
#Requires -Module Az.Resources

[CmdletBinding()]

param (
# ID of the Azure Subscription where the lab is created.
[string] [Parameter(Mandatory=$true)] $SubscriptionId,

# Name of the lab (existing) in which to create the environment.
[string] [Parameter(Mandatory=$true)] $LabName,

# Name of the connected repository in the lab. 
[string] [Parameter(Mandatory=$true)] $RepositoryName,

# Name of the template (folder name in the Git repository) based on which the environment will be created.
[string] [Parameter(Mandatory=$true)] $TemplateName,

# Name of the environment to be created in the lab.
[string] [Parameter(Mandatory=$true)] $EnvironmentName,

# The parameters to be passed to the template. Each parameter is prefixed with “-param_”. 
# For example, if the template has a parameter named “TestVMName” with a value of “MyVMName”, the string in $Params will have the form: `-param_TestVMName MyVMName`. 
# This convention allows the script to dynamically handle different templates.
[Parameter(ValueFromRemainingArguments=$true)]
    $Params
)

# Save this script as the deployenv.ps1 file
# Run the script after you specify values for SubscriptionId, ResourceGroupName, LabName, RepositoryName, TemplateName (folder) in the Git repo, EnvironmentName
# ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG994248" -RepositoryName "SP Repository" -TemplateName "My Environment template name" -EnvironmentName "SPResourceGroupEnv"    

# Comment this statement to completely automate the environment creation.    
# Sign in to Azure. 
Connect-AzAccount

# Select the subscription that has the lab.  
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

# Get information about the user, specifically the user ID, which is used later in the script.  
$UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)
        
# Get information about the lab such as lab location. 
$lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName 
if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." } 
    
# Get information about the repository in the lab. 
$repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
    -ResourceName $LabName `
    -ApiVersion 2016-05-15 `
    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
    | Select-Object -First 1
if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." } 

# Get information about the Resource Manager template based on which the environment will be created. 
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

# The custom parameters need to be extracted from $Params and formatted as name/value pairs.
$Params | ForEach-Object {
    if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
        $name = $Matches[1]                
    } elseif ( $name ) {
        $templateParameters += @{ "name" = "$name"; "value" = "$_" }
        $name = $null #reset name variable
    }
}

# Once name/value pairs are isolated, create an object to hold the necessary template properties
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

また、Azure CLI を使用して、Resource Manager テンプレートでリソースをデプロイすることもできます。 詳細については、「[Deploy resources with Resource Manager templates and Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli)」 (Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ) を参照してください。

> [!NOTE]
> ラボ所有者権限を持つユーザーだけが、Azure PowerShell を使用して Resource Manager テンプレートから VM を作成できます。 Resource Manager テンプレートを使用して VM の作成を自動化する場合でユーザー権限しか持っていない場合は、[CLI で **az lab vm create** コマンド](https://docs.microsoft.com/cli/azure/lab/vm#az-lab-vm-create) を使用します。

### <a name="general-limitations"></a>一般的な制限事項 

DevTest Labs で Resource Manager テンプレートを使用する場合は、次の制限事項を考慮してください。

- 作成した Resource Manager テンプレートから既存のリソースは参照できません。新しいリソースのみを参照できます。 さらに、通常 DevTest Labs の外部にデプロイする既存の Resource Manager テンプレートがあり、これに既存のリソースへの参照が含まれている場合は、ラボで使用できません。

   唯一の例外は、既存の仮想ネットワークを参照**できる**ことです。 

- Resource Manager テンプレートから作成されたラボ VM からは、数式を作成できません。 

- Resource Manager テンプレートから作成されたラボ VM からは、カスタム イメージを作成できません。 

- Resource Manager テンプレートをデプロイするときに、ほとんどのポリシーは評価されません。

   たとえば、ユーザーは 5 つの VM しか作成できないことを指定するラボ ポリシーがあるとします。 ただし、ユーザーは、数十台の VM を作成する Resource Manager テンプレートをデプロイできます。 次のようなポリシーは評価されません。

   - ユーザーあたりの VM 数
   - ラボ ユーザーあたりの Premium VM 数
   - ラボ ユーザーあたりの Premium ディスク数


### <a name="configure-environment-resource-group-access-rights-for-lab-users"></a>ラボ ユーザーの環境リソース グループへのアクセス権を構成する

ラボ ユーザーは、Resource Manager テンプレートをデプロイできます。 しかし既定では、ラボ ユーザーは閲覧者のアクセス権を持ちます。つまり、環境リソース グループのリソースを変更できません。 たとえば、リソースを停止または開始できません。

ラボ ユーザーに共同作成者のアクセス権を付与するには、次の手順に従います。 すると、ラボ ユーザーが Resource Manager テンプレートをデプロイするときに、その環境のリソースを編集できるようになります。 


1. ラボの **[概要]** ウィンドウで、**[構成とポリシー]** を選択します。
1. **[ラボの設定]** を選択します。
1. [ラボの設定] ウィンドウで、**[共同作成者]** を選択してラボ ユーザーに書き込み権限を付与します。

    ![ラボ ユーザーのアクセス権の構成](./media/devtest-lab-create-environment-from-arm/configure-access-rights.png)

1. **[保存]** を選択します。

## <a name="next-steps"></a>次の手順
* VM が作成されたら、その VM の管理ウィンドウで **[接続]** を選択して VM に接続できます。
* ラボの **[仮想マシン]** 一覧で環境を選択して、環境内のリソースを表示および管理します。 
* [Azure クイックスタート テンプレート ギャラリーから Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates)を検索します。
