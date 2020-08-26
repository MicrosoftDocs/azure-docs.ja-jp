---
title: Azure Key Vault から証明書をエクスポートする
description: Azure Key Vault から証明書をエクスポートする
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: afab65b22d9487f30da458346bf143a557bec0d8
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588894"
---
# <a name="export-certificate-from-azure-key-vault"></a>Azure Key Vault から証明書をエクスポートする

Azure Key Vault を使用すると、ネットワークのデジタル証明書のプロビジョニング、管理、およびデプロイを簡単に行うことができ、アプリケーションのセキュリティで保護された通信を実現できます。 証明書の一般的な情報については、[Azure Key Vault 証明書](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)に関するページを参照してください

## <a name="about-azure-key-vault-certificate"></a>Azure Key Vault 証明書について

### <a name="composition-of-certificate"></a>証明書の構成
Key Vault 証明書が作成されると、アドレス指定可能なキーとシークレットも同じ名前で作成されます。 Key Vault のキーを使うとキー操作を行うことができ、Key Vault のシークレットを使うとシークレットとして証明書の値を取得できます。 Key Vault 証明書には、公開 x509 証明書メタデータも含まれます。 [詳細については、こちらを参照してください。](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate)

### <a name="exportable-or-non-exportable-keys"></a>エクスポート可能なキーまたはエクスポート不可能なキー
Key Vault 証明書が作成されるとき、秘密キーと共にアドレス指定可能なシークレットから PFX または PEM 形式で取得できます。 証明書の作成に使用されるポリシーでは、キーがエクスポート可能であることが示されている必要があります。 ポリシーでエクスポートできないことが示されている場合、シークレットとして取得されるとき、秘密キーは値に含まれません。

証明書では、RSA または RSA HSM の 2 種類のキーがサポートされています。 エクスポート可能は RSA でのみ許可され、RSA HSM ではサポートされていません。 [詳細については、こちらを参照してください。](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#exportable-or-non-exportable-key)

Azure Key Vault に格納されている証明書は、Azure CLI、PowerShell、またはポータルを使用してエクスポートできます。

> [!NOTE]
> 証明書のパスワードは、キー コンテナーにインポートするときにのみ必要であることに注意してください。 Key Vault には、関連付けられているパスワードは保存されないため、証明書をエクスポートすると、パスワードは空白になります。

## <a name="exporting-certificate-using-cli"></a>CLI を使用して証明書をエクスポートする
次のコマンドを使用すると、Key Vault 証明書の**公開部分**をダウンロードできます。

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```
例とパラメーターの定義については、[こちらを参照](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download)してください



証明書全体 (つまり、**証明書の構成の公開部分と秘密部分の両方**) をダウンロードする場合は、証明書をシークレットとしてダウンロードします。

```azurecli
az keyvault secret download –file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```
パラメーターの定義については、[こちらを参照](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download)してください


## <a name="exporting-certificate-using-powershell"></a>PowerShell を使用して証明書をエクスポートする

このコマンドは、ContosoKV01 という名前のキー コンテナーから、TestCert01 という名前の証明書を取得します。 証明書を pfx ファイルとしてダウンロードするには、次のコマンドを実行します。 これらのコマンドは、SecretId にアクセスし、内容を pfx ファイルとして保存します。

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$kvSecret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $Cert.Name
$kvSecretBytes = [System.Convert]::FromBase64String($kvSecret.SecretValueText)
$certCollection = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2Collection
$certCollection.Import($kvSecretBytes,$null,[System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable)
$password = '******'
$protectedCertificateBytes = $certCollection.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $password)
$pfxPath = [Environment]::GetFolderPath("Desktop") + "\MyCert.pfx"
[System.IO.File]::WriteAllBytes($pfxPath, $protectedCertificateBytes)
```
これにより、証明書チェーン全体が秘密キーと共にエクスポートされ、この証明書がパスワードで保護されます。
```Get-AzKeyVaultCertificate``` コマンドとパラメーターの詳細については、[例 2](https://docs.microsoft.com/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0) を参照してください

## <a name="exporting-certificate-using-portal"></a>ポータルを使用して証明書をエクスポートする

ポータルで、[証明書] ブレードで証明書を作成またはインポートすると、証明書が正常に作成されたことを示す通知が表示されます。 証明書を選択するときに、現在のバージョンをクリックすると、ダウンロードするためのオプションが表示されます。


[CER 形式でダウンロード] または [PFX/PEM 形式でダウンロード] ボタンをクリックすると、証明書をダウンロードできます。


![証明書のダウンロード](../media/certificates/quick-create-portal/current-version-shown.png)


## <a name="exporting-app-service-certificate-from-key-vault"></a>キー コンテナーから App Service 証明書をエクスポートする

Azure App Service 証明書を使用すると、ポータル内で簡単に SSL 証明書を購入し、Azure アプリに割り当てることができます。 これらの証明書は、他の場所で使用するために、ポータルから PFX ファイルとしてエクスポートすることもできます。
インポートした App Service 証明書は、**シークレットの下で見つける**ことができます。

App Service 証明書をエクスポートする手順については、[こちらを参照](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)してください

## <a name="read-more"></a>詳細情報
* [さまざまな証明書のファイルの種類と定義](https://docs.microsoft.com/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)