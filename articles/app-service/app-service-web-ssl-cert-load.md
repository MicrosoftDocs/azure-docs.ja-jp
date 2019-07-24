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
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 763aadc50a8760b4265dbfc21e9278f909b68433
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342019"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Azure App Service のアプリケーション コードに SSL 証明書を使用する

このガイドでは、Azure App Service のアプリケーション コードにアップロード、またはインポートされた SSL 証明書を使用する方法について説明します。 ユース ケースの例では、証明書の認証を必要とするアプリが外部サービスにアクセスします。 

コードで SSL 証明書を使用するこの方法では App Service の SSL 機能を使用するため、アプリは **Basic** 層以上にある必要があります。 もう 1 つの方法は、アプリケーション ディレクトリに証明書ファイルを含め、直接読み込むことです (「[代替方法: ファイルとして証明書を読み込む](#file)」を参照してください)。 ただし、この代替方法では、アプリケーション コードや開発者から、証明書の秘密キーを隠すことはできません。 さらに、アプリケーション コードがオープン ソース リポジトリ内にある場合は、リポジトリ内に秘密キーで証明書を保管することはできません。

App Service の SSL 証明書の管理機能を使用すれば、証明書とアプリケーション コードを分離して管理し、機密データを保護できます。

## <a name="prerequisites"></a>前提条件

この手順を実行するには、以下が必要です。

- [App Service アプリを作成する](/azure/app-service/)
- [カスタム DNS 名を Web アプリにマップする](app-service-web-tutorial-custom-domain.md)
- Web アプリに [SSL 証明書をアップロード](app-service-web-tutorial-custom-ssl.md)、または [App Service 証明書をインポート](web-sites-purchase-ssl-web-site.md)する


## <a name="load-your-certificates"></a>証明書の読み込み

App Service にアップロードまたはインポートされた証明書を使用するには、まず、アプリケーション コードからアクセスできるようにします。 これには、`WEBSITE_LOAD_CERTIFICATES` アプリ設定を使用します。

<a href="https://portal.azure.com" target="_blank">Azure ポータル</a>で Web アプリのページを開きます。

左側のナビゲーションで、**[SSL 証明書]** をクリックします。

![アップロードされた証明書](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

この Web アプリにアップロードおよびインポートされた すべての SSL 証明書のサムプリントが表示されます。 使用する証明書のサムプリントをコピーします。

左側のナビゲーションで **[アプリケーション設定]** をクリックします。

`WEBSITE_LOAD_CERTIFICATES` という名前を付けてアプリケーション設定を追加し、その値に証明書のサムプリントを設定します。 複数の証明書にアクセスできるようにするには、サムプリントの値をコンマで区切ります。 すべての証明書にアクセスできるようにするには、値を `*` に設定します。 これにより、証明書を`CurrentUser\My`ストア内に配置していることにご留意ください。

![アプリケーション設定の構成](./media/app-service-web-ssl-cert-load/configure-app-setting.png)

完了したら、**[保存]** をクリックします。

これで、構成された証明書は、コードで使用することができます。

## <a name="use-certificate-in-c-code"></a>C# コードで証明書を使用する

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
## <a name="alternative-load-certificate-as-a-file"></a>代替方法: ファイルとして証明書を読み込む

このセクションでは、アプリケーション ディレクトリ内にある証明書ファイルを読み込む方法について説明しました。 

次の C# の例では、アプリのリポジトリの `certs` ディレクトリから証明書 `mycert.pfx` を読み込みます。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

