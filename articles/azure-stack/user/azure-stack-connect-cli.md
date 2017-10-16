---
title: "CLI を使用した Azure Stack への接続 | Microsoft Docs"
description: "クロスプラットフォーム コマンドライン インターフェイス (CLI) を使用して、Azure Stack でリソースを管理およびデプロイする方法"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: f576079c-5384-4c23-b5a4-9ae165d1e3c3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: sngun
ms.openlocfilehash: bd731c32d32063b54d5899db3b3a13a911ca79be
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="install-and-configure-cli-for-use-with-azure-stack"></a>Azure Stack で使用する CLI をインストールして構成する

このドキュメントでは、Azure コマンドライン インターフェイス (CLI) を使用して、Linux および Mac クライアントのプラットフォームから Azure Stack Development Kit のリソースを管理するプロセスについて説明します。 

## <a name="export-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書をエクスポートする

Azure Stack 開発キット環境内で実行されている仮想マシンから CLI を使用する場合、Azure Stack のルート証明書は仮想マシンに既にインストールされているので直接取得できます。 それに対して、開発キット外部のワークステーションから CLI を使用する場合は、Azure Stack の CA ルート証明書を開発キットからエクスポートして、開発ワークステーション (外部の Linux または Mac プラットフォーム) の Python 証明書ストアに追加する必要があります。 

開発キットにサインインし、次のスクリプトを実行して、PEM 形式で Azure Stack ルート証明書をエクスポートします。

```powershell
   $label = "AzureStackSelfSignedRootCert"
   Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
   $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
   if (-not $root)
   {
       Log-Error "Cerficate with subject CN=$label not found"
       return
   }

   Write-Host "Exporting certificate"
   Export-Certificate -Type CERT -FilePath root.cer -Cert $root

   Write-Host "Converting certificate to PEM format"
   certutil -encode root.cer root.pem
```

## <a name="install-cli"></a>CLI のインストール

