---
title: Azure DevTest Labs を Azure Pipelines に統合する
description: Azure Pipelines の継続的インテグレーションと配信パイプラインに Azure DevTest Labs を統合する方法を説明します
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 96f99d41d0a7ea07bf3854292f9c3bd6245414b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87288924"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Azure DevTest Labs を Azure Pipelines CI/CD パイプラインに統合する

"*Azure DevTest Labs タスク*" 拡張機能を使用して、Azure Pipelines 継続的インテグレーションと継続的デリバリー (CI/CD) のビルドとリリース パイプラインを Azure DevTest Labs と統合できます。 拡張機能は、次のような複数のタスクをインストールします。 

- 仮想マシン (VM) の作成
- VM からカスタム イメージを作成する
- VM を削除する 

これらのタスクにより、たとえば特定のテスト タスク用の "*ゴールデン イメージ*" VM をすばやくデプロイし、テストの終了時にその VM を削除することが簡単になります。

この記事では、Azure DevTest Labs タスクを使用して、VM の作成とデプロイ、カスタム イメージの作成、および VM の削除のすべてを 1 つのリリース パイプラインとして実行する方法を示します。 通常は、これらのタスクを独自のカスタムのビルド、テスト、デプロイのパイプラインで個別に実行します。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>前提条件

