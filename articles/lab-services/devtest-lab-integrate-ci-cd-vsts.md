---
title: Azure DevTest Labs を Azure Pipelines の継続的インテグレーションと配信パイプラインに統合する | Microsoft Docs
description: Azure Pipelines の継続的インテグレーションと配信パイプラインに Azure DevTest Labs を統合する方法を説明します
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 7806599c1a2f1396ff4b07d6f0538057654029d7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738523"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-devops-continuous-integration-and-delivery-pipeline"></a>Azure DevOps 継続的インテグレーションと配信パイプラインへの Azure DevTest Labs の統合
Azure DevOps にインストールされる *Azure DevTest Labs タスク*拡張機能を使用して、CI/CD のビルド・リリース パイプラインを Azure DevTest Labs に簡単に統合できます。 この拡張機能は、次の 3 つのタスクをインストールします。 
* VM の作成
* VM からカスタム イメージを作成する
* VM の削除 

このプロセスは、たとえば特定のテスト タスク用の "ゴールデン イメージ" をすばやく展開し、テストの終了時にそれを簡単に削除できるようにします。

この記事では、VM の作成と展開、カスタム イメージの作成、および VM の削除について、そのすべてを 1 つの完全なパイプラインとして実行する方法を示します。 通常は、各タスクを独自のカスタムのビルド・テスト・展開パイプラインで個別に実行します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>開始する前に
CI/CD パイプラインを Azure DevTest Labs に統合する前に、拡張機能を Visual Studio Marketplace からインストールする必要があります。
1. [Azure DevTest Labs タスク](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)に移動します。
1. **[インストール]** を選択します。
1. ウィザードを終了します。

## <a name="create-a-resource-manager-template"></a>Resource Manager テンプレートを作成する
このセクションでは、Azure 仮想マシンをオンデマンドで作成するために使用する Azure Resource Manager テンプレートの作成方法について説明します。

1. Resource Manager テンプレートをサブスクリプション内に作成するには、[Resource Manager テンプレートの使用](devtest-lab-use-resource-manager-template.md)に関する記事に記載されている手順を完了します。
1. Resource Manager テンプレートを生成する前に、VM 作成の一部として [WinRM 成果物](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm)を追加します。

   *Azure File Copy* や *PowerShell on Target Machines* などの展開タスクを使用するには、WinRM へのアクセスが必要です。

   > [!NOTE]
   > WinRM を共有 IP アドレスで使用する場合は、外部ポートを WinRM ポートにマップする NAT 規則を追加する必要があります。 VM をパブリック IP アドレスを使用して作成する場合は、この手順は必要ありません。
   >
   >

1. テンプレートをお使いのコンピューター上にファイルとして保存します。 このファイルには、**CreateVMTemplate.json** という名前を付けます。
1. ソース管理システムでテンプレートをチェックします。
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

1. ソース管理システムでスクリプトをチェックします。 **GetLabVMParams.ps1** といった名前を付けます。

   このスクリプトをリリース パイプラインの一部としてエージェントで実行するときに、*Azure File Copy* や *PowerShell on Target Machines* などのタスクの手順を使用すると、スクリプトによって、アプリを VM に展開するために必要な値が収集されます。 通常は、これらのタスクを使用して、アプリを Azure VM に展開します。 タスクは、VM リソース グループ名、IP アドレス、完全修飾ドメイン名 (FQDN) などの値を必要とします。

## <a name="create-a-release-pipeline-in-release-management"></a>Release Management 内にリリース パイプラインを作成する
リリース パイプラインを作成するには、次の手順を行います。

1. **[ビルドとリリース]** ハブの **[リリース]** タブで、プラス記号 (+) ボタンを選択します。
1. **[リリース定義の作成]** ウィンドウで、**[空]** テンプレートを選択し、**[次へ]** を選択します。
1. **[後で選択]** を選択し、**[作成]** を選択して、1 つの既定の環境があり、リンクされた成果物がない新しいリリース パイプラインを作成します。
1. ショートカット メニューを開くには、新しいリリース パイプラインで、環境名の横にある省略記号 (...) を選択し、**[変数の構成]** を選択します。 
1. リリース パイプライン タスクで使用する変数用の **[構成 - 環境]** ウィンドウで、次の値を入力します。

   a. **[VM 名]** には、Azure Portal で Resource Manager テンプレートを作成したときに VM に割り当てた名前を入力します。

   b. **[ユーザー名]** には、Azure Portal で Resource Manager テンプレートを作成したときに VM に割り当てたユーザー名を入力します。

   c. **[パスワード]** には、Azure Portal で Resource Manager テンプレートを作成したときに VM に割り当てたパスワードを入力します。 "南京錠" アイコンを使用して、パスワードを非表示にしてセキュリティで保護します。

