---
title: VPN Gateway に Always On VPN トンネルを構成する
description: VPN Gateway に Always On ユーザー VPN トンネルを構成する手順
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: cherylmc
ms.openlocfilehash: dc0abf12c60f845fde0d16bd874a1436aef3b7ab
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846375"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Always On VPN ユーザー トンネルの構成

Windows 10 仮想プライベート ネットワーク (VPN) クライアントの新しい機能の 1 つに、VPN 接続を維持する機能があります。 Always On は、アクティブな VPN プロファイルを自動的に接続して、トリガー (つまり、ユーザーのサインイン、ネットワーク状態の変更、またはアクティブなデバイス画面) に基づいて接続を維持できるようにする Windows 10 の機能です。

Windows 10 Always On で Azure 仮想ネットワーク ゲートウェイを使用すると、Azure への永続的なユーザー トンネルとデバイス トンネルを確立することができます。 この記事は、Always On VPN ユーザー トンネルの構成に役立ちます。

Always On VPN 接続には、次の 2 種類のトンネルが含まれます。

* **デバイス トンネル**: ユーザーがデバイスにサインインする前に、指定した VPN サーバーに接続します。 デバイス トンネルは、ログイン前の接続シナリオとデバイス管理の目的で使用されます。

* **ユーザー トンネル**: ユーザーがデバイスにサインインした後にのみ接続します。 ユーザー トンネルを使用すると、ユーザーが VPN サーバーを通じて組織のリソースにアクセスすることができます。

デバイス トンネルとユーザー トンネルはどちらも、自身の VPN プロファイルを使用して独立して動作します。 これらは同時に接続でき、必要に応じてさまざまな認証方法と他の VPN の構成設定を使用できます。

## <a name="1-configure-the-gateway"></a>1.ゲートウェイの構成

この[ポイント対サイトの記事](vpn-gateway-howto-point-to-site-resource-manager-portal.md)を使用して、IKEv2 と証明書ベースの認証を使用するように VPN ゲートウェイを構成します。

## <a name="2-configure-the-user-tunnel"></a>2.ユーザー トンネルの構成

1. この[ポイント対サイト VPN クライアントの記事](point-to-site-how-to-vpn-client-install-azure-cert.md)に示されているように、Windows 10 クライアントにクライアント証明書をインストールします。 証明書は、現在のユーザー ストアにある必要があります。
2. [この手順](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/deploy/vpn-deploy-client-vpn-connections)に従って、PowerShell、SCCM、または Intune を使用して Always On VPN クライアントを構成します。

### <a name="configuration-example-for-user-tunnel"></a>ユーザー トンネルの構成例

仮想ネットワーク ゲートウェイを構成し、Windows 10 クライアントのローカル コンピューター ストアにクライアント証明書をインストールしたら、次の例を使用してクライアント デバイス トンネルを構成します。

1. 次のテキストをコピーし、***usercert.ps1*** として保存します。

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
1. 次のテキストをコピーし、***VPNProfile.xml*** として **usercert.ps1** と同じフォルダーに保存します。 お使いの環境に合わせて次のテキストを編集します。

   * `<Servers>azuregateway-1234-56-78dc.cloudapp.net</Servers>`
   * `<Address>192.168.3.5</Address>`
   * `<Address>192.168.3.4</Address>`

   ```
    <VPNProfile>  
      <NativeProfile>  
    <Servers>azuregateway-b115055e-0882-49bc-a9b9-7de45cba12c0-8e6946892333.vpn.azure.com</Servers>  
    <NativeProtocolType>IKEv2</NativeProtocolType>  
    <Authentication>  
    <UserMethod>Eap</UserMethod>
    <Eap>
    <Configuration>
    <EapHostConfig xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><EapMethod><Type xmlns="http://www.microsoft.com/provisioning/EapCommon">13</Type><VendorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorId><VendorType xmlns="http://www.microsoft.com/provisioning/EapCommon">0</VendorType><AuthorId xmlns="http://www.microsoft.com/provisioning/EapCommon">0</AuthorId></EapMethod><Config xmlns="http://www.microsoft.com/provisioning/EapHostConfig"><Eap xmlns="http://www.microsoft.com/provisioning/BaseEapConnectionPropertiesV1"><Type>13</Type><EapType xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV1"><CredentialsSource><CertificateStore><SimpleCertSelection>true</SimpleCertSelection></CertificateStore></CredentialsSource><ServerValidation><DisableUserPromptForServerValidation>false</DisableUserPromptForServerValidation><ServerNames></ServerNames></ServerValidation><DifferentUsername>false</DifferentUsername><PerformServerValidation xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</PerformServerValidation><AcceptServerName xmlns="http://www.microsoft.com/provisioning/EapTlsConnectionPropertiesV2">false</AcceptServerName></EapType></Eap></Config></EapHostConfig>
    </Configuration>
    </Eap>
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
    <!-- traffic filters for the routes specified above so that only this traffic can go over the device tunnel --> 
      <TrafficFilter>  
    <RemoteAddressRanges>192.168.3.4, 192.168.3.5</RemoteAddressRanges>  
      </TrafficFilter>
    <!-- need to specify always on = true --> 
    <AlwaysOn>true</AlwaysOn>
    <RememberCredentials>true</RememberCredentials>
    <!--new node to register client IP address in DNS to enable manage out -->
    <RegisterDNS>true</RegisterDNS>
    </VPNProfile>
   ```
1. PowerShell を管理者として実行します。

1. PowerShell で、**usercert.ps1** と **VPNProfile.xml** が配置されているフォルダーに切り替えて、次のコマンドを実行します。

   ```powershell
   C:\> .\usercert.ps1 .\VPNProfile.xml UserTest
   ```
   
   ![MachineCertTest](./media/vpn-gateway-howto-always-on-user-tunnel/p2s2.jpg)
1. [VPN の設定] を確認します。

1. **UserTest** エントリを探して、 **[接続]** をクリックします。

1. 接続に成功した場合は、Always On ユーザー トンネルが正常に構成されています。

## <a name="cleanup"></a>クリーンアップ

プロファイルを削除するには、次のコマンドを実行します。

1. 接続を切断し、[自動的に接続する] をオフにします

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

![クリーンアップ](./media/vpn-gateway-howto-always-on-user-tunnel/p2s4..jpg)

## <a name="next-steps"></a>次の手順

トラブルシューティングについては、[Azure ポイント対サイト接続の問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)のページを参照してください。
