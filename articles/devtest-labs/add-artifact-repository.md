---
title: Azure DevTest Labs でラボにアーティファクト リポジトリを追加する | Microsoft Docs
description: Azure DevTest Labs で、パブリックの成果物リポジトリにはないツールを、自分のラボに自分専用の成果物リポジトリを指定して格納する方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da4e345b18a46226853d71bbf66af0487f1a761f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502197"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>DevTest Labs でラボにアーティファクト リポジトリを追加する
DevTest Labs では、VM の作成時または VM が作成された後に VM に追加されるアーティファクトを指定できます。 このアーティファクトには、VM にインストールするツールやアプリケーションがあります。 アーティファクトは、GitHub または Azure DevOps リポジトリから読み込まれた JSON ファイルで定義されます。

DevTest Labs によって維持されている[パブリック アーティファクト リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)には、Windows と Linux の両方のための一般的なツールが多数用意されています。 このリポジトリへのリンクは、ラボに自動的に追加されます。 パブリック アーティファクト リポジトリでは入手できない特定のツールを含む独自のアーティファクト リポジトリを作成できます。 カスタム アーティファクトの作成の詳細については、[カスタム アーティファクトの作成](devtest-lab-artifact-author.md)に関するページを参照してください。

この記事では、Azure Portal、Azure Resource Management テンプレート、および Azure PowerShell を使用してカスタム アーティファクト リポジトリを追加する方法に関する情報を提供します。 PowerShell または CLI スクリプトを記述することによって、ラボへのアーティファクト リポジトリの追加を自動化できます。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件
ラボにリポジトリを追加するには、まず、リポジトリから主要な情報を取得します。 以降のセクションでは、**GitHub** または **Azure DevOps** 上でホストされるリポジトリに関する必要な情報を取得する方法について説明します。

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>GitHub リポジトリのクローン URL と個人用アクセス トークンの取得

1. アーティファクトまたは Resource Manager テンプレートの定義を含む GitHub リポジトリのホーム ページに移動します。
2. **[クローンまたはダウンロード]** を選択します。
3. **[HTTPS clone URL]** ボタンをクリックして、URL をクリップボードにコピーします。 後で使用するために URL を保存します。
4. GitHub の右上隅にあるプロファイル画像を選択し、 **[Settings]** を選択します。
5. 左側にある **[Personal settings]** メニューで、 **[Developer Settings]** を選択します。
6. 左側のメニューで **[Personal access tokens]** を選択します。
7. **[新しいトークンの生成]** を選択します。
8. **[New personal access token]** ページで、 **[Token description]** に説明を入力します。 **[Select scopes]** の既定の項目をそのまま使用し、 **[Generate Token]** を選択します。
9. 生成されたトークンを保存します。 後でこのトークンを使用します。
10. GitHub を閉じます。   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Azure Repos のクローン URL と個人用アクセス トークンの取得
1. チーム コレクションのホーム ページ (たとえば、`https://contoso-web-team.visualstudio.com`) に移動し、プロジェクトを選択します。
2. プロジェクトのホーム ページで、 **[コード]** を選択します。
3. クローン URL を表示するには、プロジェクト **[Code (コード)]** ページで、 **[Clone (クローン)]** を選択します。
4. URL を保存します。 後でこの URL を使用します。
5. 個人用アクセス トークンを作成するには、ユーザー アカウントのドロップダウン メニューで **[マイ プロファイル]** を選択します。
6. プロファイル情報ページで、 **[セキュリティ]** を選択します。
7. **[セキュリティ] > [個人用アクセス トークン]** タブで、 **[+ 新しいトークン]** を選択します。
8. **[Create a new personal access token]\(新しい個人用アクセス トークンの作成\)** ページで、次の操作を行います。
   1. トークンの **[名前]** を入力します。
   2. **[組織]** の一覧で、 **[アクセス可能なすべての組織]** を選択します。
   3. **[有効期限 (UTC)]** の一覧で、 **[90 日間]** 、または顧客が定義した有効期限日を選択します。
   4. [スコープ] には、 **[フル アクセス]** オプションを選択します。
   5. **［作成］** を選択します
9. **[個人用アクセス トークン]** の一覧に新しいトークンが表示されます。 **[トークンのコピー]** を選択し、後ほど必要になるため、トークンの値を保存します。
10. 「ラボのレポジトリへの接続」のセクションに進みます。