### <a name="create-a-vm"></a>VM の作成

展開の次のステージは、以降の展開で "ゴールデン イメージ" として使用する VM を作成することです。 この目的のために特に開発されたタスクを使用して、Azure DevTest Lab インスタンス内に VM を作成します。 

1. リリース パイプラインで、**[タスクの追加]** を選択します。
1. **[展開]** タブで、*[Azure DevTest Labs Create VM]\(Azure DevTest Labs: VM の作成\)* タスクを追加します。 次に示すようにタスクを構成します。

   > [!NOTE]
   > 以降の展開で使用する VM を作成するには、「[Azure DevTest Labs タスク](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)」を参照してください。

   a. **[Azure RM サブスクリプション]** で、**[利用可能な Azure サービス接続]** の一覧から接続を選択するか、さらに制限されたアクセス許可を持つ Azure サブスクリプションへの接続を作成します。 詳しくは、「[Azure Resource Manager service endpoint](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)」(Azure Resource Manager のサービス エンドポイント) をご覧ください。

   b. **[ラボ名]** で、先ほど作成したインスタンスの名前を選択します。

   c. **[テンプレート名]** で、ソース コード リポジトリに保存したテンプレート ファイルの完全なパスと名前を入力します。 Release Management の組み込みのプロパティを使用して、パスを単純化できます。次に例を示します。

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. **[テンプレート パラメーター]** で、テンプレートに定義されている変数のパラメーターを入力します。 環境内に定義した変数の名前を使用します。次に例を示します。

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. **[Output Variables - Lab VM ID]\(出力変数 - Lab VM の ID\)** で、新しく作成した VM の ID が後続の手順で必要です。 **[出力変数]** セクションにこの ID が自動的に設定される環境変数の既定の名前を設定します。 変数は必要に応じて編集できますが、以降のタスクでは正しい名前を必ず使用してください。 Lab VM の ID は、次の形式で書き込まれます。

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

1. 先ほど作成したスクリプトを実行して、DevTest Labs VM の詳細情報を収集します。 
1. リリース パイプラインで **[タスクの追加]** を選択し、**[展開]** タブで *[Azure PowerShell]* タスクを追加します。 次に示すようにタスクを構成します。

   > [!NOTE]
   > DevTest Labs VM の詳細情報を収集するには、「[Deploy:Azure PowerShell (展開: Azure PowerShell)](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzurePowerShellV3)」を参照してスクリプトを実行してください。

   a. **[Azure 接続の種類]** で、**[Azure Resource Manager]** を選択します。

   b. **[Azure RM サブスクリプション]** で、**[利用可能な Azure サービス接続]** の一覧から接続を選択するか、さらに制限されたアクセス許可を持つ Azure サブスクリプションへの接続を作成します。 詳しくは、「[Azure Resource Manager service endpoint](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)」(Azure Resource Manager のサービス エンドポイント) をご覧ください。

   c. **[スクリプトの種類]** で、**[スクリプト ファイル]** を選択します。
 
   d. **[スクリプトのパス]** で、ソース コード リポジトリに保存したスクリプトの完全なパスと名前を入力します。 Release Management の組み込みのプロパティを使用して、パスを単純化できます。次に例を示します。
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. **[スクリプトの引数]** で、前のタスクによって Lab VM の ID が自動的に設定された環境変数の名前を入力します。 
      ```
      -labVmId '$(labVMId)'
      ```
    スクリプトは、必要な値を収集し、それらをリリース パイプライン内の環境変数に格納して、以降の手順で簡単に参照できるようにします。

1. アプリを新しい DevTest Labs VM に展開します。 アプリを展開するために通常使用するタスクは、*Azure File Copy* と *PowerShell on Target Machines* です。
   これらのタスクのパラメーターのために必要な VM に関する情報は、リリース パイプライン内の **labVmRgName**、**labVMIpAddress**、および **labVMFqdn** という名前の 3 つの構成変数に格納されます。 DevTest Labs VM とカスタム イメージの作成を実験するだけで、アプリを展開しない場合は、この手順をスキップできます。

