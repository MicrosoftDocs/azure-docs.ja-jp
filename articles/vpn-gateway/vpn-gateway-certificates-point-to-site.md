---
title: ポイント対サイトの証明書を生成してエクスポートするPowerShell:Azure | Microsoft Docs
description: Windows 10 または Windows Server 2016 で PowerShell を使用して、自己署名ルート証明書の作成、公開キーのエクスポート、クライアント証明書の生成を行います。
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 74639dee6fb548e1c9067cae6fc22f6e3cc872c3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58096233"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>PowerShell を使用したポイント対サイトの証明書の生成とエクスポート

ポイント対サイト接続では、認証に証明書を使用します。 この記事では、Windows 10 または Windows Server 2016 で PowerShell を使用した、自己署名ルート証明書の作成方法とクライアント証明書の生成方法について説明します。 別の証明書の手順が必要な場合は、[証明書 - Linux](vpn-gateway-certificates-point-to-site-linux.md) または[証明書 - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) の記事を参照してください。

この記事の手順は、Windows 10 または Windows Server 2016 を実行するコンピューターで実行する必要があります。 証明書の生成に使用する PowerShell コマンドレットは、オペレーティング システムの一部であり、その他のバージョンの Windows では機能しません。 Windows 10 または Windows Server 2016 コンピューターは、証明書の生成にのみ必要です。 証明書が生成されたら、それらをアップロードしたり、サポートされる任意のクライアント オペレーティング システムにインストールしたりできます。 

Windows 10 または Windows Server 2016 コンピューターを使用できない場合は、[MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) を使用して証明書を生成できます。 いずれかの方法を使用して生成した証明書は、[サポートされている](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)任意のクライアント オペレーティング システムにインストールです。

## <a name="rootcert"></a>1.自己署名ルート証明書の作成

New-SelfSignedCertificate コマンドレットを使用して、自己署名ルート証明書を作成します。 追加のパラメーターについては、「[New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)」を参照してください。

1. Windows 10 または Windows Server 2016 を実行しているコンピューターから、昇格された特権を使用して Windows PowerShell コンソールを開きます。 これらの例は、Azure Cloud Shell の [使ってみる] では動作しません。 これらの例は、ローカルで実行する必要があります。
2. 次の例を使用して、自己署名ルート証明書を作成します。 次の例では、"P2SRootCert" という名前の自己署名ルート証明書が作成され、"Certificates-Current User\Personal\Certificates" に自動的にインストールされます。 *certmgr.msc*、または*ユーザー証明書の管理*を開くと、証明書を表示できます。

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>2.クライアント証明書を生成

ポイント対サイトで VNet に接続するすべてのクライアント コンピューターには、クライアント証明書がインストールされている必要があります。 自己署名ルート証明書からクライアント証明書を生成し、そのクライアント証明書をエクスポートしてインストールします。 クライアント証明書がインストールされていない場合は、認証が失敗します。 

次の手順では、自己署名ルート証明書からクライアント証明書を生成する方法を示しています。 同じルート証明書から複数のクライアント証明書を生成できます。 以下の手順を使用してクライアント証明書を生成すると、証明書の生成に使用したコンピューターにクライアント証明書が自動的にインストールされます。 クライアント証明書を別のクライアント コンピューターにインストールする場合は、その証明書をエクスポートできます。

例では、New-SelfSignedCertificate コマンドレットを使用して、1 年で期限切れになるクライアント証明書を生成しています。 クライアント証明書の別の有効期限値を設定するなど、追加のパラメーター情報については、「[New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate)」を参照してください。

### <a name="example-1"></a>例 1

自己署名ルート証明書を作成した後で PowerShell コンソールを閉じていない場合は、この例を使用します。 この例では、前のセクションから継続して、宣言した "$cert" 変数を使用します。 自己署名ルート証明書の作成後に PowerShell コンソールを閉じた場合または新しい PowerShell コンソール セッションで追加のクライアント証明書を作成している場合は、[例 2](#ex2) の手順を使用してください。

クライアント証明書を生成するには、例を変更して実行します。 次の例を変更せずに実行した場合、クライアント証明書の名前は "P2SChildCert" になります。  子証明書に別の名前を付ける場合は、CN 値を変更します。 この例を実行する際に TextExtension を変更しないでください。 生成したクライアント証明書は、コンピューターの "Certificates - Current User\Personal\Certificates" に自動的にインストールされます。

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>例 2

追加のクライアント証明書を作成している場合、または自己署名ルート証明書の作成に使用したのと同じ PowerShell セッションを使用していない場合は、次の手順を使用してください。

1. コンピューターにインストールされている自己署名ルート証明書を特定します。 次のコマンドレットは、コンピューターにインストールされている証明書の一覧を返します。

   ```powershell
   Get-ChildItem -Path “Cert:\CurrentUser\My”
   ```
2. 返された一覧でサブジェクト名を探し、その横にある拇印をテキスト ファイルにコピーします。 次の例では、2 つの証明書があります。 CN 名は、子証明書の生成元となる自己署名ルート証明書の名前です。 この場合は "P2SRootCert" です。

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. 前の手順の拇印を使用して、ルート証明書の変数を宣言します。 THUMBPRINT を、子証明書の生成元となるルート証明書の拇印に置き換えます。

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   たとえば、前の手順の P2SRootCert の拇印を使用すると、変数は次のようになります。

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. クライアント証明書を生成するには、例を変更して実行します。 次の例を変更せずに実行した場合、クライアント証明書の名前は "P2SChildCert" になります。 子証明書に別の名前を付ける場合は、CN 値を変更します。 この例を実行する際に TextExtension を変更しないでください。 生成したクライアント証明書は、コンピューターの "Certificates - Current User\Personal\Certificates" に自動的にインストールされます。

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>3.ルート証明書の公開キー (.cer) のエクスポート

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>自己署名ルート証明書と証明書を保存するための秘密キーのエクスポート (省略可能)

自己署名ルート証明書は、エクスポートし、バックアップとして安全に保管することができます。 必要に応じて、後から別のコンピューターにインストールして、さらにクライアント証明書を生成することもできます。 自己署名ルート証明書を .pfx としてエクスポートするには、ルート証明書を選択し、「[クライアント証明書をエクスポートする](#clientexport)」と同じ手順を実行します。

## <a name="clientexport"></a>4.クライアント証明書のエクスポート

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5.エクスポートしたクライアント証明書のインストール

P2S 接続で VNet に接続するすべてのクライアントは、ローカルにクライアント証明書がインストールされている必要があります。

クライアント証明書をインストールするには、[ポイント対サイト接続用のクライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)に関するページを参照してください。

## <a name="install"></a>6.P2S 構成手順の続行

引き続きポイント対サイト構成を使用します。

* **Resource Manager** デプロイメント モデルの手順については、[ネイティブ Azure 証明書認証を使用した P2S の構成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関するページを参照してください。 
* **クラシック** デプロイメント モデルの手順については、[VNet へのポイント対サイト VPN 接続の構成 (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md) に関する記事を参照してください。
* P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。