## <a name="use-azure-portal"></a>Azure Portal の使用
このセクションでは、Azure Portal でラボにアーティファクト リポジトリを追加する手順を示します。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. **[その他のサービス]** を選択し、サービスの一覧で **[DevTest Labs]** を選択します。
3. ラボの一覧でラボを選択します。
4. 左側のメニューで **[構成とポリシー]** を選択します。
5. 左側のメニューの **[外部リソース]** セクションで **[レポジトリ]** を選択します。
6. ツールバーの **[+ 追加]** を選択します。

    ![リポジトリの追加ボタン](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. **[レポジトリ]** ページで、次の情報を指定します。
   1. **名前**。 リポジトリの名前を入力します。
   2. **[Git クローン URL]** : GitHub または Azure DevOps Services から先ほどコピーした Git HTTPS クローン URL を入力します。
   3. **[ブランチ]** : 定義を取得するには、ブランチを入力します。
   4. **[個人用アクセス トークン]** : GitHub または Azure DevOps Services から先ほど取得した個人用アクセス トークンを入力します。
   5. **[フォルダー パス]** : アーティファクトまたは Resource Manager テンプレートの定義を含む、複製 URL を基準としたフォルダー パスを少なくとも 1 つ入力します。 サブディレクトリを指定するときは、フォルダー パスにスラッシュを含めてください。

        ![[リポジトリ] 領域](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. **[保存]** を選択します。

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用
Azure Resource Management (Azure Resource Manager) テンプレートは、作成する Azure 内のリソースが記載された JSON ファイルです。 これらのテンプレートの詳細については、[Azure Resource Manager テンプレートの作成](../azure-resource-manager/templates/template-syntax.md)に関するページを参照してください。

このセクションでは、Azure Resource Manager テンプレートを使用してラボにアーティファクト リポジトリを追加する手順を示します。  このテンプレートでは、ラボがまだ存在しない場合は作成されます。

### <a name="template"></a>Template
この記事で使用されるサンプル テンプレートは、パラメーター経由で次の情報を収集します。 ほとんどのパラメーターには適切な既定値がありますが、指定する必要がある値もいくつかあります。 ラボ名、アーティファクト リポジトリの URI、およびリポジトリのセキュリティ トークンを指定する必要があります。

- ラボ名。
- DevTest Labs ユーザー インターフェイス (UI) でのアーティファクト リポジトリの表示名。 既定値は `Team Repository` です。
- リポジトリへの URI (例: `https://github.com/<myteam>/<nameofrepo>.git` または `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`)。
- アーティファクトを含むリポジトリ内のブランチ。 既定値は `master` です。
- アーティファクトを含むフォルダーの名前。 既定値は `/Artifacts` です。
- リポジトリの種類。 使用できる値は `VsoGit` または `GitHub` です。
- リポジトリのアクセス トークン。

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>テンプレートのデプロイ
Azure にテンプレートをデプロイし、リソースが存在しない場合は作成され、存在する場合は更新されるようにする方法にはいくつかあります。 詳しくは、次の記事を参照してください。

- [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-powershell.md)
- [Resource Manager テンプレートと Azure CLI を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-cli.md)
- [Resource Manager テンプレートと Azure Portal を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-portal.md)
- [Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-rest.md)

先に進み、PowerShell でテンプレートをデプロイする方法を見てみましょう。 テンプレートをデプロイするために使用されるコマンドレットはコンテキスト固有であるため、現在のテナントと現在のサブスクリプションが使用されます。 必要に応じて、テンプレートをデプロイする前に [Set-AzContext](/powershell/module/az.accounts/set-azcontext) を使用してコンテキストを変更します。

まず、[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) を使用してリソース グループを作成します。 使用するリソース グループが既に存在する場合は、この手順を省略してください。

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

次に、[New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) を使用して、リソース グループへのデプロイを作成します。 このコマンドレットは、Azure にリソースの変更を適用します。 特定のリソース グループに対して複数のリソース デプロイを作成できます。 同じリソース グループに複数回デプロイする場合は、各デプロイの名前が一意であることを確認してください。

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

New-AzResourceGroupDeployment が正常に実行されると、このコマンドは、プロビジョニング状態 (成功) やテンプレートに関するすべての出力などの重要な情報を出力します。

## <a name="use-azure-powershell"></a>Azure PowerShell の使用
このセクションでは、ラボにアーティファクト リポジトリを追加するために使用できるサンプルの PowerShell スクリプトを示します。 Azure PowerShell がない場合は、そのインストールのための詳細な手順について [Azure PowerShell をインストールして構成する方法](/powershell/azure/)に関するページを参照してください。

### <a name="full-script"></a>完全なスクリプト
いくつかの詳細メッセージやコメントを含む完全なスクリプトを次に示します。

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the repository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>PowerShell スクリプトの実行
次の例は、スクリプトを実行する方法を示しています。

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>パラメーター
この記事にあるサンプルの PowerShell スクリプトは、次のパラメーターを取ります。

| パラメーター | 説明 |
| --------- | ----------- |
| LabName | ラボの名前。 |
| ArtifactRepositoryName | 新しいアーティファクト リポジトリの名前。 リポジトリの名前が指定されていない場合、このスクリプトではランダムな名前が作成されます。 |
| ArtifactRepositoryDisplayName | アーティファクト リポジトリの表示名。 これは、ラボのすべてのアーティファクト リポジトリを表示したときに Azure Portal (https://portal.azure.com) ) に表示される名前です。 |
| RepositoryUri | リポジトリへの URI。 例: `https://github.com/<myteam>/<nameofrepo>.git` または `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`。|
| RepositoryBranch | アーティファクト ファイルが見つかるブランチ。 既定値は 'master' です。 |
| FolderPath | アーティファクトが見つかるフォルダー。 既定値は '/Artifacts' です。 |
| PersonalAccessToken | GitHub または VSOGit リポジトリにアクセスするためのセキュリティ トークン。 個人用アクセス トークンを取得するための手順については、前提条件のセクションを参照してください。 |
| SourceType | アーティファクトが VSOGit または GitHub リポジトリのどちらであるかを示します。 |

リポジトリ自体には、識別のための内部名が必要です。これは、Azure Portal に表示される表示名とは異なります。 内部名は、Azure Portal の使用時には表示されませんが、Azure REST API または Azure PowerShell の使用時に表示されます。 スクリプトのユーザーが名前を指定していない場合は、このスクリプトによって名前が提供されます。

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>このスクリプトで使用される PowerShell コマンド

| PowerShell コマンド | Notes |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | このコマンドは、ラボに関する詳細 (場所など) を取得するために使用されます。 |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | アーティファクト リポジトリを追加するための特定のコマンドは存在しません。 一般的な [New-AzResource](/powershell/module/az.resources/new-azresource) コマンドレットがこのジョブを実行します。 このコマンドレットには、作成するリソースの種類を認識するために **ResourceId** または **ResourceName** と **ResourceType** のペアのどちらかが必要です。 このサンプル スクリプトでは、リソース名とリソースの種類のペアを使用します。 <br/><br/>ラボと同じ場所および同じリソース グループにアーティファクト リポジトリ ソースを作成していることに注意してください。|

このスクリプトは、現在のサブスクリプションに新しいリソースを追加します。 この情報を確認するには、[Get-AzContext](/powershell/module/az.accounts/get-azcontext) を使用します。 現在のテナントとサブスクリプションを設定するには、[Set-AzContext](/powershell/module/az.accounts/set-azcontext) を使用します。

リソース名とリソースの種類の情報を検出するための最適な方法は、[体験版 Azure REST API](https://azure.github.io/projects/apis/) の Web サイトを使用することです。 [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) プロバイダーを確認して、DevTest Labs プロバイダーの使用可能な REST API を調べてください。 このスクリプトは、次のリソース ID を使用します。

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

リソースの種類は、中かっこ内に示されている項目を除き、URI の 'providers' の後に示されているすべてです。 リソース名は、中かっこ内に示されているすべてです。 リソース名に複数の項目が予測される場合は、次のように、各項目をスラッシュで区切ります。

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>次のステップ
- [Azure DevTest Labs のラボに対する必須成果物を指定する](devtest-lab-mandatory-artifacts.md)
- [DevTest Labs 仮想マシンのカスタム アーティファクトの作成](devtest-lab-artifact-author.md)
- [ラボでアーティファクトの失敗を診断する](devtest-lab-troubleshoot-artifact-failure.md)
