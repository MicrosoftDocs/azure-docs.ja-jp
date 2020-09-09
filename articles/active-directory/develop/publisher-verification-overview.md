---
title: 発行者確認の概要 - Microsoft ID プラットフォーム | Microsoft
description: Microsoft ID プラットフォームの発行者確認プログラム (プレビュー) の概要について説明します。 利点、プログラムの要件、よく寄せられる質問の一覧を示します。 アプリケーションが発行者確認済みとしてマークされている場合は、Microsoft Partner Network アカウントを使用する ID の確認プロセスが完了していることを発行者が確認し、発行者がこの MPN アカウントをアプリケーションの登録に関連付けていることを意味します。
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
ms.openlocfilehash: f611eefc50fede2ef4d738cd5abfd6afcc08b9ff
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120781"
---
# <a name="publisher-verification-preview"></a>発行者の確認 (プレビュー)

発行者の確認 (プレビュー) は、管理者とエンド ユーザーが、Microsoft ID プラットフォームと統合するアプリケーション開発者の信頼性を理解するのに役立ちます。 アプリケーションが発行者確認済みとしてマークされている場合は、[Microsoft Partner Network](https://partner.microsoft.com/membership) アカウントを使用する ID の[確認](/partner-center/verification-responses)プロセスが完了していることを発行者が確認し、発行者がこの MPN アカウントをアプリケーションの登録に関連付けていることを意味します。 

Azure AD の同意プロンプトや他の画面に、青い "確認済み" バッジが表示されます。![同意プロンプト](./media/publisher-verification-overview/consent-prompt.png)

この機能は、主に、[Microsoft ID プラットフォーム](v2-overview.md)で [OAuth 2.0 と OpenID Connect](active-directory-v2-protocols.md) を利用するマルチテナント アプリを構築している開発者を対象としています。 これらのアプリでは、OpenID Connect を使用してユーザーをサインインさせたり、OAuth 2.0 と [Microsoft Graph](https://developer.microsoft.com/graph/) などの API を使用してデータへのアクセスを要求したりできます。

## <a name="benefits"></a>メリット
発行者の確認には次のような利点があります。
- **顧客に対する透明性の向上とリスクの軽減** - この機能は、組織内で使用されているアプリのうち、顧客が信頼できる開発者によって発行されているものを、顧客が把握するのに役立ちます。 

- **ブランド化の向上** - "確認済み" バッジが、Azure AD [同意プロンプト](application-consent-experience.md)、エンタープライズ アプリ ページ、およびエンド ユーザーと管理者によって使用されるその他の UX サーフェイスに表示されます。 

- **よりスムーズなエンタープライズ導入** - 管理者は新しいユーザー同意ポリシーを構成でき、発行者確認の状態は主要なポリシー条件の 1 つになります。 

- **リスク評価の向上** - Microsoft による "リスクの高い" 同意要求の検出には、発行者確認がシグナルとして含まれます。 

## <a name="requirements"></a>必要条件
発行者確認にはいくつかの前提条件があり、その一部は、多くの Microsoft パートナーによって既に完了されています。 これらは次のとおりです。 

-  [確認](/partner-center/verification-responses)プロセスが完了している有効な [Microsoft Partner Network](https://partner.microsoft.com/membership) アカウントの MPN ID。 この MPN アカウントは、パートナーの組織の[パートナー グローバル アカウント (PGA)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga) である必要があります。 

-  DNS 検証済みの[カスタム ドメイン](../fundamentals/add-custom-domain.md)が含まれる Azure AD テナント。 カスタム ドメインは、前のステップで確認の間に使用されたメール アドレスのドメインと一致している必要があります。 

-  前に使用したのと同じドメインを使用して[パブリッシャー ドメイン](howto-configure-publisher-domain.md)が構成されている、Azure AD テナントに登録されたアプリ。 

-  確認を実行するユーザーは、Azure AD でのアプリの登録とパートナー センターでの MPN アカウントの両方を変更することが、許可されている必要があります。 

    -  Azure AD では、このユーザーは、アプリの所有者であるか、または次のいずれかの[ロール](../users-groups-roles/directory-assign-admin-roles.md)を持っている必要があります: アプリケーション管理者、クラウド アプリケーション管理者、全体管理者。 

    -  パートナー センターでは、このユーザーには次のいずれかの[ロール](/partner-center/permissions-overview)が必要です: MPN 管理者、アカウント管理者、全体管理者 (これは、Azure AD で管理されている共有ロールです)。
    
-  発行者は、[開発者用の Microsoft ID プラットフォームの使用条件](/legal/microsoft-identity-platform/terms-of-use)に同意します。

これらの前提条件を既に満たしている開発者は、数分で確認を受けることができます。 要件が満たされていない場合は、無料でセットアップできます。 

## <a name="frequently-asked-questions"></a>よく寄せられる質問 
発行者確認プログラムに関してよく寄せられる質問を以下に示します。 要件とプロセスに関する FAQ については、[発行者確認済みとしてのアプリのマーク](mark-app-as-publisher-verified.md)に関するページを参照してください。

- **発行者の確認で提供__されない__情報は何ですか。**  アプリケーションが発行者確認済みとマークされていても、それは、アプリケーションまたはその発行者が特定の認定を受けているかどうか、業界標準に準拠しているかどうか、ベスト プラクティスに従っているかどうかを、示すものではありません。この情報は、[Microsoft 365 アプリ認定](/microsoft-365-app-certification/overview)などの他の Microsoft プログラムによって提供されます。

- **コストはどれくらいですか。ライセンスは必要ですか。** 発行者確認に関して、Microsoft が開発者に料金を請求することはなく、特定のライセンスは必要ありません。 

- **これは Microsoft 365 の発行者の構成証明とどのように関連していますか。Microsoft 365 のアプリ認定とはどうですか。** これらは補完的なプログラムであり、開発者はこれらを使用して。顧客が安心して導入できる信頼できるアプリを作成できます。 発行者確認は、このプロセスの最初のステップであり、上記の条件を満たすアプリを作成するすべての開発者が完了する必要があります。 

  Microsoft 365 とも統合している開発者は、これらのプログラムから付加的なメリットを得ることができます。 詳細については、[Microsoft 365 の発行元構成証明](/microsoft-365-app-certification/docs/attestation)および [Microsoft 365 のアプリ認定](/microsoft-365-app-certification/docs/certification)に関するページを参照してください。 

- **これは Azure AD アプリケーション ギャラリーと同じものですか。** いいえ。発行者確認は補完的なものですが、[Azure Active Directory アプリケーション ギャラリー](../azuread-dev/howto-app-gallery-listing.md)とは別のプログラムです。 上記の条件を満たす開発者は、そのプログラムへの参加とは関係なく、発行者確認プロセスを完了する必要があります。 

## <a name="next-steps"></a>次のステップ
* [アプリを発行者確認済み](mark-app-as-publisher-verified.md)としてマークする方法について学習します。
* 発行者確認の[トラブルシューティング](troubleshoot-publisher-verification.md)を行います。