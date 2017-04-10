---
title: "Azure CDN カスタム ドメインの HTTPS の有効化 | Microsoft Docs"
description: "カスタム ドメインを使用して Azure CDN エンドポイントの HTTPS を有効にする方法について説明します。"
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
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b334ba6bbec1d0a7e23a514174bffae01c7fff05
ms.lasthandoff: 03/04/2017


---
# <a name="enable-https-on-an-azure-cdn-custom-domain"></a>Azure CDN カスタム ドメインの HTTPS の有効化

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Azure CDN カスタム ドメイン向けの HTTPS のサポートでは、転送中のデータのセキュリティを向上させるために、セキュリティで保護されたコンテンツを、独自のドメイン名を使用して SSL 経由で配信することができます。 カスタム ドメインの HTTPS を有効にするエンド ツー エンドのワークフローは、ワンクリックでの有効化と完全な証明書の管理によって簡略化され、すべて追加コストなしで使用できます。

転送中のすべての Web アプリケーションの機密データのプライバシーおよびデータの整合性を確保することは不可欠です。 HTTPS プロトコルを使用すると、インターネット経由で送信される機密データを確実に暗号化できます。 HTTPS は信頼と認証を提供し、Web アプリケーションを攻撃から保護します。 現時点では、Azure CDN は、CDN エンドポイントで HTTPS をサポートしています。 たとえば、Azure CDN (例: https://contoso.azureedge.net) から CDN エンドポイントを作成すると、既定で HTTPS が有効になります。 現在、カスタム ドメインの HTTPS を使用して、カスタム ドメイン (例: https://www.contoso.com) でもセキュリティで保護された配信が可能です。 

HTTPS の機能の主な特性は次のとおりです。

- 追加コストなし: 証明書の取得または更新のコストや、HTTPS トラフィックの追加コストは発生しません。 CDN からの GB 送信のみ課金されます。

- シンプルな有効化: [Azure Portal](https://portal.azure.com) からワン クリックのプロビジョニングを利用できます。 REST API やその他の開発者ツールを使用して機能を有効にすることもできます。

- 証明書の完全な管理: すべての証明書の調達と管理がユーザーに代わって実施されます。 証明書は自動的にプロビジョニングされ、有効期限になる前に更新されます。 このため、証明書の期限切れの結果としてサービスが中断されるリスクが完全になくなります。

>[!NOTE] 
>HTTPS のサポートを有効にする前に、[Azure CDN のカスタム ドメイン](./cdn-map-content-to-custom-domain.md)をあらかじめ確立する必要があります。

## <a name="step-1-enabling-the-feature"></a>手順 1. 機能の有効化 

1. [Azure Portal](https://portal.azure.com) で、Verizon Standard または Premium CDN のプロファイルを参照します。

2. エンドポイントの一覧で、カスタム ドメインを含むエンドポイントをクリックします。

3. HTTPS を有効にするカスタム ドメインをクリックします。

    ![エンドポイント ブレード](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. **[オン]** をクリックして HTTPS を有効にし、変更を保存します。

    ![カスタム HTTPS ダイアログ](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="step-2-domain-validation"></a>手順 2. ドメインの検証

>[!IMPORTANT] 
>HTTPS をカスタム ドメイン上でアクティブにする前に、ドメインの検証を完了する必要があります。 ドメインの承認には 6 営業日が必要です。 6 営業日以内に承認されない場合、要求は取り消されます。  

カスタム ドメインの HTTPS を有効にした後、HTTPS の証明書プロバイダーである DigiCert は、WHOIS 登録者情報に基づいて電子メール (既定) または電話でドメインの登録者に連絡し、ドメインの所有権を検証します。 さらに DigiCert は、次のアドレスに確認メールを送信します。 WHOIS 登録者情報がプライベートである場合は、これらのアドレスのいずれかから直接承認できることを確認してください。

>admin@<your-domain-name.com> administrator@<your-domain-name.com>  
>webmaster@<your-domain-name.com>  
>hostmaster@<your-domain-name.com>  
>postmaster@<your-domain-name.com>


この電子メールを受信した場合、検証オプションは&2; つあります。

1. consoto.com などの同じルート ドメインに対して同じアカウントを使用して、今後行われるすべての依頼を承認することができます。 同じルート ドメインで今後さらにカスタム ドメインを追加する予定の場合は、この方法をお勧めします。
 
2. この要求で使用される特定のホスト名のみを承認できます。 その後の要求では追加の承認が必要になります。

    電子メールの例:
    
    ![カスタム HTTPS ダイアログ](./media/cdn-custom-ssl/domain-validation-email-example.png)

DigiCert は、承認後、SAN 証明書にカスタム ドメイン名を追加します。 この証明書は&1; 年間有効で、有効期限が切れる前に自動更新されます。

## <a name="step-3-wait-for-the-propagation-then-start-using-your-feature"></a>手順 3. 反映を待ってから機能を使い始める

ドメイン名の検証後、カスタム ドメインの HTTPS 機能がアクティブになるまでに最大 6 ～ 8 時間がかかります。 プロセスが完了したら、Azure Portal の [カスタム HTTPS] の状態が [有効] に設定されます。 カスタム ドメインで HTTPS を利用する準備ができました。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

1. *証明書プロバイダーはだれですか。どのような種類の証明書が使用されますか。*

    DigiCert によって提供されるサブジェクト代替名 (SAN) 証明書が使用されます。 SAN 証明書は、1 つの証明書で複数の完全修飾ドメイン名をセキュリティで保護できます。

2. *自分専用の証明書を使用できますか。*
    
    現在は使用できませんが、今後使用できるようになる予定です。

3. *DigiCert からドメインの検証電子メールが送られて来ない場合はどうすればよいでしょうか。*

    24 時間以内に電子メールが届かない場合は Microsoft に問い合わせてください。

4. *SAN 証明書を使用すると専用証明書の場合よりも安全性が低くなるでしょうか。*
    
    SAN 証明書は、専用証明書と同じ暗号化およびセキュリティ標準に従っています。 発行されるすべての SSL 証明書に SHA&256; を使用して、サーバーのセキュリティが強化されています。

5. *Akamai から Azure CDN でカスタム ドメインの HTTPS を使用できますか。*

    現在、Verizon からのみ Azure CDN でこの機能を使用できます。 数か月以内に Akamai から Azure CDN でこの機能をサポートできるように取り組んでいます。


## <a name="next-steps"></a>次のステップ

- [Azure CDN エンドポイントでカスタム ドメイン](./cdn-map-content-to-custom-domain.md)を設定する方法を確認してください。



