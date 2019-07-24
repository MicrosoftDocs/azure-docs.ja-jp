---
title: チュートリアル - Azure CDN カスタム ドメインで HTTPS を構成する | Microsoft Docs
description: このチュートリアルでは、Azure CDN エンドポイント カスタム ドメインで HTTPS を有効および無効にする方法について説明します。
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/18/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 1ebac5476c90b3cb49fccbb95ef8dedf413a6127
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200295"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>チュートリアル:Azure CDN カスタム ドメインで HTTPS を構成する

このチュートリアルでは、Azure CDN エンドポイントに関連付けられたカスタム ドメインの HTTPS プロトコルを有効にする方法について説明します。 カスタム ドメイン (例: https:\//www.contoso.com) で HTTPS プロトコルを使用すると、インターネット経由での送信時、機微なデータが TLS/SSL 暗号化でセキュリティ保護されて配信されます。 Web ブラウザーが HTTPS 経由で Web サイトに接続しているときに、Web サイトのセキュリティ証明書を検証し、正当な証明機関によって発行されていることを確認します。 このプロセスによりセキュリティを確保し、Web アプリケーションを攻撃から保護します。

既定では、Azure CDN は、CDN エンドポイント ホスト名で HTTPS をサポートしています。 たとえば、CDN エンドポイント (例: https:\//contoso.azureedge.net) を作成すると、HTTPS が自動的に有効になります。  

カスタム HTTPS の機能の主な特性は次のとおりです。

- 追加コストなし: 証明書の取得または更新のコストや、HTTPS トラフィックの追加コストは発生しません。 CDN からの GB 送信のみ課金されます。

- シンプルな有効化: [Azure portal](https://portal.azure.com) からワン クリックのプロビジョニングを利用できます。 REST API やその他の開発者ツールを使用して機能を有効にすることもできます。

- 証明書の完全な管理: すべての証明書の調達と管理がユーザーに代わって実施されます。 証明書は自動的にプロビジョニングされ、有効期限になる前に更新されます。これにより、証明書の期限切れによりサービスが中断されるリスクがなくなります。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - カスタム ドメインで HTTPS プロトコルを有効にする。
> - CDN で管理された証明書を使用する 
> - 独自の証明書を使用する
> - ドメインを検証する
> - カスタム ドメインで HTTPS プロトコルを無効にする。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

このチュートリアルの手順を完了するには、最初に CDN プロファイルと少なくとも 1 つの CDN エンドポイントを作成する必要があります。 詳細については、「[クイック スタート: Azure CDN プロファイルとエンドポイントの作成](cdn-create-new-endpoint.md)」を参照してください。

さらに、Azure CDN カスタム ドメインを CDN エンドポイントに関連付ける必要があります。 詳細については、[チュートリアル: カスタム ドメインを Azure CDN エンドポイントに追加する](cdn-map-content-to-custom-domain.md)

---

## <a name="ssl-certificates"></a>SSL 証明書の数
Azure CDN カスタム ドメインでコンテンツを安全に配信するために HTTPS プロトコルを有効にするには、SSL 証明書を使用する必要があります。 Azure CDN で管理された証明書または独自の証明書を使用できます。


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificatetaboption-1-default-enable-https-with-a-cdn-managed-certificate"></a>[オプション 1 (既定): CDN で管理された証明書を使用して HTTPS を有効にする](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

CDN で管理された証明書を使用する場合、HTTPS 機能は、数回クリックするだけで有効にできます。 Azure CDN は、調達や更新などの証明書管理タスクを完全に処理します。 この機能を有効にすると、プロセスがすぐに開始します。 カスタム ドメインが既に CDN エンドポイントにマップされている場合、これ以上のアクションは必要ありません。 Azure CDN によって手順が処理され、要求が自動的に完了します。 一方、カスタム ドメインが別の場所でマップされている場合は、電子メールを使用してドメインの所有権を検証する必要があります。

カスタム ドメインで HTTPS を有効にするには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) で、お使いの **Azure CDN Standard from Microsoft**、**Azure CDN Standard from Akamai**、**Azure CDN Standard from Verizon**、**Azure CDN Premium from Verizon** のいずれかのプロファイルを参照します。

2. CDN エンドポイントの一覧で、カスタム ドメインが含まれているエンドポイントを選択します。

    ![エンドポイントの一覧](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    **[エンドポイント]** ページが表示されます。

3. カスタム ドメインの一覧で、HTTPS を有効にするカスタム ドメインを選択します。

    ![カスタム ドメイン リスト](./media/cdn-custom-ssl/cdn-custom-domain.png)

    **[カスタム ドメイン]** ページが表示されます。

4. [証明書の管理の種類] で、**[CDN 管理]** を選択します。

5. **[オン]** を選択して HTTPS を有効にします。

    ![カスタム ドメイン HTTPS ステータス](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

6. [ドメインの検証](#validate-the-domain)に進みます。


# <a name="option-2-enable-https-with-your-own-certificatetaboption-2-enable-https-with-your-own-certificate"></a>[オプション 2:独自の証明書を使用して HTTPS を有効にする](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> このオプションは、**Azure CDN Standard from Microsoft** プロファイルでのみ利用できます。 
>
 
独自の証明書を使用して、HTTPS 機能を有効にできます。 このプロセスは、Azure Key Vault との統合を通じて行われます。これにより、お使いの証明書を安全に格納できます。 Azure CDN では、お使いの証明書の取得に、セキュリティで保護されたこのメカニズムが使用されます。それには、追加の手順がいくつか必要です。 SSL 証明書を作成するときには、許可された証明書機関 (CA) を使用して作成する必要があります。 許可されていない CA を使用すると、要求が拒否されます。 許可された CA の一覧については、「[Allowed certificate authorities for enabling custom HTTPS on Azure CDN](cdn-troubleshoot-allowed-ca.md)」(Azure CDN でカスタム HTTPS を有効にするために許可された認証機関) を参照してください。

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure Key Vault のアカウントと証明書を準備する
 
1. Azure Key Vault: カスタム HTTPS を有効にする Azure CDN プロファイルおよび CDN エンドポイントと同じサブスクリプションで、Azure Key Vault アカウントを実行している必要があります。 Azure Key Vault アカウントがない場合は作成します。
 
2. Azure Key Vault 証明書: 証明書が既にある場合は、Azure Key Vault アカウントに直接アップロードできます。または、Azure Key Vault と統合されているパートナー CA の 1 つから、Azure Key Vault を使用して新しい証明書を直接作成できます。 

### <a name="register-azure-cdn"></a>Azure CDN を登録する

PowerShell を使用して、Azure Active Directory に Azure CDN をアプリとして登録します。

1. 必要があれば、ローカル コンピューターに [Azure PowerShell](/powershell/azure/install-az-ps) をインストールします。

2. PowerShell で次のコマンドを実行します。

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![PowerShell で Azure CDN を登録する](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Azure CDN にお使いのキー コンテナーへのアクセス権を付与する
 
Azure Key Vault アカウント内の証明書 (シークレット) にアクセスするには、Azure CDN のアクセス許可を付与します。

1. キー コンテナー アカウントで、[設定] で **[アクセス ポリシー]**、**[新規追加]** の順に選択して新しいポリシーを作成します。

    ![新しいアクセス ポリシーの作成](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. **[プリンシパルの選択]** で、**205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** を検索し、**[Microsoft.Azure.Cdn]** を選択します。 **[選択]** をクリックします。

    ![アクセス ポリシーの設定](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. **[シークレットのアクセス許可]** で、**[取得]** を選択して、CDN がこれらのアクセス許可を実行して証明書を取得し、一覧表示できるようにします。 

4. **[OK]** を選択します。 

    Azure CDN は、このキー コンテナーと、このキー コンテナーに格納されている証明書 (シークレット) にアクセスできるようになりました。
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>デプロイする Azure CDN の証明書を選択する
 
1. Azure CDN ポータルに戻り、プロファイルおよびカスタム HTTPS を有効にする CDN エンドポイントを選択します。 

2. カスタム ドメインの一覧で、HTTPS を有効にするカスタム ドメインを選択します。

    **[カスタム ドメイン]** ページが表示されます。

3. [証明書の管理の種類] で、**[Use my own certificate]\(独自の証明書を使用する\)** を選択します。 

    ![証明書の構成](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. キー コンテナー、証明書 (シークレット)、証明書のバージョンを選択します。

    Azure CDN に次の情報が一覧表示されます。 
    - サブスクリプション ID に対するキー コンテナー アカウント。 
    - 選択したキー コンテナーの下の証明書 (シークレット)。 
    - 利用可能な証明書バージョン。 
 
5. **[オン]** を選択して HTTPS を有効にします。
  
6. 独自の証明書を使用する場合には、ドメインの検証は必要ありません。 「[伝達を待機する](#wait-for-propagation)」に進んでください。

---

## <a name="validate-the-domain"></a>ドメインを検証する

CNAME レコードでカスタム エンドポイントにマップされた使用中のカスタム ドメインが既にある場合、または独自の証明書を使用している場合には、  
「[カスタム ドメインが CNAME レコードによって CDN エンドポイントにマップされている](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record)」に進んでください。 そうではなく、エンドポイントの CNAME レコード エントリがもう存在しない場合、または CNAME レコード エントリに cdnverify サブドメインが含まれている場合は、「[カスタム ドメインが CDN エンドポイントにマップされていない](#custom-domain-is-not-mapped-to-your-cdn-endpoint)」に進んでください。

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>カスタム ドメインが CNAME レコードによって CDN エンドポイントにマップされている

カスタム ドメインをエンドポイントに追加するときに、CDN エンドポイントのホスト名にマップする、ドメイン レジストラーの DNS テーブルに CNAME レコードを作成しました。 この CNAME レコードがまだ存在し、そこに cdnverify サブドメインが含まれていない場合は、DigiCert CA は、この CNAME レコードを使用して自動でカスタム ドメインの所有権を検証します。 

独自の証明書を使用している場合には、ドメインの検証は必要ありません。

CNAME レコードは、次の形式にする必要があります。ここで *Name* がカスタム ドメイン名で、*Value* が CDN エンドポイントのホスト名です。

| Name            | type  | 値                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

CNAME レコードの詳細については、[CNAME DNS レコードの作成](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain)に関するセクションを参照してください。

CNAME レコードが正しい形式である場合、DigiCert は自動的にそのカスタム ドメイン名を検証し、ご利用のドメイン名に使用する専用の証明書を作成します。 DigiCert から検証電子メールが送信されないため、要求を承認する必要はありません。 この証明書は 1 年間有効で、有効期限が切れる前に自動更新されます。 「[伝達を待機する](#wait-for-propagation)」に進んでください。 

自動検証には通常、数分かかります。 1 時間以内にドメインが検証されない場合、サポート チケットを開いてください。

>[!NOTE]
>Certificate Authority Authorization (CAA) レコードに DNS プロバイダーが登録されている場合、そこには有効な CA として DigiCert が含まれている必要があります。 ドメイン所有者は CAA レコードで、その DNS プロバイダーとともに、ドメインの証明書を発行する権限のある CA を指定できます。 CA は、ドメインの証明書の依頼を受け取っても、そのドメインに CAA レコードがあり、そこに認証された発行者としてその CA がリストされていない場合は、そのドメインまたはサブドメインへの証明書の発行が禁じられます。 CAA レコードの管理については、「[Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/)」(CAA レコードを管理する) をご覧ください。 CAA レコード ツールについては、「[CAA Record Helper](https://sslmate.com/caa/)」(CAA レコード ヘルパー) をご覧ください。

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>カスタム ドメインが CDN エンドポイントにマップされていない

エンドポイントの CNAME レコード エントリがもう存在しない場合や、CNAME レコード エントリに cdnverify サブドメインが含まれている場合は、この手順の残りの部分に従ってください。

>[!NOTE]
>カスタム ドメイン所有権のメール検証は現在、**Azure CDN from Akamai** のプロファイルでは利用できません。 現在、この機能は未着手となっています。 

カスタム ドメインの HTTPS を有効にする要求が送信されると、DigiCert CA は、ドメインの [WHOIS](http://whois.domaintools.com/) 登録者情報に従って、ドメインの登録者に連絡し、ドメインの所有権を検証します。 連絡は、WHOIS に登録されているメール アドレス (既定) または電話番号で行われます。 HTTPS をカスタム ドメイン上でアクティブにする前に、ドメインの検証を完了する必要があります。 ドメインの承認には 6 営業日が必要です。 6 営業日以内に承認されない要求は、自動的に取り消されます。 

![WHOIS レコード](./media/cdn-custom-ssl/whois-record.png)

DigiCert は、その他のメール アドレスに確認メールも送信します。 WHOIS 登録者情報がプライベートである場合は、次のアドレスのいずれかから直接承認できることを確認してください。

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

数分以内に、要求の承認を求める次の例のようなメールを受け取ります。 スパム フィルターを使っている場合は、admin@digicert.com をホワイトリストに追加してください。 24 時間以内にメールが届かない場合は、Microsoft のサポートに問い合わせてください。
    
![ドメイン検証メール](./media/cdn-custom-ssl/domain-validation-email.png)

承認リンクをクリックすると、次のオンライン承認フォームが表示されます。 
    
![ドメイン検証フォーム](./media/cdn-custom-ssl/domain-validation-form.png)

フォームの指示に従います。2 つの検証オプションがあります。

- contoso.com などの同じルート ドメインに対して同じアカウントを使って、今後行われるすべての依頼を承認することができます。 同じルート ドメインのカスタム ドメインを追加する予定の場合は、このアプローチを使用することをお勧めします。

- この要求で使われる特定のホスト名のみを承認できます。 その後の要求では追加の承認が必要になります。

承認後、カスタム ドメイン名に使用される証明書の作成が完了します。 この証明書は 1 年間有効で、有効期限が切れる前に自動更新されます。

## <a name="wait-for-propagation"></a>伝達を待機する

ドメイン名の検証後、カスタム ドメインの HTTPS 機能がアクティブになるまでに最大 6 ～ 8 時間がかかります。 プロセスが完了すると、Azure Portal の [カスタム HTTPS] のステータスは **[有効]** に設定され、[カスタム ドメイン] ダイアログの ４つの操作ステップが完了としてマークされます。 カスタム ドメインは、HTTPS を使う準備ができました。

![HTTPS ダイアログを有効にする](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>操作の進行

次の表は、HTTPS を有効にするときの操作の進行を示したものです。 HTTPS を有効にした後、[カスタム ドメイン] ダイアログには 4 つの操作ステップが表示されます。 各ステップがアクティブになると、進行状況に合わせてステップの下にサブステップの詳細が追加表示されます。 これらのサブステップのすべてが発生するわけではありません。 ステップが正常に完了すると、横に緑色のチェック マークが表示されます。 

| 操作ステップ | 操作サブステップの詳細 | 
| --- | --- |
| 1 要求の送信 | 要求を送信しています |
| | HTTPS 要求を送信しています。 |
| | HTTPS 要求を正常に送信しました。 |
| 2 ドメインの検証 | CNAME で CDN エンドポイントにマップされている場合、ドメインは自動的に検証されます。 そうでない場合、登録レコードに記載されているメール アドレス (WHOIS 登録者) に、検証要求が送信されます。 できるだけ早く、ドメインを検証してください。 |
| | ドメインの所有権が正常に検証されました。 |
| | ドメインの所有権の検証要求が期限切れになりました (お客様から 6 日以内に返答がなかったようです)。 ドメインで HTTPS が有効になることはありません。 * |
| | ドメインの所有権の検証要求が、お客様により拒否されました。 ドメインで HTTPS が有効になることはありません。 * |
| 3 証明書のプロビジョニング | 証明機関は現在、ドメインで HTTPS を有効にする際に必要な証明書の発行処理を進めています。 |
| | 証明書の発行が完了しました。現在、証明書を CDN ネットワークにデプロイしています。 これには最大 6 時間かかることがあります。 |
| | CDN ネットワークに証明書をデプロイしました。 |
| 4 完了 | ドメインで HTTPS を有効にしました。 |

\* このメッセージは、エラーが発生しない限り表示されません。 

要求送信前にエラーが発生した場合は、次のエラー メッセージが表示されます。

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>リソースのクリーンアップ - HTTPS を無効にする

上記の手順では、カスタム ドメインで HTTPS プロトコルを有効にしました。 カスタム ドメインで HTTPS を使用する必要がなくなった場合、次の手順を実行して HTTPS を無効にできます。

### <a name="disable-the-https-feature"></a>HTTPS 機能を無効にする 

1. [Azure Portal](https://portal.azure.com) で、お使いの **Azure CDN Standard from Microsoft**、**Azure CDN Standard from Verizon** または **Azure CDN Premium from Verizon** プロファイルを参照します。

2. エンドポイントの一覧で、カスタム ドメインを含むエンドポイントをクリックします。

3. HTTPS を無効にするカスタム ドメインをクリックします。

    ![カスタム ドメイン リスト](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. **[オフ]** をクリックして HTTPS を無効にした後、**[適用]** をクリックします。

    ![カスタム HTTPS ダイアログ](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>伝達を待機する

カスタム ドメインの HTTPS 機能を無効にした後、適用されるまでには最大 6 から 8 時間かかります。 プロセスが完了すると、Azure Portal の [カスタム HTTPS] のステータスは **[無効]** に設定され、[カスタム ドメイン] ダイアログの 3つの操作ステップが完了としてマークされます。 カスタム ドメインは HTTPS を使うことができなくなります。

![[HTTPS の無効化] ダイアログ](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>操作の進行

次の表は、HTTPS を無効にするときの操作の進行を示したものです。 HTTPS を無効にした後、[カスタム ドメイン] ダイアログには 3 つの操作ステップが表示されます。 各ステップがアクティブになると、ステップの下に詳細が追加表示されます。 ステップが正常に完了すると、横に緑色のチェック マークが表示されます。 

| 操作の進行 | 操作の詳細 | 
| --- | --- |
| 1 要求の送信 | 要求を送信しています |
| 2 証明書のプロビジョニング解除 | 証明書を削除しています |
| 3 完了 | 証明書が削除されました |

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. *証明書プロバイダーはだれですか。どのような種類の証明書が使用されますか。*

    **Azure CDN from Verizon** と **Azure CDN from Microsoft** のどちらの場合も、カスタム ドメインには、Digicert から提供される専用かつ単一の証明書が使用されます。 

2. *IP ベースと SNI のどちらの TLS/SSL を使用しますか。*

    **Azure CDN from Verizon** と **Azure CDN Standard from Microsoft** のどちらも、SNI TLS/SSL が使用されます。

3. *DigiCert からドメインの検証電子メールが送られて来ない場合はどうすればよいでしょうか。*

    エンドポイントのホスト名を直接指す、カスタム ドメインの CNAME エントリがある場合 (かつ、cdnverify サブドメイン名を使用していない場合)、ドメインの検証電子メールは送られてきません。 検証は自動的に行われます。 そうではなく、CNAME エントリがないうえに 24 時間以内にメールが届かなかった場合、Microsoft のサポートに問い合わせてください。

4. *SAN 証明書を使用すると専用証明書の場合よりも安全性が低くなるでしょうか。*
    
    SAN 証明書は、専用証明書と同じ暗号化およびセキュリティ標準に従っています。 発行されるすべての SSL 証明書に SHA 256 を使用して、サーバーのセキュリティが強化されています。

5. *DNS プロバイダーに Certificate Authority Authorization レコードが必要ですか。*

    いいえ、Certificate Authority Authorization レコードは、現在必要ではありません。 ただし、所持している場合は、そこには有効な CA として DigiCert が含められている必要があります。

6. *2018 年 6 月 20 日以降、Verizon の Azure CDN で、SNI TLS/SSL による専用証明書が既定で使用されます。SAN (Subject Alternative Name: サブジェクトの別名) 証明書と IP ベースの TLS/SSL を使用している既存のカスタム ドメインはどうなるのでしょうか。*

    Microsoft の分析の結果、アプリケーションに対する要求が SNI クライアント要求だけであることがわかった場合、既にあるドメインは今後数か月内に単一の証明書へと徐々に移行されます。 アプリケーションに対する非 SNI クライアント要求が Microsoft によって検出された場合、ご利用のドメインは、IP ベースの TLS/SSL による SAN 証明書のまま維持されます。 どちらの場合でも、クライアントの要求に対するサービスやサポートは、それらの要求が SNI か非 SNI かにかかわらず中断されません。


## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> - カスタム ドメインで HTTPS プロトコルを有効にする。
> - CDN で管理された証明書を使用する 
> - 独自の証明書を使用する
> - ドメインを検証する。
> - カスタム ドメインで HTTPS プロトコルを無効にする。

次のチュートリアルに進み、CDN エンドポイントでキャッシュを構成する方法を学習してください。

> [!div class="nextstepaction"]
> [チュートリアル:Azure CDN キャッシュ規則の設定](cdn-caching-rules-tutorial.md)

