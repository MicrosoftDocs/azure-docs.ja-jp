---
title: "カスタム ドメインと SSL を Azure Web アプリに追加する | Microsoft Docs"
description: "会社のブランドを追加して、運用環境に移行する Azure Web アプリを準備する方法について説明します。 カスタム ドメイン名 (バニティ ドメイン) を Web アプリにマップし、カスタム SSL 証明書で保護します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/29/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 535a7dbe24a1badc8539b61d34c09bdeda41ad40
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017

---
# <a name="add-custom-domain-and-ssl-to-an-azure-web-app"></a>カスタム ドメインと SSL を Azure Web アプリに追加する

このチュートリアルでは、カスタム ドメイン名を Azure Web アプリに速やかにマップし、カスタム SSL 証明書で保護する方法について説明します。 

## <a name="before-you-begin"></a>開始する前に

このサンプルを実行する前に、[Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) をローカルにインストールします。

また、各ドメイン プロバイダーの DNS 構成ページへの管理アクセス権が必要です。 たとえば、`www.contoso.com` を追加するには、`contoso.com` の DNS エントリを構成できる必要があります。

さらに、アップロードしてバインドする SSL 証明書の有効な .PFX ファイル_と_パスワードも必要です。 この SSL 証明書は、希望のカスタム ドメイン名を保護するように構成する必要があります。 上記の例では、SSL 証明書で `www.contoso.com` を保護する必要があります。 

## <a name="step-1---create-an-azure-web-app"></a>手順 1 - Azure Web アプリを作成する

### <a name="log-in-to-azure"></a>Azure へのログイン

