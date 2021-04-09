---
title: Azure Active Directory の一般的なアプリケーション管理シナリオ | Microsoft Docs
description: Azure AD を使用したアプリケーション管理の一元化
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6372407a5deebefab60e09a9d0c01d977157357b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259572"
---
# <a name="centralize-application-management-with-azure-ad"></a>Azure AD を使用したアプリケーション管理の一元化

パスワードは、世界中の従業員にとっての IT 部門における悩みや苦しみの種です。 このために、クラウドと使用しているその他のすべてのリソースに Microsoft の ID およびアクセス管理ソリューションである Azure Active Directory に頼ろうとする企業が増え続けています。 アプリケーションごとにパスワードを入力する必要がなく、アプリケーションを移動できます。 Outlook から Workday や ADP に、すばやく安全に、それを起動するのと同じ速さで移動できます。 その後、IT を呼ぶ必要なく、パートナーやご自分の組織外の他のユーザーとさえ共同作業を行うことができます。 さらに、Azure AD では、ユーザーが使用するアプリを多要素認証などでユーザー確認して保護し、ユーザーがどこにいてもアプリに安全にアクセスできるように適応型機械学習とセキュリティ インテリジェンスを継続的に使用して疑わしいログインを検出し、アプリをセキュリティで保護して、リスクを管理することができます。 これはユーザーにとってだけでなく、IT にも利益があります。 Azure AD では、Just-In-Time アクセス レビューとフル スケールのガバナンス スイートにより、コンプライアンスを維持し、ポリシーを適用することも可能です。 また、これを取得すると、ユーザー アカウントのプロビジョニングを自動化して、アクセス管理を簡単にすることもできます。 Azure Active Directory のアプリケーション管理機能が使用される、お客様の一般的なシナリオをいくつかご確認ください。

**一般的なシナリオ**


> [!div class="checklist"]
> * お使いのすべてのアプリケーションに対する SSO
> * プロビジョニングとプロビジョニング解除の自動化 
> * お使いのアプリケーションのセキュリティ保護
> * お使いのアプリケーションへのアクセスの管理
> * 安全なハイブリッド アクセス

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>シナリオ 1:お使いのすべてのアプリケーションに対する SSO の設定

パスワードを管理する必要がなくなります。 ご自分の会社の資格情報を使用して、必要なすべてのリソースに安全にアクセスできます。 

|機能  | 説明 | 推奨 |
|---------|---------|---------|
|SSO|信頼の業界標準の標準ベースのフェデレーション SSO。|お使いのアプリケーションでサポートしている場合は、常に [SAML / OIDC](../develop/v2-howto-app-gallery-listing.md) を使用して SSO を有効にします。|
|マイ アプリ|すべてのアプリケーションを検出し、それにアクセスするための単純なハブをユーザーに提供します。 ユーザーが、アプリやグループへのアクセスを要求したり、他のユーザーに代わってリソースへのアクセスを管理したりするなどのセルフサービス機能を使用して生産性を向上できるようにします。| SSO 用にアプリを Azure AD と統合したら、組織に[マイ アプリ](my-apps-deployment-plan.md)を展開します。|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>シナリオ 2: プロビジョニングとプロビジョニング解除の自動化 


ほとんどのアプリケーションでは、ユーザーが必要なリソースにアクセスできる前に、アプリケーションにユーザーをプロビジョニングする必要があります。 CSV ファイルや複雑なスクリプトの使用は、コストがかかり管理が困難になる場合があります。 また、アクセス権がなくなったユーザー アカウントが削除されたことを顧客は確保する必要があります。 プロビジョニングとプロビジョニング解除を自動化するには、以下のツールを使用します。 


