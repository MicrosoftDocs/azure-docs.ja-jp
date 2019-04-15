---
title: App Service on Azure Stack Update 1 のリリース ノート | Microsoft Docs
description: App Service on Azure Stack Update 1 の内容、既知の問題、更新プログラムをダウンロードする場所について説明します。
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: sethm
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 3d75467da01f0672bb735e01cbd6d7634cdf843e
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445240"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>App Service on Azure Stack Update 1 のリリース ノート

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

これらのリリース ノートでは、Azure App Service on Azure Stack Update 1 における機能強化と修正点、および既知の問題について説明します。 既知の問題は、デプロイおよび更新プロセスに直接関係する問題と、ビルド (インストール後) に関する問題に分けられています。

> [!IMPORTANT]
> Azure App Service をデプロイする前に、Azure Stack 統合システムに 1802 更新プログラムを適用するか、または最新の Azure Stack 開発キットをデプロイします。
>
>

## <a name="build-reference"></a>ビルドのリファレンス

App Service on Azure Stack Update 1 のビルド番号は **69.0.13698.9** です。

### <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> Azure App Service で Kudu の SSO の処理方法が改善されたため、Azure App Service の Azure Stack への新しいデプロイには、[3 つのサブジェクトのワイルドカード証明書](azure-stack-app-service-before-you-get-started.md#get-certificates)が必要になります。 新しいサブジェクトは、**\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>** です。
>
>

デプロイを開始する前に、[前提条件に関するドキュメント](azure-stack-app-service-before-you-get-started.md)をご覧ください。

### <a name="new-features-and-fixes"></a>新機能と修正

Azure App Service on Azure Stack Update 1 には、次の機能強化と修正が含まれています。

- **Azure App Service の高可用性** - Azure Stack 1802 更新プログラムにより、ワークロードを複数の障害ドメインにデプロイできるようになりました。 そのため、App Service インフラストラクチャは、複数の障害ドメインへのデプロイによってフォールト トレランスを実現できます。 既定では、Azure App Service のすべての新しいデプロイにこの機能が含まれます。ただし、Azure Stack 1802 更新プログラムを適用する前に完了したデプロイについては、[App Service の障害ドメインに関するドキュメント](azure-stack-app-service-fault-domain-update.md)を参照してください。

