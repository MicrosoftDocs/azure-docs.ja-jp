---
title: "Azure App Service の SSL 証明書を購入して構成する"
description: "Azure App Service の SSL 証明書を購入して構成する方法を説明します"
services: app-service
documentationcenter: .net
author: apurvajo
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: f9ff33f33a196e65f6cb7ee7f5332aacb9231f6d
ms.lasthandoff: 03/29/2017


---
# <a name="buy-and-configure-an-ssl-certificate-for-your-azure-app-service"></a>Azure App Service の SSL 証明書を購入して構成する
> [!div class="op_single_selector"]
> * [Azure での SSL 証明書の購入](web-sites-purchase-ssl-web-site.md)
> * [別の場所から取得した SSL 証明書の使用](web-sites-configure-ssl-certificate.md)
> 
> 

**[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** では、*.azurewebsites.net ドメインのワイルドカード証明書を使用する Web アプリに対して、HTTPS が既定で既に有効になっています。 カスタム ドメインを構成する予定がない場合は、既定の HTTPS 証明書を利用できます。 ただし、すべての *[ワイルドカード ドメイン](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates)と同様に、カスタム ドメインに独自の証明書を使用する場合ほど安全ではありません。 Azure App Service では、ポータルを離れることなく Azure ポータルから SSL 証明書を購入して管理する簡単な方法が提供されるようになっています。  
この記事では、3 つの簡単な手順で **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)**用の SSL 証明書を購入して構成する方法を説明します。 

