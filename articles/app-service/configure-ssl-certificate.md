---
title: TLS/SSL 証明書を追加および管理する
description: Azure App Service での無料の証明書の作成、App Service 証明書のインポート、Key Vault 証明書のインポート、App Service 証明書の購入について説明します。
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 03/02/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: d6f6db34239cf8c77b6e43d4426d889fa12c0690
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102051346"
---
# <a name="add-a-tlsssl-certificate-in-azure-app-service"></a>Azure App Service で TLS/SSL 証明書を追加する

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。 この記事では、App Service にプライベート証明書またはパブリック証明書を作成、アップロード、またはインポートする方法について説明します。 

証明書が App Service アプリまたは[関数アプリ](../azure-functions/index.yml)に追加されたら、[その証明書を使用してカスタム DNS 名をセキュリティで保護](configure-ssl-bindings.md)したり、[その証明書をアプリケーション コードで使用](configure-ssl-certificate-in-code.md)したりできます。

> [!NOTE]
> アプリにアップロードされた証明書は、アプリのリソース グループとリージョンの組み合わせ (内部的には "*Web 空間*" と呼ばれます) にバインドされたデプロイ ユニットに格納されます。 こうして、リソース グループとリージョンの組み合わせが同じである他のアプリが証明書にアクセスできるようになります。 

次の表に、App Service で証明書を追加するためのオプションを示します。

