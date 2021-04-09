---
title: Azure AD B2C の ISV パートナー ギャラリー
titleSuffix: Azure AD B2C
description: ISV パートナーと統合して、エンドユーザー エクスペリエンスをニーズに合わせて調整する方法について説明します。 Microsoft のパートナー ネットワークは、ソリューションの機能を拡張し、MFA、セキュリティで保護されたカスタマー認証、ロールベースのアクセス制御を有効にし、ID の検証と証明を通じて不正行為を防止します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/11/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a1ee632e3aaae7b858ab43b45f6e72aff8d1fb77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361768"
---
# <a name="azure-active-directory-b2c-isv-partners"></a>Azure Active Directory B2C の ISV パートナー

ISV パートナー ネットワークは、Microsoft のソリューションの機能を拡張し、シームレスなエンドユーザー エクスペリエンスを構築するのに役立ちます。 Azure AD B2C を使用すると、ISV パートナーと統合して、多要素認証 (MFA) 方法の有効化、ロールベースのアクセス制御の実施、本人確認と証明の有効化、ボット検出と不正行為防止によるセキュリティの向上、Payment Services Directive 2 (PSD2) のセキュリティで保護されたカスタマー認証 (SCA) の要件の充足が可能です。 詳細なサンプルのチュートリアルを使用して、ISV パートナーとアプリを統合する方法を学習してください。

