---
title: Chocolatey を使用した Azure Automation State Configuration の継続的なデプロイ
description: Azure Automation State Configuration と Chocolatey パッケージ マネージャーを使用した DevOps の継続的なデプロイメント。  完全な JSON の Resource Manager テンプレートと PowerShell ソースの例です。
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.date: 08/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: ddbf652c35c4f1504e3253838a983fd0f6039401
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850365"
---
# <a name="usage-example-continuous-deployment-to-virtual-machines-using-automation-state-configuration-and-chocolatey"></a>使用例: Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ

DevOps 領域には、継続的な統合パイプラインのさまざまなポイントで役立つ多くのツールがあります。 Azure Automation State Configuration は、DevOps チームが採用できる新たに追加された待望のオプションです。 この記事では、Windows コンピューター用の継続的なデプロイメント (CD) の設定について説明します。 必要な数の Windows コンピューターをロール (Web サイトなど) に含め、さらにそこから追加ロールにも含めるために手法を簡単に拡張することができます。

![IaaS VM の継続的なデプロイメント](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>概要

ここにはかなり多くのことが示されていますが、幸いにも次の 2 つのメイン プロセスに分けることができます。

- コードを記述し、テストしてから、メジャーおよびマイナー バージョンのシステム用にインストール パッケージを作成して発行する。
- パッケージ内のコードをインストールおよび実行する VM を作成および管理する。  

これらのコア プロセスの両方が実施されていれば、新しいバージョンの作成およびデプロイ時に、特定の VM で実行されているパッケージを短い手順で自動的に更新できるようになります。

## <a name="component-overview"></a>コンポーネントの概要

[apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) などのパッケージ マネージャーは、Linux の世界ではかなりよく知られていますが、Windows の世界ではそれほどでもありません。
[Chocolatey](https://chocolatey.org/) もあまり知られてはいませんが、Scott Hanselman の[ブログ](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx)に Chocolatey の概要が適切にまとめられています。 簡単に言えば、Chocolatey ではコマンドラインを使用して、パッケージの中央リポジトリから Windows システムにパッケージをインストールすることができます。 ユーザーは独自のリポジトリを作成してそれを管理でき、Chocolatey を使用して、指定した任意の数のリポジトリからパッケージをインストールすることができます。

Desired State Configuration (DSC) ([概要](/powershell/scripting/dsc/overview/overview)) は、マシンに必要な構成を宣言できるようにする PowerShell ツールです。 たとえば、Chocolatey をインストールする必要がある、IIS をインストールする必要がある、ポート 80 を開く必要がある、バージョン 1.0.0 の自分の Web サイトをインストールする必要がある、などと示すことができます。 DSC Local Configuration Manager (LCM) により、その構成が実装されます。 DSC プル サーバーは、マシンの構成リポジトリを保持します。 各マシンの LCM は、その構成が格納されている構成と一致するかどうかを定期的に確認します。 状態をレポートしたり、格納されている構成に合わせてマシンを元に戻すように試みたりすることもできます。 プル サーバーに格納されている構成を編集することで、マシンやマシン セットを変更された構成に合わせることができます。

Azure Automation は、Runbook、ノード、資格情報、スケジュールやグローバル変数などのリソースおよび資産を使用して、さまざまなタスクを自動化できる Microsoft Azure の管理されたサービスです。
Azure Automation State Configuration はこのオートメーション機能を拡張して、PowerShell DSC ツールを組み込みます。 こちらの[概要](automation-dsc-overview.md)をご覧ください。

DSC リソースは、ネットワーク、Active Directory、または SQL Server の管理などの特定の機能を備えたコードのモジュールです。 Chocolatey DSC リソースは、(特に) NuGet サーバーへのアクセス、パッケージのダウンロード、パッケージのインストールなどの方法を認識しています。 [PowerShell ギャラリー](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title)には、その他の多くの DSC リソースがあります。
これらのモジュールは、(ユーザーによって) Azure Automation State Configuration プル サーバーにインストールされるため、ユーザーの構成で使用できます。

Resource Manager テンプレートでは、インフラストラクチャ (ネットワーク、サブネット、ネットワークのセキュリティおよびルーティング、Load Balancer、NIC、VM など) を宣言的な方法で生成できます。 この[記事](../azure-resource-manager/resource-manager-deployment-model.md)では、Resource Manager デプロイ モデル (宣言型) と Azure Service Management (ASM、つまりクラシック) デプロイ モデル (命令型) が比較されており、また、コア リソース プロバイダー、コンピューティング、ストレージおよびネットワークについて記載されています。

Resource Manager テンプレートの 1 つの主要機能は、プロビジョニングされている VM に VM 拡張機能をインストールする機能です。 VM 拡張機能には、カスタム スクリプトの実行、ウイルス対策ソフトウェアのインストール、DSC 構成スクリプトの実行などの特定の機能があります。 VM 拡張機能には他にも多くの種類があります。

## <a name="quick-trip-around-the-diagram"></a>ダイアグラムの概説

上から順に、コードの記述、ビルド、テスト、インストール パッケージの作成が示されています。
Chocolatey は、MSI、MSU、ZIP など、さまざまな種類のインストール パッケージを処理することができます。 Chocolatey のネイティブ機能が適切でない場合は、PowerShell の全機能を使用して実際にインストールを行うことができます。 到達可能な場所 (パッケージ リポジトリ) にパッケージを配置します。 この使用例では、Azure BLOB ストレージ アカウントのパブリック フォルダーを使用しますが、どの場所でもかまいません。 Chocolatey は、NuGet サーバーや他のいくつかのサーバーとネイティブに動作して、パッケージ メタデータを管理します。 [この記事](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) ではオプションについて説明しています。 この使用例では、NuGet を使用します。 Nuspec は、パッケージに関するメタデータです。 Nuspec は NuPkg に "コンパイル" され、NuGet サーバーに格納されます。 構成が名前でパッケージを要求し、NuGet サーバーを参照すると、Chocolatey DSC リソース (現在 VM 上にある) は自動的にパッケージを取得して、インストールします。 特定のバージョンのパッケージを要求することもできます。

図の左下部には、Azure Resource Manager テンプレートがあります。 この使用例の VM 拡張機能では、VM をノードとして Azure Automation State Configuration プル サーバー (つまり、プル サーバー) に登録します。 構成はプル サーバーに格納されます。
実際には、2 回格納されます。つまり、プレーン テキストとして 1 回格納され、MOF ファイルとして 1 回コンパイルされます (該当するものが認識されている場合)。ポータルでは、MOF は "ノード構成" (単なる "構成" ではない) を指します。 これはノードに関連付けられているアーティファクトであるため、ノードはその構成を認識します。 ノードへのノード構成の割り当て方法の詳細については、以下に示します。

この時点で、おそらく、最上部の一部あるいは大部分の作業は既に実行されています。 nuspec を作成し、それをコンパイルして NuGet サーバーに格納するのはささいなことです。 また、VM の管理も既に行われています。 継続的なデプロイメントのための次の手順を実行するには、プル サーバーを (1 回) 設定し、それにノードを (1 回) 登録してから、そこで (最初に) 構成を作成して格納する必要があります。 次に、パッケージがアップグレードされ、リポジトリにデプロイされたときに、プル サーバーの構成およびノード構成を更新します (必要に応じて繰り返します)。

Resource Manager テンプレートを使用しなくてもかまいません。 プル サーバーと他のすべてのサーバーに VM を登録する際に役立つように設計されている PowerShell コマンドレットがあります。 詳細については、次の記事を参照してください。「[Azure Automation State Configuration による管理のためのマシンのオンボード](automation-dsc-onboarding.md)」。

## <a name="step-1-setting-up-the-pull-server-and-automation-account"></a>手順 1:プル サーバーと Automation アカウントを設定する

以下の認証済み (`Connect-AzureRmAccount`) の PowerShell コマンド ラインを使用します (プル サーバーの設定には数分かかる場合があります)。

```azurepowershell-interactive
New-AzureRmResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzureRmAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

お客様の Automation アカウントは、米国東部 2、米国中南部、US Gov バージニア、西ヨーロッパ、東南アジア、東日本、インド中部、オーストラリア南東部、カナダ中部、北ヨーロッパのいずれかのリージョン (場所) に配置できます。

## <a name="step-2-vm-extension-tweaks-to-the-resource-manager-template"></a>手順 2:VM 拡張機能を使用して Resource Manager テンプレートに応じて微調整する

(PowerShell DSC VM 拡張機能を使用する) VM 登録の詳細については、この [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)を参照してください。
この手順では、新しい VM を State Configuration ノードのリストのプル サーバーに登録します。 この登録の中で、ノードに適用するノード構成を指定します。 このノード構成は、現時点でプル サーバーに配置されている必要はありません。手順 4. で初めて実行してもかまいません。 ただし、この手順 2. でノードの名前と構成の名前を決定しておく必要があります。 この使用例では、ノードの名前は "isvbox" で、構成の名前は "ISVBoxConfig" です。 このため、(DeploymentTemplate.json で指定される) ノード構成の名前は、"ISVBoxConfig.isvbox" です。

## <a name="step-3-adding-required-dsc-resources-to-the-pull-server"></a>手順 3:プル サーバーに必要な DSC リソースを追加する

PowerShell ギャラリーは、Azure Automation アカウントに DSC リソースをインストールするためにインストルメント化されています。
必要なリソースに移動し、[Azure Automation にデプロイ] ボタンをクリックします。

![PowerShell ギャラリーの例](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Azure Portal に最近追加された別の手法を使用すると、新しいモジュールを取得したり既存のモジュールを更新したりできます。 [Automation アカウント] リソース、[アセット] タイル、[モジュール] タイルの順にクリックします。 [ギャラリーの参照] アイコンを使用すると、ギャラリー内のモジュールの一覧を表示し、詳細にドリルダウンして、Automation アカウントにインポートできます。 これは、モジュールを随時最新の状態に保つことができる素晴らしい方法です。 このインポート機能では、同期もれがないように他のモジュールとの依存関係もチェックされます。

手動による方法もあります。 Windows コンピューター用の PowerShell 統合モジュールのフォルダー構造は、Azure Automation で必要なフォルダー構造とは少し異なります。
ユーザーが少し調整する必要があります。 しかし、難しくはありません。リソースごとに一度行うだけです (将来的にアップグレードする場合を除く)。PowerShell 統合モジュールの作成の詳細については、次の記事を参照してください。「[Authoring Integration Modules for Azure Automation (Azure Automation 用の統合モジュールの作成)](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)」

- 次のように、ワークステーションに必要なモジュールをインストールします。
  - [Windows Management Framework v5](https://aka.ms/wmf5latest) をインストールします (Windows 10 では不要)。
  - `Install-Module –Name MODULE-NAME`    <—PowerShell ギャラリーからモジュールを取得します
- `c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME` からモジュール フォルダーを一時フォルダーにコピーします。
- メイン フォルダーからサンプルとドキュメントを削除します。
- メイン フォルダーを zip 圧縮します。zip ファイルにはフォルダーとまったく同じ名前を付けます。 
- Azure ストレージ アカウントの Blob Storage など、アクセス可能な HTTP の場所に zip ファイルを配置します。
- 以下の PowerShell を実行します。

  ```powershell
  New-AzureRmAutomationModule `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Name MODULE-NAME –ContentLink 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
  ```

ここに含まれる例では、cChoco と xNetworking に対してこれらの手順を実行します。 cChoco への特別な操作については、「 [メモ](#notes) 」を参照してください。

## <a name="step-4-adding-the-node-configuration-to-the-pull-server"></a>手順 4:プル サーバーにノード構成を追加する

初めて構成をプル サーバーにインポートしてコンパイルすることについて、特筆することはありません。 構成が同じであれば、以降のすべてのインポートおよびコンパイルはまったく同じに見えます。 パッケージを更新し、実稼働環境にプッシュする必要があるたびに、構成ファイル (新しいバージョンのパッケージを含む) が正しいことを確認してから、この手順を実行します。 以下に、構成ファイルと PowerShell を示します。

ISVBoxConfig.ps1:

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

New-ConfigurationScript.ps1:

```powershell
Import-AzureRmAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzureRmAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

これらの手順を実行すると、"ISVBoxConfig.isvbox" という名前の新しいノード構成がプル サーバーに生成されます。 ノード構成の名前は、"configurationName.nodeName" としてビルドされます。

## <a name="step-5-creating-and-maintaining-package-metadata"></a>手順 5:パッケージ メタデータを作成して管理する

パッケージ リポジトリに格納されているパッケージごとに、それを記述する nuspec が必要です。
その nuspec をコンパイルし、NuGet サーバーに格納する必要があります。 このプロセスについては、 [こちら](https://docs.nuget.org/create/creating-and-publishing-a-package)を参照してください。 MyGet.org を NuGet サーバーとして使用できます。 このサービスは購入する必要がありますが、スターター SKU は無料です。 NuGet.org で、プライベート パッケージの独自の NuGet サーバーをインストールする方法を参照できます。

## <a name="step-6-tying-it-all-together"></a>手順 6:まとめ

バージョンを QA に渡し、デプロイが承認されるたびに、パッケージが作成され、nuspec と nupkg が更新あれて、NuGet サーバーにデプロイされます。 さらに、構成 (前の手順 4) も、新しいバージョン番号を承認するために更新する必要があります。 構成はプル サーバーに送られ、コンパイルされる必要があります。
ここからの先の、更新をプルし、インストールする方法は、構成に依存する VM によって異なります。 これらの更新はいずれも単純で、PowerShell でほんの 1 ～ 2 行です。 Azure DevOps の場合、これらの更新の一部は、ビルドに連結できるビルド タスク内でカプセル化されます。 詳細については、この[記事](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery)を参照してください。 この [GitHub のリポジトリ](https://github.com/Microsoft/vso-agent-tasks) で、使用できるさまざまなビルド タスクを列挙しています。

## <a name="notes"></a>メモ

この使用例では、Azure ギャラリーにある一般的な Windows Server 2012 R2 イメージから VM を開始します。 格納されている任意のイメージから開始し、DSC 構成で微調整を行うことができます。
ただし、イメージに組み込まれている構成を変更するのは、DSC を使用して構成を動的に更新するよりもはるかに困難です。

VM でこの手法を使用する際に、Resource Manager テンプレートや VM 拡張機能を使用する必要はありません。 また、VM は、CD 管理下の Azure にある必要はありません。 必要なのは、Chocolatey をインストールし、プル サーバーの場所を認識するように VM で LCM を構成することだけです。

もちろん、実稼働環境にある VM 上のパッケージを更新する場合は、更新プログラムのインストール時にローテーションからその VM を除外する必要があります。 この方法はさまざまです。 たとえば、Azure Load Balancer の背後にある VM では、カスタム プローブを追加できます。 VM の更新時に、プローブ エンドポイントから 400 が返されるようにします。 この変更に必要な微調整は構成内で行うことができます。更新が完了したら、200 が返されるように微調整して元に戻すことができます。

この使用例の完全なソースは、GitHub の [この Visual Studio プロジェクト](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) にあります。

## <a name="related-articles"></a>関連記事
* [Azure Automation DSC の概要](automation-dsc-overview.md)
* [Azure Automation DSC cmdlets (Azure Automation DSC コマンドレット)](https://docs.microsoft.com/powershell/module/azurerm.automation#automation)
* [Azure Automation DSC による管理のためのマシンのオンボード](automation-dsc-onboarding.md)

## <a name="next-steps"></a>次の手順

- 概要については、[Azure Automation State Configuration](automation-dsc-overview.md) に関するページをご覧ください。
- 使用を開始するには、「[Azure Automation State Configuration の使用](automation-dsc-getting-started.md)」をご覧ください。
- DSC 構成をコンパイルしてターゲット ノードに割り当てることができるようにする方法の詳細については、「[Azure Automation State Configuration での構成のコンパイル](automation-dsc-compile.md)」をご覧ください。
- PowerShell コマンドレットのリファレンスについては、[Azure Automation State Configuration のコマンドレット](/powershell/module/azurerm.automation/#automation)に関するページをご覧ください。
- 料金情報については、[Azure Automation State Configuration の価格](https://azure.microsoft.com/pricing/details/automation/)に関するページをご覧ください。
- 継続的なデプロイ パイプラインで Azure Automation State Configuration を使う例については、「[Automation State Configuration と Chocolatey を使用した仮想マシンへの継続的なデプロイ](automation-dsc-cd-chocolatey.md)」をご覧ください。