|オプション|説明|
|-|-|
| 無料の App Service マネージド証明書を作成する (プレビュー) | App Service で[カスタム ドメイン](app-service-web-tutorial-custom-domain.md)をセキュリティで保護することだけが必要な場合に、使いやすい無料のプライベート証明書。 |
| App Service 証明書を購入する | Azure によって管理されるプライベート証明書。 自動証明書管理のシンプルさと、更新オプションとエクスポート オプションの柔軟性が組み合わされています。 |
| Key Vault から証明書をインポートする | [Azure Key Vault](../key-vault/index.yml) を使用して [PKCS12 証明書](https://wikipedia.org/wiki/PKCS_12)を管理する場合に便利です。 「[プライベート証明書の要件](#private-certificate-requirements)」を参照してください。 |
| プライベート証明書のアップロード | 既にサードパーティ プロバイダーからのプライベート証明書がある場合は、それをアップロードすることができます。 「[プライベート証明書の要件](#private-certificate-requirements)」を参照してください。 |
| パブリック証明書のアップロード | パブリック証明書はカスタム ドメインのセキュリティ保護には使用されませんが、リモート リソースにアクセスするために必要な場合は、コードに読み込むことができます。 |

## <a name="prerequisites"></a>前提条件

- [App Service アプリを作成](./index.yml)します。
- プライベート証明書の場合、[App Service のすべての要件](#private-certificate-requirements)を満たしていることを確認します。
- **無料の証明書のみ**:
    - 証明書が必要なドメインを App Service にマップします。 詳細については、「[チュートリアル: 既存のカスタム DNS 名を Azure App Service にマップする](app-service-web-tutorial-custom-domain.md)」をご覧ください。
    - ルート ドメイン (contoso.com など) の場合、アプリに [IP 制限](app-service-ip-restrictions.md)が構成されていないことを確認します。 ルート ドメインの証明書の作成と定期的な更新は、インターネットからアプリにアクセスできるかどうかによって異なります。

## <a name="private-certificate-requirements"></a>プライベート証明書の要件

[無料の App Service マネージド証明書](#create-a-free-managed-certificate-preview)と [App Service 証明書](#import-an-app-service-certificate)は、App Service の要件を既に満たしています。 App Service にプライベート証明書をアップロードまたはインポートする場合、証明書は次の要件を満たしている必要があります。

* Triple DES を使用して暗号化され、[パスワードで保護された PFX ファイル](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Certificate_filename_extensions)としてエクスポートされている
* 少なくとも 2048 ビット長の秘密キーが含まれている
* 証明書チェーン内のすべての中間証明書が含まれている

TLS バインドでカスタム ドメインをセキュリティで保護する場合、証明書には次の追加の要件があります。

* サーバー認証用の[拡張鍵使用](https://en.wikipedia.org/w/index.php?title=X.509&section=4#Extensions_informing_a_specific_usage_of_a_certificate)が含まれている (OID = 1.3.6.1.5.5.7.3.1)
* 信頼された証明機関によって署名されている

> [!NOTE]
> **楕円曲線暗号 (ECC) 証明書** は、App Service で使用できますが、この記事では説明しません。 ECC 証明書を作成する正確な手順については、証明機関にお問い合わせください。

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="create-a-free-managed-certificate-preview"></a>無料のマネージド証明書を作成する (プレビュー)

> [!NOTE]
> 無料のマネージド証明書を作成する前に、アプリの[前提条件を満たしている](#prerequisites)ことを確認してください。

無料 App Service マネージド証明書は、App Service でカスタム DNS 名をセキュリティで保護するためのターンキー ソリューションです。 これは、App Service によって管理され、自動的に更新される、完全に機能する TLS/SSL 証明書です。 無料の証明書には以下の制限が伴います。

- ワイルドカード証明書はサポートされません。
- エクスポートはできません。
- App Service Environment (ASE) ではサポートされません。
- Traffic Manager と統合されたルート ドメインではサポートされません。

> [!NOTE]
> 無料の証明書は DigiCert によって発行されます。 一部のトップレベル ドメインでは、[CAA ドメイン レコード](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization)を `0 issue digicert.com` の値を使用して作成することによって、DigiCert を証明書の発行者として明示的に許可する必要があります。
> 

<a href="https://portal.azure.com" target="_blank">Azure portal</a> で、左側のメニューから **[App Services]**  >  **\<app-name>** を選択します。

アプリの左側のナビゲーションから、 **[TLS/SSL 設定]**  >  **[秘密キー証明書 (.pfx)]**  >  **[Create App Service Managed Certificate]\(App Service マネージド証明書の作成\)** を選択します。

![App Service で無料の証明書を作成する](./media/configure-ssl-certificate/create-free-cert.png)

無料の証明書を作成するカスタム ドメインを選択し、 **[作成]** を選択します。 サポートされているカスタム ドメインごとに証明書を 1 つだけ作成できます。

操作が完了すると、 **[秘密キー証明書]** の一覧に証明書が表示されます。

![完了した無料の証明書の作成](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> この証明書を使用してカスタム ドメインをセキュリティで保護するには、証明書バインドを作成する必要があります。 「[バインドの作成](configure-ssl-bindings.md#create-binding)」の手順に従います。
>

## <a name="import-an-app-service-certificate"></a>App Service 証明書をインポートする

Azure から App Service 証明書を購入する場合は、Azure で次のタスクが管理されます。

- GoDaddy から購入プロセスを実行する。
- 証明書のドメイン検証を実行する。
- 証明書を [Azure Key Vault](../key-vault/general/overview.md) に保持する。
- 証明書の更新を管理する (「[証明書の更新](#renew-certificate)」を参照)。
- App Service アプリでインポートしたコピーと証明書を自動的に同期する。

App Service 証明書を購入するには、「[証明書の注文を開始する](#start-certificate-order)」を参照してください。

既に使用中の App Service 証明書がある場合は、次の操作を実行できます。

- [App Service に証明書をインポートする](#import-certificate-into-app-service)。
- [証明書を管理する](#manage-app-service-certificates) (たとえば、更新、キー更新、エクスポートなど)。
> [!NOTE]
> 現時点で、App Service 証明書は Azure の各国のクラウドではサポートされていません。

### <a name="start-certificate-order"></a>証明書の注文を開始する

<a href="https://portal.azure.com/#create/Microsoft.SSL" target="_blank">App Service 証明書の作成ページ</a>で、App Service 証明書の注文を開始します。

![App Service 証明書の購入の開始](./media/configure-ssl-certificate/purchase-app-service-cert.png)

次の表を使用して、証明書を構成できます。 完了したら、 **[作成]** をクリックします。

| 設定 | 説明 |
|-|-|
| 名前 | App Service 証明書のフレンドリ名。 |
| ネイキッド ドメインのホスト名 | ルート ドメインはここで指定します。 発行された証明書によって、ルート ドメインと `www` サブドメインの "*両方*" が保護されます。 発行された証明書の [共通名] フィールドにはルート ドメインが含まれ、[サブジェクトの別名] フィールドには `www` ドメインが含まれています。 任意のサブドメインのみをセキュリティで保護するには、ここでサブドメインの完全修飾ドメイン名 を指定します (例: `mysubdomain.contoso.com`)。|
| サブスクリプション | 証明書が格納されるサブスクリプション。 |
| Resource group | 証明書が格納されるリソース グループ。 新しいリソース グループを使用するか、App Service アプリと同じリソース グループなどを選択できます。 |
| 証明書 SKU | 作成する証明書の種類 (標準の証明書または[ワイルドカード証明書](https://wikipedia.org/wiki/Wildcard_certificate)) を決定します。 |
| 法律条項 | クリックして法律条項に同意したことを確認します。 証明書は GoDaddy から取得されます。 |

> [!NOTE]
> Azure から購入した App Service 証明書は、GoDaddy によって発行されます。 一部のトップレベル ドメインでは、[CAA ドメイン レコード](https://wikipedia.org/wiki/DNS_Certification_Authority_Authorization)を `0 issue godaddy.com` の値を使用して作成することによって、GoDaddy を証明書の発行者として明示的に許可する必要があります。
> 

### <a name="store-in-azure-key-vault"></a>Azure Key Vault に格納する

証明書の購入プロセスの完了後、この証明書の使用を開始する前に完了する必要のある手順がまだいくつかあります。 

[[App Service 証明書]](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) ページで証明書を選択し、 **[証明書の構成]**  >  **[手順 1:格納]** をクリックします。

![App Service 証明書の Key Vault ストレージを構成する](./media/configure-ssl-certificate/configure-key-vault.png)

[Key Vault](../key-vault/general/overview.md) は、クラウド アプリケーションやサービスで使用される暗号化キーとシークレットを保護するための Azure サービスです。 これは App Service 証明書に対して選択するストレージです。

**[Key Vault の状態]** ページで **[Key Vault リポジトリ]** をクリックして、新しいコンテナーを作成するか、既存のコンテナーを選択します。 新しいコンテナーの作成を選択する場合は、次の表を使用してコンテナーを構成し、[作成] をクリックします。 App Service アプリと同じサブスクリプションおよび同じリソース グループに新しい Key Vault を作成します。

| 設定 | 説明 |
|-|-|
| 名前 | 英数字とダッシュで構成される一意の名前。 |
| Resource group | 推奨事項として、App Service 証明書と同じリソース グループを選択します。 |
| 場所 | App Service アプリと同じ場所を選択します。 |
| Pricing tier | 詳しくは、[Azure Key Vault の価格の詳細](https://azure.microsoft.com/pricing/details/key-vault/)に関するページをご覧ください。 |
| アクセス ポリシー| コンテナー リソースに対するアプリケーションと許可されるアクセス権を定義します。 後で、[Key Vault アクセス ポリシーの割り当て](../key-vault/general/assign-access-policy-portal.md)の手順に従って構成できます。 |
| 仮想ネットワーク アクセス | 特定の Azure 仮想ネットワークへのコンテナー アクセスを制限します。 後で「[Azure Key Vault のファイアウォールと仮想ネットワークを構成する](../key-vault/general/network-security.md)」の手順に従って構成できます |

コンテナーを選択したら、 **[Key Vault リポジトリ]** ページを閉じます。 **[手順 1: 格納]** オプションに、成功を示す緑色のチェック マークが表示されます。 次の手順のためにページは開いたままにしておきます。

### <a name="verify-domain-ownership"></a>ドメインの所有権を検証する

最後の手順で使用した **[証明書の構成]** ページで、 **[手順 2:確認]** をクリックします。

![App Service 証明書のドメインを検証する](./media/configure-ssl-certificate/verify-domain.png)

**[App Service の確認]** を選択します。 Web アプリに既にドメインをマップしたので (「[前提条件](#prerequisites)」を参照)、既に確認されています。 **[確認]** をクリックして、この手順を完了します。 **"証明書はドメイン確認済みです"** というメッセージが表示されるまで、 **[最新の情報に更新]** をクリックします。

> [!NOTE]
> 4 種類のドメイン検証方法がサポートされています。 
> 
> - **App Service** - ドメインが同一のサブスクリプション内で既に App Service アプリにマップされている場合に最も便利なオプションです。 この方法は、App Service アプリがドメインの所有権を既に確認済みである事実を利用しています。
> - **ドメイン** - [Azure から購入した App Service ドメイン](manage-custom-dns-buy-domain.md)を確認します。 Azure は確認 TXT レコードを自動的に追加し、プロセスを完了します。
> - **メール** - ドメイン管理者に電子メールを送信することによってドメインを確認します。 手順は、オプションを選択したときに提供されます。
> - **手動** - HTML ページ (**標準** 証明書のみ) または DNS TXT レコードを使用してドメインを確認します。 手順は、オプションを選択したときに提供されます。

### <a name="import-certificate-into-app-service"></a>App Service に証明書をインポートする

<a href="https://portal.azure.com" target="_blank">Azure portal</a> で、左側のメニューから **[App Services]**  >  **\<app-name>** を選択します。

アプリの左側のナビゲーションから、 **[TLS/SSL 設定]**  >  **[秘密キー証明書 (.pfx)]**  >  **[App Service 証明書のインポート]** を選択します。

![App Service に App Service 証明書をインポートする](./media/configure-ssl-certificate/import-app-service-cert.png)

購入した証明書を選択し、 **[OK]** を選択します。

操作が完了すると、 **[秘密キー証明書]** の一覧に証明書が表示されます。

![完了した App Service 証明書のインポート](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!IMPORTANT] 
> この証明書を使用してカスタム ドメインをセキュリティで保護するには、証明書バインドを作成する必要があります。 「[バインドの作成](configure-ssl-bindings.md#create-binding)」の手順に従います。
>

## <a name="import-a-certificate-from-key-vault"></a>Key Vault から証明書をインポートする

Azure Key Vault を使用して証明書を管理している場合は、[要件を満たしている](#private-certificate-requirements)限り、Key Vault から App Service に PKCS12 証明書をインポートすることができます。

### <a name="authorize-app-service-to-read-from-the-vault"></a>Key Vault から読み取る権限を App Service に与える
既定では、App Service リソース プロバイダーは Key Vault にアクセスできません。 証明書のデプロイに Key Vault を使用するには、[Key Vault に読み取りアクセスする権限をリソース プロバイダーに与える](../key-vault/general/assign-access-policy-cli.md)必要があります。 

`abfa0a7c-a6b6-4736-8310-5855508787cd` は App Service のリソース プロバイダー サービス プリンシパル名です。これはすべての Azure サブスクリプションで同じです。 Azure Government クラウド環境の場合、リソース プロバイダー サービス プリンシパル名として代わりに `6a02c803-dafd-4136-b4c3-5a6f318b4714` を使用します。

### <a name="import-a-certificate-from-your-vault-to-your-app"></a>Key Vault からアプリに証明書をインポートする

<a href="https://portal.azure.com" target="_blank">Azure portal</a> で、左側のメニューから **[App Services]**  >  **\<app-name>** を選択します。

アプリの左側のナビゲーションから、 **[TLS/SSL 設定]**  >  **[秘密キー証明書 (.pfx)]**  >  **[Key Vault 証明書のインポート]** を選択します。

![App Service に Key Vault 証明書をインポートする](./media/configure-ssl-certificate/import-key-vault-cert.png)

次の表を使用すると、証明書を選択する際に役立ちます。

| 設定 | 説明 |
|-|-|
| サブスクリプション | Key Vault が属するサブスクリプション。 |
| Key Vault | インポートする証明書を含むコンテナー。 |
| Certificate | コンテナー内の PKCS12 証明書の一覧から選択します。 コンテナー内のすべての PKCS12 証明書がその拇印と共に一覧表示されますが、App Service ですべてがサポートされているわけではありません。 |

操作が完了すると、 **[秘密キー証明書]** の一覧に証明書が表示されます。 インポートがエラーで失敗する場合は、証明書が [App Service の要件](#private-certificate-requirements)を満たしていません。

![完了した Key Vault 証明書のインポート](./media/configure-ssl-certificate/import-app-service-cert-finished.png)

> [!NOTE]
> Key Vault の証明書を新しい証明書で更新する場合、App Service によって証明書が 48 時間以内に自動的に同期されます。

> [!IMPORTANT] 
> この証明書を使用してカスタム ドメインをセキュリティで保護するには、証明書バインドを作成する必要があります。 「[バインドの作成](configure-ssl-bindings.md#create-binding)」の手順に従います。
>

## <a name="upload-a-private-certificate"></a>プライベート証明書のアップロード

証明書プロバイダーから証明書を取得した後は、このセクションの手順に従って App Service で使用できるように準備します。

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

結合した TLS/SSL 証明書を、証明書の要求と一緒に生成された秘密キーと共にエクスポートします。

証明書の要求の生成に OpenSSL を使用した場合、秘密キー ファイルは作成されています。 証明書を PFX にエクスポートするには、次のコマンドを実行します。 プレースホルダーの _&lt;private-key-file>_ と _&lt;merged-certificate-file>_ をそれぞれ、秘密キーのパスとマージされた証明書ファイルに変更します。

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

メッセージが表示されたら、エクスポートのパスワードを定義します。 このパスワードは、後で TLS/SSL 証明書を App Service にアップロードするときに使用します。

IIS または _Certreq.exe_ を使用して証明書の要求を生成した場合は、ローカル コンピューターに証明書をインストールした後で [証明書を PFX にエクスポート](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11))します。

### <a name="upload-certificate-to-app-service"></a>証明書を App Service にアップロードする

これで、証明書を App Service にアップロードする準備ができました。

<a href="https://portal.azure.com" target="_blank">Azure portal</a> で、左側のメニューから **[App Services]**  >  **\<app-name>** を選択します。

アプリの左側のナビゲーションから、 **[TLS/SSL 設定]**  >  **[秘密キー証明書 (.pfx)]**  >  **[証明書のアップロード]** を選択します。

![App Service にプライベート証明書をアップロードする](./media/configure-ssl-certificate/upload-private-cert.png)

**[PFX 証明書ファイル]** で、PFX ファイルを選択します。 **[証明書のパスワード]** で、PFX ファイルのエクスポート時に作成したパスワードを入力します。 完了したら、 **[アップロード]** をクリックします。 

操作が完了すると、 **[秘密キー証明書]** の一覧に証明書が表示されます。

![完了した証明書のアップロード](./media/configure-ssl-certificate/create-free-cert-finished.png)

> [!IMPORTANT] 
> この証明書を使用してカスタム ドメインをセキュリティで保護するには、証明書バインドを作成する必要があります。 「[バインドの作成](configure-ssl-bindings.md#create-binding)」の手順に従います。
>

## <a name="upload-a-public-certificate"></a>パブリック証明書のアップロード

公開証明書は、 *.cer* 形式でサポートされています。 

<a href="https://portal.azure.com" target="_blank">Azure portal</a> で、左側のメニューから **[App Services]**  >  **\<app-name>** を選択します。

アプリの左側のナビゲーションで、 **[SSL 設定]**  >  **[公開証明書 (.cer)]**  >  **[公開キー証明書のアップロード]** の順にクリックします。

**[名前]** に、証明書の名前を入力します。 **[CER 証明書ファイル]** で、目的の CER ファイルを選択します。

**[アップロード]** をクリックします。

![App Service に公開証明書をアップロードする](./media/configure-ssl-certificate/upload-public-cert.png)

証明書がアップロードされたら、証明書の拇印をコピーし、「[証明書をアクセス可能にする](configure-ssl-certificate-in-code.md#make-the-certificate-accessible)」を参照してください。

## <a name="manage-app-service-certificates"></a>App Service 証明書を管理する

このセクションでは、「[App Service 証明書をインポートする](#import-an-app-service-certificate)」で購入した App Service 証明書を管理する方法について説明します。

- [証明書のキー更新](#rekey-certificate)
- [証明書の更新](#renew-certificate)
- [証明書のエクスポート](#export-certificate)
- [証明書の削除](#delete-certificate)

### <a name="rekey-certificate"></a>証明書のキー更新

証明書の秘密キーが侵害されたと思われる場合は、証明書のキー更新を実行できます。 [[App Service 証明書]](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) ページで証明書を選択し、左側のナビゲーションから **[キー更新と同期]** を選択します。

**[キー更新]** をクリックして処理を開始します。 処理が完了するまでに 1 ～ 10 分かかることがあります。

![App Service 証明書をキー更新する](./media/configure-ssl-certificate/rekey-app-service-cert.png)

証明書のキーを更新すると、証明機関から発行された新しい証明書が展開されます。

キー更新操作が完了したら、 **[同期]** をクリックします。同期操作によって、アプリにダウンタイムを発生させることなく、App Service 内の証明書に対するホスト名のバインドが自動的に更新されます。

> [!NOTE]
> **[同期]** をクリックしなくても、証明書は 48 時間以内に App Service によって自動的に同期されます。

### <a name="renew-certificate"></a>証明書の更新

任意の時点で証明書の自動更新をオンにするには、[[App Service 証明書]](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) ページで証明書を選択し、左側のナビゲーションで **[自動更新の設定]** をクリックします。 既定では、App Service 証明書の有効期間は 1 年です。

**[オン]** を選択して、 **[保存]** をクリックします。 自動更新をオンにすると、証明書は有効期限の 60 日前に自動更新を開始できます。

![App Service 証明書を更新する](./media/configure-ssl-certificate/auto-renew-app-service-cert.png)

証明書を手動で更新するには、 **[手動更新]** をクリックします。 有効期限の 60 日前に、証明書の手動更新を要求できます。

更新操作が完了したら、 **[同期]** をクリックします。同期操作によって、アプリにダウンタイムを発生させることなく、App Service 内の証明書に対するホスト名のバインドが自動的に更新されます。

> [!NOTE]
> **[同期]** をクリックしなくても、証明書は 48 時間以内に App Service によって自動的に同期されます。

### <a name="export-certificate"></a>証明書をエクスポートします。

App Service 証明書は [Key Vault シークレット](../key-vault/general/about-keys-secrets-certificates.md)であるため、そのファイルの PFX コピーをエクスポートして、他の Azure サービスまたは Azure の外部で使用することができます。

App Service 証明書を PFX ファイルとしてエクスポートするには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。 [Azure CLI をインストール](/cli/azure/install-azure-cli)した場合は、ローカルで実行することもできます。 プレースホルダーは、[App Service 証明書の作成](#start-certificate-order)時に使用した名前に置き換えます。

```azurecli-interactive
secretname=$(az resource show \
    --resource-group <group-name> \
    --resource-type "Microsoft.CertificateRegistration/certificateOrders" \
    --name <app-service-cert-name> \
    --query "properties.certificates.<app-service-cert-name>.keyVaultSecretName" \
    --output tsv)

az keyvault secret download \
    --file appservicecertificate.pfx \
    --vault-name <key-vault-name> \
    --name $secretname \
    --encoding base64
```

ダウンロードされた *appservicecertificate* ファイルは、パブリック証明書とプライベート証明書の両方を含む生の PKCS12 ファイルです。 各プロンプトで、インポート パスワードと PEM パスフレーズに空の文字列を使用します。

### <a name="delete-certificate"></a>証明書の削除 

App Service 証明書の削除は最終的なものであり、元に戻すことができません。 App Service 証明書リソースを削除すると、証明書が失効します。 この証明書との App Service のバインディングは無効になります。 誤って削除されないようにするために、Azure により証明書にロックが設定されます。 App Service 証明書を削除するには、最初に証明書の削除ロックを削除する必要があります。

[[App Service 証明書]](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.CertificateRegistration%2FcertificateOrders) ページで証明書を選択し、左側のナビゲーションから **[ロック]** を選択します。

ロックの種類 **[削除]** で、証明書のロックを見つけます。 その右側にある **[削除]** を選択します。

![App Service 証明書のロックを削除する](./media/configure-ssl-certificate/delete-lock-app-service-cert.png)

これで、App Service 証明書を削除できるようになりました。 左側のナビゲーションから **[概要]**  >  **[削除]** を選択します。 確認ダイアログで、証明書の名前を入力し、 **[OK]** を選択します。

## <a name="automate-with-scripts"></a>スクリプトで自動化する

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>その他のリソース

* [Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)
* [HTTPS の適用](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 の適用](configure-ssl-bindings.md#enforce-tls-versions)
* [Azure App Service のご自分のコードから TLS/SSL 証明書を使用する](configure-ssl-certificate-in-code.md)
* [FAQ:App Service 証明書](./faq-configuration-and-management.md)