>[!NOTE]
>[GitHub の Azure Active Directory B2C コミュニティ サイト](https://azure-ad-b2c.github.io/azureadb2ccommunity.io/)にも、コミュニティからのサンプル カスタム ポリシーが提供されています。

## <a name="identity-verification-and-proofing"></a>本人確認と証明

お客様は、Azure AD B2C パートナーと共に、アカウントの登録またはアクセスを許可する前に、エンド ユーザーの本人確認と証明を有効にすることができます。 本人確認と証明により、ドキュメント、ナレッジベースの情報、および活性が確認できます。

アーキテクチャの概要図でフローを示します。

![ID 証明のフローを示す図](./media/partner-gallery/third-party-identity-proofing.png)

Microsoft は、本人確認と証明に関して次の ISV と提携しています。

| ISV パートナー | 説明と統合のチュートリアル |
|:-------------------------|:--------------|
|![Experian ロゴのスクリーンショット。](./media/partner-gallery/experian-logo.png) | [Experian](./partner-experian.md) は、詐欺を防ぐためにユーザー属性に基づいてリスク評価を行う、本人確認および証明プロバイダーです。 |
|![IDology ロゴのスクリーンショット。](./media/partner-gallery/idology-logo.png) | [IDology](./partner-idology.md) は、本人確認ソリューション、不正行為防止ソリューション、コンプライアンス ソリューションなどを備えた本人確認および証明プロバイダーです。|
|![Jumio ロゴのスクリーンショット。](./media/partner-gallery/jumio-logo.png) | [Jumio](./partner-jumio.md) は ID 検証サービスです。リアルタイムの自動 ID 検証が可能になり、顧客データが保護されます。 |
| ![LexisNexis ロゴのスクリーンショット。](./media/partner-gallery/lexisnexis-logo.png) | [LexisNexis](./partner-lexisnexis.md) は、ユーザー ID を検証し、ユーザーのデバイスに基づいて包括的なリスク評価を提供する、プロファイルと ID 検証のプロバイダーです。 |
| ![Onfido ロゴのスクリーンショット](./media/partner-gallery/onfido-logo.png) | [Onfido](./partner-onfido.md) は、ドキュメント ID および顔の生体認証検証ソリューションであり、企業は *Know Your Customer* (顧客確認) と ID 要件をリアルタイムで満たすことができます。  |

## <a name="mfa-and-passwordless-authentication"></a>MFA とパスワードレス認証

Microsoft は、MFA とパスワードレス認証に関して次の ISV と提携しています。

| ISV パートナー | 説明と統合のチュートリアル |
|:-------------------------|:--------------|
| ![Hypr ロゴのスクリーンショット](./media/partner-gallery/hypr-logo.png) | [Hypr](./partner-hypr.md) は、パスワードを公開キー暗号化に置き換えることによって、不正行為、フィッシング、資格情報の再利用を排除するパスワードレス認証プロバイダーです。 |
| ![itsme ロゴのスクリーンショット](./media/partner-gallery/itsme-logo.png) | [itsme](./partner-itsme.md) は、ユーザーがカード リーダー、パスワード、2 要素認証、および複数の PIN コードを使用せずに安全にサインインできる、eiDAS (Electronic Identification, Authentication and Trust Services) に準拠したデジタル ID ソリューションです。 |
|![Keyless ロゴのスクリーンショット。](./media/partner-gallery/keyless-logo.png) | [Keyless](./partner-keyless.md) は、顔の生体認証スキャンの形式で認証を行い、不正行為、フィッシング、および資格情報の再利用を排除するパスワードレス認証プロバイダーです。
| ![Nevis ロゴのスクリーンショット](./media/partner-gallery/nevis-logo.png) | [Nevis](./partner-nevis.md) を使用すると、パスワードレス認証が有効になり、強力な顧客認証と PSD2 のトランザクション要件に準拠するために、Nevis Access アプリによる、モバイルファーストの完全にブランド化されたエンド ユーザー エクスペリエンスが提供されます。 |
| ![Trusona ロゴのスクリーンショット](./media/partner-gallery/trusona-logo.png) | [Trusona](./partner-trusona.md) 統合を使用すると、安全にサインインし、パスワードレス認証、MFA、およびデジタル ライセンス スキャンを有効にすることができます。 |
| ![Twilio ロゴのスクリーンショット。](./media/partner-gallery/twilio-logo.png) | [Twilio Verify App](./partner-twilio.md) は、SMS ワンタイム パスワード (OTP)、時間ベースのワンタイム パスワード (TOTP)、およびプッシュ通知を使用して MFA を有効にしたり、PSD2 の SCA 要件に準拠したりするための複数のソリューションを提供しています。 |
| ![TypingDNA ロゴのスクリーンショット](./media/partner-gallery/typingdna-logo.png) | [TypingDNA](./partner-typingdna.md) は、ユーザーの入力パターンを分析して、強力な顧客認証を実現します。 これは、企業がサイレント MFA を有効にし、PSD2 の SCA 要件に準拠するのに役立ちます。 |
| ![WhoIAM ロゴのスクリーンショット](./media/partner-gallery/whoiam-logo.png) | [WhoIAM](./partner-whoiam.md) はブランド化された ID 管理システム (BRIMS) アプリケーションです。組織はこのアプリケーションを使用して、音声、SMS、および電子メールでユーザー ベースを確認できます。 |

## <a name="role-based-access-control"></a>ロール ベースのアクセス制御 
 
Microsoft は、ロールベースのアクセス制御に関して次の ISV と提携しています。

| ISV パートナー | 説明と統合のチュートリアル |
|:-------------------------|:--------------|
| ![N8Identity ロゴのスクリーンショット](./media/partner-gallery/n8identity-logo.png) | [N8Identity](./partner-n8identity.md) はサービスとしての ID ガバナンス プラットフォームであり、Microsoft Azure で実行されている顧客アカウントの移行とカスタマー サービス要求 (CSR) の管理に対処するためのソリューションを提供します。 |
| ![Saviynt ロゴのスクリーンショット](./media/partner-gallery/saviynt-logo.png) | [Saviynt](./partner-Saviynt.md) クラウドネイティブ プラットフォームを使用すると、インテリジェントな分析とアプリケーション間の統合により、セキュリティ、コンプライアンス、およびガバナンスの向上が促進され、IT 最新化の効率が高まります。 |

## <a name="security"></a>セキュリティ

Microsoft は、セキュリティに関して次の ISV と提携しています。

| ISV パートナー | 説明と統合のチュートリアル |
|:-------------------------|:--------------|
| ![Arkose Labs ロゴのスクリーンショット](./media/partner-gallery/arkose-logo.png) | [Arkose Labs](./partner-arkose-labs.md) は、組織がボット攻撃、アカウント乗っ取り攻撃、および不正なアカウント開設を防ぐのに役立つ不正行為防止ソリューション プロバイダーです。 |
| ![Microsoft Dynamics 365 ロゴのスクリーンショット](./media/partner-gallery/microsoft-dynamics365-logo.png) | [Microsoft Dynamics 365 Fraud Protection](./partner-dynamics-365-fraud-protection.md) は、組織がデバイス フィンガープリンティングによる不正なアカウント開設を防ぐのに役立つソリューションです。 |
| ![Ping ロゴのスクリーンショット](./media/partner-gallery/ping-logo.png) | [Ping Identity](./partner-ping-identity.md) を使用すると、複数のクラウドにわたってオンプレミスのレガシ アプリケーションへの安全なハイブリッド アクセスが可能になります。 |
| ![Strata ロゴのスクリーンショット](./media/partner-gallery/strata-logo.png) | [Strata](./partner-strata.md) は、一貫性のあるアクセス ポリシーを適用し、ID の同期を維持し、レガシ ID システムから Azure AD B2C によって提供される標準ベースの認証とアクセス制御にアプリケーションを簡単に移行できるようにすることで、オンプレミスのアプリケーションへの、セキュリティで保護されたハイブリッド アクセスを提供します。 |
| ![Zscaler ロゴのスクリーンショット](./media/partner-gallery/zscaler-logo.png) | [Zscaler](./partner-zscaler.md) は、VPN のコスト、手間、セキュリティ上のリスクが生じることなく、非公開のアプリケーションおよび資産への、セキュリティで保護されたポリシーベースのアクセスを実現します。 |

## <a name="additional-information"></a>関連情報

- [Azure AD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [Azure AD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)

## <a name="next-steps"></a>次のステップ

表に記載したパートナーを選択して、Azure AD B2C とソリューションを統合する方法を確認します。