- [Azure DevOps](https://dev.azure.com) 組織を登録するか既存の組織にログインし、その組織に[プロジェクトを作成](/vsts/organizations/projects/create-project)します。 
  
- Visual Studio Marketplace から Azure DevTest Labs タスクの拡張機能をインストールします。
  
  1. [Azure DevTest Labs タスク](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)に移動します。
  1. **[Get it free]\(無料で入手\)** を選択します。
  1. ドロップダウンから Azure DevOps 組織を選択し、**[インストール]** を選択します。 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Azure VM を構築するためのテンプレートを作成する 

このセクションでは、Azure VM をオンデマンドで作成するために使用する Azure Resource Manager テンプレートの作成方法について説明します。

1. Resource Manager テンプレートをサブスクリプション内に作成するには、[Resource Manager テンプレートの使用](devtest-lab-use-resource-manager-template.md)に関する記事に記載されている手順に従います。
   
1. Resource Manager テンプレートを生成する前に、VM 作成の一部として [WinRM 成果物](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)を追加します。 "*Azure File Copy (Azure ファイル コピー)*" や "*PowerShell on Target Machines (ターゲット コンピューターでの PowerShell)*" などのデプロイ タスクには、WinRM へのアクセスが必要です。 WinRM 成果物には、パラメーターとしてホスト名が必要です。これは、VM の完全修飾ドメイン名 (FQDN) である必要があります。 
   
   > [!NOTE]
   > WinRM を共有 IP アドレスで使用する場合は、外部ポートを WinRM ポートにマップする NAT 規則を追加する必要があります。 パブリック IP アドレスを使用して VM を作成する場合は、NAT 規則は不要です。
   
   
1. テンプレートを *CreateVMTemplate. json* という名前のファイルとして自分のコンピューターに保存します。
   
1. ソース管理システムにテンプレートをチェックインします。

## <a name="create-a-script-to-get-vm-properties"></a>VM のプロパティを取得するスクリプトを作成する

リリース パイプラインの "*Azure File Copy (Azure ファイル コピー)*" や "*PowerShell on Target Machines (ターゲット コンピューターでの PowerShell)*" などのタスク ステップを実行するときに、次のスクリプトによって、アプリを VM にデプロイするために必要な値が収集されます。 通常は、これらのタスクを使用して、アプリを Azure VM にデプロイします。 これらのタスクには、VM リソース グループ名、IP アドレス、FQDN などの値が必要です。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

スクリプト ファイルを作成するには、次の手順を実行します。

1. テキスト エディターを開き、次のコードを貼り付けます。
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. *GetLabVMParams.ps1* などの名前でファイルを保存し、ソース管理システムにチェックインします。 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Azure Pipelines にリリース パイプラインを作成する

新しいリリース パイプラインを作成するには、次の手順を実行します。

1. Azure DevOps プロジェクト ページの左側のナビゲーションで **[パイプライン]**  >  **[リリース]** を選択します。
1. **[新しいパイプライン]** を選択します。
1. **[テンプレートの選択]** で下にスクロールして **[Empty Job]\(空のジョブ\)** を選択し、**[適用]** を選択します。

### <a name="add-and-set-variables"></a>変数の追加と設定

パイプライン タスクでは、Azure portal で Resource Manager テンプレートを作成したときに VM に割り当てた値が使用されます。 

値の変数を追加するには、次の手順を実行します。 

1. パイプライン ページで、**[変数]** タブを選択します。
   
1. 変数ごとに **[追加]** を選択し、名前と値を入力します。
   
   |名前|値|
   |---|---|
   |*vmName*|Resource Manager テンプレートで割り当てた VM 名|
   |*userName*|VM にアクセスするユーザー名|
   |*password*|ユーザー (userName) のパスワード。 ロック アイコンを選択し、パスワードを非表示にして保護します。

### <a name="create-a-devtest-labs-vm"></a>DevTest Labs VM の作成

次の手順で、将来のデプロイに使用するゴールデン イメージ VM を作成します。 *[Azure DevTest Labs Create VM]\(Azure DevTest Labs: VM の作成\)* タスクを使用して、Azure DevTest Labs インスタンス内に VM を作成します。

1. リリース パイプラインの **[パイプライン]** タブで、**[ステージ 1]** のハイパーリンク テキストを選択して **ステージ タスクを表示** し、**[Agent job]\(エージェント ジョブ\)** の横にあるプラス記号 **+** を選択します。 
   
1. **[Add tasks]\(タスクの追加\)** で、**[Azure DevTest Labs Create VM]\(Azure DevTest Labs: VM の作成\)** を選択し、**[追加]** を選択します。 
   
1. 左側のウィンドウで **[Azure DevTest Labs Create VM]\(Azure DevTest Labs: VM の作成\)** を選択します。 

1. 右側のウィンドウのフォームに、次のとおりに入力します。
   
   |フィールド|値|
   |---|---|
   |**Azure RM サブスクリプション**|必要に応じて、ドロップダウンリストの **[利用可能な Azure サービス接続]** または **[利用可能な Azure サブスクリプション]** からサービス接続またはサブスクリプションを選択し、**[承認]** を選択します。<br /><br />[注:](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager) Azure サブスクリプションへのさらに制限されたアクセス許可を持つ接続の作成方法については、**Azure Resource Manager サービス エンドポイント** に関するページを参照してください。|
   |**ラボ名**|ラボ VM が作成される既存のラボの名前を選択します。|
   |**テンプレート名**|ソース コード リポジトリに保存したテンプレート ファイルの完全なパスと名前を入力します。 組み込みのプロパティを使用して、パスを簡略化できます。次に例を示します。<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**テンプレート パラメーター**|前に定義した変数のパラメーターを次のように入力します。<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Output Variables (出力変数)**  > **Lab VM ID (ラボ VM ID)**|作成されたラボ VM ID の変数を入力します。 既定の **labVMId** を使用する場合、後続のタスクでその変数を *$(labVMId)* として参照できます。<br /><br />既定以外の名前を作成することもできますが、後続のタスクで必ず正しい名前を使用してください。 ラボ VM ID は、次の形式で記述できます。<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>DevTest Labs VM の詳細情報の収集

先ほど作成したスクリプトを実行して、DevTest Labs VM の詳細情報を収集します。 

1. リリース パイプラインの **[パイプライン]** タブで、**[ステージ 1]** のハイパーリンク テキストを選択して **ステージ タスクを表示** し、**[Agent job]\(エージェント ジョブ\)** の横にあるプラス記号 **+** を選択します。 
   
1. **[Add tasks]\(タスクの追加\)** で、**[Azure PowerShell]** を選択し、**[追加]** を選択します。 
   
1. 左側のウィンドウの **[Azure PowerShell スクリプト: FilePath]** を選択します。 
   
1. 右側のウィンドウのフォームに、次のとおりに入力します。
   
   |フィールド|値|
   |---|---|
   |**Azure 接続の種類**|**[Azure Resource Manager]** を選択します。|
   |**Azure サブスクリプション**|サービス接続またはサブスクリプションを選択します。| 
   |**スクリプトの種類**|**[Script File Path]\(スクリプト ファイル パス\)** を選択します。|
   |**スクリプト パス**|ソース コード リポジトリに保存した PowerShell スクリプトの完全なパスと名前を入力します。 組み込みのプロパティを使用して、パスを簡略化できます。次に例を示します。<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**スクリプトの引数**|前のタスクで設定された *labVmId* 変数の名前を入力します。次に例を示します。<br /><br />`-labVmId '$(labVMId)'`|

このスクリプトは、必要な値を収集し、それらをリリース パイプライン内の環境変数に格納して、以降のステップで簡単に参照できるようにします。

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>DevTest Labs VM からの VM イメージの作成

次のタスクは、新しくデプロイされた VM のイメージを Azure DevTest Labs インスタンス内に作成することです。 この後、開発タスクやいくつかのテストを実行するときに、要求に応じてこのイメージを使用して VM のコピーを作成できます。 

1. リリース パイプラインの **[パイプライン]** タブで、**[ステージ 1]** のハイパーリンク テキストを選択して **ステージ タスクを表示** し、**[Agent job]\(エージェント ジョブ\)** の横にあるプラス記号 **+** を選択します。 
   
1. **[Add tasks]\(タスクの追加\)** で、**[Azure DevTest Labs Create Custom Image]\(Azure DevTest Labs: カスタム イメージの作成\)** を選択し、**[追加]** を選択します。 
   
1. 次に示すようにタスクを構成します。
   
   |フィールド|値|
   |---|---|
   |**Azure RM サブスクリプション**|サービス接続またはサブスクリプションを選択します。|
   |**ラボ名**|イメージが作成される既存のラボの名前を選択します。|
   |**Custom Image Name (カスタム イメージ名)**|カスタム イメージの名前を入力します。|
   |**説明** (省略可能)|後で適切なイメージを選択しやすくするための説明を入力します。|
   |**Source Lab VM (ソース ラボ VM)**  > **Source Lab VM ID (ソース ラボ VM ID)**|LabVMId 変数の既定の名前を変更した場合は、それをここに入力します。 既定値は **$(labVMId)** です。|
   |**Output Variables (出力変数)**  > **カスタム イメージ ID**|必要に応じて、この変数の既定の名前を編集できます。|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>DevTest Labs VM へのアプリのデプロイ (省略可能)

アプリを新しい DevTest Labs VM にデプロイするタスクを追加できます。 アプリをデプロイするために通常使用するタスクは、"*Azure File Copy (Azure ファイル コピー)*" と "*PowerShell on Target Machines (ターゲット コンピューターでの PowerShell)*" です。

これらのタスクのパラメーターに必要な VM 情報は、リリース パイプライン内の **labVmRgName**、**labVMIpAddress**、**labVMFqdn** という名前の 3 つの構成変数に格納されます。 DevTest Labs VM とカスタム イメージの作成を実験するだけで、アプリを展開しない場合は、この手順をスキップできます。

### <a name="delete-the-vm"></a>VM の削除

最後のタスクは、Azure DevTest Labs インスタンスにデプロイした VM を削除することです。 通常は、開発タスクの実行や、展開した VM で必要なテストを実行した後、VM を削除します。 

1. リリース パイプラインの **[パイプライン]** タブで、**[ステージ 1]** のハイパーリンク テキストを選択して **ステージ タスクを表示** し、**[Agent job]\(エージェント ジョブ\)** の横にあるプラス記号 **+** を選択します。 
   
1. **[Add tasks]\(タスクの追加\)** で、**[Azure DevTest Labs Delete VM]\(Azure DevTest Labs: VM の削除\)** を選択し、**[追加]** を選択します。 
   
1. 次に示すようにタスクを構成します。
   
   - **[Azure RM サブスクリプション]** で、サービス接続またはサブスクリプションを選択します。 
   - **[Lab VM ID]\(ラボ VM ID\)** には、LabVMId 変数の既定の名前を変更した場合、それをここに入力します。 既定値は **$(labVMId)** です。
   
### <a name="save-the-release-pipeline"></a>リリース パイプラインの保存

新しいリリース パイプラインを保存するには、次の手順を実行します。

1. リリース パイプラインのページで、名前 **[新しいリリース パイプライン]** を選択し、パイプラインの新しい名前を入力します。 
   
1. 右上の **[保存]** アイコンを選択します。 

## <a name="create-and-run-a-release"></a>リリースの作成と実行

新しいパイプラインを使用してリリースを作成および実行するには、次の手順を実行します。

1. リリース パイプラインのページの右上にある **[Create release]\(リリースの作成\)** を選択します。 
   
1. **[成果物]** で、最新のビルドを選択し、**[作成]** を選択します。
   
1. 各リリース ステージで、Azure portal の DevTest Labs インスタンスのビューを更新して、VM の作成、イメージの作成、および VM の削除を表示します。

カスタム イメージを使用して、必要な場合にいつでも VM を作成できます。

## <a name="next-steps"></a>次のステップ
- [Resource Manager テンプレートを使用してマルチ VM 環境を作成する](devtest-lab-create-environment-from-arm.md)方法を確認します。
- [パブリックの DevTest Labs GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)から、DevTest Labs 自動化のためのクイックスタート Resource Manager テンプレートをさらに調べます。
- 必要に応じて、[Azure DevOps のトラブルシューティング](/azure/devops/pipelines/troubleshooting)に関するページを参照します。
 