|機能  |説明|推奨 |
|---------|---------|---------|
|SCIM でのプロビジョニング|[SCIM](https://aka.ms/SCIMOverview) は、ユーザーのプロビジョニングを自動化する業界のベスト プラクティスです。 Azure AD には、SCIM に準拠しているすべてのアプリケーションを統合できます。 CSV ファイル、カスタム スクリプト、またはオンプレミス ソリューションを維持する必要なく、ユーザー アカウントを自動的に作成、更新、削除できます。|Azure AD アプリケーション ギャラリーの増え続ける[事前に統合されている](../saas-apps/tutorial-list.md)アプリの一覧を確認してください。|
|Microsoft Graph|Azure AD のデータの底深さと幅広さを利用して、お使いのアプリケーションに必要なデータを充実させることができます。|Microsoft のエコシステム全体からデータを取得するには、[Microsoft Graph](https://developer.microsoft.com/graph/) を使用します。 |


## <a name="scenario-3-secure-your-applications"></a>シナリオ 3: お使いのアプリケーションのセキュリティ保護
ID は、セキュリティの根幹です。 ID が侵害された場合、取り返しがつかなくなる前に連鎖反応を停止するのは非常に困難です。 組織では平均で 100 日を超えてからでないと侵害は検出されません。 Azure AD で提供されるツールを使用して、お使いのアプリケーションのセキュリティ体制を強化してください。 

|機能  |説明| 推奨 |
|---------|---------| ---------|
|Azure AD MFA|Azure AD Multi-Factor Authentication (MFA) は、Microsoft の 2 段階認証ソリューションです。 管理者が承認した認証方式を使用することにより、Azure AD MFA では、シンプルなサインイン プロセスへの要求に応えながら、データやアプリケーションへのアクセスを保護することができます。| ご自分のユーザーに対して [MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) を有効にします。  |
|条件付きアクセス|条件付きアクセスを使用すると、クラウド アプリへのアクセスをだれに許可するかを各種の条件に基づいて自動的に判断するアクセスの制御を実装できます。| お客様が使用している[セキュリティの既定値](../fundamentals/concept-fundamentals-security-defaults.md)と[一般ポリシー](../conditional-access/concept-conditional-access-policy-common.md)を確認します。 | 
|Identity Protection|Identity Protection は、Azure AD により組織について、Microsoft アカウントによりコンシューマー領域について、そして Xbox によりゲームについて得られた学習内容を使用して、ユーザーを保護します。 Microsoft は、脅威を特定し顧客を保護するために、1 日あたり 6 兆 5000 億の信号を分析します。|弊社のサービスで提供される[既定の ID 保護ポリシー](../identity-protection/concept-identity-protection-policies.md)を有効にしてください。 | 

## <a name="scenario-4-govern-access-to-your-applications"></a>シナリオ 4: お使いのアプリケーションへのアクセスの管理
Identity Governance により、組織は、生産性 (従業員が組織に加わったときなどに、必要なリソースへのアクセスできるようになるまでの時間) と セキュリティ (従業員の雇用形態の変更などによって、時間の経過に伴いアクセス権をどのように変更すべきか) とのバランスを取ることができます。 

|機能  |説明|推奨 |
|---------|---------| ---------|
|ELM|Azure AD のエンタイトルメント管理では、ご自分の組織内外のユーザーのアプリケーションへのアクセスをより効率的に管理できます。| 管理者以外のユーザーが[アクセス パッケージ](../governance/entitlement-management-access-package-first.md)を使用することにより、自分のアプリケーションへのアクセスを管理できます。|
|アクセス レビュー|アプリへのユーザー アクセスを定期的に確認して、適切なユーザーのみが継続的にアクセスできることを確認できます。| お使いの最も機密性の高いアプリケーションに対する[アクセスを確認](../governance/access-reviews-overview.md)します。 |
|Log Analytics|どのユーザーがどのアプリケーションにアクセスしているかのレポートを生成し、お使いの選んだ SIEM ツールに格納し、データ ソースと時間の経過のデータを関連付けることができます。| [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) を有効にし、お使いのアプリケーションと関連する重要なイベントにアラートを設定します。 |


## <a name="scenario-5-hybrid-secure-access"></a>シナリオ 5: 安全なハイブリッド アクセス
ID は、クラウドとオンプレミスのアプリケーション間のすべてを接続できる場合にのみ、ご自分のコントロール プレーンとなります。 レガシ認証ベース アプリケーションへのアクセスをセキュリティで保護するには、Azure AD とそのパートナーによって提供されるツールを活用します。

|機能  |説明|推奨 |
|---------|---------|---------|
|アプリケーション プロキシ|現在、従業員は、どこでも、いつでも、どんなデバイスからでも生産的であることを望んでいます。 クラウド内の SaaS アプリとオンプレミスの企業アプリにアクセスする必要があります。 Azure AD アプリケーション プロキシを使用すると、コストが高く複雑な仮想プライベート ネットワーク (VPN) や非武装地帯 (DMZ) を使用することなく、この堅牢なアクセスが可能になります。|オンプレミスのお使いのアプリ用に[リモートアクセス](./application-proxy.md)をセットアップします。 |
|F5、Akamai、Zscaler|ビジネス プロセスにとってまだ非常に重要であるのに、Azure AD を使用する前は保護できなかったレガシ アプリケーションを、既存のネットワークとデリバリー コントローラーを使用して容易に保護できます。 お客様はおそらく、これらのアプリケーションの保護を開始するために必要なものはすべて既に所有しています。| Akamai、Citrix、F5、または Zscaler を使用している場合は、 [事前構築されたソリューション](./secure-hybrid-access.md)をご確認ください。 | 

## <a name="related-articles"></a>関連記事

- [アプリケーション管理](./index.yml)
- [アプリケーションのプロビジョニング](../app-provisioning/user-provisioning.md)
- [安全なハイブリッド アクセス](./secure-hybrid-access.md)
- [Identity Governance](../governance/identity-governance-overview.md)
- [Microsoft ID プラットフォーム](../develop/v2-overview.md)
- [ID セキュリティ](../conditional-access/index.yml)