- **既存の仮想ネットワークへのデプロイ** - App Service on Azure Stack を既存の仮想ネットワーク内にデプロイできるようになりました。 既存の仮想ネットワークにデプロイすると、Azure App Service に必要な SQL Server とファイル サーバーにプライベート ポート経由で接続できます。 デプロイ時に既存の仮想ネットワークへのデプロイを選択できますが、デプロイ前に、[App Service が使用するサブネットを作成する必要があります](azure-stack-app-service-before-you-get-started.md#virtual-network)。

- **App Service のテナント ポータル、管理ポータル、Functions ポータル、Kudu ツール**の更新。 Azure Stack Portal SDK バージョンと一致しています。

- **Azure Functions ランタイム**が **v1.0.11388** に更新されました。

- **次のアプリケーション フレームワークとツールの更新**:
    - **.NET Core 2.0** のサポートが追加されました。
    - **Node.js** の次のバージョンが追加されました。
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - **NPM** の次のバージョンが追加されました。
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - 次の **PHP** 更新プログラムが追加されました。
        - 5.6.32
        - 7.0.26 (x86 および x64)
        - 7.1.12 (x86 および x64)
    - **Git for Windows** が v 2.14.1 に更新されました。
    - **Mercurial** が v4.5.0 に更新されました。

  - App Service テナント ポータルの [カスタム ドメイン] 機能内で **[HTTPS のみ]** 機能のサポートが追加されました。 

  - Azure Functions のカスタム ストレージ ピッカーにストレージ接続の検証が追加されました。 

#### <a name="fixes"></a>修正

- オフライン展開パッケージの作成時に、App Service インストーラーからフォルダーを開くときに、アクセス拒否エラー メッセージが表示されることはなくなります。

- App Service テナント ポータルの [カスタム ドメイン] 機能の使用時の問題が解決されました。

- セットアップ中に予約済みの管理者名を使用できなくなります。

- **ドメイン参加済み**ファイル サーバーで App Service デプロイメントが有効になりました。

- スクリプトでの Azure Stack のルート証明書の取得が改善され、App Service インストーラーでルート証明書が検証されるようになりました。

- Microsoft.Web 名前空間のリソースを含むサブスクリプションが削除されたときに、Azure Resource Manager に正しくない状態が返される問題が修正されました。

### <a name="known-issues-with-the-deployment-process"></a>デプロイ プロセスに関する既知の問題

- 証明書の検証エラー

インストーラーの検証が過度に制限されているため、統合システムにデプロイするときに App Service インストーラーに証明書を指定しようとして問題が発生することがあります。 App Service インストーラーは再リリースされました。ユーザーは[更新されたインストーラーをダウンロードする](https://aka.ms/appsvconmasinstaller)必要があります。 更新されたインストーラーでも証明書の検証の問題が発生する場合は、サポートにお問い合わせください。

- 統合システムから Azure Stack ルート証明書を取得するときの問題。

ルート証明書がインストールされていないマシンでスクリプトを実行すると、Get-AzureStackRootCert.ps1 のエラーにより Azure Stack ルート証明書の取得に失敗する問題がありました。 このスクリプトも再リリースされ、この問題は解決しました。[更新されたヘルパー スクリプト](https://aka.ms/appsvconmashelpers)をダウンロードするようにユーザーに依頼してください。 更新されたスクリプトを使用してもルート証明書の取得時に問題が発生する場合は、サポートにお問い合わせください。

### <a name="known-issues-with-the-update-process"></a>更新プロセスに関する既知の問題

- Azure App Service on Azure Stack Update 1 の更新に関する既知の問題はありません。

### <a name="known-issues-post-installation"></a>既知の問題 (インストール後)

- スロットスワップが機能しない

このリリースでは、サイトのスロット スワップが破損しています。 機能を復元するには、次の手順を実行します。

1. App Services コントローラー インスタンスへのリモート デスクトップ接続を**許可**するように ControllersNSG Network Security Group を変更します。 AppService.local を、App Service をデプロイしたリソース グループの名前に置き換えます。

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. Azure Stack Administrator ポータルの [Virtual Machines] で **CN0-VM** を参照し、**[接続] をクリックして**コントローラー インスタンスとのリモート デスクトップ セッションを開きます。 App Service のデプロイ時に指定した資格情報を使用します。
3. **管理者権限で PowerShell** を起動し、次のスクリプトを実行します

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. リモート デスクトップ セッションを終了します。
5. App Services コントローラー インスタンスへのリモート デスクトップ接続を**拒否**するように ControllersNSG Network Security Group を戻します。 AppService.local を、App Service をデプロイしたリソース グループの名前に置き換えます。

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. App Service が既存の仮想ネットワークにデプロイされ、ファイル サーバーがプライベート ネットワークでしか使用できない場合、worker はファイル サーバーに到達することができません。

ファイル サーバーに接続するために既存の仮想ネットワークと内部 IP アドレスへデプロイする場合は、送信セキュリティ規則を追加して、worker サブネットとファイル サーバー間の SMB トラフィックを有効にする必要があります。 これを行うには、管理者ポータルで WorkersNsg に移動し、次のプロパティを持つ送信セキュリティ規則を追加します。

- ソース:任意
- 送信元ポート範囲: *
- 変換先:IP アドレス
- 宛先 IP アドレス範囲:ファイル サーバーの IP の範囲
- 送信先ポート範囲:445
- プロトコル:TCP
- アクション:ALLOW
- 優先順位:700
- 名前:Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack を運用するクラウド管理者に関する既知の問題

[Azure Stack 1802 リリース ノート](azure-stack-update-1802.md)内のドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順

- Azure App Service の概要については、「[Azure Stack 上の App Service の概要](azure-stack-app-service-overview.md)」をご覧ください。
- App Service on Azure Stack のデプロイの準備をする方法の詳細については、「[App Service on Azure Stack を開始する前に](azure-stack-app-service-before-you-get-started.md)」をご覧ください。
