---
title: DMZ で使用する Azure サンプル アプリケーション
titlesuffix: Azure Virtual Network
description: トラフィック フローのシナリオをテストするには、DMZ を作成した後で、この簡単な Web アプリケーションをデプロイします。
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: 6753b3a76ff5d3e0266f238d8e354943dec694a8
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58480460"
---
# <a name="sample-application-for-use-with-dmzs"></a>DMZ で使用するサンプル アプリケーション
[セキュリティ境界のベスト プラクティス ページに戻る][HOME]

以下に示す PowerShell スクリプトを IIS01 および AppVM01 サーバーでローカルに実行すると、バックエンドの AppVM01 サーバーからのコンテンツを持つフロントエンドのサーバー IIS01 からの html ページを表示する単純な Web アプリケーションをインストールしセットアップすることができます。

このアプリケーションでは、多くの DMZ 例で使用する単純なテスト環境を提供すると共に、エンドポイント、NSG、UDR、およびファイアウォールのルールに加えた変更がトラフィック フローに与える影響について説明します。

## <a name="firewall-rule-to-allow-icmp"></a>ICMP を許可するファイアウォール ルール
この単純な PowerShell ステートメントを任意の Windows VM で実行することで、ICMP (Ping) トラフィックを許可することができます。 このファイアウォールの更新により、ping プロトコルが Windows ファイアウォールを通過するのを許可して、テストやトラブルシューティングを容易にすることができます (ほとんどの Linux ディストリビューションでは、ICMP が既定でオンになっています)。

```powershell
# Turn On ICMPv4
New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
    -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
```

以下のスクリプトを使用した場合は、最初のステートメントで、このファイアウォール ルールが追加されます。

## <a name="iis01---web-application-installation-script"></a>IIS01 - Web アプリケーションのインストール スクリプト
このスクリプトでは、次のことが行われます。

1. テストを簡単にするために、ローカル サーバーの Windows ファイアウォールで IMCPv4 (Ping) を開きます。
2. IIS と .NET Framework v4.5 をインストールします。
3. ASP.NET Web ページと Web.config ファイルを作成します。
4. 既定のアプリケーション プールを変更して、ファイルへのアクセスを容易にます。
5. 管理者アカウントとパスワードに [匿名ユーザー] を設定します。

この PowerShell スクリプトは、RDP が IIS01 に転送されるときに、ローカルに実行する必要があります。

```powershell
# IIS Server Post Build Config Script
# Get Admin Account and Password
    Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
    $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
    $thePassword = Read-Host -Prompt "The Admin Password"

# Turn On ICMPv4
    Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Install IIS
    Write-Host "Installing IIS and .NET 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
    add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console

# Create Web App Pages
    Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
    $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
    <%@ Import Namespace="System.IO" %>
    <script language="vb" runat="server">
        Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
            Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
            Dim objStreamReader As StreamReader
            objStreamReader = File.OpenText(FILENAME)
            Dim contents As String = objStreamReader.ReadToEnd()
            lblOutput.Text = contents
            objStreamReader.Close()
            lblTime.Text = Now()
        End Sub
    </script>

    <!DOCTYPE html>
    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
        <title>DMZ Example App</title>
    </head>
    <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
      <form id="frmMain" runat="server">
        <div>
          <h1>Looks like you made it!</h1>
          This is a page from the inside (a web server on a private network),<br />
          and it is making its way to the outside! (If you are viewing this from the internet)<br />
          <br />
          The following sections show:
          <ul style="margin-top: 0px;">
            <li> Local Server Time - Shows if this page is or isn''t cached anywhere</li>
            <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
            <li> Image from the Internet - Doesn''t really show anything, but it made me happy to see this when the app worked</li>
          </ul>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
          <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
            <b>Image File Linked from the Internet</b>:<br />
            <br />
            <img src="https://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
        </div>
      </form>
    </body>
    </html>'

    $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.web>
        <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
        <httpRuntime targetFramework="4.5" />
        <identity impersonate="true" />
        <customErrors mode="Off"/>
      </system.web>
      <system.webServer>
        <defaultDocument>
          <files>
            <add value="Home.aspx" />
          </files>
        </defaultDocument>
      </system.webServer>
    </configuration>'

    $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
    $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii

# Set App Pool to Classic Pipeline to remote file access will work easier
    Write-Host "Updating IIS Settings" -ForegroundColor Cyan
    c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
    c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost

# Make sure the IIS settings take
    Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
    Restart-Service -Name W3SVC

    Write-Host
    Write-Host "Web App Creation Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="appvm01---file-server-installation-script"></a>AppVM01 - ファイル サーバーのインストール スクリプト
このスクリプトでは、この簡単なアプリケーションのためにバックエンドをセットアップします。 このスクリプトでは、次のことが行われます。

1. テストを簡単にするために、ファイアウォールで IMCPv4 (Ping) を開きます。
2. Web サイト用のディレクトリを作成します。
3. この Web ページによってリモートでアクセスされるテキスト ファイルを作成します。
4. ディレクトリおよびファイルに対するアクセス許可を [匿名] に設定して、アクセスを許可します。
5. [IE セキュリティ強化の構成] をオフにして、このサーバーから簡単に参照できるようにします。

> [!IMPORTANT]
> **ベスト プラクティス**: 運用サーバーでは [IE セキュリティ強化の構成] をオフにしないでください。運用サーバーから Web を閲覧することは一般的にお勧めできません。 さらに、匿名アクセス用にファイル アクセスを開くことはお勧めできませんが、ここでは、便宜上、開いています。
>
>

この PowerShell スクリプトは、RDP が AppVM01 に転送されるときに、ローカルに実行する必要があります。 PowerShell は、正常に実行されるように、管理者として実行する必要があります。

```powershell
# AppVM01 Server Post Build Config Script
# PowerShell must be run as Administrator for Net Share commands to work

# Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

# Create Directory
    New-Item "C:\WebShare" -ItemType Directory

# Write out Rand.txt
    $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
    $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii

# Set Permissions on share
    $Acl = Get-Acl "C:\WebShare"
    $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
    $Acl.SetAccessRule($AccessRule)
    Set-Acl "C:\WebShare" $Acl

# Create network share
    Net Share WebShare=C:\WebShare "/grant:Everyone,READ"

# Turn Off IE Enhanced Security Configuration for Admins
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0

    Write-Host
    Write-Host "File Server Set up Successful!" -ForegroundColor Green
    Write-Host
```

## <a name="dns01---dns-server-installation-script"></a>DNS01 - DNS サーバーのインストール スクリプト
このサンプル アプリケーションには、DNS サーバーをセットアップするためのスクリプトは含まれていません。 ファイアウォール ルール、NSG、または UDR のテストに DNS トラフィックを含める必要がある場合は、DNS01 サーバーを手動でセットアップする必要があります。 両方の例で使用するネットワーク構成 xml ファイルおよび Resource Manager テンプレートには、プライマリ DNS サーバーとして DNS01 が含まれ、バックアップ DNS サーバーとしてレベル 3 でホストされるパブリック DNS サーバーが含まれています。 レベル 3 の DNS サーバーの場合は非ローカル トラフィックで使用される実際の DNS サーバーとなります。DNS01 がセットアップされていない場合、ローカル ネットワーク DNS は発生しません。

## <a name="next-steps"></a>次の手順
* IIS サーバーで IIS01 スクリプトを実行します。
* AppVM01 でファイル サーバーのスクリプトを実行します。
* IIS01 でパブリック IP を参照してビルドを検証します。

<!--Link References-->
[HOME]: ../best-practices-network-security.md
