---
title: "Azure MFA のバージョンと従量制の料金プラン | Microsoft Docs"
description: "Multi-Factor Authentication クライアントや各種の認証方法、利用可能なバージョンのほか、従量制の料金プランについて取り上げています。 Details about each consumption plan"
keywords: 
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 875b4e3cab6eb4b45ed2d4b6160c60f320703dd8
ms.openlocfilehash: 1c8ebf2e1dd9400ad153f5b42e7b72f36d28feac
ms.lasthandoff: 02/23/2017

---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の入手方法

2 段階認証は、アカウントを保護するための標準的な手段として組織全体で導入するのが理想的です。 リソースへの特権アクセスが認められている管理者アカウントでは、この機能が特に重要となります。 このような理由から、Office 365 と Azure の管理者には、基本的な 2 段階認証の機能が備わっています。 管理者向けの機能をアップグレードしたり、2 段階認証を他のユーザーにまで拡張したりするニーズを想定して、Azure Multi-Factor Authentication が販売されています。 

この記事では、Azure MFA のフル バージョンと管理者向けに提供されているバージョンとの違いについて説明し、それぞれのバージョンでどのような機能が利用できるのかを紹介しています。 Azure MFA のフル バージョンをデプロイする用意ができている場合は、実装上の選択肢や、Microsoft からの課金に使われる使用量の計算方法を後半のセクションでご覧ください。

>[!IMPORTANT]
>この記事は、Azure Multi-Factor Authentication を購入するための各種の方法についてわかりやすく説明することを主眼としています。 具体的な料金と課金の詳細については、必ず「[Multi-Factor Authentication の価格](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)」を参照してください。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の使用可能なバージョン

以下の表は、Multi-Factor Authentication の&3; つのバージョンの違いについて説明したものです。

