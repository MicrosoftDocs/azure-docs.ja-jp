---
title: Windows PowerShell DSC を使用して Connected Machine エージェントをインストールする
description: この記事では、Windows PowerShell DSC を使用して、Azure Arc 対応サーバー (プレビュー) を利用する Azure にマシンを接続する方法について学習します。
ms.date: 03/12/2020
ms.topic: conceptual
ms.openlocfilehash: 25d6e435c261a83bf81c15d5dd445a936d48a08b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213078"
---
# <a name="how-to-install-the-connected-machine-agent-using-windows-powershell-dsc"></a>Windows PowerShell DSC を使用して Connected Machine エージェントをインストールする方法

[Windows PowerShell Desired State Configuration](/powershell/scripting/dsc/getting-started/winGettingStarted?view=powershell-7) (DSC) を使用して、Windows コンピューターのソフトウェアのインストールと構成を自動化できます。 この記事では、DSC を使用して Azure Arc 対応サーバー (プレビュー) Connected Machine エージェントをハイブリッド Windows マシンにインストールする方法について説明します。

## <a name="requirements"></a>必要条件

- Windows PowerShell バージョン 4.0 以降

- [AzureConnectedMachineDsc](https://www.powershellgallery.com/packages/AzureConnectedMachineDsc/1.0.1.0) DSC モジュール

- マシンを Azure Arc 対応サーバー (プレビュー) に非対話的に接続するためのサービス プリンシパル。 Arc 対応サーバー (プレビュー) のサービス プリンシパルをまだ作成していない場合は、「[大規模なオンボーディング用にサービス プリンシパルを作成する](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)」セクションの手順に従ってください。

## <a name="install-the-connectedmachine-dsc-module"></a>ConnectedMachine DSC モジュールをインストールする

1. モジュールを手動でインストールするには、ソース コードをダウンロードし、プロジェクト ディレクトリの内容を `$env:ProgramFiles\WindowsPowerShell\Modules folder` に解凍します。 または、次のコマンドを実行して、(PowerShell 5.0 の) PowerShellGet を使用して PowerShell ギャラリーからインストールします。

    ```powershell
    Find-Module -Name AzureConnectedMachineDsc -Repository PSGallery | Install-Module
    ```

2. インストールを確認するには、次のコマンドを実行して、Azure Connected Machine DSC リソースが使用可能であることを確認します。

    ```powershell
    Get-DscResource -Module AzureConnectedMachineDsc
    ```

   次のような出力結果が表示されます。

   ![Connected Machine DSC モジュールのインストールの確認の例](./media/onboard-dsc/confirm-module-installation.png)

## <a name="install-the-agent-and-connect-to-azure"></a>エージェントをインストールして Azure に接続する

このモジュールのリソースは、Azure Connected Machine Agent の構成を管理するように設計されています。 また、`AzureConnectedMachineDsc\examples` フォルダーにある PowerShell スクリプト `AzureConnectedMachineAgent.ps1` も含まれています。 コミュニティ リソースを使用して、ダウンロードとインストールが自動化され、Azure Arc との接続が確立されます。このスクリプトでは、「[Azure portal からハイブリッド マシンを Azure に接続する](onboard-portal.md)」に説明されているものに似た手順が実行されます。

マシンでプロキシ サーバーを介してサービスと通信する必要がある場合は、エージェントをインストールした後、[こちら](manage-agent.md#update-or-remove-proxy-settings)に記載されているコマンドを実行する必要があります。 これにより、プロキシ サーバーのシステム環境変数 `https_proxy` が設定されます。 コマンドを手動で実行する代わりに、[ComputeManagementDsc](https://www.powershellgallery.com/packages/ComputerManagementDsc/6.0.0.0) モジュールを使用して DSC でこの手順を実行できます。

>[!NOTE]
>DSC で実行できるようにするには、localhost の構成を実行している場合でも PowerShell のリモート コマンドを受信するように Windows を構成する必要があります。 環境を簡単に正しく構成するには、管理者特権の PowerShell ターミナルで `Set-WsManQuickConfig -Force` を実行するだけです。
>

`Start-DscConfiguration` コマンドレットを使用して、構成ドキュメント (MOF ファイル) をマシンに適用できます。

PowerShell スクリプトに渡して使用するパラメーターを次に示します。

- `TenantId`:Azure AD の専用インスタンスを表す一意識別子 (GUID)。

- `SubscriptionId`:その中でマシンが必要な Azure サブスクリプションのサブスクリプション ID (GUID)。

- `ResourceGroup`:接続されたマシンの所属先にしたいリソース グループの名前。

- `Location`:[サポート対象の Azure リージョン](overview.md#supported-regions)に関するページを参照してください。 この場所は、リソース グループの場所と同じ場合も異なる場合もあります。

- `Tags`:Cnnected Machine リソースに適用する必要があるタグの文字列配列。

- `Credential`:[サービス プリンシパル](onboard-service-principal.md)を使用して大規模にマシンを登録するために使用される、**ApplicationId** と **password** を持つ PowerShell 資格情報オブジェクト。 

1. PowerShell コンソールで、`.ps1` ファイルを保存したフォルダーに移動します。

2. 次の PowerShell コマンドを実行して、MOF ドキュメントをコンパイルします (DSC 構成のコンパイルについては、「[DSC 構成](/powershell/scripting/dsc/configurations/configurations?view=powershell-7)」をご覧ください):

    ```powershell
    .\`AzureConnectedMachineAgent.ps1 -TenantId <TenantId GUID> -SubscriptionId <SubscriptionId GUID> -ResourceGroup '<ResourceGroupName>' -Location '<LocationName>' -Tags '<Tag>' -Credential <psCredential>
    ```

3. これにより、`C:\dsc` という名前の新しいフォルダーに、`localhost.mof file` が作成されます。

エージェントをインストールし、Azure Arc 対応サーバー (プレビュー) に接続するように構成したら、Azure portal に移動して、サーバーが正常に接続されていることを確認します。 自分のマシンは [Azure portal](https://aka.ms/hybridmachineportal) に表示されます。

## <a name="adding-to-existing-configurations"></a>既存の構成への追加

このリソースを既存の DSC 構成に追加して、コンピューターのエンドツーエンドの構成を表すことができます。 たとえば、セキュリティで保護されたオペレーティング ステムの設定を行う構成に、このリソースを追加できます。

PowerShell ギャラリーの [CompsiteResource](https://www.powershellgallery.com/packages/compositeresource/0.4.0) モジュールを使用して、構成例の[複合リソース](/powershell/scripting/dsc/resources/authoringResourceComposite?view=powershell-7)を作成して、構成の組み合わせをさらにシンプルにすることができます。

## <a name="next-steps"></a>次のステップ

- [Azure Policy](../../governance/policy/overview.md) を使用してマシンを管理する方法を確認します。VM の[ゲスト構成](../../governance/policy/concepts/guest-configuration.md)、マシンの報告先が、予期された Log Analytics ワークスペースであることの確認、[VM での Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md) を使用した監視の有効化などの方法です。

- [Log Analytics エージェント](../../azure-monitor/platform/log-analytics-agent.md)の詳細を確認します。 マシン上で実行されている OS とワークロードをプロアクティブに監視したい場合、それを Automation Runbook やソリューション (Update Management など) を使用して管理したい場合、または他の Azure サービス ([Azure Security Center](../../security-center/security-center-intro.md) など) を使用したい場合は、Windows 用および Linux 用の Log Analytics エージェントが必要となります。