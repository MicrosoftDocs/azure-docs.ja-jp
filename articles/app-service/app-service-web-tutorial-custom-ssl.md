---
title: "既存のカスタム SSL 証明書の Azure Web Apps へのバインド | Microsoft Docs"
description: "Azure App Service で、Web アプリ、モバイル アプリのバックエンド、または API アプリにカスタム SSL 証明書をバインドする方法について説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/23/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 1923a145d258e558811b610be9a6bb8ea0e4c933
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>既存のカスタム SSL 証明書の Azure Web Apps へのバインド

Azure Web Apps では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 このチュートリアルでは、信頼された証明機関から購入したカスタム SSL 証明書を [Azure Web Apps](app-service-web-overview.md) にバインドする方法について説明します。 完了したら、カスタムの DNS ドメインの HTTPS エンドポイントで、Web アプリにアクセスできるようになります。

![カスタム SSL 証明書付きの Web アプリ](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリの価格レベルをアップグレードする
> * カスタム SSL 証明書を App Service にバインドする
> * アプリに HTTPS を適用する
> * スクリプトで SSL 証明書のバインドを自動化する

> [!NOTE]
> カスタム SSL 証明書を取得する必要がある場合、Azure Portal から直接取得して Web アプリにバインドすることができます。 [App Service 証明書のチュートリアル](web-sites-purchase-ssl-web-site.md)に従ってください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

- [App Service アプリを作成する](/azure/app-service/)
- [カスタム DNS 名を Web アプリにマップする](app-service-web-tutorial-custom-domain.md)
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

## <a name="prepare-your-web-app"></a>Web アプリの準備

カスタム SSL 証明書を Web アプリにバインドするには、[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が **Basic**、**Standard** または **Premium** のいずれかのレベルである必要があります。 この手順では、Web アプリが、サポートされている価格レベルであることを確認します。

### <a name="log-in-to-azure"></a>Azure へのログイン

[Azure Portal](https://portal.azure.com)を開きます。

### <a name="navigate-to-your-web-app"></a>Web アプリに移動する

左側のメニューで **[App Services]** をクリックした後、Web アプリの名前をクリックします。

![Web アプリの選択](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Web アプリの管理ページが表示されます。  

### <a name="check-the-pricing-tier"></a>価格レベルの確認

Web アプリ ページの左側のナビゲーションで **[設定]** セクションまでスクロールし、**[スケール アップ (App Service のプラン)]** を選択します。

![スケール アップ メニュー](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Web アプリが **Free** レベルまたは **Shared** レベルに含まれていないことを確認します。 Web アプリの現在のレベルは、ダーク ブルーのボックスで強調表示されます。

![価格レベルの確認](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

カスタム SSL は、**Free** レベルまたは **Shared** レベルではサポートされていません。 スケール アップする必要がある場合は、次のセクションの手順に従います。 それ以外の場合は、**[価格レベルの選択]** ページを閉じて、[SSL 証明書のアップロードとバインド](#upload)に関するセクションにスキップします。

### <a name="scale-up-your-app-service-plan"></a>App Service プランのスケール アップ

**Basic**、**Standard**、**Premium** のいずれかのレベルを選択します。

**[選択]**をクリックします。

![価格レベルの選択](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

次の通知が表示されたら、スケール操作は完了です。

![スケール アップの通知](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>SSL 証明書のバインド

Web アプリに、SSL 証明書をアップロードする準備ができました。

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

```
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

メッセージが表示されたら、エクスポートのパスワードを定義します。 このパスワードは、後で SSL 証明書を App Service にアップロードするときに使用します。

IIS または _Certreq.exe_ を使用して証明書の要求を生成した場合は、ローカル コンピューターに証明書をインストールした後で[証明書を PFX にエクスポート](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)します。

### <a name="upload-your-ssl-certificate"></a>SSL 証明書のアップロード

SSL 証明書をアップロードするには、Web アプリの左側のナビゲーションで **[SSL 証明書]** をクリックします。

**[証明書のアップロード]** をクリックします。

**[PFX 証明書ファイル]** で、PFX ファイルを選択します。 **[証明書のパスワード]** で、PFX ファイルのエクスポート時に作成したパスワードを入力します。

**[アップロード]**をクリックします。

![証明書のアップロード](./media/app-service-web-tutorial-custom-ssl/upload-certificate.png)

App Service による証明書のアップロードが完了すると、**[SSL 証明書]** ページにアップロードした証明書が表示されます。

![アップロードされた証明書](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>SSL 証明書のバインド

**[SSL バインディング]** セクションで、**[Add bindings] \(バインドの追加)** をクリックします。

**[SSL バインディングの追加]** ページで、ドロップダウンから保護するドメインの名前と使用する証明書を選択します。

> [!NOTE]
> 証明書をアップロードしたのに **[ホスト名]** ドロップダウンにドメイン名が表示されない場合は、ブラウザのページを最新の情報に更新してみてください。
>
>

**[SSL Type] \(SSL の種類)** で、**[Server Name Indication (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ベースの SSL を使用するか IP ベースの SSL を使用するかを選択します。

- **SNI ベースの SSL** - SNI ベースの SSL バインドを複数追加することができます。 このオプションでは、複数の SSL 証明書を使用して、同一の IP アドレス上の複数のドメインを保護できます。 最新のブラウザーのほとんど (Inernet Explorer、Chrome、Firefox、Opera など) が SNI をサポートしています (ブラウザーのサポートに関するより包括的な情報については、「[Server Name Indication](http://wikipedia.org/wiki/Server_Name_Indication)」を参照してください)。
- **IP ベースの SSL** - IP ベースの SSL バインドを 1 つだけ追加することができます。 このオプションでは、SSL 証明書を 1 つだけ使用して、専用のパブリック IP アドレスを保護します。 複数のドメインを保護するには、同じ SSL 証明書を使用してすべてのドメインを保護する必要があります。 これは、SSL バインドの従来のオプションです。

**[Add Binding] \(バインドの追加)** をクリックします。

![SSL 証明書のバインド](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

App Service による証明書のアップロードが完了すると、**[SSL バインド]** セクションにアップロードした証明書が表示されます。

![Web アプリにバインドされた証明書](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>IP SSL の A レコードの再マップ

Web アプリで IP ベースの SSL を使用していない場合、[カスタム ドメインの HTTPS のテスト](#test)に関するセクションにスキップしてください。

既定では、Web アプリは、共有のパブリック IP アドレスを使用します。 IP ベースの SSL で証明書をバインドすると、Web アプリ用の新規の専用 IP アドレスが App Service によって作成されます。

Web アプリに A レコードをマップした場合は、この新規の専用 IP アドレスでドメイン レジストリを更新します。

Web アプリの **[カスタム ドメイン]** ページが、新規の専用 IP アドレスで更新されます。 [この IP アドレスをコピー](app-service-web-tutorial-custom-domain.md#info)して、この新しい IP アドレスに [A レコードを再マップ](app-service-web-tutorial-custom-domain.md#map-an-a-record)します。

<a name="test"></a>

## <a name="test-https"></a>HTTPS のテスト

この時点で残っている作業は、HTTPS がカスタム ドメインで機能するかどうかを確認することだけです。 さまざまなブラウザーで `https://<your.custom.domain>` にアクセスし、Web アプリの要求を処理できることを確認します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Web アプリで証明書検証エラーが返された場合は、自己署名証明書を使用している可能性があります。
>
> そうでない場合は、証明書を PFX ファイルにエクスポートするときに中間証明書を含めなかった可能性があります。

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>HTTPS の適用

App Service *では* HTTPS の使用が強制されないため、どなたでも引き続き HTTP を使用してアプリにアクセスできます。 Web アプリに対して HTTPS の使用を強制するには、Web アプリの _web.config_ ファイルで書き換え規則を定義します。 App Service は、Web アプリの言語フレームワークに関係なく、このファイルを使用します。

> [!NOTE]
> 言語に固有の要求のリダイレクトがあります。 ASP.NET MVC では、_web.config_ 内の書き換え規則の代わりに [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) フィルターを使用できます。

.NET 開発者の方は、比較的このファイルに馴染みがあると思います。 このファイルは、ソリューションのルートにあります。

また、PHP、Node.js、Python または Java で開発する場合は、お客様の代わりに App Service でこのファイルを生成することもできます。

「[FTP/S を使用した Azure App Service へのアプリのデプロイ](app-service-deploy-ftp.md)」の指示に従って、Web アプリの FTP エンドポイントに接続します。

このファイルは、_/home/site/wwwroot_ にあります。 ない場合は、次の XML を含む _web.config_ ファイルをこのフォルダーに作成します。

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule ELEMENT FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule ELEMENT FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

既存の _web.config_ ファイルの場合は、`<rule>` 要素全体を _web.config_ の `configuration/system.webServer/rewrite/rules` 要素にコピーします。 他の `<rule>` 要素が _web.config_ 内にある場合は、コピーした `<rule>` 要素を他の `<rule>` タグの前に配置します。

この規則は、ユーザーが HTTP を Web アプリに要求したときに、HTTPS プロトコルに HTTP 301 (永続的リダイレクト) を返します。 たとえば、`http://contoso.com` から `https://contoso.com` にリダイレクトします。

IIS URL 書き換えモジュールの詳細については、 [URL 書き換え](http://www.iis.net/downloads/microsoft/url-rewrite) のドキュメントを参照してください。

## <a name="enforce-https-for-web-apps-on-linux"></a>Web Apps on Linux に対する HTTPS の適用

App Service on Linux *では* HTTPS の使用が強制されないため、どなたでも引き続き HTTP を使用して Web アプリにアクセスできます。 Web アプリに対して HTTPS の使用を強制するには、Web アプリの _.htaccess_ ファイルで書き換え規則を定義します。 

「[FTP/S を使用した Azure App Service へのアプリのデプロイ](app-service-deploy-ftp.md)」の指示に従って、Web アプリの FTP エンドポイントに接続します。

_/home/site/wwwroot_ で、次のコードを使用して _.htaccess_ ファイルを作成します。

```
RewriteEngine On
RewriteCond %{HTTP:X-ARR-SSL} ^$
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

この規則は、ユーザーが HTTP を Web アプリに要求したときに、HTTPS プロトコルに HTTP 301 (永続的リダイレクト) を返します。 たとえば、`http://contoso.com` から `https://contoso.com` にリダイレクトします。

## <a name="automate-with-scripts"></a>スクリプトによる自動化

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/overview) を使用すると、Web アプリの SSL バインドをスクリプトで自動化することができます。

### <a name="azure-cli"></a>Azure CLI

次のコマンドは、エクスポートした PFX ファイルをアップロードし、拇印を取得します。

```bash
thumbprint=$(az appservice web config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

次のコマンドは、直前のコマンドで取得した拇印を使用して、SNI ベースの SSL バインドを追加します。

```bash
az appservice web config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

次のコマンドは、エクスポートした PFX ファイルをアップロードし、SNI ベースの SSL バインドを追加します。

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * アプリの価格レベルをアップグレードする
> * カスタム SSL 証明書を App Service にバインドする
> * アプリに HTTPS を適用する
> * スクリプトで SSL 証明書のバインドを自動化する

次のチュートリアルに進み、Azure Content Delivery Network の使用方法を学習してください。

> [!div class="nextstepaction"]
> [Azure App Service に Content Delivery Network (CDN) を追加する](app-service-web-tutorial-content-delivery-network.md)
