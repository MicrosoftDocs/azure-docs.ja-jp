---
title: Azure から SSL 証明書を購入して構成する - App Service | Microsoft Docs
description: App Service 証明書を購入して App Service アプリにバインドする方法を説明します。
services: app-service
documentationcenter: .net
author: cephalin
manager: jpconnoc
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: cephalin
ms.reviewer: apurvajo
ms.custom: seodec18
ms.openlocfilehash: 7675a22b4b2d8b13524f06f45d6bb805c1e2fad1
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019129"
---
# <a name="buy-and-configure-an-ssl-certificate-for-azure-app-service"></a>Azure App Service の SSL 証明書を購入して構成する

このチュートリアルでは、[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) に App Service 証明書を作成 (購入) し、それを App Service アプリにバインドすることによって、[App Service アプリ](https://docs.microsoft.com/azure/app-service/)または[関数アプリ](https://docs.microsoft.com/azure/azure-functions/)をセキュリティで保護する方法を示します。

> [!TIP]
> App Service 証明書はあらゆる Azure Service と Azure ではないサービスで利用できます。App Service に限定されません。 そのためには、任意の場所で利用できるように、App Service 証明書のローカル PFX コピーを作成する必要があります。 詳しくは、[App Service 証明書のローカル PFX コピーの作成](https://blogs.msdn.microsoft.com/benjaminperkins/2017/04/12/export-an-azure-app-service-certificate-pfx-powershell/)に関する記事をご覧ください。
>

## <a name="prerequisites"></a>前提条件

この攻略ガイドに従うには:

- [App Service アプリを作成する](/azure/app-service/)
- [ドメイン名をアプリにマップする](app-service-web-tutorial-custom-domain.md)か、[Azure で購入して構成する](manage-custom-dns-buy-domain.md)

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="start-certificate-order"></a>証明書の注文を開始する

<a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service 証明書の作成ページ</a>で、App Service 証明書の注文を開始します。

![証明書の作成](./media/app-service-web-purchase-ssl-web-site/createssl.png)

次の表を使用して、証明書を構成できます。 完了したら、 **[作成]** をクリックします。

| Setting | 説明 |
|-|-|
| EnableAdfsAuthentication | App Service 証明書のフレンドリ名。 |
| ネイキッド ドメインのホスト名 | ルート ドメインはここで指定します。 発行された証明書によって、ルート ドメインと `www` サブドメインの "*両方*" が保護されます。 発行された証明書の [共通名] フィールドにはルート ドメインが含まれ、[サブジェクトの別名] フィールドには `www` ドメインが含まれています。 任意のサブドメインのみをセキュリティで保護するには、ここでサブドメインの完全修飾ドメイン名 を指定します (例: `mysubdomain.contoso.com`)。|
| Subscription | Web アプリがホストされているデータ センターです。 |
| Resource group | 証明書が含まれるリソース グループ。 新しいリソース グループを使用するか、App Service アプリと同じリソース グループなどを選択できます。 |
| 証明書 SKU | 作成する証明書の種類 (標準の証明書または[ワイルドカード証明書](https://wikipedia.org/wiki/Wildcard_certificate)) を決定します。 |
| 法律条項 | クリックして法律条項に同意したことを確認します。 証明書は GoDaddy から取得されます。 |

## <a name="store-in-azure-key-vault"></a>Azure Key Vault に格納する

証明書の購入プロセスの完了後、この証明書の使用を開始する前に完了する必要のある手順がまだいくつかあります。 

[[App Service 証明書]](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) ページで証明書を選択し、 **[証明書の構成]**  >  **[手順 1:格納]** をクリックします。

![KV に格納する準備完了のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/ReadyKV.png)

[Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットを保護するための Azure サービスです。 これは App Service 証明書に対して選択するストレージです。

**[Key Vault の状態]** ページで **[Key Vault リポジトリ]** をクリックして、新しいコンテナーを作成するか、既存のコンテナーを選択します。 新しいコンテナーの作成を選択する場合は、次の表を使用してコンテナーを構成し、[作成] をクリックします。 同じサブスクリプションおよびリソース グループ内に新しい Key Vault を作成します。

| Setting | 説明 |
|-|-|
| EnableAdfsAuthentication | 英数字とダッシュで構成される一意の名前。 |
| Resource group | 推奨事項として、App Service 証明書と同じリソース グループを選択します。 |
| Location | App Service アプリと同じ場所を選択します。 |
| 価格レベル | 詳しくは、[Azure Key Vault の価格の詳細](https://azure.microsoft.com/pricing/details/key-vault/)に関するページをご覧ください。 |
| アクセス ポリシー| コンテナー リソースに対するアプリケーションと許可されるアクセス権を定義します。 後で「[さまざまなアプリケーションにキー コンテナーへのアクセス許可を付与する](../key-vault/key-vault-group-permissions-for-apps.md)」の手順に従って構成できます。 |
| 仮想ネットワーク アクセス | 特定の Azure 仮想ネットワークへのコンテナー アクセスを制限します。 後で「[Azure Key Vault のファイアウォールと仮想ネットワークを構成する](../key-vault/key-vault-network-security.md)」の手順に従って構成できます |

コンテナーを選択したら、 **[Key Vault リポジトリ]** ページを閉じます。 **[ストア]** オプションに、成功を示す緑色のチェック マークが表示されます。 次の手順のためにページは開いたままにしておきます。

## <a name="verify-domain-ownership"></a>ドメインの所有権を検証する

最後の手順で使用した **[証明書の構成]** ページで、 **[手順 2:確認]** をクリックします。

![](./media/app-service-web-purchase-ssl-web-site/verify-domain.png)

**[App Service の確認]** を選択します。 Web アプリに既にドメインをマップしたので (「[前提条件](#prerequisites)」を参照)、既に確認されています。 **[確認]** をクリックして、この手順を完了します。 **"証明書はドメイン確認済みです"** というメッセージが表示されるまで、 **[最新の情報に更新]** をクリックします。

> [!NOTE]
> 4 種類のドメイン検証方法がサポートされています。 
> 
> - **App Service** - ドメインが同一のサブスクリプション内で既に App Service アプリにマップされている場合に最も便利なオプションです。 この方法は、App Service アプリがドメインの所有権を既に確認済みである事実を利用しています。
> - **ドメイン** - [Azure から購入した App Service ドメイン](manage-custom-dns-buy-domain.md)を確認します。 Azure は確認 TXT レコードを自動的に追加し、プロセスを完了します。
> - **メール** - ドメイン管理者に電子メールを送信することによってドメインを確認します。 手順は、オプションを選択したときに提供されます。
> - **手動** - HTML ページ (**標準**証明書のみ) または DNS TXT レコードを使用してドメインを確認します。 手順は、オプションを選択したときに提供されます。

## <a name="bind-certificate-to-app"></a>アプリに証明書をバインドする

**[Azure portal](https://portal.azure.com/)** の左側のメニューから、 **[App Services]**  >  **[\<your_ app>]** を選択します。

アプリの左側のナビゲーションから、 **[SSL 設定]**  >  **[プライベート証明書 (.pfx)]**  >  **[App Service 証明書のインポート]** を選択します。

![証明書インポートのイメージを挿入](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

購入した証明書を選択します。

証明書がインポートされたら、それをアプリ上のマップされたドメイン名にバインドする必要があります。 **[バインド]**  >  **[SSL バインディングの追加]** を選択します。 

![証明書インポートのイメージを挿入](./media/app-service-web-purchase-ssl-web-site/AddBinding.png)

次の表を使用して、 **[SSL バインディング]** ダイアログでバインディングを構成してから、 **[バインディングの追加]** をクリックします。

| Setting | 説明 |
|-|-|
| ホスト名 | SSL バインディングを追加するドメイン名。 |
| プライベート証明書のサムプリント | バインドする証明書。 |
| SSL の種類 | <ul><li>**SNI SSL** - 複数の SNI ベースの SSL バインディングを追加できます。 このオプションでは、複数の SSL 証明書を使用して、同一の IP アドレス上の複数のドメインを保護できます。 最新のブラウザーのほとんど (Inernet Explorer、Chrome、Firefox、Opera など) が SNI をサポートしています (ブラウザーのサポートに関するより包括的な情報については、「[Server Name Indication](https://wikipedia.org/wiki/Server_Name_Indication)」を参照してください)。</li><li>**IP ベースの SSL** - IP ベースの SSL バインドを 1 つだけ追加することができます。 このオプションでは、SSL 証明書を 1 つだけ使用して、専用のパブリック IP アドレスを保護します。 バインディングを構成した後は、「[IP SSL の A レコードの再マップ](app-service-web-tutorial-custom-ssl.md#remap-a-record-for-ip-ssl)」の手順に従います。 </li></ul> |

## <a name="verify-https-access"></a>HTTPS アクセスを確認する

`HTTP://<domain_name>` ではなく `HTTPS://<domain_name>` を使用してアプリにアクセスし、証明書が正しく構成されていることを確認します。

## <a name="rekey-certificate"></a>証明書のキー更新

証明書の秘密キーが侵害されたと思われる場合は、証明書のキー更新を実行できます。 [[App Service 証明書]](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) ページで証明書を選択し、左側のナビゲーションから **[キー更新と同期]** を選択します。

**[キー更新]** をクリックして処理を開始します。 処理が完了するまでに 1 ～ 10 分かかることがあります。

![SSL キー更新のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/Rekey.png)

証明書のキーを更新すると、証明機関から発行された新しい証明書が展開されます。

キー更新操作が完了したら、 **[同期]** をクリックします。同期操作によって、アプリにダウンタイムを発生させることなく、App Service 内の証明書に対するホスト名のバインドが自動的に更新されます。

> [!NOTE]
> **[同期]** をクリックしなくても、証明書は 48 時間以内に App Service によって自動的に同期されます。

## <a name="renew-certificate"></a>証明書の更新

任意の時点で証明書の自動更新をオンにするには、[[App Service 証明書]](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) ページで証明書を選択し、左側のナビゲーションで **[自動更新の設定]** をクリックします。

**[オン]** を選択して、 **[保存]** をクリックします。 自動更新をオンにすると、証明書は有効期限の 60 日前に自動更新を開始できます。

![証明書を自動的に更新する](./media/app-service-web-purchase-ssl-web-site/auto-renew.png)

証明書を手動で更新するには、 **[手動更新]** をクリックします。 有効期限の 60 日前に、証明書の手動更新を要求できます。

更新操作が完了したら、 **[同期]** をクリックします。同期操作によって、アプリにダウンタイムを発生させることなく、App Service 内の証明書に対するホスト名のバインドが自動的に更新されます。

> [!NOTE]
> **[同期]** をクリックしなくても、証明書は 48 時間以内に App Service によって自動的に同期されます。

## <a name="automate-with-scripts"></a>スクリプトで自動化する

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="more-resources"></a>その他のリソース

* [HTTPS の適用](app-service-web-tutorial-custom-ssl.md#enforce-https)
* [TLS 1.1/1.2 の適用](app-service-web-tutorial-custom-ssl.md#enforce-tls-versions)
* [Azure App Service のアプリケーション コードに SSL 証明書を使用する](app-service-web-ssl-cert-load.md)
* [FAQ:App Service 証明書](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
