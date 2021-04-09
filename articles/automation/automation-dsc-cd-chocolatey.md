---
title: Chocolatey を使用した Azure Automation の継続的なデプロイを設定する
description: この記事では、State Configuration と Chocolatey パッケージ マネージャーを使用した継続的なデプロイを設定する方法について説明します。
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: bb5f7b5e8214bd3b04bd7b9544ab4bc589f6c4bf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896327"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Chocolatey を使用して継続的配置を設定する

DevOps 環境には、継続的インテグレーション パイプラインのさまざまなポイントで役に立つ多くのツールがあります。 Azure Automation [State Configuration](automation-dsc-overview.md) は、DevOps チームが採用できる新たに追加された待望のオプションです。 

Azure Automation は、Runbook、ノード、資格情報やスケジュールやグローバル変数などの共有リソースを使用して、さまざまなタスクを自動化できる Microsoft Azure の管理サービスです。 Azure Automation State Configuration では、このオートメーション機能を拡張して、PowerShell Desired State Configuration (DSC) ツールが組み込まれます。 こちらの[概要](automation-dsc-overview.md)をご覧ください。

この記事では、Windows コンピューターに対して継続的配置 (CD) を設定する方法について説明します。 必要な数の Windows コンピューターをロール (Web サイトなど) に含め、さらにそこから追加ロールにも含めるために手法を簡単に拡張することができます。

