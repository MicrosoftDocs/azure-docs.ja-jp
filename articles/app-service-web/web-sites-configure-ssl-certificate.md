---
title: "HTTPS を使用したアプリのカスタム ドメインのセキュリティ保護 | Microsoft Docs"
description: "SSL 証明書バインドを構成することによって Azure App Service でアプリのカスタム ドメイン名のセキュリティを高める方法について説明します。 複数のツールで SSL 証明書を取得する方法についても説明します。"
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
tags: top-support-issue
ms.assetid: 613d7932-73aa-4318-867c-1ce1416224dc
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: f7a2066f43219e8748b5c5356ff6c81535b7842a
ms.lasthandoff: 03/17/2017


---
# <a name="secure-your-apps-custom-domain-with-https"></a>HTTPS を使用したアプリのカスタム ドメインのセキュリティ保護
> [!div class="op_single_selector"]
> * [Azure での SSL 証明書の購入](web-sites-purchase-ssl-web-site.md)
> * [別の場所から取得した SSL 証明書の使用](web-sites-configure-ssl-certificate.md)
> 
> 

この記事では、 [Azure App Service](../app-service/app-service-value-prop-what-is.md) でカスタム ドメイン名を使用している Web アプリ、モバイル アプリ バックエンド、または API アプリに対して HTTPS を有効にする方法について説明します。 サーバーのみの認証について取り上げます。 相互認証 (クライアント認証を含む) を使用する必要がある場合は、「 [Web アプリの TLS 相互認証を構成する方法](app-service-web-configure-tls-mutual-auth.md)」を参照してください。

カスタム ドメイン名を使用しているアプリのセキュリティを HTTPS で保護するには、そのドメイン名の証明書を追加します。 Azure の既定では、1 つの SSL 証明書を使用して **\*.azurewebsites.net** というワイルドカード ドメインをセキュリティで保護しているため、クライアントは最初から **https://*&lt;アプリ名>*.azurewebsites.net**のアプリにアクセスできるようになっています。ただし、**contoso.com**、**www.contoso.com**、**\*.contoso.com** のようなカスタム ドメインを使用する場合、既定の証明書ではセキュリティを確保できません。 さらに、すべての[ワイルドカード証明書](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/)がそうであるように、既定の証明書は、カスタム ドメインを使用し、そのカスタム ドメイン用の証明書を使用する場合ほど安全性は高くありません。   

