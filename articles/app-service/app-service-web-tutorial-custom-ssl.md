---
title: 既存のカスタム SSL 証明書のバインド - Azure App Service | Microsoft Docs
description: Azure App Service で、Web アプリ、モバイル アプリのバックエンド、または API アプリにカスタム SSL 証明書をバインドする方法について説明します。
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 08/24/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 0a5b8bdbcd5a05574d824e3f57cfc23967278e27
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2019
ms.locfileid: "58487738"
---
# <a name="tutorial-bind-an-existing-custom-ssl-certificate-to-azure-app-service"></a>チュートリアル:既存のカスタム SSL 証明書を Azure App Service にバインドする

Azure App Service では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、信頼された証明機関から購入したカスタム SSL 証明書を [Azure App Service](overview.md) にバインドする方法について説明します。 完了したら、カスタムの DNS ドメインの HTTPS エンドポイントでアプリにアクセスできるようになります。

![カスタム SSL 証明書付きの Web アプリ](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * アプリの価格レベルをアップグレードする
> * カスタム証明書を App Service にバインドする
> * 証明書の更新
> * HTTPS の適用
> * TLS 1.1/1.2 の適用
> * スクリプトを使って TLS 管理を自動化する

> [!NOTE]
> カスタム SSL 証明書を取得する必要がある場合、Azure portal から直接取得してアプリにバインドすることができます。 [App Service 証明書のチュートリアル](web-sites-purchase-ssl-web-site.md)に従ってください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [App Service アプリを作成する](/azure/app-service/)
- [カスタム DNS 名を App Service アプリにマップする](app-service-web-tutorial-custom-domain.md)
- 信頼された証明機関から SSL 証明書を取得する
- SSL 証明書要求の署名に利用した秘密キーを用意する

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>SSL 証明書の必要条件

App Service で証明書を使用するには、証明書が次のすべての要件を満たしている必要があります。

* 信頼された証明機関によって署名されている
* パスワードで保護された PFX ファイルとしてエクスポートされている
* 少なくとも 2048 ビット長の秘密キーが含まれている
* 証明書チェーン内のすべての中間証明書が含まれている

> [!NOTE]
> **楕円曲線暗号 (ECC) 証明書** は、App Service で使用できますが、この記事では説明しません。 ECC 証明書を作成する正確な手順については、証明機関にお問い合わせください。

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>SSL 証明書のバインド

アプリに SSL 証明書をアップロードする準備ができました。

### <a name="merge-intermediate-certificates"></a>中間証明書を結合する

証明機関から証明書チェーンの複数の証明書を提供された場合は、それらの証明書を順番に結合する必要があります。 

これを行うには、受信した各証明書をテキスト エディターで開きます。 

結合した証明書用に _mergedcertificate.crt_ という名前のファイルを作成します。 テキスト エディターで、このファイルに各証明書の内容をコピーします。 証明書の順序は、証明書チェーンの順番に従う必要があります。自分の証明書から始まり、ルート証明書で終わります。 次の例のようになります。

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>PFX への証明書のエクスポート

結合した SSL 証明書を、証明書の要求と一緒に生成された秘密キーと共にエクスポートします。

証明書の要求の生成に OpenSSL を使用した場合、秘密キー ファイルは作成されています。 証明書を PFX にエクスポートするには、次のコマンドを実行します。 プレースホルダーの _&lt;private-key-file>_ と _&lt;merged-certificate-file>_ をそれぞれ、秘密キーのパスとマージされた証明書ファイルに変更します。

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

メッセージが表示されたら、エクスポートのパスワードを定義します。 このパスワードは、後で SSL 証明書を App Service にアップロードするときに使用します。

IIS または _Certreq.exe_ を使用して証明書の要求を生成した場合は、ローカル コンピューターに証明書をインストールした後で[証明書を PFX にエクスポート](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)します。

### <a name="upload-your-ssl-certificate"></a>SSL 証明書のアップロード

SSL 証明書をアップロードするには、アプリの左側のナビゲーションで **[SSL 設定]** をクリックします。

**[証明書のアップロード]** をクリックします。 

**[PFX 証明書ファイル]** で、PFX ファイルを選択します。 **[証明書のパスワード]** で、PFX ファイルのエクスポート時に作成したパスワードを入力します。

**[アップロード]** をクリックします。

![証明書のアップロード](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

App Service による証明書のアップロードが完了すると、**[SSL 設定]** ページにアップロードした証明書が表示されます。

![アップロードされた証明書](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>SSL 証明書のバインド

**[SSL バインディング]** セクションで、**[Add bindings] \(バインドの追加)** をクリックします。

**[SSL バインディングの追加]** ページで、ドロップダウンから保護するドメインの名前と使用する証明書を選択します。

> [!NOTE]
> 証明書をアップロードしたのに **[ホスト名]** ドロップダウンにドメイン名が表示されない場合は、ブラウザのページを最新の情報に更新してみてください。
>
>

**[SSL Type] \(SSL の種類)** で、**[Server Name Indication (SNI)](https://en.wikipedia.org/wiki/Server_Name_Indication)** ベースの SSL を使用するか IP ベースの SSL を使用するかを選択します。

- **SNI ベースの SSL** - SNI ベースの SSL バインドを複数追加することができます。 このオプションでは、複数の SSL 証明書を使用して、同一の IP アドレス上の複数のドメインを保護できます。 最新のブラウザーのほとんど (Inernet Explorer、Chrome、Firefox、Opera など) が SNI をサポートしています (ブラウザーのサポートに関するより包括的な情報については、「[Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)」を参照してください)。
- **IP ベースの SSL** - IP ベースの SSL バインドを 1 つだけ追加することができます。 このオプションでは、SSL 証明書を 1 つだけ使用して、専用のパブリック IP アドレスを保護します。 複数のドメインを保護するには、同じ SSL 証明書を使用してすべてのドメインを保護する必要があります。 これは、SSL バインドの従来のオプションです。

**[Add Binding] \(バインドの追加)** をクリックします。

![SSL 証明書のバインド](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

App Service による証明書のアップロードが完了すると、**[SSL バインド]** セクションにアップロードした証明書が表示されます。

![Web アプリにバインドされた証明書](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>IP SSL の A レコードの再マップ

アプリで IP ベースの SSL を使用していない場合、[カスタム ドメインの HTTPS のテスト](#test)に関するセクションにスキップしてください。

既定では、アプリは、共有のパブリック IP アドレスを使用します。 IP ベースの SSL で証明書をバインドすると、アプリ用の新規の専用 IP アドレスが App Service によって作成されます。

アプリに A レコードをマップした場合は、この新規の専用 IP アドレスでドメイン レジストリを更新します。

アプリの **[カスタム ドメイン]** ページが、新規の専用 IP アドレスで更新されます。 [この IP アドレスをコピー](app-service-web-tutorial-custom-domain.md#info)して、この新しい IP アドレスに [A レコードを再マップ](app-service-web-tutorial-custom-domain.md#map-an-a-record)します。

<a name="test"></a>

## <a name="test-https"></a>HTTPS のテスト

この時点で残っている作業は、HTTPS がカスタム ドメインで機能するかどうかを確認することだけです。 さまざまなブラウザーで `https://<your.custom.domain>` にアクセスし、アプリの要求を処理できることを確認します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> アプリで証明書検証エラーが返された場合は、自己署名証明書を使用している可能性があります。
>
> そうでない場合は、証明書を PFX ファイルにエクスポートするときに中間証明書を含めなかった可能性があります。

<a name="bkmk_enforce"></a>

## <a name="renew-certificates"></a>証明書の更新

バインディングを削除すると、着信 IP アドレスが変化する場合があります。そのバインディングが IP ベースであっても同様です。 IP ベースのバインディングに既に存在する証明書を更新するときには、このことが特に重要となります。 アプリの IP アドレスに変更が生じないようにするには、次の手順に従います。

1. 新しい証明書をアップロードします。
2. 古い証明書を削除せずに、新しい証明書を目的のカスタム ドメインにバインドします。 これは、古い証明書を削除する代わりに、バインディングを置き換える操作となります。
3. 古い証明書を削除します。 

## <a name="enforce-https"></a>HTTPS の適用

既定では、どなたでも引き続き HTTP を使用してアプリにアクセスできます。 すべての HTTP 要求を HTTPS ポートにリダイレクトできます。

アプリ ページで、左側のナビゲーションにある **[SSL 設定]** を選択します。 その後、**[HTTPS のみ]** で、**[On]** を選択します。

![HTTPS の適用](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

操作が完了すると、アプリを指定する HTTP URL のいずれかに移動します。 例: 

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS バージョンを適用する

アプリでは既定で [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 が有効です。これは、[PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) などの業界標準で推奨されている TLS レベルです。 異なる TLS バージョンを適用するには、次の手順を実行します。

アプリ ページで、左側のナビゲーションにある **[SSL 設定]** を選択します。 次に、**[TLS version]\(TLS バージョン\)** で、必要な最低限の TLS バージョンを選択します。 この設定により、受信呼び出しのみが制御されます。 

![TLS 1.1/1.2 の適用](./media/app-service-web-tutorial-custom-ssl/enforce-tls1.2.png)

操作が完了すると、アプリは下位の TLS バージョンでの接続をすべて拒否します。

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/overview) を使用すると、アプリの SSL バインドをスクリプトで自動化することができます。

### <a name="azure-cli"></a>Azure CLI

次のコマンドは、エクスポートした PFX ファイルをアップロードし、拇印を取得します。

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

次のコマンドは、直前のコマンドで取得した拇印を使用して、SNI ベースの SSL バインドを追加します。

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

次のコマンドを実行すると、TLS の最低バージョンが強制的に 1.2 になります。

```bash
az webapp config set \
    --name <app_name> \
    --resource-group <resource_group_name>
    --min-tls-version 1.2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次のコマンドは、エクスポートした PFX ファイルをアップロードし、SNI ベースの SSL バインドを追加します。

```powershell
New-AzWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>パブリック証明書 (省略可能)
アプリがクライアントとしてリモート リソースにアクセスする必要があり、リモート リソースが証明書認証を必要とする場合は、[パブリック証明書](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/)をアプリにアップロードできます。 パブリック証明書は、アプリの SSL バインディングには必要ありません。

アプリにパブリック証明書を読み込んで使用する方法の詳細については、「[Azure App Service のアプリケーション コードに SSL 証明書を使用する](app-service-web-ssl-cert-load.md)」を参照してください。 App Service Environment でも、アプリでパブリック証明書を使用することができます。 LocalMachine の証明書ストアに証明書を格納する必要がある場合は、App Service Environment でアプリを使用する必要があります。 詳細については、[App Service アプリに合わせてパブリック証明書を構成する方法](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer)に関するページを参照してください。

![パブリック証明書のアップロード](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * アプリの価格レベルをアップグレードする
> * カスタム証明書を App Service にバインドする
> * 証明書の更新
> * HTTPS の適用
> * TLS 1.1/1.2 の適用
> * スクリプトを使って TLS 管理を自動化する

次のチュートリアルに進み、Azure Content Delivery Network の使用方法を学習してください。

> [!div class="nextstepaction"]
> [Azure App Service に Content Delivery Network (CDN) を追加する](../cdn/cdn-add-to-web-app.md)

詳細については、「[Use an SSL certificate in your application code in Azure App Service (Azure App Service の SSL 証明書を購入して構成する)](app-service-web-ssl-cert-load.md)」をご覧ください。