ターミナル ウィンドウで Azure CLI 2.0 を使用して、Azure で Node.js アプリをホストするために必要なリソースを作成します。  [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。 

```azurecli 
az login 
``` 
   
### <a name="create-a-resource-group"></a>リソース グループの作成   
[az group create](/cli/azure/group#create) でリソース グループを作成します。 Azure リソース グループとは、Web アプリ、データベース、ストレージ アカウントなどの Azure リソースのデプロイと管理に使用する論理コンテナーです。 

```azurecli
az group create --name myResourceGroup --location westeurope 
```

`---location` に使用できる値を確認するには、Azure CLI コマンド `az appservice list-locations` を使用してください。

## <a name="create-an-app-service-plan"></a>App Service プランを作成する

[az appservice plan create](/cli/azure/appservice/plan#create) コマンドで、App Service プランを作成します。 

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

次の例では、**Basic** 価格レベルを使用して、`myAppServicePlan` という名前の App Service プランを作成します。

az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1

App Service プランが作成されると、Azure CLI によって次の例のような情報が表示されます。 

```json 
{ 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "kind": "app", 
    "location": "West Europe", 
    "sku": { 
    "capacity": 1, 
    "family": "B", 
    "name": "B1", 
    "tier": "Basic" 
    }, 
    "status": "Ready", 
    "type": "Microsoft.Web/serverfarms" 
} 
``` 

## <a name="create-a-web-app"></a>Web アプリを作成する

App Service プランを作成したので、`myAppServicePlan` App Service プラン内に Web アプリを作成します。 Web アプリにより、コードをデプロイするためのホスト領域が取得され、デプロイされたアプリケーションを表示するための URL が提供されます。 Web アプリを作成するには、[az appservice web create](/cli/azure/appservice/web#create) コマンドを使用します。 

次のコマンドで、`<app_name>` プレースホルダーを独自の一意のアプリ名に置き換えてください。 この一意の名前は、Web アプリの既定のドメイン名の一部として使用されます。そのため、この名前は Azure のすべてのアプリ間で一意である必要があります。 後で、Web アプリをユーザーに公開する前に、任意のカスタム DNS エントリを Web アプリにマップできます。 

```azurecli
az appservice web create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan 
```

Web アプリが作成されると、Azure CLI によって次の例のような情報が表示されます。 

```json 
{ 
    "clientAffinityEnabled": true, 
    "defaultHostName": "<app_name>.azurewebsites.net", 
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/sites/<app_name>", 
    "isDefaultContainer": null, 
    "kind": "app", 
    "location": "West Europe", 
    "name": "<app_name>", 
    "repositorySiteName": "<app_name>", 
    "reserved": true, 
    "resourceGroup": "myResourceGroup", 
    "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan", 
    "state": "Running", 
    "type": "Microsoft.Web/sites", 
} 
```

この JSON 出力の `defaultHostName` は、Web アプリの既定のドメイン名を示しています。 ブラウザーでこのアドレスに移動します。

```
http://<app_name>.azurewebsites.net 
``` 
 
![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-created.png)  

## <a name="step-2---configure-dns-mapping"></a>手順 2 - DNS マッピングを構成する

この手順では、カスタム ドメインから Web アプリの既定のドメイン名 (`<app_name>.azurewebsites.net`) へのマッピングを追加します。 通常、この手順はドメイン プロバイダーの Web サイトで実行します。 ドメイン レジストラーの Web サイトはレジストラーによって若干異なるため、ご使用のプロバイダーのドキュメントを参照してください。 ここでは、一般的なガイドラインを示します。 

### <a name="navigate-to-to-dns-management-page"></a>DNS 管理ページに移動する

まず、ドメイン レジストラーの Web サイトにログインします。  

次に、DNS レコードの管理ページを見つけます。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。 通常、リンクを見つけるには、アカウント情報を表示し、 **[ドメイン]**などのリンクを探します。

このページを見つけたら、DNS レコードを追加または編集するためのリンクを探します。 このリンクは、**[ゾーン ファイル]**、**[DNS レコード]**、または **[詳細構成]** と表示されている可能性があります。

### <a name="create-a-cname-record"></a>CNAME レコードを作成する

目的のサブドメイン名を Web アプリの既定のドメイン名 (`<app_name>.azurewebsites.net`。`<app_name>` はアプリの一意の名前) にマップする CNAME レコードを追加します。

`www.contoso.com` の場合、`www` ホスト名を `<app_name>.azurewebsites.net` にマップする CNAME を作成します。

## <a name="step-3---configure-the-custom-domain-on-your-web-app"></a>手順 3 - Web アプリでカスタム ドメインを構成する

ドメイン プロバイダーの Web サイトでホスト名のマッピングの構成が完了したら、Web アプリでカスタム ドメインを構成できます。 この構成を追加するには、[az appservice web config hostname add](/cli/azure/appservice/web/config/hostname#add) コマンドを使用します。 

次のコマンドで、`<app_name>` を一意のアプリ名に、<your_custom_domain> を完全修飾カスタム ドメイン名 (例: `www.contoso.com`) にそれぞれ置き換えてください。 

```azurecli
az appservice web config hostname add --webapp <app_name> --resource-group myResourceGroup --name <your_custom_domain>
```

これで、カスタム ドメインが Web アプリに完全にマップされました。 ブラウザーでカスタム ドメイン名に移動します。 次に例を示します。

```
http://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-custom-domain.png)  

## <a name="step-4---bind-a-custom-ssl-certificate-to-your-web-app"></a>手順 4 - カスタム SSL 証明書を Web アプリにバインドする

ブラウザーのアドレス バーに希望のドメイン名を入力して、Azure Web アプリにアクセスできるようになりました。 しかし、`https://<your_custom_domain>` に移動すると、証明書エラーが発生します。 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-cert-error.png)  

このエラーが発生するのは、カスタム ドメイン名に一致する SSL 証明書が Web アプリにまだバインドされていないためです。 ただし、`https://<app_name>.azurewebsites.net` に移動しても、エラーは発生しません。 これは、作成したアプリも含め、すべての Azure App Service アプリが、既定で `*.azurewebsites.net` ワイルドカード ドメインの SSL 証明書によって保護されているためです。 

カスタム ドメイン名で Web アプリにアクセスするには、カスタム ドメインの SSL 証明書を Web アプリにバインドする必要があります。 そのためには、次の手順を実行します。 

### <a name="upload-the-ssl-certificate"></a>SSL 証明書をアップロードする

[az appservice web config ssl upload](/cli/azure/appservice/web/config/ssl#upload) コマンドを使用して、カスタム ドメインのSSL 証明書を Web アプリにアップロードします。

次のコマンドで、`<app_name>` を一意のアプリ名に、`<path_to_ptx_file>` を .PFX ファイルのパスに、`<password>` を証明書のパスワードにそれぞれ置き換えてください。 

```azurecli
az appservice web config ssl upload --name <app_name> --resource-group myResourceGroup --certificate-file <path_to_pfx_file> --certificate-password <password> 
```

証明書がアップロードされると、Azure CLI によって次の例のような情報が表示されます。

```json
{
  "cerBlob": null,
  "expirationDate": "2018-03-29T14:12:57+00:00",
  "friendlyName": "",
  "hostNames": [
    "www.contoso.com"
  ],
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/cert
ificates/9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "issueDate": "2017-03-29T14:12:57+00:00",
  "issuer": "www.contoso.com",
  "keyVaultId": null,
  "keyVaultSecretName": null,
  "keyVaultSecretStatus": "Initialized",
  "kind": null,
  "location": "West Europe",
  "name": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00__West Europe_myResourceGroup",
  "password": null,
 "pfxBlob": null,
  "publicKeyHash": null,
  "resourceGroup": "myResourceGroup",
  "selfLink": null,
  "serverFarmId": null,
  "siteName": null,
  "subjectName": "www.contoso.com",
  "tags": null,
  "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00",
  "type": "Microsoft.Web/certificates",
  "valid": null
}
```

この JSON 出力の `thumbprint` は、アップロードされた証明書の拇印を示しています。 次の手順で使用するため、この値をコピーします。

### <a name="bind-the-uploaded-ssl-certificate-to-the-web-app"></a>アップロードした SSL 証明書を Web アプリにバインドする

Web アプリで希望のカスタム ドメイン名が使用されるようになりました。また、そのカスタム ドメインを保護する SSL 証明書も用意できました。 あとは、アップロードした証明書を Web アプリにバインドするだけです。 そのためには、[az appservice web config ssl bind](/cli/azure/appservice/web/config/ssl#bind) コマンドを使用します。

次のコマンドで、`<app_name>` を一意のアプリ名に、`<thumbprint-from-previous-output>` を前のコマンドで取得した証明書の拇印にそれぞれ置き換えてください。 

az appservice web config ssl bind --name <app_name> --resource-group myResourceGroup --certificate-thumbprint <thumbprint-from-previous-output> --ssl-type SNI

証明書が Web アプリにバインドされると、Azure CLI によって次の例のような情報が表示されます。

{ "availabilityState": "Normal", "clientAffinityEnabled": true, "clientCertEnabled": false, "cloningInfo": null, "containerSize": 0, "dailyMemoryTimeQuota": 0, "defaultHostName": "<app_name>.azurewebsites.net", "enabled": true, "enabledHostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net", "<app_name>.scm.azurewebsites.net" ], "gatewaySiteName": null, "hostNameSslStates": [ { "hostType": "Standard", "name": "<app_name>.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Repository", "name": "<app_name>.scm.azurewebsites.net", "sslState": "Disabled", "thumbprint": null, "toUpdate": null, "virtualIp": null }, { "hostType": "Standard", "name": "www.contoso.com", "sslState": "SniEnabled", "thumbprint": "9FD1D2D06E2293673E2A8D1CA484A092BD016D00", "toUpdate": null, "virtualIp": null } ], "hostNames": [ "www.contoso.com", "<app_name>.azurewebsites.net" ], "hostNamesDisabled": false, "hostingEnvironmentProfile": null, "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Web/site s/<app_name>", "isDefaultContainer": null, "kind": "WebApp", "lastModifiedTimeUtc": "2017-03-29T14:36:18.803333", "location": "West Europe", "maxNumberOfWorkers": null, "microService": "false", "name": "<app_name>", "outboundIpAddresses": "13.94.143.57,13.94.136.57,40.68.199.146,13.94.138.55,13.94.140.1", "premiumAppDeployed": null, "repositorySiteName": "<app_name>", "reserved": false, "resourceGroup": "myResourceGroup", "scmSiteAlsoStopped": false, "serverFarmId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsof t.Web/serverfarms/myAppServicePlan", "siteConfig": null, "slotSwapStatus": null, "state": "Running", "suspendedTill": null, "tags": null, "targetSwapSlot": null, "trafficManagerHostNames": null, "type": "Microsoft.Web/sites", "usageState": "Normal" }

ブラウザーで、カスタム ドメイン名の HTTPS エンドポイントに移動します。 For example:

```
https://www.contoso.com 
``` 

![app-service-web-service-created](media/app-service-web-tutorial-domain-ssl/web-app-ssl-success.png)  

## <a name="more-resources"></a>その他のリソース

[Azure App Service でのカスタム ドメイン名の購入と構成](custom-dns-web-site-buydomains-web-app.md)
[Azure App Service の SSL 証明書を購入して構成する](web-sites-purchase-ssl-web-site.md)