次に、開発ワークステーションにサインインして CLI をインストールします。 Azure Stack では Azure CLI バージョン 2.0 が必要ですが、こちらは「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](https://docs.microsoft.com/cli/azure/install-azure-cli)」で説明されている手順を実行してインストールできます。 インストールが正常に完了したことを確認するには、ターミナルまたはコマンド プロンプト ウィンドウを開いて次のコマンドを実行します。

```azurecli
az --version
```

お使いのコンピューターにインストールされている Azure CLI と依存するその他のライブラリのバージョンが表示されます。

## <a name="trust-the-azure-stack-ca-root-certificate"></a>Azure Stack の CA ルート証明書を信頼する

Azure Stack の CA ルート証明書を信頼するには、Python の既存の証明書に追加する必要があります。 Azure Stack 環境内で作成された Linux マシンから CLI を実行する場合は、次の bash コマンドを実行します。

```bash
sudo cat /var/lib/waagent/Certificates.pem >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

Azure Stack 環境外のマシンから CLI を実行する場合は、まず [Azure Stack への VPN 接続](azure-stack-connect-azure-stack.md)を設定する必要があります。 先ほどエクスポートした PEM 証明書を開発ワークステーションにコピーし、お使いの開発ワークステーションの OS に応じて次のコマンドを実行します。

### <a name="linux"></a>Linux

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="macos"></a>macOS

```bash
sudo cat PATH_TO_PEM_FILE >> ~/lib/azure-cli/lib/python2.7/site-packages/certifi/cacert.pem
```

### <a name="windows"></a>Windows

```powershell
$pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

$root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$root.Import($pemFile)

Write-Host "Extracting needed information from the cert file"
$md5Hash=(Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
$sha1Hash=(Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
$sha256Hash=(Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

$issuerEntry = [string]::Format("# Issuer: {0}", $root.Issuer)
$subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
$labelEntry = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
$serialEntry = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
$md5Entry = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
$sha1Entry  = [string]::Format("# SHA1 Finterprint: {0}", $sha1Hash)
$sha256Entry = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
$certText = (Get-Content -Path root.pem -Raw).ToString().Replace("`r`n","`n")

$rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
$serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

Write-Host "Adding the certificate content to Python Cert store"
Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

Write-Host "Python Cert store was updated for allowing the azure stack CA root certificate"
```

## <a name="set-up-the-virtual-machine-aliases-endpoint"></a>仮想マシンのエイリアス エンドポイントを設定する

ユーザーが CLI を使用して仮想マシンを作成するためには、まず、クラウド管理者が仮想マシン イメージのエイリアスを含んだパブリックにアクセスできるエンドポイントを設定し、このエンドポイントをクラウドに登録する必要があります。 `az cloud register` コマンドの `endpoint-vm-image-alias-doc` パラメーターは、この目的に使用します。 クラウド管理者は、イメージをイメージ エイリアス エンドポイントに追加する前に、そのイメージを Azure Stack Marketplace にダウンロードする必要があります。
   
たとえば、Azure では、https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json という URI を使用します。 クラウド管理者は Azure Stack Marketplace で使用可能なイメージを使用して、Azure Stack 用に同様のエンドポイントを設定する必要があります。

## <a name="connect-to-azure-stack"></a>Azure Stack への接続

次の手順を使用して Azure Stack に接続します。

1. az cloud register コマンドを実行して、Azure Stack 環境を登録します。
   
   a. **クラウド管理**環境を登録するには、次のコマンドを使用します。

   ```azurecli
   az cloud register \ 
     -n AzureStackAdmin \ 
     --endpoint-resource-manager "https://adminmanagement.local.azurestack.external" \ 
     --suffix-storage-endpoint "local.azurestack.external" \ 
     --suffix-keyvault-dns ".adminvault.local.azurestack.external" \ 
     --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
     --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
   ```

   b. **ユーザー**環境を登録するには、次のコマンドを使用します。

   ```azurecli
   az cloud register \ 
     -n AzureStackUser \ 
     --endpoint-resource-manager "https://management.local.azurestack.external" \ 
     --suffix-storage-endpoint "local.azurestack.external" \ 
     --suffix-keyvault-dns ".vault.local.azurestack.external" \ 
     --endpoint-active-directory-graph-resource-id "https://graph.windows.net/" \
     --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
   ```

2. 次のコマンドを使用して、アクティブな環境を設定します。

   a. **クラウド管理**環境の場合は、次のコマンドを使用します。

   ```azurecli
   az cloud set \
     -n AzureStackAdmin
   ```

   b. **ユーザー**環境の場合は、次のコマンドを使用します。

   ```azurecli
   az cloud set \
     -n AzureStackUser
   ```

3. Azure Stack 固有の API バージョンのプロファイルを使用するようにお使いの環境の構成を更新します。 構成を更新するには、次のコマンドを実行します。

   ```azurecli
   az cloud update \
     --profile 2017-03-09-profile
   ```

4. **az login** コマンドを使って Azure Stack 環境にサインインします。 Azure Stack 環境には、ユーザーまたは[サービス プリンシパル](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects)としてサインインできます。 

   * **ユーザー**としてサインインする場合 ： az login コマンド内で直接ユーザー名とパスワードを指定するか、ブラウザーを使用して認証します。 多要素認証が有効になっているアカウントの場合は、後者を実行する必要があります。

   ```azurecli
   az login \
     -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
     --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
   ```

   > [!NOTE]
   > お使いのユーザー アカウントで多要素認証が有効になっている場合は、-u パラメーターを入力せずに、az login コマンドを使用できます。 コマンドを実行すると、認証で使用する必要がある URL とコードを取得できます。
   
   * **サービス プリンシパル**としてサインインする場合 ： サインインする前に、CLI または [Azure Portal でサービス プリンシパルを作成](azure-stack-create-service-principals.md)してロールに割り当てます。 次のコマンドを使用してログインします。

   ```azurecli
   az login \
     --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
     --service-principal \
     -u <Application Id of the Service Principal> \
     -p <Key generated for the Service Principal>
   ```

## <a name="test-the-connectivity"></a>接続をテストする

必要な設定がすべて整ったら、CLI を使って Azure Stack にリソースを作成してみましょう。 たとえば、アプリケーションのリソース グループを作成して仮想マシンを追加できます。 次のコマンドを使用して、"MyResourceGroup" という名前のリソース グループを作成します。

```azurecli
az group create \
  -n MyResourceGroup -l local
```

リソース グループが正常に作成されると、上記のコマンドによって、新たに作成されたリソースの次のプロパティが出力されます。

![リソース グループ作成の出力](media/azure-stack-connect-cli/image1.png)

## <a name="next-steps"></a>次のステップ

[Azure CLI を使用したテンプレートのデプロイ](azure-stack-deploy-template-command-line.md)

[ユーザー アクセス許可の管理](azure-stack-manage-permissions.md)