> [!NOTE]
> [Azure フォーラム](https://azure.microsoft.com/support/forums/)では、Azure の専門家からいつでもアドバイスを得ることができます。 個別対応のサポートが必要な場合は、 [Azure のサポート](https://azure.microsoft.com/support/options/) にアクセスし、 **[サポートの要求]**をクリックしてください。
> 
> 

<a name="bkmk_domainname"></a>

## <a name="what-you-need"></a>必要なもの
HTTPS でカスタム ドメイン名をセキュリティで保護するには、Azure でそのカスタム ドメインにカスタム SSL 証明書をバインドします。 カスタム証明書をバインドする前に、次の手順を実行する必要があります。

* **カスタム ドメインを構成する** - App Service では、アプリで既に構成されているドメイン名の証明書のみを追加できます。 手順については、「 [Azure アプリへのカスタム ドメイン名のマッピング](web-sites-custom-domain-name.md)」を参照してください。 
* **Basic レベル以上にスケールアップする** - 低価格層の App Service プランでは、カスタム SSL 証明書がサポートされていません。 手順については、「 [Azure でのアプリのスケールアップ](web-sites-scale.md)」を参照してください。 
* **SSL 証明書を取得する** - まだ取得していない場合は、信頼された [証明機関](http://en.wikipedia.org/wiki/Certificate_authority) (CA) から SSL 証明書を取得する必要があります。 証明書は、次のすべての要件を満たしている必要があります。
  
  * 信頼された CA (プライベート CA サーバーではなく) によって署名されていること。
  * 秘密キーが含まれていること。
  * キー交換用に作成され、.PFX ファイルとしてエクスポートされていること。
  * 2048 ビット以上の暗号化を使用していること。
  * サブジェクト名が、セキュリティで保護する対象のカスタム ドメインと一致すること。 1 つの証明書で複数のドメインを保護するには、ワイルドカード名 (例: **\*.contoso.com**) を使用するか、subjectAltName の値を指定する必要があります。
  * CA で使用されているすべての**[中間証明書](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)**と統合されていること。 そうでない場合、一部のクライアントで再現性のない相互運用性の問題が発生することがあります。
    
    > [!NOTE]
    > すべての要件を満たす SSL 証明書を取得する最も簡単な方法は、 [Azure ポータルで直接購入する](web-sites-purchase-ssl-web-site.md)ことです。 この記事では、それを手動で行う方法と、App Service でカスタム ドメインにバインドする方法を示します。
    > 
    > **楕円曲線暗号 (ECC) 証明書** は、App Service で使用できますが、この記事では説明しません。 ECC 証明書を作成する正確な手順については、CA にお問い合わせください。
    > 
    > 

<a name="bkmk_getcert"></a>

## <a name="step-1-get-an-ssl-certificate"></a>手順 1. SSL 証明書を取得する
さまざまな種類の SSL 証明書が、さまざまな価格で CA から提供されているため、購入する SSL 証明書の種類を決めることからまず始めます。 1 つのドメイン名 (**www.contoso.com**) を保護する場合は、基本的な証明書で十分です。 複数のドメイン名 (**contoso.com**、**www.contoso.com**、 
**mail.contoso.com** のすべて) を保護する場合は、[ワイルドカード証明書](http://en.wikipedia.org/wiki/Wildcard_certificate)、または[サブジェクト代替名](http://en.wikipedia.org/wiki/SubjectAltName) (`subjectAltName`) を使用した証明書が必要です****。

どの SSL 証明書を購入するかが決まったら、証明書署名要求 (CSR) を CA に送信します。 要求した証明書が CA から送信されてきたら、証明書から .pfx ファイルを生成します。 この手順は任意のツールを使用して実行できます。 一般的なツールでの手順は次のとおりです。

* [Certreq.exe を使用した手順](#bkmk_certreq) - 証明書要求を作成するための Windows ユーティリティです。 
  Windows XP または Windows Server 2000 以降の Windows に組み込まれています。
* [IIS マネージャーを使用した手順](#bkmk_iismgr) - 既に使い慣れている場合は、このツールを選んでください。
* [OpenSSL を使用した手順](#bkmk_openssl) - [オープン ソースのクロスプラットフォーム ツール](https://www.openssl.org)です。 このツールを使用すると、任意のプラットフォームから SSL 証明書を取得できます。
* [OpenSSL を使用した subjectAltName 取得の手順](#bkmk_subjectaltname) - `subjectAltName` 証明書を取得するための手順です。

証明書を購入する前に App Service で設定をテストする場合は、 [自己署名証明書](https://en.wikipedia.org/wiki/Self-signed_certificate)を生成することができます。 このチュートリアルでは、それを生成する 2 つの方法を示します。

* [Certreq.exe を使用した自己署名証明書の手順](#bkmk_sscertreq)
* [OpenSSL を使用した自己署名証明書の手順](#bkmk_ssopenssl)

<a name="bkmk_certreq"></a>

### <a name="get-a-certificate-using-certreqexe"></a>Certreq.exe を使用した証明書の取得
1. ファイル (例: **myrequest.txt**) を作成し、そのファイルに次のテキストをコピーして、作業ディレクトリに保存します。 
   プレースホルダー `<your-domain>` はアプリのカスタム ドメイン名に置き換えてください。
   
        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; Required minimum is 2048
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = FALSE
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
   
        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication
   
    CSR に関するオプションの詳細と他の使用可能なオプションについては、 [Certreq のリファレンス ドキュメント](https://technet.microsoft.com/library/dn296456.aspx)を参照してください。
2. コマンド プロンプトで、 `CD` を使用して作業ディレクトリに移動し、次のコマンドを実行して CSR を作成します。
   
        certreq -new myrequest.txt myrequest.csr
   
    **myrequest.csr** が現在の作業ディレクトリに作成されます。
3. **myrequest.csr** を CA に送信して SSL 証明書を取得します。 ファイルをアップロードするか、テキスト エディターから Web フォームにファイルの内容をコピーします。
   
    Microsoft によって信頼された CA の一覧については、「[Microsoft Trusted Root Certificate Program: Participants (Microsoft が信頼するルート証明書プログラム: 参加者)][cas]」を参照してください。
4. CA から証明書 (.CER) ファイルを受け取ったら、作業ディレクトリに保存します。 それから次のコマンドを実行して保留中の CSR を完了します。
   
        certreq -accept -user <certificate-name>.cer
   
    このコマンドで、完成した証明書を Windows 証明書ストアに格納します。
5. CA が中間証明書を使用している場合は、中間証明書をインストールしてから続行します。 通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じて複数の形式で提供されています。 Microsoft IIS 用のバージョンを選択します。
   
    証明書をダウンロードした後、エクスプローラーで各証明書を右クリックし、  **[証明書のインストール]**を選択します。 **証明書のインポート ウィザード**で既定値を使用し、インポートが完了するまで、**[次へ]** のクリックを続けます。
6. 証明書ストアから SSL 証明書をエクスポートするには、`Win`+`R` キーを押して、**certmgr.msc** を実行し証明書マネージャーを起動します。 
   **[Personal (個人設定)]**  >  **[証明書]** を選択します。 **[発行先]** 列に、カスタム ドメイン名に対応するエントリが表示されます。**[発行元]** 列には、証明書を生成するために使用した証明機関が表示されます。
   
    ![証明書マネージャーに関するイメージをここに挿入します][certmgr]
7. 証明書を右クリックし、**[すべてのタスク]**  >  **[エクスポート]** を選択します。 **証明書のエクスポート ウィザード**で、**[次へ]** をクリックしてから **[はい、秘密キーをエクスポートします]** を選択し、**[次へ]** を選択します。
   
    ![秘密キーをエクスポートします][certwiz1]
8. **[個人情報の交換 - PKCS #12]**、**[証明のパスにある証明書を可能であればすべて含む]**、および **[すべての拡張プロパティをエクスポートする]** を選択します。 次に、 **[次へ]**をクリックします。
   
   ![すべての証明書と拡張プロパティを含める][certwiz2]
9. **[パスワード]**をクリックし、パスワードの入力と確認入力を行います。 **[次へ]**をクリックします。
   
   ![パスワードの指定][certwiz3]
10. エクスポートした証明書を格納するパスとファイル名を指定します。ファイル名には **.pfx** という拡張子を付ける必要があります。 **[次へ]** をクリックして完了します。
    
    ![ファイル パスを指定する][certwiz4]

これで、エクスポートした PFX ファイルを App Service にアップロードできるようになります。 「[手順 2.カスタム SSL 証明書のアップロードとバインド](#bkmk_configuressl)」を参照してください。

<a name="bkmk_iismgr"></a>

### <a name="get-a-certificate-using-the-iis-manager"></a>IIS マネージャーを使用した証明書の取得
1. IIS マネージャーを使用して、証明機関に送信する CSR を生成します。 CSR 生成の詳細については、「[Request an Internet Server Certificate (IIS 7) (インターネット サーバー証明書を要求する (IIS 7))][iiscsr]」を参照してください。
2. SSL 証明書を取得するために、CSR を CA に送信します。 Microsoft によって信頼された CA の一覧については、「[Microsoft Trusted Root Certificate Program: Participants (Microsoft が信頼するルート証明書プログラム: 参加者)][cas]」を参照してください。
3. CA から届いた証明書を使用して CSR を完了します。 CSR の完了手順については、「[Install an Internet Server Certificate (IIS 7) (インターネット サーバー証明書をインストールする (IIS 7))][installcertiis]」を参照してください。
4. CA が中間証明書を使用している場合は、中間証明書をインストールしてから続行します。 通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じて複数の形式で提供されています。 Microsoft IIS 用のバージョンを選択します。
   
    証明書をダウンロードした後、エクスプローラーで各証明書を右クリックし、 **[証明書のインストール]**を選択します。 
    **証明書のインポート ウィザード**で既定値を使用し、インポートが完了するまで、**[次へ]** のクリックを続けます。
5. IIS マネージャーから SSL 証明書をエクスポートします。 証明書のエクスポートの詳細については、「[Export a Server Certificate (IIS 7) (サーバー証明書をエクスポートする (IIS 7))][exportcertiis]」を参照してください。 
   
   > [!IMPORTANT]
   > **証明書のエクスポート ウィザード**で、**[はい、秘密キーをエクスポートします]** を選択していることを確認します。  
   > 
   > ![秘密キーをエクスポートします][certwiz1]  
   > 
   > **[個人情報の交換 - PKCS #12]**、**[証明のパスにある証明書を可能であればすべて含む]**、および **[すべての拡張プロパティをエクスポートする]** を選択します。
   > 
   > ![すべての証明書と拡張プロパティを含める][certwiz2]
   > 
   > 

これで、エクスポートした PFX ファイルを App Service にアップロードできるようになります。 「[手順 2.カスタム SSL 証明書のアップロードとバインド](#bkmk_configuressl)」を参照してください。

<a name="bkmk_openssl"></a>

### <a name="get-a-certificate-using-openssl"></a>OpenSSL を使用した証明書の取得
1. コマンド ライン端末で、 `CD` を使用して作業ディレクトリに移動し、次のコマンドを実行して秘密キーと CSR を生成します。
   
        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048
2. メッセージが表示されたら、適切な情報を入力します。 次に例を示します。
   
         Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:
   
        Please enter the following 'extra' attributes to be sent with your certificate request
   
           A challenge password []:
   
    この処理が完了すると、**myserver.key** と **server.csr** という 2 つのファイルが作業ディレクトリに生成されます。 
    **server.csr** には CSR が含まれます。**myserver.key** は後で必要になります。
3. SSL 証明書を取得するために、CSR を CA に送信します。 Microsoft によって信頼された CA の一覧については、「[Microsoft Trusted Root Certificate Program: Participants (Microsoft が信頼するルート証明書プログラム: 参加者)][cas]」を参照してください。
4. 要求した証明書が CA から届いたら、作業ディレクトリの **myserver.crt** という名前のファイルに保存します。 CA からテキスト形式で証明書が提供された場合は、テキスト エディターでその内容を **myserver.crt** にコピーするだけです。 ファイルは次のようになります。
   
        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----
5. コマンド ライン端末で次のコマンドを実行して、**myserver.pfx** を **myserver.key** および **myserver.crt** からエクスポートします。
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    メッセージが表示されたら、.pfx ファイルをセキュリティで保護するパスワードを定義します。
   
   > [!NOTE]
   > CA が中間証明書を使用している場合は、`-certfile` パラメーターを使用して、それらの証明書を含める必要があります。 通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じて複数の形式で提供されています。 拡張子 `.pem` のバージョンを選択します。
   > 
   > `openssl -export` コマンドは次の例のようになります。このコマンドを実行すると、**intermediate-cets.pem** ファイルにある中間証明書を含む .pfx ファイルが作成されます。
   > 
   > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`
   > 
   > 

これで、エクスポートした PFX ファイルを App Service にアップロードできるようになります。 「[手順 2.カスタム SSL 証明書のアップロードとバインド](#bkmk_configuressl)」を参照してください。

<a name="bkmk_subjectaltname"></a>

### <a name="get-a-subjectaltname-certificate-using-openssl"></a>OpenSSL を使用した SubjectAltName 証明書の取得
1. **sancert.cnf**という名前のファイルを作成し、そのファイルに次のテキストをコピーして、作業ディレクトリに保存します。
   
        # -------------- BEGIN custom sancert.cnf -----
        HOME = .
        oid_section = new_oids
        [ new_oids ]
        [ req ]
        default_days = 730
        distinguished_name = req_distinguished_name
        encrypt_key = no
        string_mask = nombstr
        req_extensions = v3_req # Extensions to add to certificate request
        [ req_distinguished_name ]
        countryName = Country Name (2 letter code)
        countryName_default =
        stateOrProvinceName = State or Province Name (full name)
        stateOrProvinceName_default =
        localityName = Locality Name (eg, city)
        localityName_default =
        organizationalUnitName  = Organizational Unit Name (eg, section)
        organizationalUnitName_default  =
        commonName              = Your common name (eg, domain name)
        commonName_default      = www.mydomain.com
        commonName_max = 64
        [ v3_req ]
        subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
        # -------------- END custom sancert.cnf -----
   
    `subjectAltName` で始まる行で、( `commonName` に加えて) セキュリティ保護するすべてのドメイン名に値を置き換えます。 次に例を示します。
   
        subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com
   
    `commonName`を含め、その他のフィールドを変更する必要はありません。 この後のいくつかの手順で、それらを指定するよう求められます。
2. コマンド ライン端末で、 `CD` を使用して作業ディレクトリに移動し、次のコマンドを実行します。
   
        openssl req -sha256 -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf
3. メッセージが表示されたら、適切な情報を入力します。 次に例を示します。
   
         Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
   
    この処理が完了すると、**myserver.key** と **server.csr** という 2 つのファイルが作業ディレクトリに生成されます。 
    **server.csr** には CSR が含まれます。**myserver.key** は後で必要になります。
4. SSL 証明書を取得するために、CSR を CA に送信します。 Microsoft によって信頼された CA の一覧については、「[Microsoft Trusted Root Certificate Program: Participants (Microsoft が信頼するルート証明書プログラム: 参加者)][cas]」を参照してください。
5. 要求した証明書が CA から届いたら、 **myserver.crt**という名前のファイルに保存します。 CA からテキスト形式で証明書が提供された場合は、テキスト エディターでその内容を **myserver.crt** にコピーするだけです。 ファイルは次のようになります。
   
        -----BEGIN CERTIFICATE-----
        MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
        UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
        c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
        NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
        ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
        ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
        enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
        3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
        xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
        ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
        Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
        AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
        Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
        F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
        7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
        lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
        A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
        -----END CERTIFICATE-----
6. コマンド ライン端末で次のコマンドを実行して、**myserver.pfx** を **myserver.key** および **myserver.crt** からエクスポートします。
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    メッセージが表示されたら、.pfx ファイルをセキュリティで保護するパスワードを定義します。
   
   > [!NOTE]
   > CA が中間証明書を使用している場合は、`-certfile` パラメーターを使用して、それらの証明書を含める必要があります。 通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じて複数の形式で提供されています。 拡張子 `.pem` のバージョンを選択します。
   > 
   > `openssl -export` コマンドは次の例のようになります。このコマンドを実行すると、**intermediate-cets.pem** ファイルにある中間証明書を含む .pfx ファイルが作成されます。
   > 
   > `openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem`
   > 
   > 

これで、エクスポートした PFX ファイルを App Service にアップロードできるようになります。 「[手順 2.カスタム SSL 証明書のアップロードとバインド](#bkmk_configuressl)」を参照してください。

<a name="bkmk_sscertreq"></a>

### <a name="generate-a-self-signed-certificate-using-certreqexe"></a>Certreq.exe を使用した自己署名証明書の生成
> [!IMPORTANT]
> 自己署名証明書はテスト目的専用です。 ほとんどのブラウザーは、自己署名証明書で保護されている Web サイトにアクセスしたときにエラーを返します。 ブラウザーによっては、サイトの表示を拒否することもあります。 
> 
> 

1. テキスト ファイル (例: **mycert.txt**) を作成し、そのファイルに次のテキストをコピーして、作業ディレクトリに保存します。 
   プレースホルダー `<your-domain>` はアプリのカスタム ドメイン名に置き換えてください。
   
        [NewRequest]
        Subject = "CN=<your-domain>"  ; E.g. "CN=www.contoso.com", or "CN=*.contoso.com" for a wildcard certificate
        Exportable = TRUE
        KeyLength = 2048              ; KeyLength can be 2048, 4096, 8192, or 16384 (required minimum is 2048)
        KeySpec = 1
        KeyUsage = 0xA0
        MachineKeySet = True
        ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
        ProviderType = 12
        HashAlgorithm = SHA256
        RequestType = Cert            ; Self-signed certificate
        ValidityPeriod = Years
        ValidityPeriodUnits = 1
   
        [EnhancedKeyUsageExtension]
        OID=1.3.6.1.5.5.7.3.1         ; Server Authentication
   
    `RequestType = Cert`は、自己署名証明書を指定する重要なパラメーターです。 
    CSR に関するオプションの詳細と他の使用可能なオプションについては、 [Certreq のリファレンス ドキュメント](https://technet.microsoft.com/library/dn296456.aspx)を参照してください。
2. コマンド プロンプトで、 `CD` を使用して作業ディレクトリに移動し、次のコマンドを実行します。
   
        certreq -new mycert.txt mycert.crt
   
    これで、新しい自己署名証明書が証明書ストアにインストールされました。
3. 証明書ストアから証明書をエクスポートするには、`Win`+`R` キーを押して、**certmgr.msc** を実行して証明書マネージャーを起動します。 
   **[Personal (個人設定)]**  >  **[証明書]** を選択します。 **[発行先]** 列に、カスタム ドメイン名に対応するエントリが表示されます。**[発行元]** 列には、証明書を生成するために使用した証明機関が表示されます。
   
    ![証明書マネージャーに関するイメージをここに挿入します][certmgr]
4. 証明書を右クリックし、**[すべてのタスク]**  >  **[エクスポート]** を選択します。 **証明書のエクスポート ウィザード**で、**[次へ]** をクリックしてから **[はい、秘密キーをエクスポートします]** を選択し、**[次へ]** を選択します。
   
    ![秘密キーをエクスポートします][certwiz1]
5. **[個人情報の交換 - PKCS #12]**、**[証明のパスにある証明書を可能であればすべて含む]**、および **[すべての拡張プロパティをエクスポートする]** を選択します。 次に、 **[次へ]**をクリックします。
   
   ![すべての証明書と拡張プロパティを含める][certwiz2]
6. **[パスワード]**をクリックし、パスワードの入力と確認入力を行います。 **[次へ]**をクリックします。
   
   ![パスワードの指定][certwiz3]
7. エクスポートした証明書を格納するパスとファイル名を指定します。ファイル名には **.pfx** という拡張子を付ける必要があります。 **[次へ]** をクリックして完了します。
   
   ![ファイル パスを指定する][certwiz4]

これで、エクスポートした PFX ファイルを App Service にアップロードできるようになります。 「[手順 2.カスタム SSL 証明書のアップロードとバインド](#bkmk_configuressl)」を参照してください。

<a name="bkmk_ssopenssl"></a>

### <a name="generate-a-self-signed-certificate-using-openssl"></a>OpenSSL を使用した自己署名証明書の生成
> [!IMPORTANT]
> 自己署名証明書はテスト目的専用です。 ほとんどのブラウザーは、自己署名証明書で保護されている Web サイトにアクセスしたときにエラーを返します。 ブラウザーによっては、サイトの表示を拒否することもあります。 
> 
> 

1. **serverauth.cnf**という名前のテキスト ファイルを作成し、そのファイルに次の内容をコピーして、作業ディレクトリに保存します。
   
        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca
   
        [ req_distinguished_name ]
        countryName            = Country Name (2 letter code)
        countryName_min            = 2
        countryName_max            = 2
        stateOrProvinceName        = State or Province Name (full name)
        localityName            = Locality Name (eg, city)
        0.organizationName        = Organization Name (eg, company)
        organizationalUnitName        = Organizational Unit Name (eg, section)
        commonName            = Common Name (eg, your app's domain name)
        commonName_max            = 64
        emailAddress            = Email Address
        emailAddress_max        = 40
   
        [ req_attributes ]
        challengePassword        = A challenge password
        challengePassword_min        = 4
        challengePassword_max        = 20
   
        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth
2. コマンド ライン端末で、 `CD` を使用して作業ディレクトリに移動し、次のコマンドを実行します。
   
        openssl req -sha256 -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf
   
    このコマンドを実行すると、**myserver.crt** (自己署名証明書) および **myserver.key** (秘密キー) という 2 つのファイルが、**serverauth.cnf** の設定に基づいて作成されます。
3. 次のコマンドを実行して証明書を .pfx ファイルにエクスポートします。
   
        openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
   
    メッセージが表示されたら、.pfx ファイルをセキュリティで保護するパスワードを定義します。

これで、エクスポートした PFX ファイルを App Service にアップロードできるようになります。 「[手順 2.カスタム SSL 証明書のアップロードとバインド](#bkmk_configuressl)」を参照してください。

<a name="bkmk_configuressl"></a>

## <a name="step-2-upload-and-bind-the-custom-ssl-certificate"></a>手順 2. カスタム SSL 証明書のアップロードとバインド
先に進む前に、「 [必要なもの](#bkmk_domainname) 」セクションを見直し、次のことを確認します。

* Azure アプリにマップするカスタム ドメインがある。
* アプリが **Basic** レベル以上で実行されている。
* CA から発行されたカスタム ドメインの SSL 証明書がある。

1. ブラウザーで、**[Azure Portal ](https://portal.azure.com/)** を開きます。
2. ページの左側にある **[App Service]** オプションをクリックします。
3. この証明書を割り当てるアプリの名前をクリックします。 
4. **[設定]**で、**[SSL 証明書]** をクリックします。
5. **[証明書のアップロード]**
6. [手順 1](#bkmk_getcert) でエクスポートした .pfx ファイルを選択し、以前に作成したパスワードを指定します。 
   次に、 **[アップロード]** をクリックして、証明書をアップロードします。 アップロードした証明書が **[SSL certificate (SSL 証明書)]** ブレードに表示されます。
7. **[SSL バインド]** セクションで、**[Add bindings (バインドの追加)]** をクリックします。
8. **[Add SSL Binding (SSL バインドの追加)]** ブレードで、ドロップダウン リストから SSL でセキュリティ保護するドメイン名、および使用する証明書を選択します。 また、**[Server Name Indication (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)**または IP ベースの SSL のどちらを使用するかを選択できます。
   
    ![SSL バインドのイメージを挿入](./media/web-sites-configure-ssl-certificate/sslbindings.png)
   
    > [!NOTE] 
    > **IP ベースの SSL** は、サーバーの専用パブリック IP アドレスをドメイン名にマッピングすることによって、証明書をドメイン名に関連付けします。 これは、サービスに関連付けられている各ドメイン名 (contoso.com、fabricam.com など) の専用の IP アドレスが必要となります。 これは SSL 証明書と Web サーバーを関連付ける従来の方式です。  
    >
    > **SNI ベースの SSL** は、SSL と**[トランスポート層セキュリティ](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) の拡張機能です。TLS では、複数のドメインが同じ IP アドレスを共有し、各ドメインが独自のセキュリティ証明書を持つことができます。 最新のブラウザー (Internet Explorer、Chrome、Firefox、Opera を含む) のほとんどが SNI をサポートしていますが、古いブラウザーには、SNI をサポートしていないものもあります。 SNI について詳しくは、Wikipedia の **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** に関する記事をご覧ください。
    > 

9. 変更を保存して SSL を有効にするには、 **[Add Binding (バインドの追加)]** をクリックします。

## <a name="step-3-change-your-domain-name-mapping-ip-based-ssl-only"></a>手順 3. ドメイン名マッピングの変更 (IP ベースの SSL のみ)
**SNI SSL** バインドを使用する場合にのみ、このセクションをスキップしてください。 アプリに割り当てられている既存の共有 IP アドレスで複数の **SNI SSL** バインドを使用できます。 ただし、**IP ベースの SSL** バインドを作成した場合、App Service はバインドのための専用 IP アドレスを作成します (**IP ベースの SSL** で必要なため)。 専用 IP アドレスは 1 つだけ作成できるので、 **IP ベースの SSL** バインドも 1 つだけ追加できます。

この専用 IP アドレスのために、次の場合はアプリの追加構成が必要です。

* [A レコードを使用してカスタム ドメインを Azure アプリにマッピング](web-sites-custom-domain-name.md#a) 済みで、 **IP ベースの SSL** バインドを追加した場合。 このシナリオでは、次の手順を実行して、専用 IP アドレスを参照するように既存の A レコードをマッピングし直す必要があります。
  
  1. IP ベースの SSL バインドを構成すると、専用の IP アドレスがアプリに割り当てられます。 この IP アドレスは、アプリの設定の **[カスタム ドメイン]** ページで確認できます。これは、**[ホスト名]** セクションの上にあります。 このアドレスは、**[外部 IP アドレス]** として示されます。
     
      ![Virtual IP address](./media/web-sites-configure-ssl-certificate/virtual-ip-address.png)
  2. [カスタム ドメイン名の A レコードをこの新しい IP アドレスにマッピングし直します](web-sites-custom-domain-name.md#a)。
* 既に 1 つ以上の **SNI SSL** バインドがアプリに存在し、**IP ベースの SSL** バインドを追加した場合。 
  バインドが完了すると、*&lt;アプリ名>*.azurewebsites.net ドメイン名は新しい IP アドレスを参照するようになります。 
  そのため、既存の [CNAME マッピング](web-sites-custom-domain-name.md#cname) (カスタム ドメインから *&lt;アプリ名>*.azurewebsites.net へのマッピング) は、**SNI SSL** で保護されているものも含めて、**IP ベースの SSL** 専用に作成された新しいアドレスでトラフィックを受信します。 このシナリオでは、次の手順に従って、 **SNI SSL** トラフィックを元の共有 IP アドレスに送り返す必要があります。
  
  1. アプリに対する [カスタム ドメインの CNAME マッピング](web-sites-custom-domain-name.md#cname) で、 **SNI SSL** バインドを使用するマッピングをすべて識別します。
  2. 各 CNAME レコードを &lt;アプリ名>.azurewebsites.net ではなく **sni.**&lt;アプリ名>.azurewebsites.net にマッピングし直します。

## <a name="step-4-test-https-for-your-custom-domain"></a>手順 4. カスタム ドメインに対して HTTPS をテストする
この時点で残っている作業は、HTTPS がカスタム ドメインで機能するかどうかを確認することだけです。 さまざまなブラウザーで `https://<your.custom.domain>` にアクセスし、アプリの要求を処理できることを確認します。

* アプリで証明書検証エラーが返された場合は、自己署名証明書を使用している可能性があります。
* そうでない場合は、.pfx 証明書をエクスポートするときに中間証明書を含めなかった可能性があります。 「 [必要なもの](#bkmk_domainname) 」に戻り、CSR が App Service のすべての要件を満たしていることを確認します。

<a name="bkmk_enforce"></a>

## <a name="enforce-https-on-your-app"></a>アプリに HTTPS を適用する
HTTP によるアプリへのアクセスを許可する場合は、この手順をスキップしてください。 App Service では HTTPS の使用が強制されないため、訪問者は引き続き HTTP を使用してアプリにアクセスできます**。 アプリで HTTPS を強制する場合は、アプリの `web.config` ファイルで書き換え規則を定義することができます。 このファイルは、アプリの言語フレームワークに関係なく、すべての App Service アプリに存在します。

> [!NOTE]
> 言語に固有の要求のリダイレクトがあります。 ASP.NET MVC では、`web.config` 内の書き換え規則の代わりに [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) フィルターを使用できます ([セキュリティで保護された ASP.NET MVC 5 アプリを Web アプリにデプロイする方法](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)に関するページを参照してください)。
> 
> 

次の手順に従います。

1. アプリの Kudu デバッグ コンソールに移動します。 アドレスは `https://<appname>.scm.azurewebsites.net/DebugConsole`です。
2. デバッグ コンソールで、CD を使用して `D:\home\site\wwwroot`に移動します。
3. 鉛筆のアイコンをクリックして `web.config` を開きます。
   
    ![](./media/web-sites-configure-ssl-certificate/openwebconfig.png)
   
    Visual Studio または Git を使用してアプリをデプロイする場合、App Service は .NET、PHP、Node.js、または Python アプリ用の適切な `web.config` をアプリケーションのルートに自動的に作成します。 
    `web.config` がない場合は、Web ベースのコマンド プロンプトで `touch web.config` を実行して作成します。 または、ローカル プロジェクトでこのファイルを作成し、コードを再デプロイすることができます。
4. `web.config`を作成する必要がある場合は、次のコードをコピーし、ファイルを保存します。 既存の web.config を開いた場合は、`<rule>` タグ全体を `web.config` の `configuration/system.webServer/rewrite/rules` 要素にコピーするだけで済みます。
   
        <?xml version="1.0" encoding="UTF-8"?>
        <configuration>
          <system.webServer>
            <rewrite>
              <rules>
                <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
                <rule name="Force HTTPS" enabled="true">
                  <match url="(.*)" ignoreCase="false" />
                  <conditions>
                    <add input="{HTTPS}" pattern="off" />
                  </conditions>
                  <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
                </rule>
                <!-- END rule TAG FOR HTTPS REDIRECT -->
              </rules>
            </rewrite>
          </system.webServer>
        </configuration>
   
    この規則は、ユーザーが HTTP を使用しているページを要求したときに、HTTPS プロトコルに HTTP 301 (永続的リダイレクト) を返します。 これにより、http://contoso.com から https://contoso.com にリダイレクトされます。
   
   > [!IMPORTANT]
   > 既に他の `<rule>` タグが `web.config` 内にある場合は、コピーした `<rule>` タグを他の `<rule>` タグの前に配置します。
   > 
   > 
5. Kudu デバッグ コンソールでファイルを保存します。 ファイルはすぐに有効になり、すべての要求を HTTPS にリダイレクトします。

IIS URL 書き換えモジュールの詳細については、 [URL 書き換え](http://www.iis.net/downloads/microsoft/url-rewrite) のドキュメントを参照してください。

## <a name="more-resources"></a>その他のリソース
* [Microsoft Azure のトラスト センター](/support/trust-center/security/)
* [Azure Web Sites でロックを解除された構成オプション](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
* [診断ログの有効化](web-sites-enable-diagnostic-log.md)
* [Azure App Service での Web アプリの構成](web-sites-configure.md)
* [Azure 管理ポータル](https://manage.windowsazure.com)

> [!NOTE]
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、[App Service の試用](https://azure.microsoft.com/try/app-service/)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

[customdomain]: web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://social.technet.microsoft.com/wiki/contents/articles/31634.microsoft-trusted-root-certificate-program-participants-v-2016-april.aspx
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/web-sites-configure-ssl-certificate/staticip.png
[website]: ./media/web-sites-configure-ssl-certificate/sslwebsite.png
[scale]: ./media/web-sites-configure-ssl-certificate/sslscale.png
[standard]: ./media/web-sites-configure-ssl-certificate/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/web-sites-configure-ssl-certificate/sslconfig.png
[uploadcert]: ./media/web-sites-configure-ssl-certificate/ssluploadcert.png
[uploadcertdlg]: ./media/web-sites-configure-ssl-certificate/ssluploaddlg.png
[sslbindings]: ./media/web-sites-configure-ssl-certificate/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/web-sites-configure-ssl-certificate/waws-certmgr.png
[certwiz1]: ./media/web-sites-configure-ssl-certificate/waws-certwiz1.png
[certwiz2]: ./media/web-sites-configure-ssl-certificate/waws-certwiz2.png
[certwiz3]: ./media/web-sites-configure-ssl-certificate/waws-certwiz3.png
[certwiz4]: ./media/web-sites-configure-ssl-certificate/waws-certwiz4.png



