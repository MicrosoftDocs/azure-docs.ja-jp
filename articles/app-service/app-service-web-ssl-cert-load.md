---
title: アプリケーション コードでクライアント SSL 証明書を使用する - Azure App Service | Microsoft Docs
description: クライアント証明書を使用して、そのクライアント証明書を必要とするリモート リソースに接続する方法について説明します。
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/29/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 6820daf34e63fd48e83c645e7509a3256bc8435b
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066987"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Azure App Service のアプリケーション コードに SSL 証明書を使用する

この攻略ガイドでは、アプリケーション コードで公開またはプライベートの証明書を使用する方法について説明します。 ユース ケースの例では、証明書の認証を必要とするアプリが外部サービスにアクセスします。

コードで証明書を使用するこの方法では、App Service の SSL 機能を利用します。そのため、お客様のアプリは **Basic** レベル以上でなければなりません。 または、[アプリ リポジトリに証明書ファイルを含める](#load-certificate-from-file)ことができます。しかし、これはプライベート証明書では推奨されません。

App Service の SSL 証明書の管理機能を使用すれば、証明書とアプリケーション コードを分離して管理し、機密データを保護できます。

## <a name="upload-a-private-certificate"></a>プライベート証明書のアップロード

プライベート証明書をアップロードする前に、[すべての要件を満たす](app-service-web-tutorial-custom-ssl.md#prepare-a-private-certificate)ようにしてください。ただし、サーバー認証用に構成する必要はありません。

アップロードの準備ができたら、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを実行します。

```azurecli-interactive
az webapp config ssl upload --name <app-name> --resource-group <resource-group-name> --certificate-file <path-to-PFX-file> --certificate-password <PFX-password> --query thumbprint
```

証明書の拇印をコピーし、「[証明書をアクセス可能にする](#make-the-certificate-accessible)」を参照してください。

## <a name="upload-a-public-certificate"></a>パブリック証明書のアップロード

公開証明書は、 *.cer* 形式でサポートされています。 公開証明書をアップロードするには、<a href="https://portal.azure.com" target="_blank">Azure portal</a> で自分のアプリに移動します。

アプリの左側のナビゲーションで、 **[SSL 設定]**  >  **[公開証明書 (.cer)]**  >  **[公開証明書のアップロード]** の順にクリックします。

**[名前]** に、証明書の名前を入力します。 **[CER 証明書ファイル]** で、目的の CER ファイルを選択します。

**[アップロード]** をクリックします。

![パブリック証明書をアップロードする](./media/app-service-web-ssl-cert-load/private-cert-upload.png)

証明書がアップロードされたら、証明書の拇印をコピーし、「[証明書をアクセス可能にする](#make-the-certificate-accessible)」を参照してください。

## <a name="import-an-app-service-certificate"></a>App Service 証明書をインポートする

「[Azure App Service の SSL 証明書を購入して構成する](web-sites-purchase-ssl-web-site.md)」を参照してください。

証明書がインポートされたら、証明書の拇印をコピーし、「[証明書をアクセス可能にする](#make-the-certificate-accessible)」を参照してください。

## <a name="make-the-certificate-accessible"></a>証明書をアクセス可能にする

アップロードまたはインポートした証明書をアプリ コードで使用するには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを実行し、`WEBSITE_LOAD_CERTIFICATES` アプリ設定を使用して目的の拇印をアクセス可能にします。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

自分の証明書をすべてアクセス可能にするには、値を `*` に設定します。

> [!NOTE]
> この設定により、ほとんどの価格レベルでは指定された証明書が [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) ストアに配置されます。ただし、**Isolated** レベルの場合 (つまり、アプリが [App Service Environment](environment/intro.md) で実行される場合) は、証明書が [Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) ストアに配置されます。
>

![アプリケーション設定の構成](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

完了したら、 **[保存]** をクリックします。

これで、構成された証明書をコードで使用する準備が整いました。

## <a name="load-the-certificate-in-code"></a>証明書をコードに読み込む

証明書にアクセスできるようになったら、証明書のサムプリントを使用して C# コードからアクセスします。 次のコードでは、サムプリントが `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` の証明書を読み込みます。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>ファイルから証明書を読み込む

自分のアプリケーション ディレクトリから証明書ファイルを読み込む必要がある場合、[Git](deploy-local-git.md) などは使用せず、[FTPS](deploy-ftp.md) を使用してアップロードすることをお勧めします。 プライベート証明書などの機密データは、ソース管理から分離しておく必要があります。

.NET コードに直接ファイルを読み込む場合でも、ライブラリでは現在のユーザー プロファイルが読み込まれるかどうかを確認します。 現在のユーザー プロファイルを読み込むには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを使用して、`WEBSITE_LOAD_USER_PROFILE` アプリ設定を構成します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

この設定が構成されると、次の C# の例では、アプリのリポジトリの `certs` ディレクトリから `mycert.pfx` という証明書が読み込まれます。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```
