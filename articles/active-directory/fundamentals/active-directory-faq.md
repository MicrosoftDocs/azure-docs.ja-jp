---
title: よくあるご質問 (FAQ) - Azure Active Directory | Microsoft Docs
description: Azure と Azure Active Directory、パスワード管理、およびアプリケーション アクセスに関する一般的な質問とその回答です。
services: active-directory
author: eross-msft
manager: daveba
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c67eec3a17e949d630bedf920c690f9f5a36424d
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204564"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Azure Active Directory についてよくあるご質問
Azure Active Directory (Azure AD) は、包括的なサービスとしての ID (IDaaS) ソリューションです。その範囲は ID、アクセス管理、セキュリティのあらゆる面に及びます。

詳細については、「[Azure Active Directory とは](active-directory-whatis.md)」を参照してください。


## <a name="access-azure-and-azure-active-directory"></a>Azure と Azure Active Directory へのアクセス
**Q:Azure portal で Azure AD にアクセスしようとすると、"サブスクリプションが見つかりません" と表示されるのはなぜですか?**

**A:** Azure portal にアクセスするには、各ユーザーは Azure サブスクリプションを使用したアクセス許可が必要です。 有料の Office 365 または Azure AD サブスクリプションをお持ちの場合は、1 回限りのアクティブ化の手順を行うために [https://aka.ms/accessAAD](https://aka.ms/accessAAD) にアクセスしてください。 これらをお持ちでない場合は、無料の [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)または有料のサブスクリプションをアクティブ化する必要があります。

詳細については、次を参照してください。

* [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)

---
**Q:Azure AD、Office 365、Azure の関係はどうなっていますか?**

**A:** Azure AD を使用すると、すべての Web サービスに共通の ID とアクセス機能が提供されます。 Office 365、Microsoft Azure、Intune などのサービスをご利用の場合は、Azure AD を既に使用しているため、これらのサービスすべてでサインオンとアクセス管理が有効になります。

Web サービスを使用するように設定されていないユーザーはすべて、1 つ以上の Azure AD インスタンス内のユーザー アカウントとして定義されます。 これらのアカウントは、クラウド アプリケーション アクセスなど、無料の Azure AD 機能に対して設定できます。

Azure AD 有料サービス (Enterprise Mobility + Security など) は、エンタープライズ規模の包括的な管理ソリューションとセキュリティ ソリューションによって、Office 365 や Microsoft Azure などの他の Web サービスを補完します。

---

**Q:所有者と全体管理者の違いは何ですか?**

**A:** 既定では、Azure サブスクリプションにサインアップしたユーザーには、Azure リソースの所有者ロールが割り当てられます。 所有者は、Microsoft アカウントと、Azure サブスクリプションが関連付けられているディレクトリの職場または学校アカウントのどちらも使用できます。  このロールには、Azure Portal でのサービスの管理が許可されます。

他のユーザーが同じサブスクリプションを使用してサービスにサインインしてアクセスする必要がある場合は、適切な[組み込みロール](../../role-based-access-control/built-in-roles.md)を割り当てることができます。 詳細については、「[RBAC と Azure portal を使用してアクセスを管理する](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

既定では、Azure サブスクリプションにサインアップしたユーザーには、ディレクトリの全体管理者ロールが割り当てられます。 全体管理者は、すべての Azure AD ディレクトリ機能にアクセスできます。 Azure AD には、ディレクトリおよび ID 関連の機能を管理するために異なる管理者ロールのセットがあります。 これらの管理者は、Azure portal のさまざまな機能にアクセスできるようになります。 管理者のロールによって、ユーザーの作成または編集、他のユーザーへの管理者ロールの割り当て、ユーザー パスワードのリセット、ユーザー ライセンスの管理、ドメインの管理など、管理者が実行できる操作が決まります。  Azure AD ディレクトリ管理者とそのロールの詳細については、「[Azure Active Directory でユーザーを管理者ロールに割り当てる](active-directory-users-assign-role-azure-portal.md)」と「[Azure Active Directory での管理者ロールのアクセス許可](../users-groups-roles/directory-assign-admin-roles.md)」を参照してください。

さらに、Azure AD 有料サービス (Enterprise Mobility + Security など) は、エンタープライズ規模の包括的な管理ソリューションとセキュリティ ソリューションによって、Office 365 や Microsoft Azure などの他の Web サービスを補完します。

---
**Q:Azure AD ユーザー ライセンスの有効期限がいつ切れるかを示すレポートはありますか?**

**A:** いいえ。  これは、現在使用できません。

---

## <a name="get-started-with-hybrid-azure-ad"></a>ハイブリッド Azure AD の使用


**Q:コラボレーターとして追加されたときにテナントから切り替えるにはどうすればよいですか?**

<a name="a-when-you-are-added-to-another-organizations-tenant-as-a-collaborator-you-can-use-the-tenant-switcher-in-the-upper-right-to-switch-between-tenants--currently-there-is-no-way-to-leave-the-inviting-organization-and-microsoft-is-working-on-providing-this-functionality--until-this-feature-is-available-you-can-ask-the-inviting-organization-to-remove-you-from-their-tenant"></a>**A:** コラボレーターとして別の組織のテナントに追加されるときに、右上にある "テナント スイッチャー" を使用してテナントを切り替えることができます。  現時点では、招待元の組織から切り替える方法はありません。Microsoft では、この機能の提供に向けて取り組んでいます。  この機能が利用可能になるまでは、招待元の組織に、その組織のテナントから自分を削除するよう依頼できます。
---
**Q:オンプレミスのディレクトリを Azure AD に接続するにはどうすればよいですか?**

**A:** Azure AD Connect を使用すると、オンプレミスのディレクトリを Azure AD に接続できます。

詳細については、「[オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)」を参照してください。

---
**Q:オンプレミスのディレクトリとクラウド アプリケーションの間で SSO を設定するにはどうすればよいですか?**

**A:** オンプレミスのディレクトリと Azure AD の間でシングル サインオン (SSO) を設定するだけです。 Azure AD を介してクラウド アプリケーションにアクセスしている限り、自動的にこのサービスによって、オンプレミスの資格情報を使ったユーザー認証が正しく行われます。

オンプレミスからの SSO は、Active Directory フェデレーション サービス (ADFS) などのフェデレーション ソリューションを使用するか、パスワード ハッシュ同期を構成すると、簡単に実装できます。どちらのオプションも Azure AD Connect 構成ウィザードを使って簡単にデプロイできます。

詳細については、「[オンプレミス ID と Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)」を参照してください。

---
**Q:Azure AD には、所属する組織のユーザーのためのセルフサービス ポータルが用意されていますか?**

**A:** はい。Azure AD には、ユーザーによるアプリケーションのセルフ サービス アクセス用に [Azure AD アクセス パネル](https://myapps.microsoft.com)が用意されています。 Office 365 を利用している方は、これと同じ機能の多くを [Office 365 ポータル](https://portal.office.com)で使用できます。

詳細については、「[アクセス パネルの概要](../user-help/active-directory-saas-access-panel-introduction.md)」を参照してください。

---
**Q:Azure AD はオンプレミスのインフラストラクチャの管理に役立ちますか?**

**A:** はい。 Azure AD Premium Edition には Azure AD Connect Health が用意されています。 Azure AD Connect Health では、オンプレミスの ID インフラストラクチャと同期サービスを監視、分析できます。  

詳細については、「[クラウド内のオンプレミスの ID インフラストラクチャと同期サービスの監視](../hybrid/whatis-hybrid-identity-health.md)」を参照してください。  

---
## <a name="password-management"></a>パスワード管理
**Q:パスワード同期を使わずに Azure AD パスワード ライトバックを使用することはできますか?(このシナリオでは、クラウドにパスワードを保存せずに、パスワード ライトバックで Azure AD のセルフサービスによるパスワードのリセット (SSPR) を使用できますか?)**

**A:** ライトバックを有効にするために、Active Directory パスワードを Azure AD に同期する必要はありません。 フェデレーション環境では、Azure AD シングル サインオン (SSO) は、ユーザーの認証のためにオンプレミスのディレクトリを利用します。 このシナリオでは、オンプレミスのパスワードを Azure AD で追跡する必要はありません。

---
**Q:オンプレミスの Active Directory へのパスワード ライトバックにはどれくらいの時間がかかりますか?**

**A:** パスワード ライトバックはリアルタイムで行われます。

詳細については、「[パスワード管理の概要](../authentication/quickstart-sspr.md)」を参照してください。

---
**Q:管理者によって管理されるパスワードでパスワード ライトバックを使用できますか?**

**A:** はい。パスワード ライトバックが有効な状態であれば、管理者によって実行されるパスワード操作は、オンプレミスの環境に書き戻されます。  

<a name="for-more-answers-to-password-related-questions-see-password-management-frequently-asked-questionsauthenticationactive-directory-passwords-faqmd"></a>パスワード関連の質問に対する回答については、「[パスワード管理に関するよく寄せられる質問 (FAQ)](../authentication/active-directory-passwords-faq.md)」を参照してください。
---
**Q:パスワードを変更しようとしているときに既存の Office 365/Azure AD パスワードを忘れた場合は、どうすればよいですか?**

**A:** このような状況では、いくつかの選択肢があります。  セルフサービスによるパスワードのリセット (SSPR) が使用可能な場合は、それを使用してください。  SSPR が動作するかどうかは、その構成方法によって決まります。  詳細については、「[パスワード リセット ポータルのしくみ](../authentication/howto-sspr-deployment.md)」を参照してください。

Office 365 ユーザーの場合は、「[ユーザー パスワードの再設定](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US)」で説明されている手順を使用して、パスワードをリセットできます。

Azure AD アカウントの場合、管理者は、次のいずれかの方法を使用してパスワードをリセットできます。

- [Azure Portal でアカウントをリセットする](active-directory-users-reset-password-azure-portal.md)
- [PowerShell の使用](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


---
## <a name="security"></a>セキュリティ
**Q:何回か試行が失敗した後、アカウントはロックされますか。または、より高度な戦略はありますか?**

アカウントは、より高度な戦略によってロックされます。  これは、使用の IP と入力したパスワードに基づいています。 また、ロックアウト期間は、攻撃されている可能性に応じて長くなります。  

**Q:(一般的な) あるパスワードが拒否され、"このパスワードは何回も使用されています" という内容のメッセージが表示されました。これは、パスワードが現在 Active Directory で使用されていることを意味しますか?**

これは、パスワードが、"Password"、"123456" など、一般的によく使用されるものであることを意味します。

**Q:不審なソース (ボットネット、tor エンドポイント) からのサインイン要求は B2C テナントでブロックされますか。また、これには Basic または Premium Edition のテナントが必要ですか?**

要求をフィルター処理するゲートウェイによって、ボットネットからの保護を提供します。また、これは B2C テナントすべてに適用されます。

## <a name="application-access"></a>アプリケーション アクセス

**Q:Azure AD とその機能にあらかじめ統合されているアプリケーションの一覧はどこにありますか?**

**A:** Azure AD には、Microsoft、アプリケーション サービス プロバイダー、パートナーが提供する、2,600 個を超える事前統合されたアプリケーションがあります。 事前統合されたアプリケーションすべてで、シングル サインオン (SSO) がサポートされています。 SSO を使用すると、アプリへのアクセスに、所属する組織の資格情報を使うことができます。 また、一部のアプリケーションでは、プロビジョニングとプロビジョニング解除の自動化がサポートされています。

事前統合されたアプリケーションの完全な一覧については、「 [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/)」を参照してください。

---
**Q:必要なアプリケーションが Azure AD Marketplace にない場合はどうすればよいですか?**

**A:** Azure AD Premium をご利用の場合は、お求めのアプリケーションを追加して構成できます。 アプリケーションの機能と必要性に応じて SSO と自動プロビジョニングを構成できます。  

詳細については、次を参照してください。

* [Azure Active Directory アプリケーション ギャラリーに含まれていないアプリケーションへのシングル サインオンの構成](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [SCIM を使用して、Azure Active Directory からアプリケーションへのユーザーとグループの自動プロビジョニングを有効にする](../manage-apps/use-scim-to-provision-users-and-groups.md)

---
**Q:ユーザーが Azure AD を使用してアプリケーションにサインインする場合、どのような方法がありますか?**

**A:** Azure AD では、ユーザーがアプリケーションを表示してアクセスする方法が複数あります。たとえば、次の方法があります。

* Azure AD アクセス パネル
* Office 365 アプリケーション起動プログラム
* フェデレーション アプリへの直接サインイン
* フェデレーション アプリ、パスワードベースのアプリ、または既存のアプリへのディープ リンク

詳細については、[アプリケーションのエンド ユーザー エクスペリエンス](../manage-apps/end-user-experiences.md)に関するページを参照してください。

---
**Q:Azure AD でアプリケーションに対する認証とシングル サインオンを有効にする方法には、どのようなものがありますか?**

**A:** Azure AD では、SAML 2.0、OpenID Connect、OAuth 2.0、WS-Federation など、認証と承認に関する標準化されたプロトコルが多数サポートされています。 Azure AD では、フォーム ベース認証しかサポートしていないアプリのために、パスワード保管と自動サインインの機能もサポートされています。  

詳細については、次を参照してください。

* [Azure AD の認証シナリオ](../develop/authentication-scenarios.md)
* [Active Directory の認証プロトコル](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Azure AD でのアプリケーションのシングル サインオン](../manage-apps/what-is-single-sign-on.md)

---
**Q:オンプレミスで実行しているアプリケーションを追加することはできますか?**

**A:** Azure AD アプリケーション プロキシを使用すれば、選択したオンプレミスの Web アプリケーションに簡単かつ安全にアクセスできます。 これらのアプリケーションには、Azure AD でサービスとしてのソフトウェア (SaaS) アプリにアクセスするのと同じ方法でアクセスできます。 VPN を使ったり、ネットワーク インフラストラクチャを変更したりする必要はありません。  

詳細については、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](../manage-apps/application-proxy.md)」を参照してください。

---
**Q:特定のアプリケーションにアクセスするユーザーに多要素認証を要求するにはどうすればよいですか?**

**A:** Azure AD 条件付きアクセスを使用して、各アプリケーションに一意のアクセス ポリシーを割り当てることができます。 ポリシーでは、多要素認証を常に要求することも、ユーザーがローカル ネットワークに接続されていない場合に要求することもできます。  

詳細については、[Azure Active Directory に接続されている Office 365 やその他のアプリへのアクセスのセキュリティ保護](../active-directory-conditional-access-azure-portal.md)に関する記事を参照してください。

---
**Q:SaaS アプリの自動化されたユーザー プロビジョニングについて教えてください。**

**A:** Azure AD を使用すると、多くの一般的なクラウド SaaS アプリでユーザー ID の作成、保守、削除を自動化できます。

詳細については、「[Azure Active Directory による SaaS アプリへのユーザー プロビジョニングとプロビジョニング解除の自動化](../manage-apps/user-provisioning.md)」を参照してください。

---
**Q:Azure AD で Secure LDAP 接続を設定できますか?**

**A:** いいえ。 Azure AD では、ライトウェイト ディレクトリ アクセス プロトコル (LDAP) や Secure LDAP は直接サポートされません。 ただし、Azure のネットワークを通じてネットワーク セキュリティ グループを適切に構成すれば、Azure AD テナント上で Azure AD Domain Services (Azure AD DS) インスタンスを有効にして、LDAP 接続を実現できます。 詳細については、 https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap を参照してください。
