---
title: Azure MFA のバージョンと従量制の料金プラン - Azure Active Directory
description: Multi-Factor Authentication クライアント、各種認証方法、利用可能なバージョンに関する情報。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 61a2afad33aea7fa2a21135dca0995ad862a2258
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848665"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の入手方法

2 段階認証は、アカウントを保護するための標準的な手段として組織全体で導入するのが理想的です。 リソースへの特権アクセスが認められているアカウントでは、この機能が特に重要となります。 このような理由から、Microsoft は Office 365 と Azure Active Directory (Azure AD) の管理者に、無償で基本的な 2 段階認証の機能を提供しています。 管理者向けの機能をアップグレードしたり、2 段階認証を他のユーザーに拡張したりといった希望がある場合には、いくつかの方法で Azure Multi-Factor Authentication を購入できます。

> [!IMPORTANT]
> この記事は、Azure Multi-Factor Authentication を購入するための各種の方法についてわかりやすく説明することを主眼としています。 具体的な料金と課金の詳細については、必ず「[Multi-Factor Authentication の価格](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)」を参照してください。
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の使用可能なバージョン

以下の表は、多要素認証のバージョン間の違いについて説明したものです。

| Version | 説明 |
| --- | --- |
| Free オプション | Azure AD の無料特典を利用しているお客様は、[セキュリティ既定値](../conditional-access/concept-conditional-access-security-defaults.md)を使用して、環境内で多要素認証を有効にすることができます。 |
| Office 365 の多要素認証 | このバージョンは、Office 365 または Microsoft 365 ポータルから管理されます。 管理者は [2 段階認証を使用して Office 365 リソースを保護](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)することができます。 このバージョンは、Office 365 サブスクリプションの一部です。 |
| Azure AD 管理者用の Multi-Factor Authentication | Azure AD テナントの Azure AD グローバル管理者ロールを割り当てられているユーザーは、追加費用なしで 2 段階認証を適用できます。 |
| Azure Multi-Factor Authentication | "完全" バージョンとも呼ばれる Azure Multi-Factor Authentication は、最も豊富な機能を備えています。 [Azure Portal](https://portal.azure.com) を介した追加の構成オプション、高度なレポート、および一連のオンプレミスおよびクラウド アプリケーションのサポートを提供します。 Azure Multi-Factor Authentication は、[Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features) および [Microsoft 365 Business](https://www.microsoft.com/microsoft-365/business) の機能です。 |

> [!NOTE]
> 2018 年 9 月 1 日以降、新しいお客様は、スタンドアロン オファーとして Azure Multi-Factor Authentication を購入できなくなります。 多要素認証は、今後も Azure AD Premium または Microsoft 365 Business ライセンスの一機能としてご利用いただけます。

## <a name="feature-comparison-of-versions"></a>バージョンごとの機能の比較

次の表に、さまざまなバージョンの Azure Multi-Factor Authentication で使用できる機能の一覧を示します。

> [!NOTE]
> この比較表では、各バージョンの Multi-Factor Authentication に含まれる機能について説明します。 完全な Azure Multi-Factor Authentication サービスを使用している場合、[クラウドの MFA とオンプレミスの MFA](concept-mfa-whichversion.md) のどちらを使用するかによって、一部の機能を利用できない可能性があります。
>

| 機能 | Office 365 の多要素認証 | Azure AD 管理者用の Multi-Factor Authentication | Azure Multi-Factor Authentication | セキュリティの既定値 | 
| --- |:---:|:---:|:---:|:---:|
| MFA で Azure AD 管理者アカウントを保護する |● |● (Azure AD グローバル管理者アカウントの場合のみ) |● |● |
| モバイル アプリを 2 番目の要素にする |● |● |● |● |
| 音声通話を 2 番目の要素にする |● |● |● |   |
| SMS を 2 番目の要素にする |● |● |● |   |
| MFA をサポートしていないクライアントのアプリ パスワード |● |● |● |   |
| 検証方法の管理制御 |● |● |● |   |
| MFA で管理者以外のアカウントを保護する |● | |● |● |
| PIN モード | | |● |   |
| 不正アクセスのアラート | | |● |   |
| MFA レポート | | |● |   |
| ワンタイム バイパス | | |● |   |
| 音声通話のカスタムあいさつ文 | | |● |   |
| 音声通話のカスタム発信元 ID | | |● |   |
| 信頼できる IP | | |● |   |
| 信頼済みデバイスの MFA の記憶 |● |● |● |   |
| オンプレミス アプリケーション用の MFA | | |● |   |

> [!IMPORTANT]
> 2019 年 3 月以降、無料/試用版の Azure AD テナントの MFA および SSPR ユーザーは、音声通話オプションを利用できなくなります。 この変更は、SMS メッセージには影響しません。 有料の Azure AD テナントのユーザーは、引き続き音声通話を利用できます。 この変更は、無料/試用版の Azure AD テナントのみに影響します。

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Azure AD 管理者用の Azure Multi-Factor Authentication を有効にする方法

Azure AD テナントのグローバル管理者ロールを割り当てられているユーザーは、追加費用なしで、その Azure AD グローバル管理者アカウントに 2 段階認証を適用できます。 Microsoft アカウントを使用している場合は、Microsoft アカウントのサポート記事「[2 段階認証について](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)」に記載されているガイダンスを使用して多要素認証を登録できます。 Microsoft アカウントを使用していない場合は、「[ユーザーまたはグループに 2 段階認証を要求する方法](howto-mfa-userstates.md)」の記事に記載されているガイダンスを使用して、グローバル管理者に対して多要素認証を有効にします。

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の購入方法

Azure Active Directory Premium などの Azure Multi-Factor Authentication が含まれるライセンス、または Azure AD Premium または条件付きアクセスが含まれるライセンス バンドルを購入して、Azure Active Directory 内のユーザーに割り当てます。

### <a name="consumption-based-licensing"></a>使用量ベースのライセンス

2018 年 9 月 1 日以降、新規のお客様は、使用量ベースのライセンスを利用できません。

2018 年 9 月 1 日以降、新しい認証プロバイダーを作成できなくなります。 既存の認証プロバイダーは引き続き使用および更新できます。 多要素認証認証は、今後も Azure AD Premium ライセンスで利用できます。

Azure Multi-Factor Authentication プロバイダーには次の 2 つの使用モデルがあり、どちらも Azure サブスクリプションを通じて請求されます。

1. **有効化されたユーザーごと** - 定期的な認証が必要な一定数の従業員に対して、2 段階認証を有効にしたい企業向けです。 ユーザーごとの課金は、Azure AD テナント内や Azure MFA サーバー内で MFA が有効になっているユーザーの数に基づくモデルです。 Azure AD と Azure MFA サーバーの両方でユーザーの MFA が有効になっており、なおかつドメインの同期 (Azure AD Connect) が有効になっている場合、ユーザー数の多い方がカウントとして採用されます。 ドメインの同期が有効になっていない場合は、Azure AD と Azure MFA サーバーとで、MFA が適用されている全ユーザーの合計がカウントされます。 課金額は日割り計算され、毎日 Commerce システムに報告されます。

   > [!NOTE]
   > 課金例 1: MFA が有効になっているユーザーが今日の時点で 5,000 人いるとします。 この人数が MFA システムによって 31 で除算され、その日のユーザー数として 161.29 が報告されます。 翌日さらに 15 ユーザーについて MFA を有効にした場合、その日のユーザー数が 161.77 として報告されます。 課金サイクルの最後には、Azure サブスクリプションに対して課金されるユーザーの総数が約 5,000 ユーザーになります。
   >
   > 課金例 2: ライセンスを持つユーザーと持たないユーザーとが混在している場合、その不足分は、ユーザーごとの Azure MFA プロバイダーによって埋め合わせすることになります。 テナントにある Enterprise Mobility + Security のライセンス数が 4,500 であるとき、5,000 ユーザーに対して MFA を有効にしたとします。 Azure サブスクリプションには 500 ユーザー分の料金が課金されます。料金は日割り計算され、16.13 ユーザーとして毎日報告されます。
   >

1. **認証ごと** - 頻繁に認証する必要がない大規模グループのユーザーに対して、2 段階認証を有効にしたい企業向けです。 認証が成功したかどうかに関係なく、2 段階認証要求の件数に基づいて請求額が決定されます。 この請求額は、Azure の使用量明細書に 10 認証単位で記載され、毎日報告されます。

   > [!NOTE]
   > 課金例 3: 今日、Azure MFA サービスに 2 段階認証要求が 3,105 件送信されたとします。 Azure サブスクリプションには、310.5 認証単位分が課金されます。
   >

使用量ベースの構成に関しては、ライセンスを所有しているかどうかに関係なく別途料金が発生することに注意してください。 "認証ごと" の Azure MFA プロバイダーをセットアップした場合、2 段階認証の要求者であるユーザーがライセンスを所有しているかどうかに関係なく、要求ごとに課金されます。 "ユーザーごと" の Azure MFA プロバイダーを、Azure AD テナントにリンクされていないドメインでセットアップした場合、Azure AD 上におけるライセンスの有無に関係なく、2 段階認証が有効にされているユーザーごとに料金が発生します。

## <a name="next-steps"></a>次の手順

- 価格の詳細については、[Azure MFA の価格](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)に関するページをご覧ください。
