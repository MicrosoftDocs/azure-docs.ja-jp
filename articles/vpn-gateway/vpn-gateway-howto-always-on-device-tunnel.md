---
title: VPN Gateway に Always On VPN トンネルを構成する
description: VPN Gateway に Always On VPN トンネルを構成する手順
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: cherylmc
ms.openlocfilehash: 98d8c2f6870be16f3eb92219fc3d02f988390a41
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295465"
---
# <a name="configure-an-always-on-vpn-device-tunnel"></a>Always On VPN デバイス トンネルの構成

Windows 10 仮想プライベート ネットワーク (VPN) クライアントの新しい機能の 1 つに、VPN 接続を維持する機能があります。 Always On は、アクティブな VPN プロファイルを自動的に接続して、トリガー (つまり、ユーザーのサインイン、ネットワーク状態の変更、またはアクティブなデバイス画面) に基づいて接続を維持できるようにする Windows 10 の機能です。

Windows 10 Always On で Azure 仮想ネットワーク ゲートウェイを使用すると、Azure への永続的なユーザー トンネルとデバイス トンネルを確立することができます。 この記事は、Always ON VPN デバイス トンネルの構成に役立ちます。

Always On VPN 接続には、次の 2 種類のトンネルが含まれます。

* **デバイス トンネル**: ユーザーがデバイスにサインインする前に、指定した VPN サーバーに接続します。 デバイス トンネルは、ログイン前の接続シナリオとデバイス管理の目的で使用されます。

* **ユーザー トンネル**: ユーザーがデバイスにサインインした後にのみ接続します。 ユーザー トンネルを使用すると、ユーザーが VPN サーバーを通じて組織のリソースにアクセスすることができます。

デバイス トンネルとユーザー トンネルはどちらも、自身の VPN プロファイルを使用して独立して動作します。 これらは同時に接続でき、必要に応じてさまざまな認証方法と他の VPN の構成設定を使用できます。

## <a name="1-configure-the-gateway"></a>1.ゲートウェイの構成

この[ポイント対サイトの記事](vpn-gateway-howto-point-to-site-resource-manager-portal.md)を使用して、IKEv2 と証明書ベースの認証を使用するように VPN ゲートウェイを構成します。

## <a name="2-configure-the-user-tunnel"></a>2.ユーザー トンネルの構成

1. この[ポイント対サイト VPN クライアントの記事](point-to-site-how-to-vpn-client-install-azure-cert.md)に示されているように、Windows 10 クライアントにクライアント証明書をインストールします。 証明書は、現在のユーザー ストアにある必要があります。
2. [この手順](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)に従って、PowerShell、SCCM、または Intune を使用して Always On VPN クライアントを構成します。

## <a name="3-configure-the-device-tunnel"></a>手順 3.デバイス トンネルの構成

デバイス トンネルを正常に確立するには、次の要件を満たす必要があります。

* デバイスが、Windows 10 Enterprise または Education バージョン 1709 以降を実行しているドメイン参加済みのコンピューターであること。
* トンネルが、Windows の組み込み VPN ソリューションに対してのみ構成可能で、IKEv2 とコンピューター証明書認証を使用して確立されていること。 
* 構成できるデバイス トンネルは、デバイスごとに 1 つのみ。

