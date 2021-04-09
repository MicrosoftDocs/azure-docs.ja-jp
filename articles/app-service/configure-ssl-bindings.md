---
title: TLS/SSL バインドを使用してカスタム DNS をセキュリティで保護する
description: 証明書を使用して TLS/SSL バインディングを作成することによって、カスタム ドメインに対する HTTPS アクセスのセキュリティを確保します。 HTTPS または TLS 1.2 を強制して Web サイトのセキュリティを強化しましょう。
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 04/30/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: a713d869a754f17f4030c531999c822a2cbb76ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039803"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する

この記事では、証明書バインドを作成することで [App Service アプリ](./index.yml)または[関数アプリ](../azure-functions/index.yml)の[カスタム ドメイン](app-service-web-tutorial-custom-domain.md)をセキュリティで保護する方法について説明します。 完了したら、カスタム DNS 名 (`https://www.contoso.com` など) の `https://` エンドポイントで App Service アプリにアクセスできます。 

![カスタム TLS/SSL 証明書を使用した Web アプリ](./media/configure-ssl-bindings/app-with-custom-ssl.png)

証明書で[カスタム ドメイン](app-service-web-tutorial-custom-domain.md)を保護する方法は次の 2 つの手順から構成されています。

- [プライベート証明書の要件](configure-ssl-certificate.md#private-certificate-requirements)をすべて満たす[プライベート証明書を App Service に追加する](configure-ssl-certificate.md)。
-  該当カスタム ドメインの TLS バインドを作成する。 この 2 つ目の手順についてこの記事で説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * アプリの価格レベルをアップグレードする
> * 証明書を使用してカスタム ドメインをセキュリティで保護する
> * HTTPS の適用
> * TLS 1.1/1.2 の適用
> * スクリプトを使って TLS 管理を自動化する

## <a name="prerequisites"></a>前提条件

この攻略ガイドに従うには:

- [App Service アプリを作成する](./index.yml)
- [ドメイン名をアプリにマップする](app-service-web-tutorial-custom-domain.md)か、[Azure で購入して構成する](manage-custom-dns-buy-domain.md)
- [プライベート証明書をアプリに追加する](configure-ssl-certificate.md)

> [!NOTE]
> プライベート証明書を追加する最も簡単な方法は、[無料の App Service マネージド証明書 (プレビュー) を作成する](configure-ssl-certificate.md#create-a-free-managed-certificate-preview)ことです。

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>カスタム ドメインをセキュリティで保護する

手順は次のとおりです。

<a href="https://portal.azure.com" target="_blank">Azure portal</a> で、左側のメニューから **[App Services]**  >  **\<app-name>** を選択します。

アプリの左側のナビゲーションから、 **[TLS/SSL バインド]** ダイアログを開きます。

- **[カスタム ドメイン]** 、 **[バインディングの追加]** の順に選択します
- **[TLS/SSL 設定]** 、 **[TLS/SSL バインディングの追加]** の順に選択します

![ドメインにバインディングを追加する](./media/configure-ssl-bindings/secure-domain-launch.png)

**[カスタム ドメイン]** で、バインドを追加するカスタム ドメインを選択します。

選択したカスタム ドメインの証明書がアプリに既に設定されている場合、[[バインドの作成]](#create-binding) に直接進みます。 それ以外の場合は続行します。

### <a name="add-a-certificate-for-custom-domain"></a>カスタム ドメインの証明書を追加する

カスタムドメインの証明書がアプリに設定されていない場合、次の 2 つのオプションがあります。

- **PFX 証明書のアップロード** - 「[プライベート証明書をアップロードする](configure-ssl-certificate.md#upload-a-private-certificate)」のワークフローに従い、ここでこのオプションを選択します。
- **App Service 証明書のインポート** - 「[App Service 証明書をインポートする](configure-ssl-certificate.md#import-an-app-service-certificate)」のワークフローに従い、ここでこのオプションを選択します。

> [!NOTE]
> [無料証明書を作成](configure-ssl-certificate.md#create-a-free-managed-certificate-preview)したり (プレビュー)、[Key Vault 証明書をインポート](configure-ssl-certificate.md#import-a-certificate-from-key-vault)したりすることもできますが、それは別々に行い、 **[TLS/SSL バインド]** ダイアログに戻る必要があります。

### <a name="create-binding"></a>バインドを作成する

次の表を参考に使用して、 **[TLS/SSL バインディング]** ダイアログで TLS バインドを構成してから、 **[バインドの追加]** をクリックします。

| 設定 | 説明 |
|-|-|
| カスタム ドメイン | TLS/SSL バインドを追加するドメイン名。 |
| プライベート証明書のサムプリント | バインドする証明書。 |
| TLS/SSL の種類 | <ul><li>**[SNI SSL](https://en.wikipedia.org/wiki/Server_Name_Indication)** - 複数の SNI SSL バインディングを追加できます。 このオプションでは、複数の TLS/SSL 証明書を使用して、同一の IP アドレス上の複数のドメインを保護できます。 最新のブラウザーのほとんど (Inernet Explorer、Chrome、Firefox、Opera など) が SNI をサポートしています (詳細については、「[Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)」を参照してください)。</li><li>**IP SSL** - IP SSL バインディングを 1 つだけ追加できます。 このオプションでは、TLS/SSL 証明書を 1 つだけ使用して、専用のパブリック IP アドレスを保護します。 バインディングを構成した後は、[IP SSL のレコードの再マッピング](#remap-records-for-ip-ssl)の手順に従います。<br/>IP SSL は、**Standard** レベル以上でのみサポートされます。 </li></ul> |

操作が完了すると、カスタム ドメインの TLS/SSL 状態が **セキュリティで保護された** 状態に変わります。

![TLS/SSL バインド成功](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> **[カスタム ドメイン]** での **[Secure]\(セキュリティ保護\)** 状態とは、それが証明書を使用してセキュリティで保護されているが、App Service はその証明書が自己署名されたものか有効期限が切れているかをチェックしないことを意味します。たとえば、それによって、ブラウザーにエラーや警告が表示されることもあります。

## <a name="remap-records-for-ip-ssl"></a>IP SSL のレコードの再マップ

アプリで IP SSL を使用していない場合、[カスタム ドメインの HTTPS のテスト](#test-https)に関するセクションにスキップしてください。

行うべき変更は 2 つ考えられます。

- 既定では、アプリは、共有のパブリック IP アドレスを使用します。 IP SSL で証明書をバインドすると、アプリ用の新規の専用 IP アドレスが App Service によって作成されます。 アプリに A レコードをマップした場合は、この新規の専用 IP アドレスでドメイン レジストリを更新します。

    アプリの **[カスタム ドメイン]** ページが、新規の専用 IP アドレスで更新されます。 [この IP アドレスをコピー](app-service-web-tutorial-custom-domain.md#info)して、この新しい IP アドレスに [A レコードを再マップ](app-service-web-tutorial-custom-domain.md#map-an-a-record)します。

- `<app-name>.azurewebsites.net` への SNI SSL バインディングがある場合は、`sni.<app-name>.azurewebsites.net` を指すように [CNAME マッピングを再マップ](app-service-web-tutorial-custom-domain.md#map-a-cname-record)します (`sni` プレフィックスを追加)。

## <a name="test-https"></a>HTTPS のテスト

さまざまなブラウザーで `https://<your.custom.domain>` にアクセスし、アプリの要求を処理できることを確認します。

:::image type="content" source="./media/configure-ssl-bindings/app-with-custom-ssl.png" alt-text="contoso.com のカスタム ドメインにブラウザーでアクセスする例を示す画面のスクリーンショット (URL が強調表示されている)。":::

プロトコルは、アプリケーション コードから "x-appservice-proto" ヘッダーを介して調査することができます。 ヘッダーの値は `http` または `https` になります。 

> [!NOTE]
> アプリで証明書検証エラーが返された場合は、自己署名証明書を使用している可能性があります。
>
> そうでない場合は、証明書を PFX ファイルにエクスポートするときに中間証明書を含めなかった可能性があります。

## <a name="prevent-ip-changes"></a>IP 変更を禁止する

バインディングを削除すると、着信 IP アドレスが変化する場合があります。そのバインディングが IP SSL であっても同様です。 IP SSL バインディングに既に存在する証明書を更新するときには、このことが特に重要となります。 アプリの IP アドレスに変更が生じないようにするには、次の手順に従います。

1. 新しい証明書をアップロードします。
2. 古い証明書を削除せずに、新しい証明書を目的のカスタム ドメインにバインドします。 これは、古い証明書を削除する代わりに、バインディングを置き換える操作となります。
3. 古い証明書を削除します。 

## <a name="enforce-https"></a>HTTPS の適用

既定では、どなたでも引き続き HTTP を使用してアプリにアクセスできます。 すべての HTTP 要求を HTTPS ポートにリダイレクトできます。

アプリ ページで、左側のナビゲーションにある **[SSL 設定]** を選択します。 その後、 **[HTTPS のみ]** で、 **[On]** を選択します。

![HTTPS の適用](./media/configure-ssl-bindings/enforce-https.png)

操作が完了すると、アプリを指定する HTTP URL のいずれかに移動します。 次に例を示します。

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>TLS バージョンを適用する

アプリでは既定で [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 が有効です。これは、[PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) などの業界標準で推奨されている TLS レベルです。 異なる TLS バージョンを適用するには、次の手順を実行します。

アプリ ページで、左側のナビゲーションにある **[SSL 設定]** を選択します。 次に、 **[TLS version]\(TLS バージョン\)** で、必要な最低限の TLS バージョンを選択します。 この設定により、受信呼び出しのみが制御されます。 

![TLS 1.1/1.2 の適用](./media/configure-ssl-bindings/enforce-tls1-2.png)

操作が完了すると、アプリは下位の TLS バージョンでの接続をすべて拒否します。

## <a name="handle-tls-termination"></a>TLS 終了の処理

App Service では、[TLS 終了](https://wikipedia.org/wiki/TLS_termination_proxy)がネットワーク ロード バランサーで発生するため、すべての HTTPS 要求は暗号化されていない HTTP 要求としてアプリに到達します。 ユーザー要求が暗号化されているかどうかをアプリ ロジックが確認する必要がある場合は、`X-Forwarded-Proto` ヘッダーを調べます。

[Linux Node.js 構成](configure-language-nodejs.md#detect-https-session)ガイドなどの言語固有の構成ガイドでは、アプリケーション コード内の HTTPS セッションを検出する方法について説明しています。

## <a name="automate-with-scripts"></a>スクリプトで自動化する

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>その他のリソース

* [Azure App Service のご自分のコードから TLS/SSL 証明書を使用する](configure-ssl-certificate-in-code.md)
* [FAQ:App Service 証明書](./faq-configuration-and-management.md)