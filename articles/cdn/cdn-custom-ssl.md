---
title: "Azure Content Delivery Network のカスタム ドメインで HTTPS を有効または無効にする | Microsoft Docs"
description: "カスタム ドメインを使って Azure CDN エンドポイントの HTTPS を有効または無効にする方法について説明します。"
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
ms.openlocfilehash: c92f1e20acf55b8bd791fad43f17e162a5cb3847
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enable-or-disable-https-on-an-azure-content-delivery-network-custom-domain"></a>Azure Content Delivery Network のカスタム ドメインで HTTPS を有効または無効にする

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Microsoft Azure Content Delivery Network (CDN) カスタム ドメイン向けの HTTPS のサポートでは、転送中のデータのセキュリティを向上させるために、セキュリティで保護されたコンテンツを、独自のドメイン名を使って SSL 経由で配信することができます。 カスタム ドメインの HTTPS を有効にするエンド ツー エンドのワークフローは、ワンクリックでの有効化と完全な証明書の管理によって簡略化され、すべて追加コストなしで使用できます。

転送中のすべての Web アプリケーションの機密データのプライバシーおよびデータの整合性を確保することは不可欠です。 HTTPS プロトコルを使用すると、インターネット経由で送信される機密データを確実に暗号化できます。 HTTPS は信頼と認証を提供し、Web アプリケーションを攻撃から保護します。 現時点では、Azure CDN は、CDN エンドポイントで HTTPS をサポートしています。 たとえば、Azure CDN (例: https://contoso.azureedge.net) から CDN エンドポイントを作成すると、既定で HTTPS が有効になります。 現在、カスタム ドメインの HTTPS を使用して、カスタム ドメイン (例: https://www.contoso.com) でもセキュリティで保護された配信が可能です。 

HTTPS の機能の主な特性は次のとおりです。

- 追加コストなし: 証明書の取得または更新のコストや、HTTPS トラフィックの追加コストは発生しません。 CDN からの GB 送信のみ課金されます。

- シンプルな有効化: [Azure Portal](https://portal.azure.com) からワン クリックのプロビジョニングを利用できます。 REST API やその他の開発者ツールを使用して機能を有効にすることもできます。

- 証明書の完全な管理: すべての証明書の調達と管理がユーザーに代わって実施されます。 証明書は自動的にプロビジョニングされ、有効期限になる前に更新されます。 このため、証明書の期限切れの結果としてサービスが中断されるリスクが完全になくなります。

>[!NOTE] 
>HTTPS のサポートを有効にする前に、[Azure CDN のカスタム ドメイン](./cdn-map-content-to-custom-domain.md)をあらかじめ確立する必要があります。

## <a name="enabling-https"></a>HTTPS を有効にする

HTTPS を有効にするには、次の手順のようにします。

### <a name="step-1-enable-the-feature"></a>ステップ 1: 機能を有効にする 

1. [Azure Portal](https://portal.azure.com) で、Verizon Standard または Premium CDN のプロファイルを参照します。

2. エンドポイントの一覧で、カスタム ドメインを含むエンドポイントをクリックします。

3. HTTPS を有効にするカスタム ドメインをクリックします。

    ![エンドポイント ブレード](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. **[オン]** をクリックして HTTPS を有効にした後、**[適用]** をクリックします。

    ![カスタム HTTPS ダイアログ](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>ステップ 2: ドメインを検証する

>[!IMPORTANT] 
>HTTPS をカスタム ドメイン上でアクティブにする前に、ドメインの検証を完了する必要があります。 ドメインの承認には 6 営業日が必要です。 6 営業日以内に承認されない要求は、自動的に取り消されます。 

カスタム ドメインの HTTPS を有効にした後、HTTPS の証明書プロバイダーである DigiCert は、ドメインの [WHOIS](http://whois.domaintools.com/) 登録者情報に従って、ドメインの登録者に連絡し、ドメインの所有権を検証します。 連絡は、WHOIS に登録されているメール アドレス (既定) または電話番号で行われます。 

![WHOIS レコード](./media/cdn-custom-ssl/whois-record.png)

さらに、DigiCert は次のアドレスに確認メールを送信します。 WHOIS 登録者情報がプライベートである場合は、これらのアドレスのいずれかから直接承認できることを確認してください。

admin@&lt;your-domain-name.com&gt;  
administrator@&lt;your-domain-name.com&gt;  
webmaster@&lt;your-domain-name.com&gt;  
hostmaster@&lt;your-domain-name.com&gt;  
postmaster@&lt;your-domain-name.com&gt;  

数分以内に、要求の承認を求める次の例のようなメールを受け取ります。 スパム フィルターを使っている場合は、admin@digicert.com をホワイトリストに追加してくださいす。 24 時間以内にメールが届かない場合は、Microsoft のサポートに問い合わせてください。
    
![カスタム HTTPS ダイアログ](./media/cdn-custom-ssl/domain-validation-email.png)

承認リンクをクリックすると、次のオンライン承認フォームが表示されます。 
    
![カスタム HTTPS ダイアログ](./media/cdn-custom-ssl/domain-validation-form.png)

フォームの指示に従います。2 つの検証オプションがあります。

- contoso.com などの同じルート ドメインに対して同じアカウントを使って、今後行われるすべての依頼を承認することができます。同じルート ドメインで今後さらにカスタム ドメインを追加する予定の場合は、この方法をお勧めします。

- この要求で使われる特定のホスト名のみを承認できます。 その後の要求では追加の承認が必要になります。

DigiCert は、承認後、サブジェクト代替名 (SAN) 証明書にカスタム ドメイン名を追加します。 この証明書は 1 年間有効で、有効期限が切れる前に自動更新されます。

### <a name="step-3-wait-for-propagation"></a>ステップ 3: 伝達を待機する

ドメイン名の検証後、カスタム ドメインの HTTPS 機能がアクティブになるまでに最大 6 ～ 8 時間がかかります。 プロセスが完了すると、Azure Portal の [カスタム HTTPS] のステータスは [有効] に設定され、[カスタム ドメイン HTTPS] ブレードの ４つの操作ステップが完了としてマークされます。 カスタム ドメインは、HTTPS を使う準備ができました。

![HTTPS ダイアログを有効にする](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>操作の進行

次の表は、HTTPS を有効にするときの操作の進行を示したものです。 HTTPS を有効にした後、[カスタム ドメイン HTTPS] ブレードには 4 つの操作ステップが表示されます。 各ステップがアクティブになると、進行状況に合わせてステップの下に詳細が追加表示されます。 ステップが正常に完了すると、横に緑色のチェック マークが表示されます。 

| 操作ステップ | 操作ステップの詳細 | 
| --- | --- |
| 1 要求の送信 | 要求を送信しています |
| | HTTPS 要求を送信しています。 |
| | HTTPS 要求を正常に送信しました。 |
| 2 ドメインの検証 | ドメインの所有権の検証を求めるメールを送信しました。 確認を待っています。 |
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

## <a name="disabling-https"></a>HTTPS を無効にする

有効にした HTTPS を後で無効にすることができます。 HTTPS を無効にするには、次の手順のようにします。

### <a name="step-1-disable-the-feature"></a>ステップ 1: 機能を無効にする 

1. [Azure Portal](https://portal.azure.com) で、Verizon Standard または Premium CDN のプロファイルを参照します。

2. エンドポイントの一覧で、カスタム ドメインを含むエンドポイントをクリックします。

3. HTTPS を無効にするカスタム ドメインをクリックします。

    ![エンドポイント ブレード](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. **[オフ]** をクリックして HTTPS を無効にした後、**[適用]** をクリックします。

    ![カスタム HTTPS ダイアログ](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>ステップ 2: 伝達を待機する

カスタム ドメインの HTTPS 機能を無効にした後、適用されるまでには最大 6 から 8 時間かかります。 プロセスが完了すると、Azure Portal の [カスタム HTTPS] のステータスは [無効] に設定され、[カスタム ドメイン HTTPS] ブレードの 3つの操作ステップが完了としてマークされます。 カスタム ドメインは HTTPS を使うことができなくなります。

![[HTTPS の無効化] ダイアログ](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>操作の進行

次の表は、HTTPS を無効にするときの操作の進行を示したものです。 HTTPS を無効にした後、[カスタム ドメイン HTTPS] ブレードには 3 つの操作ステップが表示されます。 各ステップがアクティブになると、ステップの下に詳細が追加表示されます。 ステップが正常に完了すると、横に緑色のチェック マークが表示されます。 

| 操作の進行 | 操作の詳細 | 
| --- | --- |
| 1 要求の送信 | 要求を送信しています |
| 2 証明書のプロビジョニング解除 | 証明書を削除しています |
| 3 完了 | 証明書が削除されました |

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. *証明書プロバイダーはだれですか。どのような種類の証明書が使用されますか。*

    DigiCert によって提供されるサブジェクト代替名 (SAN) 証明書が使用されます。 SAN 証明書は、1 つの証明書で複数の完全修飾ドメイン名をセキュリティで保護できます。

2. *自分専用の証明書を使用できますか。*
    
    現在は使用できませんが、今後使用できるようになる予定です。

3. *DigiCert からドメインの検証電子メールが送られて来ない場合はどうすればよいでしょうか。*

    24 時間以内にメールが届かない場合は、Microsoft のサポートに問い合わせてください。

4. *SAN 証明書を使用すると専用証明書の場合よりも安全性が低くなるでしょうか。*
    
    SAN 証明書は、専用証明書と同じ暗号化およびセキュリティ標準に従っています。発行されるすべての SSL 証明書に SHA 256 を使用して、サーバーのセキュリティが強化されています。

5. *Akamai から Azure CDN でカスタム ドメインの HTTPS を使用できますか。*

    現在、Verizon からのみ Azure CDN でこの機能を使用できます。 数か月以内に Akamai から Azure CDN でこの機能をサポートできるように取り組んでいます。


## <a name="next-steps"></a>次のステップ

- [Azure CDN エンドポイントでカスタム ドメイン](./cdn-map-content-to-custom-domain.md)を設定する方法を確認してください。


