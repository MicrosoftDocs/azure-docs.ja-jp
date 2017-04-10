---
title: "Azure リソース グループ プロジェクトのデプロイメント スクリプトの概要 | Microsoft Docs"
description: "Azure リソース グループのデプロイメント プロジェクトの PowerShell スクリプトの動作について説明します。"
services: visual-studio-online
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: fecfb74f-363f-4cc8-9743-36e5ddd879c0
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: a0695e51b595874ce3d4ccd2dca863e883e2840b


---
# <a name="overview-of-the-azure-resource-group-project-deployment-script"></a>Azure リソース グループ プロジェクトのデプロイメント スクリプトの概要
Azure リソース グループのデプロイメント プロジェクトは、ファイルとその他のアーティファクトを Azure にステージングおよびデプロイする作業を支援します。 Visual Studio で Azure リソース マネージャーのデプロイメント プロジェクトを作成するときは、 **Deploy-AzureResourceGroup.ps1** と呼ばれる PowerShell スクリプトがプロジェクトに追加されます。 このトピックでは、このスクリプトで実行される操作と、Visual Studio の内部と外部の両方でのスクリプトの実行方法について説明します。

## <a name="what-does-the-script-do"></a>このスクリプトで実行される操作
Deploy-AzureResourceGroup.ps1 スクリプトは、デプロイメント ワークフローで重要な次の 2 つの操作を実行します。

* テンプレートのデプロイメントに必要なファイルまたはアーティファクトのアップロード
* テンプレートのデプロイ

スクリプトの最初の部分では、デプロイメント用のファイルとアーティファクトをアップロードし、スクリプト内の最後のコマンドレットで実際にテンプレートをデプロイします。 たとえば、スクリプトを使用して仮想マシンを構成する必要がある場合、デプロイメント スクリプトは最初に構成スクリプトを Azure ストレージ アカウントへセキュリティで保護された方法でアップロードします。 これにより、プロビジョニング中に仮想マシンを構成するために Azure リソース マネージャーで構成スクリプトを使用できるようになります。

アップロードする必要がある追加のアーティファクトは、すべてのテンプレート デプロイメントで必要なわけではないため、 *uploadArtifacts* と呼ばれるスイッチ パラメーターが評価されます。 アーティファクトをアップロードする必要がある場合は、スクリプトの呼び出し時に *uploadArtifacts* スイッチを設定します。 メインのテンプレート ファイルとパラメーター ファイルはアップロードする必要がないことに注意してください。 構成スクリプト、ネストしたデプロイメント テンプレート、およびアプリケーション ファイルなど、その他のファイルのみをアップロードする必要があります。

## <a name="detailed-script-description"></a>スクリプトの詳細な説明
Azure PowerShell スクリプト Deploy-AzureResourceGroup.ps1 の特定のセクションでの実行内容について、次に説明します。

> [!NOTE]
> ここでは、バージョン 1.0 の Deploy-AzureResourceGroup.ps1 スクリプトについて説明します。
> 
> 

1. Azure リソース マネージャーのデプロイメント プロジェクトに必要なパラメーターを宣言します。 いくつかのパラメーターには、プロジェクトの作成時に設定された既定値があります。 スクリプト内でこれらの既定値を変更したり、スクリプトを実行する前に異なるパラメーター値を追加したりすることができます。
   
   ```
   Param(
   [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
   [string] $ResourceGroupName = 'AzureResourceGroup1',
   [switch] $UploadArtifacts,
   [string] $StorageAccountName,
   [string] $StorageAccountResourceGroupName,
   [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
   [string] $TemplateFile = '..\Templates\azuredeploy.json',
   [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
   [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
   [string] $AzCopyPath = '..\Tools\AzCopy.exe',
   [string] $DSCSourceFolder = '..\DSC'
   )
   ```
   
   | パラメーター | 説明 |
   | --- | --- |
   | $ResourceGroupLocation |**米国西部**や**東アジア**など、リソース グループに対するリージョンまたはデータ センターの場所。 |
   | $ResourceGroupName |Azure リソース グループの名前。 |
   | $UploadArtifacts |アーティファクトをシステムから Azure にアップロードする必要があるかどうかを示すバイナリ値。 |
   | $StorageAccountName |アーティファクトがアップロードされる Azure ストレージ アカウントの名前。 |
   | $StorageAccountResourceGroupName |ストレージ アカウントを含む Azure リソース グループの名前。 |
   | $StorageContainerName |アーティファクトをアップロードするために使用するストレージ コンテナーの名前。 |
   | $TemplateFile |Azure リソース グループ プロジェクト内のデプロイメント ファイル (`<app name>.json`) へのパス。 |
   | $TemplateParametersFile |Azure リソース グループ プロジェクト内のパラメーター ファイル (`<app name>.parameters.json`) へのパス。 |
   | $ArtifactStagingDirectory |PowerShell スクリプトのルート フォルダーを含む、アーティファクトがローカルにアップロードされているシステム上のパス。 このパスには、絶対パス、またはスクリプトの場所に対する相対パスを指定できます。 |
   | $AzCopyPath |PowerShell スクリプトのルート フォルダーを含む、AzCopy.exe ツールがその .zip ファイルをコピーするパス。 このパスには、絶対パス、またはスクリプトの場所に対する相対パスを指定できます。 |
   | $DSCSourceFolder |PowerShell スクリプトのルート フォルダーを含む、DSC (Desired State Configuration) ソース フォルダーへのパス。 このパスには、絶対パス、またはスクリプトの場所に対する相対パスを指定できます。 該当する場合、詳細については、「 [Azure PowerShell DSC (Desired State Configuration) 拡張機能の概要](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)」を参照してください。 |