![IaaS VM の継続的なデプロイメント](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>概要

ここにはかなり多くのことが示されていますが、幸いにも次の 2 つのメイン プロセスに分けることができます。

- コードを記述し、テストしてから、メジャーおよびマイナー バージョンのシステム用にインストール パッケージを作成して発行する。
- パッケージ内のコードをインストールおよび実行する VM を作成および管理する。  

これらのコア プロセスが両方とも存在すれば、新しいバージョンが作成されてデプロイされたときに、VM 上のパッケージを簡単に自動更新できます。

## <a name="component-overview"></a>コンポーネントの概要

[apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) などのパッケージ マネージャーは、Linux の世界ではよく知られていますが、Windows の世界ではそれほどでもありません。
[Chocolatey](https://chocolatey.org/) もあまり知られてはいませんが、Scott Hanselman の[ブログ](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)に Chocolatey の概要が適切にまとめられています。 簡単に言えば、Chocolatey を使うと、コマンド ラインを使用して、中央リポジトリから Windows オペレーティング システムにパッケージをインストールすることができます。 ユーザーは独自のリポジトリを作成してそれを管理でき、Chocolatey を使用して、指定した任意の数のリポジトリからパッケージをインストールすることができます。

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) は、マシンに必要な構成を宣言できるようにする PowerShell ツールです。 たとえば、Chocolatey をインストールし、IIS をインストールし、ポート 80 を開き、Web サイトのバージョン 1.0.0 をインストールしたい場合は、DSC のローカル構成マネージャー (LCM) でその構成を実装します。 DSC プル サーバーには、マシンの構成リポジトリが保持されています。 各マシンの LCM は、その構成が格納されている構成と一致するかどうかを定期的に確認します。 状態をレポートしたり、格納されている構成に合わせてマシンを元に戻すように試みたりすることもできます。 プル サーバーに格納されている構成を編集することで、マシンやマシン セットを変更された構成に合わせることができます。

DSC リソースは、ネットワーク、Active Directory、または SQL Server の管理などの特定の機能を備えたコードのモジュールです。 Chocolatey DSC リソースは、(特に) NuGet サーバーへのアクセス、パッケージのダウンロード、パッケージのインストールなどの方法を認識しています。 [PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)には、その他の多くの DSC リソースがあります。 ユーザーの構成で使用するには、これらのモジュールを Azure Automation State Configuration プル サーバーにインストールします。

Resource Manager テンプレートでは、インフラストラクチャ (ネットワーク、サブネット、ネットワークのセキュリティおよびルーティング、Load Balancer、NIC、VM など) を宣言的な方法で生成できます。 こちらの[記事](../azure-resource-manager/management/deployment-models.md)では、Resource Manager デプロイ モデル (宣言型) と、Azure サービス管理 (ASM、つまり、クラシック) デプロイ モデル (命令型) が比較されています。 この記事には、コア リソース プロバイダー (コンピューティング、ストレージ、ネットワーク) の説明も含まれています。

Resource Manager テンプレートの 1 つの主要機能は、プロビジョニングされている VM に VM 拡張機能をインストールする機能です。 VM 拡張機能には、カスタム スクリプトの実行、ウイルス対策ソフトウェアのインストール、DSC 構成スクリプトの実行などの特定の機能があります。 VM 拡張機能には他にも多くの種類があります。

## <a name="quick-trip-around-the-diagram"></a>ダイアグラムの概説

上から順に、コードの記述、ビルド、テスト、インストール パッケージの作成が示されています。 Chocolatey は、MSI、MSU、ZIP など、さまざまな種類のインストール パッケージを処理することができます。 Chocolatey のネイティブ機能が適切でない場合は、PowerShell の全機能を使用して実際にインストールを行うことができます。 到達可能な場所 (パッケージ リポジトリ) にパッケージを配置します。 この使用例では、Azure BLOB ストレージ アカウントのパブリック フォルダーを使用しますが、どの場所でもかまいません。 Chocolatey は、NuGet サーバーや他のいくつかのサーバーとネイティブに動作して、パッケージ メタデータを管理します。 [この記事](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) ではオプションについて説明しています。 使用例では、NuGet を使用します。 Nuspec は、パッケージに関するメタデータです。 Nuspec の情報は NuPkg にコンパイルされて、NuGet サーバーに格納されます。 構成において名前でパッケージが要求され、NuGet サーバーが参照されていると、VM 上の Chocolatey DSC リソースでパッケージが取得されて、インストールされます。 特定のバージョンのパッケージを要求することもできます。

図の左下には、Azure Resource Manager テンプレートがあります。 この使用例の VM 拡張機能では、VM がノードとして Azure Automation State Configuration プル サーバーに登録されます。 構成は、1 回はプレーンテキストとして、もう 1 回は MOF ファイルとしてコンパイルされた状態で、プル サーバーに 2 回保存されます。 Azure portal では、MOF は、単なる構成ではなくノード構成を表します。 これはノードに関連付けられた成果物であるため、ノードではその構成が認識されます。 ノードへのノード構成の割り当て方法の詳細については、以下に示します。

Nuspec を作成し、それをコンパイルして NuGet サーバーに格納するのはささいなことです。 また、VM の管理も既に行われています。 

継続的配置の次のステップを実行するには、プル サーバーを 1 回設定し、それにノードを 1 回登録してから、初期構成を作成してサーバーに格納する必要があります。 パッケージがアップグレードされて、リポジトリにデプロイされたら、必要に応じてプル サーバーで構成とノード構成を更新することだけが必要です。

Resource Manager テンプレートを使用して始めなくてもかまいません。 プル サーバーに VM を登録するときに役立つ PowerShell コマンドレットがあります。 詳細については、「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」をご覧ください。

## <a name="about-the-usage-example"></a>使用例について

この記事の使用例では、Azure ギャラリーにある一般的な Windows Server 2012 R2 イメージから VM を開始します。 格納されている任意のイメージから開始し、DSC 構成で微調整を行うことができます。
ただし、イメージに組み込まれている構成を変更するのは、DSC を使用して構成を動的に更新するよりもはるかに困難です。

VM でこの手法を使用する際に、Resource Manager テンプレートや VM 拡張機能を使用する必要はありません。 また、VM は、CD 管理下の Azure にある必要はありません。 必要なのは、Chocolatey をインストールし、プル サーバーの場所を認識するように VM で LCM を構成することだけです。

運用環境にある VM 上のパッケージを更新する場合は、更新プログラムのインストール時にローテーションからその VM を除外する必要があります。 この方法はさまざまです。 たとえば、Azure Load Balancer の背後にある VM では、カスタム プローブを追加できます。 VM の更新時に、プローブ エンドポイントから 400 が返されるようにします。 この変更に必要な微調整は構成内で行うことができます。更新が完了したら、200 が返されるように微調整して元に戻すことができます。

この使用例の完全なソースは、GitHub の [この Visual Studio プロジェクト](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) にあります。

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>手順 1:プル サーバーと Automation アカウントを設定する

以下の認証済み (`Connect-AzAccount`) の PowerShell コマンド ラインを使用します (プル サーバーの設定には数分かかる場合があります)。

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

Automation アカウントは、米国東部 2、米国中南部、US Gov バージニア、西ヨーロッパ、東南アジア、東日本、インド中部、オーストラリア南東部、カナダ中部、北ヨーロッパのいずれかのリージョン (場所) に配置できます。

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>手順 2:VM 拡張機能を使用して Resource Manager テンプレートに応じて微調整する

(PowerShell DSC VM 拡張機能を使用する) VM 登録の詳細については、この [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)を参照してください。
この手順では、新しい VM を State Configuration ノードのリストのプル サーバーに登録します。 この登録の中で、ノードに適用するノード構成を指定します。 このノード構成は、現時点でプル サーバーに配置されている必要はありません。手順 4 で初めて実行してもかまいません。 ただし、この手順 2. でノードの名前と構成の名前を決定しておく必要があります。 この使用例では、ノードの名前は "isvbox" で、構成の名前は "ISVBoxConfig" です。 このため、(DeploymentTemplate.json で指定される) ノード構成の名前は、"ISVBoxConfig.isvbox" です。

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>手順 3:プル サーバーに必要な DSC リソースを追加する

PowerShell ギャラリーは、Azure Automation アカウントに DSC リソースをインストールするためにインストルメント化されています。
必要なリソースに移動し、[Deploy to Azure Automation]\(Azure Automation にデプロイ\) ボタンをクリックします。

![PowerShell ギャラリーの例](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Azure portal に最近追加された別の手法を使用すると、新しいモジュールを取得したり既存のモジュールを更新したりできます。 [Automation アカウント] リソース、[アセット] タイル、[モジュール] タイルの順にクリックします。 [ギャラリーの参照] アイコンを使用すると、ギャラリー内のモジュールの一覧が表示され、詳細にドリルダウンして、Automation アカウントにインポートできます。 これは、モジュールを随時最新の状態に保つことができる素晴らしい方法です。 このインポート機能では、同期もれがないように他のモジュールとの依存関係もチェックされます。

また、後でアップグレードする必要がない場合は、リソースごとに 1 回だけ使用される手動のアプローチもあります。 PowerShell 統合モジュールの作成の詳細については、「[Azure Automation 用の統合モジュールの作成](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)」を参照してください。

>[!NOTE]
>Windows コンピューター用の PowerShell 統合モジュールのフォルダー構造は、Azure Automation で必要なフォルダー構造とは少し異なります。 

1. [Windows Management Framework v5](https://aka.ms/wmf5latest) をインストールします (Windows 10 では不要)。

2. 統合モジュールをインストールします。

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. モジュール フォルダーを **c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** から一時フォルダーにコピーします。

4. メイン フォルダーからサンプルとドキュメントを削除します。

5. メイン フォルダーを ZIP で圧縮し、ZIP ファイルにフォルダーの名前を付けます。

6. Azure ストレージ アカウントの Blob Storage など、アクセス可能な HTTP の場所に ZIP ファイルを配置します。

7. 次のコマンドを実行します。

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

ここに含まれる例では、cChoco と xNetworking のための手順が実装されます。 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>手順 4:プル サーバーにノード構成を追加する

初めて構成をプル サーバーにインポートしてコンパイルすることについて、特筆することはありません。 構成が同じであれば、以降のすべてのインポートおよびコンパイルはまったく同じに見えます。 パッケージを更新し、実稼働環境にプッシュする必要があるたびに、構成ファイル (新しいバージョンのパッケージを含む) が正しいことを確認してから、この手順を実行します。 **ISVBoxConfig.ps1** 構成ファイルを次に示します。

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

**New-ConfigurationScript.ps1** スクリプト (Az モジュールを使用するように変更されたもの) を次に示します。

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

これらの手順を実行すると、**ISVBoxConfig.isvbox** という名前の新しいノード構成がプル サーバーに配置されます。 ノード構成の名前は `configurationName.nodeName` です。

## <a name="step-5-create-and-maintain-package-metadata"></a>手順 5:パッケージ メタデータを作成して管理する

パッケージ リポジトリに格納されているパッケージごとに、それを記述する Nuspec が必要です。 それをコンパイルし、NuGet サーバーに格納する必要があります。 このプロセスについては、 [こちら](https://docs.nuget.org/create/creating-and-publishing-a-package)を参照してください。 

**MyGet.org** を NuGet サーバーとして使用できます。 このサービスを購入することもできますが、無料のスターター SKU があります。 [NuGet](https://www.nuget.org/) で、プライベート パッケージの独自の NuGet サーバーをインストールする方法を参照できます。

## <a name="step-6-tie-it-all-together"></a>手順 6:すべてをまとめて関連付ける

バージョンが QA に合格し、デプロイを承認されるたびに、パッケージが作成され、nuspec と nupkg が更新あれて、NuGet サーバーにデプロイされます。 構成 (手順 4) も、新しいバージョン番号を承認するために更新する必要があります。 構成はプル サーバーに送られ、コンパイルされる必要があります。

ここからの先の、更新をプルし、インストールする方法は、構成に依存する VM によって異なります。 これらの更新はいずれも単純で、PowerShell でほんの 1 ないし 2 行です。 Azure DevOps の場合、これらの更新の一部は、ビルドに連結できるビルド タスク内でカプセル化されます。 詳細については、この[記事](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)を参照してください。 この [GitHub のリポジトリ](https://github.com/Microsoft/vso-agent-tasks)では、使用できるビルド タスクの詳細が示されています。

## <a name="related-articles"></a>関連記事
* [Azure Automation DSC の概要](automation-dsc-overview.md)
* [Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)

## <a name="next-steps"></a>次のステップ

- 概要については、[Azure Automation State Configuration の概要](automation-dsc-overview.md)に関するページを参照してください。
- この機能の使用を開始するには、「[Azure Automation State Configuration の使用を開始する](automation-dsc-getting-started.md)」を参照してください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てる方法の詳細については、「[Azure Automation State Configuration で DSC 構成をコンパイルする](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、「[Az.Automation](/powershell/module/az.automation)」をご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
