---
title: 発行者確認の概要 - Microsoft ID プラットフォーム | Microsoft
description: Microsoft ID プラットフォームの発行者確認プログラムの概要について説明します。 利点、プログラムの要件、よく寄せられる質問の一覧を示します。 アプリケーションが発行者確認済みとしてマークされている場合は、Microsoft Partner Network アカウントを使用する ID の確認プロセスが完了していることを発行者が確認し、発行者がこの MPN アカウントをアプリケーションの登録に関連付けていることを意味します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 1e913e3a5356ad7f49d8b3066f5bd3da7eddd2c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93308781"
---
# <a name="publisher-verification"></a>発行者の確認

発行者の確認は、管理者とエンド ユーザーが、Microsoft ID プラットフォームと統合するアプリケーション開発者の信頼性を理解するのに役立ちます。 

> [!VIDEO https://www.youtube.com/embed/IYRN2jDl5dc]

アプリケーションが発行者確認済みとしてマークされている場合は、[Microsoft Partner Network](https://partner.microsoft.com/membership) アカウントを使用する ID の[確認](/partner-center/verification-responses)プロセスが完了していることを発行者が確認し、発行者がこの MPN アカウントをアプリケーションの登録に関連付けていることを意味します。 

Azure AD の同意プロンプトや他の画面に、青い "確認済み" バッジが表示されます。![同意プロンプト](./media/publisher-verification-overview/consent-prompt.png)

この機能は、主に、[Microsoft ID プラットフォーム](v2-overview.md)で [OAuth 2.0 と OpenID Connect](active-directory-v2-protocols.md) を利用するマルチテナント アプリを構築している開発者を対象としています。 これらのアプリでは、OpenID Connect を使用してユーザーをサインインさせたり、OAuth 2.0 と [Microsoft Graph](https://developer.microsoft.com/graph/) などの API を使用してデータへのアクセスを要求したりできます。

## <a name="benefits"></a>メリット
発行者の確認には次のような利点があります。
- **顧客に対する透明性の向上とリスクの軽減** - この機能は、組織内で使用されているアプリのうち、顧客が信頼できる開発者によって発行されているものを、顧客が把握するのに役立ちます。 

- **ブランド化の向上** - "確認済み" バッジが、Azure AD [同意プロンプト](application-consent-experience.md)、エンタープライズ アプリ ページ、およびエンド ユーザーと管理者によって使用されるその他の UX サーフェイスに表示されます。 

- **よりスムーズなエンタープライズ導入** - 管理者は [ユーザー同意ポリシー](../manage-apps/configure-user-consent.md)を構成でき、発行者確認の状態は主要なポリシー条件の 1 つになります。

> [!NOTE]
> 2020 年 11 月以降、エンド ユーザーは、新しく登録された、発行元が確認済みでないマルチテナント アプリのほとんどに対して、同意を付与することができなくなります。 これは、2020 年 11 月 8 日以降に登録されたアプリで、OAuth2.0 を使用して基本的なサインインやユーザー プロファイルの読み取り以上の権限を要求したり、アプリが登録されているテナントとは異なるテナントのユーザーに同意を求めたりする場合に適用されます。 同意画面には、これらのアプリにはリスクが伴い、未確認の発行元からのものであることをユーザーに通知する警告が表示されます。    

## <a name="requirements"></a>必要条件
発行者確認にはいくつかの前提条件があり、その一部は、多くの Microsoft パートナーによって既に完了されています。 これらは次のとおりです。 

-  [確認](/partner-center/verification-responses)プロセスが完了している有効な [Microsoft Partner Network](https://partner.microsoft.com/membership) アカウントの MPN ID。 この MPN アカウントは、パートナーの組織の[パートナー グローバル アカウント (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) である必要があります。 

-  [パブリッシャー ドメイン](howto-configure-publisher-domain.md)が構成されている、Azure AD テナントに登録されたアプリ。

-  MPN アカウント検証時に使用つれる電子メール アカウントのドメインは、アプリで構成されているパブリッシャー ドメインに一致するか、Azure AD に追加された、DNS で検証済みの[カスタム ドメイン](../fundamentals/add-custom-domain.md)に一致する必要があります。 

-  確認を実行するユーザーは、Azure AD でのアプリの登録とパートナー センターでの MPN アカウントの両方を変更することが、許可されている必要があります。 

    -  Azure AD では、このユーザーは次の[ロール](../roles/permissions-reference.md)のいずれかに属する必要があります。アプリケーション管理者、クラウド アプリケーション管理者、全体管理者。 

    -  パートナー センターでは、このユーザーには次のいずれかの[ロール](/partner-center/permissions-overview)が必要です: MPN 管理者、アカウント管理者、全体管理者 (これは、Azure AD で管理されている共有ロールです)。
    
-  確認を実行するユーザーは、[多要素認証](../authentication/howto-mfa-getstarted.md)を使用してサインインする必要があります。

-  発行者は、[開発者用の Microsoft ID プラットフォームの使用条件](/legal/microsoft-identity-platform/terms-of-use)に同意します。

これらの前提条件を既に満たしている開発者は、数分で確認を受けることができます。 要件が満たされていない場合は、無料でセットアップできます。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問 
発行者確認プログラムに関してよく寄せられる質問を以下に示します。 要件とプロセスに関する FAQ については、[発行者確認済みとしてのアプリのマーク](mark-app-as-publisher-verified.md)に関するページを参照してください。

- **発行者の確認で提供 __されない__ 情報は何ですか。**  アプリケーションが発行者確認済みとマークされていても、それは、アプリケーションまたはその発行者が特定の認定を受けているかどうか、業界標準に準拠しているかどうか、ベスト プラクティスに従っているかどうかを、示すものではありません。この情報は、[Microsoft 365 アプリ認定](/microsoft-365-app-certification/overview)などの他の Microsoft プログラムによって提供されます。

- **コストはどれくらいですか。ライセンスは必要ですか。** 発行者確認に関して、Microsoft が開発者に料金を請求することはなく、特定のライセンスは必要ありません。 

- **これは Microsoft 365 の発行者の構成証明とどのように関連していますか。Microsoft 365 のアプリ認定とはどうですか。** これらは補完的なプログラムであり、開発者はこれらを使用して。顧客が安心して導入できる信頼できるアプリを作成できます。 発行者確認は、このプロセスの最初のステップであり、上記の条件を満たすアプリを作成するすべての開発者が完了する必要があります。 

  Microsoft 365 とも統合している開発者は、これらのプログラムから付加的なメリットを得ることができます。 詳細については、[Microsoft 365 の発行元構成証明](/microsoft-365-app-certification/docs/attestation)および [Microsoft 365 のアプリ認定](/microsoft-365-app-certification/docs/certification)に関するページを参照してください。 

- **これは Azure AD アプリケーション ギャラリーと同じものですか。** いいえ。発行者確認は補完的なものですが、[Azure Active Directory アプリケーション ギャラリー](v2-howto-app-gallery-listing.md)とは別のプログラムです。 上記の条件を満たす開発者は、そのプログラムへの参加とは関係なく、発行者確認プロセスを完了する必要があります。 

## <a name="next-steps"></a>次のステップ
* [アプリを発行者確認済み](mark-app-as-publisher-verified.md)としてマークする方法について学習します。
* 発行者確認の[トラブルシューティング](troubleshoot-publisher-verification.md)を行います。