### <a name="create-an-image"></a>イメージを作成する

次のステージは、新しく展開した VM のイメージを Azure DevTest Labs インスタンス内に作成することです。 この後、開発タスクやいくつかのテストを実行するときに、要求に応じてこのイメージを使用して VM のコピーを作成できます。 

1. リリース パイプラインで、**[タスクの追加]** を選択します。
1. **[展開]** タブで、**[Azure DevTest Labs Create Custom Image]\(Azure DevTest Labs: カスタム イメージの作成\)** タスクを追加します。 SAP コネクタを次のように構成します。

   > [!NOTE]
   > イメージを作成するには、「[Azure DevTest Labs タスク](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)」を参照してください。

   a. **[Azure RM サブスクリプション]** で、**[利用可能な Azure サービス接続]** の一覧から接続を選択するか、さらに制限されたアクセス許可を持つ接続を Azure サブスクリプションに作成します。 詳しくは、「[Azure Resource Manager service endpoint](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)」(Azure Resource Manager のサービス エンドポイント) をご覧ください。

   b. **[ラボ名]** で、先ほど作成したインスタンスの名前を選択します。

   c. **[カスタム イメージ名]** で、カスタム イメージの名前を入力します。

   d. (省略可能) **[説明]** で、後で適切なイメージを簡単に選択できるようにするための説明を入力します。

   e. **[Source Lab VM - Source Lab VM ID]\(ソース Lab VM - ソース Lab VM の ID\)** で、前のタスクによって Lab VM の ID が自動的に設定された環境変数の既定の名前を変更した場合は、ここでそれを編集します。 既定値は **$(labVMId)** です。

   f. **[出力変数 - カスタム イメージ ID]** では、新しく作成したイメージの ID が必要です。この ID は、イメージを管理または削除するときに使用されます。 この ID が自動的に設定される環境変数の既定の名前が、**[出力変数]** セクションに設定されます。 必要に応じて、この変数を編集できます。

### <a name="delete-the-vm"></a>VM の削除

最終ステージは、Azure DevTest Labs インスタンスに展開した VM を削除することです。 通常は、開発タスクの実行や、展開した VM で必要なテストを実行した後、VM を削除します。 

1. リリース パイプラインで、**[タスクの追加]** を選択し、**[展開]** タブで、*[Azure DevTest Labs Delete VM]\(Azure DevTest Labs: VM の削除\)* タスクを追加します。 SAP コネクタを次のように構成します。

      > [!NOTE]
      > VM を削除するには、「[Azure DevTest Labs タスク](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)」を参照してください。

   a. **[Azure RM サブスクリプション]** で、**[利用可能な Azure サービス接続]** の一覧から接続を選択するか、さらに制限されたアクセス許可を持つ Azure サブスクリプションへの接続を作成します。 詳しくは、「[Azure Resource Manager service endpoint](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm)」(Azure Resource Manager のサービス エンドポイント) をご覧ください。
 
   b. **[Lab VM ID]\(Lab VM の ID\)** で、前のタスクによって Lab VM の ID が自動的に設定された環境変数の既定の名前を変更した場合は、ここでそれを編集します。 既定値は **$(labVMId)** です。

1. リリース パイプラインの名前を入力し、それを保存します。
1. 新しいリリースを作成し、最新のビルドを選択し、パイプライン内の 1 つの環境に展開します。

ステージごとに、Azure Portal で DevTest Labs インスタンスの表示を更新して、作成される VM とイメージを表示し、再度削除される VM を表示します。

これで、必要な場合にカスタム イメージを使用して VM を作成できるようになりました。


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>次の手順
* [Resource Manager テンプレートを使用してマルチ VM 環境を作成する](devtest-lab-create-environment-from-arm.md)方法を確認します。
* [パブリックの DevTest Labs GitHub リポジトリ](https://github.com/Azure/azure-quickstart-templates)から、DevTest Labs 自動化のためのクイックスタート Resource Manager テンプレートをさらに調べます。
* 必要に応じて、[Azure DevOps トラブルシューティング](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)に関するページを参照します。
 