2. アーティファクトを Azure にアップロードする必要があるかどうかを確認します。 必要ない場合は、手順 11 に進みます。 それ以外の場合は、次の手順を実行します。
3. 相対パスを含むすべての変数を絶対パスに変換します。 たとえば、`..\Tools\AzCopy.exe` のようなパスを `C:\YourFolder\Tools\AzCopy.exe` に変更します。 また、変数 *ArtifactsLocationName* と *ArtifactsLocationSasTokenName* を null に初期化します。 *ArtifactsLocation* と *SaSToken* はテンプレートに対するパラメーターである可能性があります。 パラメーター ファイル内を読み取った後にそれらの値が null の場合、スクリプトは、それらの値を生成します。
   
   Azure Tools では、アーティファクトを管理するために、テンプレート内でパラメーター値 *_artifactsLocation* と *_artifactsLocationSasToken* を使用します。 PowerShell スクリプトが、これらの名前を持つパラメーターを見つけて、パラメーターの値が指定されていない場合、スクリプトはアーティファクトをアップロードして、これらのパラメーターの適切な値を返します。 その後、 `@OptionsParameters`を通じてコマンドレットにそれらの値を渡します。
   
   | 変数 | 説明 |
   | --- | --- |
   | ArtifactsLocationName |Azure アーティファクトが配置されている場所のパス。 |
   | ArtifactsLocationSasTokenName |Service Bus を認証するためにスクリプトによって使用される SAS (Shared Access Signature) トークン名。 詳細については、「 [Service Bus での Shared Access Signature 認証](service-bus-messaging/service-bus-shared-access-signature-authentication.md) 」を参照してください。 |
   
   ```
   if ($UploadArtifacts) {
   # Convert relative paths to absolute paths if needed
   $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
   $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
   $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)
   
   Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
   Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly
   
   $OptionalParameters.Add($ArtifactsLocationName, $null)
   $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
   ```
4. このセクションでは、<app name>.parameters.json ファイル ("パラメーター ファイル" と呼ばれます) に **parameters** という名前の親ノードが (`else` ブロック内に) あるかどうかを確認します。 それ以外の場合、親ノードはありません。 いずれの形式も受け入れられます。
   
   ```
   if ($JsonParameters -eq $null) {
         $JsonParameters = $JsonContent
     }
     else {
         $JsonParameters = $JsonContent.parameters
     }
   ```
5. JSON パラメーターのコレクションを反復処理します。 パラメーター値が *_artifactsLocation* または *_artifactsLocationSasToken* に割り当てられている場合、変数 *$OptionalParameters* にそれらの値を設定します。 これにより、指定されたパラメーター値をスクリプトが誤って上書きすることを防止できます。
   
   ```
   $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
     $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name
   
     if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
         $OptionalParameters[$_.Name] = $ParameterValue.value
     }
   }
   ```
6. デプロイメント用のアーティファクトを保持するために使用されるストレージ アカウント リソースのストレージ アカウント キーとコンテキストを取得します。
   
   ```
   $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
   
   $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
   ```
7. 仮想マシンを構成するために PowerShell DSC を使用している場合、DSC 拡張機能では、アーティファクトが 1 つの zip ファイル内に含まれている必要があります。 そのため、DSC 構成用の .zip アーカイブ ファイルを作成します。 これを行うには、$DSCSourceFolder が存在するかどうかを確認します。 DSC 構成が存在する場合は、それを削除して、dsc.zip という名前の新しい圧縮ファイルを作成します。
   
   ```
   # Create DSC configuration archive
   if (Test-Path $DSCSourceFolder) {
   Add-Type -Assembly System.IO.Compression.FileSystem
     $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
     Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
     [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
   }
   ```
8. Azure アーティファクトのパスがパラメーター ファイルに指定されていない場合は、アーティファクトをアップロードするときに使用する PowerShell スクリプトのパスを設定します。 これを行うには、ストレージ アカウントのエンドポイント パスとストレージ コンテナー名の組み合わせを使用して、パスを作成します。 次に、この新しいパスでパラメーター ファイルを更新します。
   
   ```
   # Generate the value for artifacts location if it is not provided in the parameter file
   $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
   if ($ArtifactsLocation -eq $null) {
     $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
     $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
   }
   ```