| バージョン | Description |
| --- | --- |
| Office 365 の多要素認証 |このバージョンは、Office 365 アプリケーション専用に動作し、Office 365 ポータルから管理されます。 管理者は [2 段階認証を使用して Office 365 リソースを保護](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)することができます。 このバージョンは、Office 365 サブスクリプションの一部です。 |
| Azure 管理者用の多要素認証 | Azure の管理者は、追加費用なしでその管理者アカウントに&2; 段階認証を適用することができます。|
| Azure Multi-Factor Authentication | "完全" バージョンとも呼ばれる Azure Multi-Factor Authentication は、最も豊富な機能を備えています。 [Azure クラシック ポータル](https://manage.windowsazure.com)を介した追加の構成オプション、高度なレポート、および一連のオンプレミスおよびクラウド アプリケーションのサポートを提供します。 Azure Multi-Factor Authentication は、Azure Active Directory Premium (P1 プランと P2 プラン) と Enterprise Mobility + Security (E3 プランと E5 プラン) に含まれており、[クラウドとオンプレミス](multi-factor-authentication-get-started.md)のどちらにでもデプロイすることができます。 |

## <a name="feature-comparison-of-versions"></a>バージョンごとの機能の比較
次の表に、さまざまなバージョンの Azure Multi-Factor Authentication で使用できる機能の一覧を示します。

> [!NOTE]
> この比較表では、各バージョンの Multi-Factor Authentication に含まれる機能について説明します。 完全な Azure Multi-Factor Authentication サービスを使用している場合、[クラウドの MFA とオンプレミスの MFA](multi-factor-authentication-get-started.md) のどちらを使用するかによって、一部の機能を利用できない可能性があります。


| 機能 | Office 365 の多要素認証 | Azure 管理者用の多要素認証 | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| MFA で管理者アカウントを保護する |● |● (Azure 管理者アカウントのみ使用可能) |● |
| モバイル アプリを&2; 番目の要素にする |● |● |● |
| 音声通話を&2; 番目の要素にする |● |● |● |
| SMS を&2; 番目の要素にする |● |● |● |
| MFA をサポートしていないクライアントのアプリ パスワード |● |● |● |
| 検証方法の管理制御 |● |● |● |
| PIN モード | | |● |
| 不正アクセスのアラート | | |● |
| MFA レポート | | |● |
| ワンタイム バイパス | | |● |
| 音声通話のカスタムあいさつ文 | | |● |
| 音声通話のカスタム発信元 ID | | |● |
| 信頼できる IP | | |● |
| 信頼済みデバイスの MFA の記憶 |● |● |● |
| MFA の SDK | | |● (Multi-Factor Authentication プロバイダーと完全な Azure サブスクリプションが必要) |
| オンプレミス アプリケーション用の MFA | | |● |

## <a name="how-to-get-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の入手方法
Azure Multi-Factor Authentication で提供されるすべての機能を利用するには、次のような方法があります。

### <a name="option-1---mfa-licenses"></a>方法 1 - MFA ライセンス

Azure Multi-Factor Authentication のライセンスを購入し、Azure Active Directory 内のユーザーに割り当てます。 

この方法を選択した場合、Azure Multi-Factor Authentication プロバイダーの作成が必要になるのは、ライセンスが割り当てられていないユーザーに追加で&2; 段階認証を適用しなければならないケースのみです。 それ以外のケースで Azure Multi-Factor Authentication プロバイダーを作成した場合、二重に課金される可能性があります。

### <a name="option-2---bundled-licenses-that-include-mfa"></a>方法 2 - MFA を含んだバンドル ライセンス

Azure Active Directory Premium (P1 または P2) または Enterprise Mobility + Security (E3 または E5) など、Azure Multi-Factor Authentication を含んだライセンスを購入し、Azure Active Directory 内のユーザーに割り当てます。 

この方法を選択した場合、Azure Multi-Factor Authentication プロバイダーの作成が必要になるのは、ライセンスが割り当てられていないユーザーに追加で&2; 段階認証を適用しなければならないケースのみです。 それ以外のケースで Azure Multi-Factor Authentication プロバイダーを作成した場合、二重に課金される可能性があります。 

### <a name="option-3---mfa-consumption-based-model"></a>方法 3 - 使用量ベースの MFA モデル

Azure サブスクリプション内で Azure Multi-Factor Authentication プロバイダーを作成する。 Azure MFA プロバイダーは、他のあらゆる Azure リソースと同様、Enterprise Agreement、Azure 年額コミットメント、クレジット カードのいずれかに対して課金される Azure リソースです。 これらのプロバイダーは、フル Azure サブスクリプションでのみ作成でき、制限付きの ($0 の使用制限のある) Azure サブスクリプションでは作成できません。 制限付きサブスクリプションは、方法 1 や方法 2 などでライセンスをアクティブ化したときに作成されます。 

Azure Multi-Factor Authentication プロバイダーには次の&2; つの使用モデルがあり、どちらも Azure サブスクリプションを通じて請求されます。  

1. **ユーザーごと** - 定期的な認証が必要な一定数の従業員に対して、2 段階認証を有効にしたい企業向けです。 "ユーザーごと" の課金は、Azure AD テナント内や Azure MFA サーバー内で MFA が有効にされているユーザーの数に基づくモデルです。 Azure AD と Azure MFA サーバーの両方でユーザーの MFA が有効になっており、なおかつドメインの同期 (Azure AD Connect) が有効になっている場合、ユーザー数の多い方がカウントとして採用されます。 ドメインの同期が有効になっていない場合は、Azure AD と Azure MFA サーバーとで、MFA が適用されている全ユーザーの合計がカウントされます。 課金額は日割り計算され、毎日 Commerce システムに報告されます。 

  > [!NOTE]
  > 課金例 1: MFA が有効になっているユーザーが今日の時点で 5,000 人いるとします。 この人数が MFA システムによって 31 で除算され、その日のユーザー数として 161.29 が報告されます。 翌日さらに 15 ユーザーについて MFA を有効にした場合、その日のユーザー数が 161.77 として報告されます。 課金サイクルの最後には、Azure サブスクリプションに対して課金されるユーザーの総数が約 5,000 ユーザーになります。 
  >
  > 課金例 2: ライセンスを持つユーザーと持たないユーザーとが混在している場合、その不足分は、ユーザーごとの Azure MFA プロバイダーによって埋め合わせすることになります。 テナントにある Enterprise Mobility + Security のライセンス数が 4,500 であるとき、5,000 ユーザーに対して MFA を有効にしたとします。 Azure サブスクリプションには 500 ユーザー分の料金が課金されます。料金は日割り計算され、16.13 ユーザーとして毎日報告されます。 

2. **認証ごと** - 頻繁に認証する必要がない大規模グループのユーザーに対して、2 段階認証を有効にしたい企業向けです。 認証が成功したかどうかに関係なく Azure MFA クラウド サービスが受け取った&2; 段階認証要求の件数に基づいて請求額が決定されます。 この請求額は、Azure の使用量明細書に 10 認証単位で記載され、Commerce システムには毎日報告されます。 

  > [!NOTE]
  > 課金例 3: 今日、Azure MFA サービスに 2 段階認証要求が 3,105 件送信されたとします。 Azure サブスクリプションには、310.5 認証単位分が課金されます。 

使用量ベースの構成に関しては、Azure MFA ライセンスを所有しているかどうかに関係なく別途料金が発生することに注意してください。 "認証ごと" の Azure MFA プロバイダーをセットアップした場合、2 段階認証の要求者であるユーザーがライセンスを有しているかどうかに関係なく、要求ごとに課金されます。 "ユーザーごと" の Azure MFA プロバイダーを、Azure AD テナントにリンクされていないドメインでセットアップした場合、Azure AD 上におけるライセンスの有無に関係なく、2 段階認証が有効にされているユーザーごとに料金が発生します。 

## <a name="next-steps"></a>次のステップ

- 価格の詳細については、[Azure MFA の価格](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)に関するページをご覧ください。

- Azure MFA を[クラウドとオンプレミス](multi-factor-authentication-get-started.md)のどちらにデプロイするかを選択します。