1. この[ポイント対サイト VPN クライアントの記事](point-to-site-how-to-vpn-client-install-azure-cert.md)に示されているように、Windows 10 クライアントにクライアント証明書をインストールします。 この証明書は、ローカル コンピューターのストアに格納されている必要があります。
1. [この手順](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/vpn-device-tunnel-config#vpn-device-tunnel-configuration)を使用して、VPN プロファイルを作成し、ローカル システム アカウントのコンテキストでデバイス トンネルを構成します。

### <a name="configuration-example-for-device-tunnel"></a>デバイス トンネルの構成例

仮想ネットワーク ゲートウェイを構成し、Windows 10 クライアントのローカル コンピューター ストアにクライアント証明書をインストールしたら、次の例を使用してクライアント デバイス トンネルを構成します。

1. 次のテキストをコピーし、***devicecert.ps1*** として保存します。

   ```
   Param(
   [string]$xmlFilePath,
   [string]$ProfileName
   )

   $a = Test-Path $xmlFilePath
   echo $a

   $ProfileXML = Get-Content $xmlFilePath

   echo $XML

   $ProfileNameEscaped = $ProfileName -replace ' ', '%20'

   $Version = 201606090004

   $ProfileXML = $ProfileXML -replace '<', '&lt;'
   $ProfileXML = $ProfileXML -replace '>', '&gt;'
   $ProfileXML = $ProfileXML -replace '"', '&quot;'

   $nodeCSPURI = './Vendor/MSFT/VPNv2'
   $namespaceName = "root\cimv2\mdm\dmmap"
   $className = "MDM_VPNv2_01"

   $session = New-CimSession

   try
   {
   $newInstance = New-Object Microsoft.Management.Infrastructure.CimInstance $className, $namespaceName
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ParentID", "$nodeCSPURI", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("InstanceID", "$ProfileNameEscaped", 'String', 'Key')
   $newInstance.CimInstanceProperties.Add($property)
   $property = [Microsoft.Management.Infrastructure.CimProperty]::Create("ProfileXML", "$ProfileXML", 'String', 'Property')
   $newInstance.CimInstanceProperties.Add($property)

   $session.CreateInstance($namespaceName, $newInstance)
   $Message = "Created $ProfileName profile."
   Write-Host "$Message"
   }
   catch [Exception]
   {
   $Message = "Unable to create $ProfileName profile: $_"
   Write-Host "$Message"
   exit
   }
   $Message = "Complete."
   Write-Host "$Message"
   ```
1. 次のテキストをコピーし、***VPNProfile.xml*** として **devicecert.ps1** と同じフォルダーに保存します。 お使いの環境に合わせて次のテキストを編集します。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
   <VPNProfile>  
     <NativeProfile>  
   <Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>  
   <NativeProtocolType>IKEv2</NativeProtocolType>  
   <Authentication>  
     <MachineMethod>Certificate</MachineMethod>  
   </Authentication>  
   <RoutingPolicyType>SplitTunnel</RoutingPolicyType>  
    <!-- disable the addition of a class based route for the assigned IP address on the VPN interface -->
   <DisableClassBasedDefaultRoute>true</DisableClassBasedDefaultRoute>  
     </NativeProfile> 
     <!-- use host routes(/32) to prevent routing conflicts -->  
     <Route>  
   <Address>192.168.3.5</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
     <Route>  
   <Address>192.168.3.4</Address>  
   <PrefixSize>32</PrefixSize>  
     </Route>  
   <!-- need to specify always on = true --> 
     <AlwaysOn>true</AlwaysOn> 
   <!-- new node to specify that this is a device tunnel -->  
    <DeviceTunnel>true</DeviceTunnel>
   <!--new node to register client IP address in DNS to enable manage out -->
   <RegisterDNS>true</RegisterDNS>
   </VPNProfile>
   ```
1. [Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psexec) から **PsExec** をダウンロードして、**C:\PSTools** にファイルを抽出します。
1. 管理者コマンド プロンプトから、次を実行して PowerShell を起動します。

   ```
   C:\PsTools\PsExec.exe Powershell for 32-bit Windows
   C:\PsTools\PsExec64.exe Powershell for 64-bit Windows
   ```

   ![powershell](./media/vpn-gateway-howto-always-on-device-tunnel/powershell.png)
1. PowerShell で、**devicecert.ps1** と **VPNProfile.xml** が配置されているフォルダーに切り替えて、次のコマンドを実行します。

   ```powershell
   C:\> .\devicecert.ps1 .\VPNProfile.xml MachineCertTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-device-tunnel/machinecerttest.png)
1. **rasphone** を実行します。

   ![rasphone](./media/vpn-gateway-howto-always-on-device-tunnel/rasphone.png)
1. **MachineCertTest** エントリを探して、 **[接続]** をクリックします。

   ![接続](./media/vpn-gateway-howto-always-on-device-tunnel/connect.png)
1. 接続に成功したら、コンピューターを再起動します。 トンネルが自動的に接続されます。

## <a name="cleanup"></a>クリーンアップ

プロファイルを削除するには、次のコマンドを実行します。

![クリーンアップ](./media/vpn-gateway-howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>次の手順

トラブルシューティングについては、[Azure ポイント対サイト接続の問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)のページを参照してください。