> [!NOTE]
> カスタム ドメイン名用の SSL 証明書は、無料および共有 Web アプリでは使用できません。 Web アプリを Basic モード、Standard モード、または Premium モード用に構成する必要があります。その場合、サブスクリプションに対して課金される金額が変更される可能性があります。 詳細については、「**[Web Apps の料金詳細](https://azure.microsoft.com/pricing/details/web-sites/)**」を参照してください。
> 
> 

## <a name="bkmk_Overview"></a>概要
> [!NOTE]
> 有効なクレジット カードが関連付けられていないサブスクリプションを使用して SSL 証明書を購入しないでください。 お使いのサブスクリプションが無効になる可能性があります。 
> 
> 

## <a name="a-purchase-store-and-assign-an-ssl-certificate-for-your-custom-domain-a"></a><a>カスタム ドメイン用の SSL 証明書を購入、保存、および割り当てる</a>
contoso.comなどのカスタム ドメインに対して HTTPS を有効にするには、まず **[Azure App Service でカスタム ドメイン名を構成する](web-sites-custom-domain-name.md)**必要があります。

SSL 証明書を要求する前に、その証明書により、どのドメイン名をセキュリティで保護するかを最初に決定する必要があります。 この結果、どのような種類の証明書を取得する必要があるかが決まります。 contoso.com または www.contoso.com のように、ただ 1 つのドメイン名を保護する必要がある場合は、Standard (基本的な) 証明書で十分です。 contoso.com、www.contoso.com、および mail.contoso.com のように、複数のドメイン名を保護する必要がある場合は、**[ワイルドカード証明書](http://en.wikipedia.org/wiki/Wildcard_certificate)**を取得できます。

## <a name="bkmk_purchasecert"></a>手順 0: SSL 証明書を注文する
この手順では、選択した SSL 証明書を注文する方法を説明します。

1. **[Azure Portal](https://portal.azure.com/)**で、[参照] をクリックし、検索バーに「App Service 証明書」と入力し、結果から "App Service 証明書" を選択して、[追加] をクリックします。 
   
   ![参照を使用して作成のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
   ![参照を使用して作成のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/add.jpg)
2. SSL 証明書の **フレンドリ名** を入力します。
3. SSL 証明書の **[ホスト名]**
   
   > [!NOTE]
   > これは、購入プロセスの最も重要な部分の 1 つです。 この証明書で保護する正しいホスト名 (カスタム ドメイン) を入力してください。 ホスト名には WWW を**付けないでください**。 たとえば、カスタム ドメイン名が www.contoso.com の場合、[ホスト名] には contoso.com だけを入力します。購入した証明書では、www ドメインとルート ドメインの両方が保護されます。 
   > 
   > 
4. **サブスクリプション**を選択します。 
   
   複数のサブスクリプションがある場合は、対象のカスタム ドメインまたは Web アプリに使用したものと同じサブスクリプションで、SSL 証明書を作成してください。
5. **リソース グループ**を選択または作成します。
   
   リソース グループを使用すると、関連する Azure リソースを 1 つの単位として管理でき、アプリ用にロール ベースのアクセス制御 (RBAC) 規則を作成する際に便利です。 詳細については、Azure リソースの管理に関するページを参照してください。
6. **[証明書 SKU]** 
   
   最後に、ニーズに合った証明書 SKU を選択し、[作成] をクリックします。 今日では、Azure App Service を使用すると 2 つの異なる Sku を購入するには
   
        •    S1 – Standard Certificate with 1-year validity and auto renewal  
        •    W1 – Wild card Certificate with 1-year validity and auto renewal      
   詳細については、「**[Web Apps の料金詳細](https://azure.microsoft.com/pricing/details/web-sites/)**」を参照してください。

![証明書 SKU のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [!NOTE]
> SSL 証明書の作成には 1 ～ 10 分かかります。 このプロセスでは、手作業で行うと非常に面倒な複数の手順がバック グラウンドで実行されます。  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>手順 1: 証明書を Azure Key Vault に保存する
この手順では、選択した Azure Key Vault に購入した SSL 証明書を格納する方法を説明します。

1. SSL 証明書の購入が完了した後は、**[App Service 証明書]** リソース ブレードを再度参照して手動で開く必要があります (手順 1 を参照)。   
   
   ![KV に格納する準備完了のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   この証明書の使用を開始する前に完了する必要のある手順がまだいくつかあるため、証明書の状態は **[発行保留中]** になっています。
2. [証明書のプロパティ] ブレードの **[証明書の構成]** をクリックし、**[ステップ 1: 格納]** をクリックして Azure Key Vault にこの証明書を格納します。
3. **[Key Vault の状態]** ブレードの **[Key Vault リポジトリ]** をクリックして、この証明書を格納する既存の Key Vault を選択するか、**[Key Vault の新規作成]** をクリックして同じサブスクリプションとリソース グループに新しい Key Vault を作成します。
   
   ![新規 KV 作成のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > 最小限の料金でこの証明書を Azure Key Vault に格納できます。 詳細については、「**[Key Vault の価格](https://azure.microsoft.com/pricing/details/key-vault/)**」をご覧ください。
   > 
   > 
4. この証明書を格納する Key Vault リポジトリを選択した後、**[Key Vault の状態]** ブレードの上部にある **[格納]** ボタンをクリックして格納します。  
   
    購入した証明書を選択した Azure Key Vault に格納する手順はこれで完了です。 ブレードを更新すると、この手順に緑のチェック マークが表示されます。

## <a name="bkmk_VerifyOwnership"></a>手順 2: ドメインの所有権を検証する
この手順では、注文した SSL 証明書のドメイン所有権を検証する方法を説明します。 

1. **[証明書の構成]** ブレードで **[ステップ 2: 検証]** をクリックします。 App Service 証明書では 3 種類のドメイン検証がサポートされています。
   
   * **ドメイン検証** 
     
     * この方法は、**[Azure App Service からカスタム ドメインを購入した](custom-dns-web-site-buydomains-web-app.md)****場合にのみ**、最も便利な方法です。
     * **[検証]** ボタンをクリックして、この手順を実行します。
     * 検証が完了した後、 **[更新]** をクリックして証明書の状態を更新します。 検証が完了するまで数分かかる場合があります。
   * **メール検証**
     
     * 検証の電子メールは、このカスタム ドメインに関連付けられているメール アドレスに既に送信されています。
     * 電子メールを開き、検証リンクをクリックして、電子メール検証手順を実行します。 
     * 検証メールを再送信する必要がある場合は、 **[電子メールを再送信]** ボタンをクリックします。
   * **手動検証**    
     
      **HTML Web ページの検証 (標準の証明書の SKU でのみ動作)**

        * **"starfield.html"** という名前の HTML ファイルを作成します。
        * このファイルの内容は、ドメイン確認トークンの名前と正確に同じにする必要があります。 (ドメインの確認の状態のブレードからトークンをコピーすればよい)
        * ドメインをホストする Web サーバーのルートに、このファイルをアップロードします。**/.well-known/pki-validation/starfield.html**
        * 検証が完了した後、 **[更新]** をクリックして証明書の状態を更新します。 検証が完了するまで数分かかる場合があります。
          
          たとえば、**tgjgthq8d11ttaeah97s3fr2sh** というドメイン確認トークンで **contosocertdemo.com** の標準の証明書を購入した場合、**http://contosocertdemo.com/.well-known/pki-validation/starfield.html** に対する Web 要求からは **tgjgthq8d11ttaeah97s3fr2sh** が返ります。

      **DNS TXT レコード検証**
        
        * DNS マネージャーを使用して、**‘@’** サブドメインに、**ドメイン確認トークン**と同じ値の TXT レコードを作成します。
        * 検証が完了した後、 **[更新]** をクリックして証明書の状態を更新します。 検証が完了するまで数分かかる場合があります。
          
          たとえば、ホスト名が**\*.contosocertdemo.com** または **\*.subdomain.contosocertdemo.com** でドメイン確認トークンが **tgjgthq8d11ttaeah97s3fr2sh** であるワイルドカード証明書の検証を実行するには、**contosocertdemo.com** に **tgjgthq8d11ttaeah97s3fr2sh** という値の TXT レコードを作成する必要があります。     

## <a name="bkmk_AssignCertificate"></a>手順 3: App Service アプリに証明書を割り当てる
この手順では、この新しく購入した証明書を App Service アプリに割り当てる方法を説明します。 

> [!NOTE]
> このセクションの手順を実行する前に、アプリにカスタム ドメイン名が関連付けられている必要があります。 詳細については、「**[Azure App Service のカスタム ドメイン名の構成](web-sites-custom-domain-name.md)**」を参照してください。
> 
> 

1. ブラウザーで、**[Azure Portal](https://portal.azure.com/)** を開きます。
2. ページの左側にある **[App Service]** オプションをクリックします。
3. この証明書を割り当てるアプリの名前をクリックします。 
4. **[設定]**で、**[SSL 証明書]** をクリックします。
5. **[Import App Service Certificate (App Service 証明書のインポート)]** をクリックして、購入した証明書を選択します。
   
   ![証明書インポートのイメージを挿入](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)
6. **[SSL バインド]** セクションで、**[バインドの追加]** をクリックします。
7. **[Add SSL Binding (SSL バインドの追加)]** ブレードで、ドロップダウン リストから SSL でセキュリティ保護するドメイン名、および使用する証明書を選択します。 また、**[Server Name Indication (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)**または IP ベースの SSL のどちらを使用するかを選択できます。
   
    ![SSL バインドのイメージを挿入](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
       •    IP based SSL associates a certificate with a domain name by mapping the dedicated public IP address of the server to the domain name. This requires each domain name (contoso.com, fabricam.com, etc.) associated with your service to have a dedicated IP address. This is the traditional          method of associating SSL certificates with a web server.
       •    SNI based SSL is an extension to SSL and **[Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) that allows multiple domains to share the same IP address, with separate security certificates for each domain. Most modern browsers (including Internet Explorer, Chrome, Firefox and Opera) support SNI, however older browsers may not support SNI. For more information on SNI, see the **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** article on Wikipedia.
8. 変更を保存して SSL を有効にするには、 **[Add Binding (バインドの追加)]** をクリックします。

**[IP ベースの SSL]** を選択し、カスタム ドメインが A レコードを使用して構成されている場合は、次の追加手順を実行する必要があります。

* IP ベースの SSL バインドを構成すると、専用の IP アドレスがアプリに割り当てられます。 この IP アドレスは、アプリの設定の **[カスタム ドメイン]** ページで確認できます。これは、**[ホスト名]** セクションの上にあります。 このアドレスは、**[外部 IP アドレス]** として示されます。
  
    ![IP SSL のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
  
    この IP アドレスは、ドメイン用の A レコードを構成するために以前使用した仮想 IP アドレスとは異なります。 SNI ベースの SSL を使用するように構成する場合、または SSL を使用するように構成しない場合は、このエントリに対してアドレスは表示されません。

* ドメイン名レジストラーから提供されるツールを使用して、前の手順の IP アドレスを指定するようにカスタム ドメイン名用の A レコードを変更します。
   ここで、証明書が正しく構成されていることを確認するために、HTTP:// ではなく、HTTPS:// を使用してアプリを参照することができる必要があります。

## <a name="bkmk_Export"></a>App Service 証明書をエクスポートする
他の Azure サービスで使用できるように、App Service 証明書のローカル PFX コピーを作成することができます。 詳細については、**[こちらのブログ記事を参照してください](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)**。

## <a name="bkmk_Renew"></a>App Service 証明書の自動更新
証明書の自動更新の設定を切り替えたり、証明書を手動で更新したりするには、**[証明書のプロパティ]** ブレードの **[自動更新の設定]** オプションを選択します。 


  ![参照を使用して作成のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/autorenew.png)

期限切れになる前に証明書を自動的に更新する場合は、**[自動更新]** を有効にします。 これは既定のオプションです。 このオプションが有効な場合、90 日目以降、期限切れ前に証明書の更新が試みられます。 Azure Portal での操作を使用して App Service アプリに SSL バインディングを作成した場合は、(キー更新および同期のシナリオと同様に) 準備が整ったら、新しい証明書と共にこれらのバインドも更新されます。 一方、手動で更新する場合は、この設定を無効する必要があります。 App Service 証明書は、有効期限が 90 日以内の場合のみ手動で更新できます。

## <a name="bkmk_Rekey"></a>キーを更新して証明書を同期する
1. セキュリティ上の理由から証明書のキーを更新する必要がある場合は、**[証明書のプロパティ]** ブレードの **[キーの更新と同期]** オプションを選択します。 
2. **[キー更新]** ボタンをクリックして処理を開始します。 処理が完了するまでに 1 ～ 10 分かかることがあります。 
   
    ![SSL キー更新のイメージを挿入](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)
3. 証明書のキーを更新すると、証明機関から発行された新しい証明書が展開されます。
4. 証明書の有効期間中は、キーを更新しても課金されません。 
5. 証明書のキーを更新すると、発行保留中状態になります。 
6. 証明書の準備ができたら、サービスの中断を防ぐため、この証明書を使用してリソースを同期する必要があります。
7. 同期オプションは、Web アプリにまだ割り当てられていない証明書に対しては使用できません。 

## <a name="more-resources"></a>その他のリソース
* [アプリに対する HTTPS を Azure App Service で有効にする](web-sites-configure-ssl-certificate.md)
* [Azure App Service でのカスタム ドメイン名の購入と構成](custom-dns-web-site-buydomains-web-app.md)
* [Microsoft Azure のトラスト センター](/support/trust-center/security/)
* [Azure Web Sites でロックを解除された構成オプション](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [Azure 管理ポータル](https://manage.windowsazure.com)

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 


