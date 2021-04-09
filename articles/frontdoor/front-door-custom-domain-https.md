---
title: チュートリアル - Azure Front Door 用のカスタム ドメインで HTTPS を構成する | Microsoft Docs
description: このチュートリアルでは、カスタム ドメイン用の Azure Front Door 構成で HTTPS を有効および無効にする方法について説明します。
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: duau
ms.openlocfilehash: 17677ea89b04659de66b9bda35975b96ff33473a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740785"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>チュートリアル:Front Door カスタム ドメインで HTTPS を構成する

このチュートリアルでは、フロントエンド ホスト セクションで Front Door に関連付けられたカスタム ドメインの HTTPS プロトコルを有効にする方法について説明します。 カスタム ドメイン (例: https:\//www.contoso.com) で HTTPS プロトコルを使用すると、インターネット経由での送信時、機微なデータが TLS/SSL 暗号化でセキュリティ保護されて配信されます。 Web ブラウザーが HTTPS 経由で Web サイトに接続しているときに、Web サイトのセキュリティ証明書を検証し、正当な証明機関によって発行されていることを確認します。 このプロセスによりセキュリティを確保し、Web アプリケーションを攻撃から保護します。

Azure Front Door では、既定で、Front Door の既定のホスト名の HTTPS がサポートされます。 たとえば、Front Door (例: `https://contoso.azurefd.net`) を作成すると、`https://contoso.azurefd.net` に対して行われた要求で HTTPS が自動的に有効になります。 しかし、カスタム ドメイン 'www.contoso.com' をオンボードした場合、このフロントエンド ホストでも HTTPS を有効にする必要があります。   

カスタム HTTPS の機能の主な特性は次のとおりです。

- 追加コストなし: 証明書の取得または更新のコストや、HTTPS トラフィックの追加コストは発生しません。 

- シンプルな有効化: [Azure portal](https://portal.azure.com) からワン クリックのプロビジョニングを利用できます。 REST API やその他の開発者ツールを使用して機能を有効にすることもできます。

- 証明書の完全な管理: すべての証明書の調達と管理がユーザーに代わって実施されます。 証明書は自動的にプロビジョニングされ、有効期限になる前に更新されます。これにより、証明書の期限切れによりサービスが中断されるリスクがなくなります。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> - カスタム ドメインで HTTPS プロトコルを有効にする。
> - AFD で管理された証明書を使用する 
> - 独自の証明書 (つまり、カスタム SSL TLS/証明書) を使用する
> - ドメインを検証する
> - カスタム ドメインで HTTPS プロトコルを無効にする


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルの手順を完了するには、最初に Front Door と、オンボードされる 1 つ以上のカスタム ドメインを作成する必要があります。 詳細については、「[チュートリアル:Front Door にカスタム ドメインを追加する](front-door-custom-domain.md)」を参照してください。

## <a name="tlsssl-certificates"></a>TLS/SSL 証明書

Front Door カスタム ドメインでコンテンツを安全に配信するために HTTPS プロトコルを有効にするには、TLS/SSL 証明書を使用する必要があります。 Azure Front Door で管理された証明書、または独自の証明書を使用できます。


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>オプション 1 (既定): Front Door によって管理される証明書を使用する

Azure Front Door で管理された証明書を使用する場合、HTTPS 機能は、数回クリックするだけで有効にできます。 Azure Front Door では、調達や更新などの証明書管理タスクが完全に処理されます。 この機能を有効にすると、プロセスがすぐに開始します。 カスタム ドメインが既に Front Door の既定のフロントエンド ホストにマップされている場合 (`{hostname}.azurefd.net`)、これ以上のアクションは必要ありません。 Front Door によって手順が処理され、要求が自動的に完了します。 一方、カスタム ドメインが別の場所でマップされている場合は、電子メールを使用してドメインの所有権を検証する必要があります。

カスタム ドメインで HTTPS を有効にするには、次の手順のようにします。

1. [Azure portal](https://portal.azure.com) で、**Front Door** プロファイルを参照します。

2. フロントエンド ホストのリストで、カスタム ドメインを含めるために HTTPS を有効にするカスタム ドメインを選択します。

3. **[カスタム ドメイン HTTPS]** セクションで、 **[有効]** をクリックして、証明書のソースとして **[Front Door managed]** \(Front Door による管理\) を選択します。

4. [保存] をクリックします。

5. [ドメインの検証](#validate-the-domain)に進みます。

> [!NOTE]
> AFD マネージドの証明書の場合、DigiCert の 64 文字の制限が適用されます。 制限を超えた場合、検証は失敗します。


### <a name="option-2-use-your-own-certificate"></a>オプション 2:独自の証明書を使用する

独自の証明書を使用して、HTTPS 機能を有効にできます。 このプロセスは、Azure Key Vault との統合を通じて行われます。これにより、お使いの証明書を安全に格納できます。 Azure Front Door では、お使いの証明書の取得に、セキュリティで保護されたこのメカニズムが使用されます。それには、追加の手順がいくつか必要です。 TLS/SSL 証明書を作成するときには、許可された証明機関 (CA) を使用して作成する必要があります。 許可されていない CA を使用すると、要求が拒否されます。 許可された CA のリストについては、「[Azure Front Door でカスタム HTTPS を有効にするために許可された認証機関](front-door-troubleshoot-allowed-ca.md)」を参照してください。

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Azure Key Vault のアカウントと証明書を準備する
 
1. Azure Key Vault: カスタム HTTPS を有効にする Front Door と同じサブスクリプションで、Azure Key Vault アカウントを実行している必要があります。 Azure Key Vault アカウントがない場合は作成します。

> [!WARNING]
> Azure Front Door では、現在、Front Door 構成と同じサブスクリプションの Key Vault アカウントのみがサポートされています。 Front Door とは異なるサブスクリプションの Key Vault を選択すると、エラーが発生します。

2. Azure Key Vault 証明書: 証明書が既にある場合は、Azure Key Vault アカウントに直接アップロードできます。または、Azure Key Vault と統合されているパートナー CA の 1 つから、Azure Key Vault を使用して新しい証明書を直接作成できます。 証明書は、**シークレット** ではなく **証明書** オブジェクトとしてアップロードします。

> [!NOTE]
> 独自の TLS/SSL 証明書については、Front Door は EC 暗号化アルゴリズムを使用した証明書をサポートしていません。

#### <a name="register-azure-front-door"></a>Azure Front Door を登録する

PowerShell を使用して、Azure Active Directory にアプリとして Azure Front Door 用のサービス プリンシパルを登録します。

> [!NOTE]
> この操作はグローバル管理者のアクセス許可を必要とし、テナントごとに **1 回** だけ実行する必要があります。

1. 必要があれば、PowerShell でローカル マシンに [Azure PowerShell](/powershell/azure/install-az-ps) をインストールします。

2. PowerShell で次のコマンドを実行します。

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>キー コンテナーへの Azure Front Door のアクセス権を付与する
 
Azure Key Vault アカウント内の証明書にアクセスするには、Azure Front Door のアクセス許可を付与します。

1. キー コンテナー アカウントで、[設定] で **[アクセス ポリシー]** 、 **[新規追加]** の順に選択して新しいポリシーを作成します。

2. **[プリンシパルの選択]** で、**ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** を検索し、 **[Microsoft.Azure.Frontdoor]** を選びます。 **[選択]** をクリックします。

3. **[シークレットのアクセス許可]** で **[取得]** を選択して、Front Door に証明書の取得を許可します。

4. **[証明書のアクセス許可]** で **[取得]** を選択して、Front Door に証明書の取得を許可します。

5. **[OK]** を選択します。 

    これで、Azure Front Door でこのキー コンテナーと、このキー コンテナーに格納されている証明書にアクセスできるようになりました。
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>デプロイする Azure Front Door の証明書を選択する
 
1. ポータルで Front Door に戻ります。 

2. カスタム ドメインの一覧で、HTTPS を有効にするカスタム ドメインを選択します。

    **[カスタム ドメイン]** ページが表示されます。

3. [証明書の管理の種類] で、 **[Use my own certificate]\(独自の証明書を使用する\)** を選択します。 

4. Azure Front Door では、Key Vault アカウントのサブスクリプションが Front Door と同じである必要があります。 キー コンテナー、証明書 (シークレット)、証明書のバージョンを選択します。

    Azure Front Door では以下の情報が一覧表示されます。 
    - サブスクリプション ID に対するキー コンテナー アカウント。 
    - 選択したキー コンテナーの下の証明書 (シークレット)。 
    - 利用可能な証明書バージョン。
 
5. 独自の証明書を使用する場合には、ドメインの検証は必要ありません。 「[伝達を待機する](#wait-for-propagation)」に進んでください。

## <a name="validate-the-domain"></a>ドメインを検証する

CNAME レコードでカスタム エンドポイントにマップされた使用中のカスタム ドメインが既にある場合、または独自の証明書を使用している場合には、  
「[カスタム ドメインが CNAME レコードによって Front Door にマップされている](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record)」に進んでください。 そうではなく、ドメインの CNAME レコード エントリがもう存在しない場合、または CNAME レコード エントリに afdverify サブドメインが含まれている場合は、「[カスタム ドメインが Front Door にマップされていない](#custom-domain-is-not-mapped-to-your-front-door)」に進んでください。

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>カスタム ドメインが CNAME レコードによって Front Door にマップされている

カスタム ドメインを Front Door のフロントエンド ホストに追加したときに、Front Door の既定の .azurefd.net ホスト名にマップする、ドメイン レジストラーの DNS テーブルに CNAME レコードを作成しました。 この CNAME レコードがまだ存在し、そこに afdverify サブドメインが含まれていない場合、DigiCert 証明機関では、この CNAME レコードを使用して自動でカスタム ドメインの所有権を確認します。 

独自の証明書を使用している場合には、ドメインの検証は必要ありません。

CNAME レコードは、次の形式にする必要があります。ここで *Name* はカスタム ドメイン名で、*Value* は Front Door の既定の .azurefd.net ホスト名です。

| 名前            | Type  | 値                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

CNAME レコードの詳細については、[CNAME DNS レコードの作成](../cdn/cdn-map-content-to-custom-domain.md)に関するセクションを参照してください。

CNAME レコードが正しい形式である場合、DigiCert は自動的にそのカスタム ドメイン名を検証し、ご利用のドメイン名に使用する専用の証明書を作成します。 DigiCert から検証電子メールが送信されないため、要求を承認する必要はありません。 この証明書は 1 年間有効で、有効期限が切れる前に自動更新されます。 「[伝達を待機する](#wait-for-propagation)」に進んでください。 

自動検証には通常、数分かかります。 1 時間以内にドメインが確認されない場合は、サポート チケットを開いてください。

>[!NOTE]
>Certificate Authority Authorization (CAA) レコードに DNS プロバイダーが登録されている場合、そこには有効な CA として DigiCert が含まれている必要があります。 ドメイン所有者は CAA レコードで、その DNS プロバイダーとともに、ドメインの証明書を発行する権限のある CA を指定できます。 CA は、ドメインの証明書の依頼を受け取っても、そのドメインに CAA レコードがあり、そこに認証された発行者としてその CA がリストされていない場合は、そのドメインまたはサブドメインへの証明書の発行が禁じられます。 CAA レコードの管理については、「[Manage CAA records](https://support.dnsimple.com/articles/manage-caa-record/)」(CAA レコードを管理する) をご覧ください。 CAA レコード ツールについては、「[CAA Record Helper](https://sslmate.com/caa/)」(CAA レコード ヘルパー) をご覧ください。

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>カスタム ドメインが Front Door にマップされていない

エンドポイントの CNAME レコード エントリがもう存在しない場合や、CNAME レコード エントリに afdverify サブドメインが含まれている場合は、この手順の残りの部分に従ってください。

カスタム ドメインの HTTPS を有効にした後、DigiCert CA は、ドメインの [WHOIS](http://whois.domaintools.com/) 登録者情報に従って、ドメインの登録者に連絡し、ドメインの所有権を検証します。 連絡は、WHOIS に登録されているメール アドレス (既定) または電話番号で行われます。 HTTPS をカスタム ドメイン上でアクティブにする前に、ドメインの検証を完了する必要があります。 ドメインの承認には 6 営業日が必要です。 6 営業日以内に承認されない要求は、自動的に取り消されます。 

![WHOIS レコード](./media/front-door-custom-domain-https/whois-record.png)

DigiCert は、その他のメール アドレスに確認メールも送信します。 WHOIS 登録者情報がプライベートである場合は、次のアドレスのいずれかから直接承認できることを確認してください。

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

数分以内に、要求の承認を求める次の例のようなメールを受け取ります。 スパム フィルターを使っている場合は、admin@digicert.com を許可リストに追加してください。 24 時間以内にメールが届かない場合は、Microsoft のサポートに問い合わせてください。

承認リンクをクリックすると、オンライン承認フォームが表示されます。 フォームの指示に従います。2 つの検証オプションがあります。

- contoso.com などの同じルート ドメインに対して同じアカウントを使って、今後行われるすべての依頼を承認することができます。 同じルート ドメインのカスタム ドメインを追加する予定の場合は、このアプローチを使用することをお勧めします。

- この要求で使われる特定のホスト名のみを承認できます。 その後の要求では追加の承認が必要になります。

承認後、カスタム ドメイン名に使用される証明書の作成が完了します。 この証明書は 1 年間有効で、有効期限が切れる前に自動更新されます。

## <a name="wait-for-propagation"></a>伝達を待機する

ドメイン名の検証後、カスタム ドメインの HTTPS 機能がアクティブになるまでに最大 6 ～ 8 時間がかかります。 プロセスが完了すると、Azure Portal の [カスタム HTTPS] のステータスは **[有効]** に設定され、[カスタム ドメイン] ダイアログの ４つの操作ステップが完了としてマークされます。 カスタム ドメインは、HTTPS を使う準備ができました。

### <a name="operation-progress"></a>操作の進行

次の表は、HTTPS を有効にするときの操作の進行を示したものです。 HTTPS を有効にした後、[カスタム ドメイン] ダイアログには 4 つの操作ステップが表示されます。 各ステップがアクティブになると、進行状況に合わせてステップの下にサブステップの詳細が追加表示されます。 これらのサブステップのすべてが発生するわけではありません。 ステップが正常に完了すると、横に緑色のチェック マークが表示されます。 

| 操作ステップ | 操作サブステップの詳細 | 
| --- | --- |
| 1 要求の送信 | 要求を送信しています |
| | HTTPS 要求を送信しています。 |
| | HTTPS 要求を正常に送信しました。 |
| 2 ドメインの検証 | CNAME で Front Door の既定の .azurefd.net フロントエンド ホストにマップされている場合、ドメインは自動的に確認されます。 そうでない場合、ドメインの登録レコードにリストされているメール アドレス (WHOIS 登録者) に、確認要求が送信されます。 できるだけ早く、ドメインを検証してください。 |
| | ドメインの所有権が正常に検証されました。 |
| | ドメインの所有権の検証要求が期限切れになりました (お客様から 6 日以内に返答がなかったようです)。 ドメインで HTTPS が有効になることはありません。 * |
| | ドメインの所有権の検証要求が、お客様により拒否されました。 ドメインで HTTPS が有効になることはありません。 * |
| 3 証明書のプロビジョニング | 証明機関は現在、ドメインで HTTPS を有効にする際に必要な証明書の発行処理を進めています。 |
| | 証明書の発行が完了しました。現在、証明書を Front Door にデプロイしています。 このプロセスには最長で 1 時間かかることがあります。 |
| | Front Door に証明書が正常にデプロイされました。 |
| 4 完了 | ドメインで HTTPS を有効にしました。 |

\* このメッセージは、エラーが発生しない限り表示されません。 

要求送信前にエラーが発生した場合は、次のエラー メッセージが表示されます。

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. *証明書プロバイダーはだれですか。どのような種類の証明書が使用されますか。*

    カスタム ドメインには、DigiCert によって提供される、専用/単一の証明書が使用されます。 

2. *IP ベースと SNI のどちらの TLS/SSL を使用しますか。*

    Azure Front Door では、SNI TLS/SSL が使用されます。

3. *DigiCert からドメインの検証電子メールが送られて来ない場合はどうすればよいでしょうか。*

    エンドポイントのホスト名を直接指す、カスタム ドメインの CNAME エントリがある場合 (かつ、afdverify サブドメイン名を使用していない場合)、ドメインの確認メールは送られてきません。 検証は自動的に行われます。 そうではなく、CNAME エントリがないうえに 24 時間以内にメールが届かなかった場合、Microsoft のサポートに問い合わせてください。

4. *SAN 証明書を使用すると専用証明書の場合よりも安全性が低くなるでしょうか。*
    
    SAN 証明書は、専用証明書と同じ暗号化およびセキュリティ標準に従っています。 発行されるすべての TLS/SSL 証明書には、サーバーのセキュリティを強化するために SHA-256 が使用されます。

5. *DNS プロバイダーに Certificate Authority Authorization レコードが必要ですか。*

    いいえ、Certificate Authority Authorization レコードは、現在必要ではありません。 ただし、所持している場合は、そこには有効な CA として DigiCert が含められている必要があります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

上記の手順では、カスタム ドメインで HTTPS プロトコルを有効にしました。 カスタム ドメインで HTTPS を使用する必要がなくなった場合、次の手順を実行して HTTPS を無効にできます。

### <a name="disable-the-https-feature"></a>HTTPS 機能を無効にする 

1. [Azure portal](https://portal.azure.com) で、**Azure Front Door** 構成を参照します。

2. フロントエンド ホストのリストで、HTTPS を無効にするカスタム ドメインをクリックします。

3. **[無効]** をクリックして HTTPS を無効にした後、 **[保存]** をクリックします。

### <a name="wait-for-propagation"></a>伝達を待機する

カスタム ドメインの HTTPS 機能を無効にした後、適用されるまでには最大 6 から 8 時間かかります。 プロセスが完了すると、Azure Portal の [カスタム HTTPS] のステータスは **[無効]** に設定され、[カスタム ドメイン] ダイアログの 3つの操作ステップが完了としてマークされます。 カスタム ドメインは HTTPS を使うことができなくなります。

#### <a name="operation-progress"></a>操作の進行

次の表は、HTTPS を無効にするときの操作の進行を示したものです。 HTTPS を無効にした後、[カスタム ドメイン] ダイアログには 3 つの操作ステップが表示されます。 各ステップがアクティブになると、ステップの下に詳細が追加表示されます。 ステップが正常に完了すると、横に緑色のチェック マークが表示されます。 

| 操作の進行 | 操作の詳細 | 
| --- | --- |
| 1 要求の送信 | 要求を送信しています |
| 2 証明書のプロビジョニング解除 | 証明書を削除しています |
| 3 完了 | 証明書が削除されました |

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

* Key Vault に証明書をアップロードする。
* ドメインを検証する。
* カスタム ドメインの HTTPS を有効にする。

Front Door のジオフィルタリング ポリシーを設定する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [geo フィルタリング ポリシーを設定する](front-door-geo-filtering.md)