9. **AzCopy** ユーティリティ (Azure リソース グループ デプロイメント プロジェクトの **Tools** フォルダーに含まれます) を使用して、ローカル ストレージのドロップ パスから、オンラインの Azure ストレージ アカウントにファイルをコピーします。 この手順に失敗した場合、必要なアーティファクトがないとデプロイメントは成功しないため、スクリプトを終了します。
   
   ```
   # Use AzCopy to copy files from the local storage drop path to the storage account container
   & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
   if ($LASTEXITCODE -ne 0) { return }
   ```
10. アーティファクトの場所に対する SAS トークンがパラメーター ファイルに指定されていない場合は、その値を作成して、オンライン ストレージ コンテナーへの一時的な読み取り専用アクセスを提供します。 次に、その SAS トークンを "optionalParameter" としてコマンドラインに渡します。 コマンドラインで渡されるパラメーターは、パラメーター ファイルに指定された値より優先されることに注意してください。
    
    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
     # Create a SAS token for the storage container - this gives temporary read-only access to the container
     $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
     $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
     $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```
11. リソース グループがまだ存在していない場合は作成し、デプロイメントの成功を妨げる検証エラーがないか、テンプレートおよびパラメーター ファイルを確認します。
    
    ```
    # Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop
    
    Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```
12. 最後に、テンプレートをデプロイします。 このコードでは、タイムスタンプを使用してデプロイメントの一意の名前が作成されます。
    
     ```
     New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
         -ResourceGroupName $ResourceGroupName `
         -TemplateFile $TemplateFile `
         -TemplateParameterFile $TemplateParametersFile `
         @OptionalParameters `
         -Force -Verbose
     ```

## <a name="deploy-the-resource-group"></a>リソース グループのデプロイ
### <a name="to-deploy-the-resource-group-in-visual-studio"></a>Visual Studio でリソース グループをデプロイするには
1. Azure リソース グループ プロジェクトのショートカット メニューで **[デプロイ]** > **New [デプロイ]ment**など、リソース グループに対するリージョンまたはデータ センターの場所。
   
    ![][0]
2. **[リソース グループに配置する]** ダイアログ ボックスで、配置先となる既存のリソース グループをドロップダウン リスト ボックスで選択するか、または **[&lt;新規作成…&gt;]**  を選択して新しいリソース グループを作成します。
   
    ![][1]
3. 求められた場合は、**[リソース グループの作成]** ダイアログ ボックスにリソース グループの名前と場所を入力し、**[作成]** ボタンをクリックします。
   
    ![][2]
4. **[パラメーターの編集]** ボタンをクリックして、**[パラメーターの編集]** ダイアログ ボックスを表示し、不足しているパラメーターの値を入力します。
   
    ![][3]
   
   > [!NOTE]
   > 必須パラメーターの値が必要な場合、デプロイ時に、このダイアログ ボックスが自動的に表示されます。
   > 
   > 
   
    ![][4]
5. パラメーター値の入力が完了したら、**[保存]** ボタンをクリックして、次に **[デプロイ]** ボタンをクリックします。
   
    デプロイメント スクリプト (Deploy-AzureResourceGroup.ps1) が実行され、テンプレートがすべてのアーティファクトと共に Azure にデプロイされます。

### <a name="to-deploy-the-resource-group-by-using-powershell"></a>PowerShell を使用してリソース グループをデプロイするには
Visual Studio の [デプロイ] コマンドと UI を使用せずにスクリプトを実行する場合、スクリプトのショートカット メニューで **[PowerShell ISE で開く]**を選択します。

![][5]

## <a name="command-deployment-examples"></a>コマンド デプロイメントの例
### <a name="deploy-using-default-values"></a>既定値を使用したデプロイ
この例では、既定のパラメーター値を使用してスクリプトを実行する方法を示します。 (場所のパラメーターには既定値がないため、明示的に指定する必要があります。)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### <a name="deploy-overriding-the-default-values"></a>既定値をオーバーライドするデプロイ
この例では、既定値とは異なるテンプレートおよびパラメーター ファイルをデプロイするスクリプトの実行方法を示します。

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### <a name="deploy-using-uploadartifacts-for-staging"></a>ステージング用の UploadArtifacts を使用したデプロイ
この例では、リリース フォルダーからアーティファクトをアップロードして、既定以外のテンプレートをデプロイするスクリプトの実行方法を示します。

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

この例では、Visual Studio Online の Azure PowerShell タスク内のスクリプトを実行する方法を示します。

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## <a name="next-steps"></a>次のステップ
Azure Resource Manager の詳細については、「[Azure リソース マネージャーの概要](azure-resource-manager/resource-group-overview.md)」を参照してください。

Azure リソース グループ プロジェクトを使用した作業の例については、[HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) 2015 Connect の[デモ](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/)の「[Deploy and manage Azure resources (Azure リソースのデプロイと管理)](https://github.com/Microsoft/HealthClinic.biz/wiki/Deploy-and-manage-Azure-resources)」を参照してください。 HealthClinic.biz のデモに関連する他のクイック スタートについては、「 [Azure Developer Tools Quickstarts (Azure 開発者ツールのクイック スタート)](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts)」を参照してください。

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png



<!--HONumber=Feb17_HO3-->


