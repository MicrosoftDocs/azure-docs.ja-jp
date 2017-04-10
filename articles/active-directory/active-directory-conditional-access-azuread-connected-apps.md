---
title: "SaaS アプリ向けの Azure の条件付きアクセス | Microsoft Docs"
description: "Azure AD の条件付きアクセスを使用すると、アプリケーションごとの Multi-Factor Authentication のアクセス規則と、信頼されたネットワークにないユーザーのアクセスをブロックする機能を構成できます。 "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 51a1ee61-3ffe-4f65-b8de-ff21903e1e74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 4720d168f5ecd3a5e39cdcad812efb52755fb59a
ms.lasthandoff: 12/29/2016


---
# <a name="getting-started-with-azure-active-directory-conditional-access"></a>Azure Active Directory 条件付きアクセスの概要
[SaaS](https://azure.microsoft.com/overview/what-is-saas/) アプリや Azure AD 接続アプリ向けの Azure Active Directory 条件付きアクセスを使用すると、グループや場所、アプリケーションの秘密度に基づいて条件付きアクセスを構成できます。 

アプリケーションの秘密度に基づく条件付きアクセスでは、Multi-Factor Authentication (MFA) アクセス規則をアプリケーションごとに設定できます。 アプリケーションごとの MFA により、信頼されたネットワーク上に存在しないユーザーのアクセスをブロックできます。 MFA 規則は、アプリケーションに割り当てられているすべてのユーザーに適用することも、指定したセキュリティ グループのユーザーにのみ適用することもできます。  ユーザーが組織のネットワーク内の IP アドレスからアプリケーションにアクセスしている場合は、そのユーザーを MFA の要件から除外できます。

これらの機能は、Azure Active Directory Premium ライセンスを購入したお客様に提供されます。

## <a name="scenario-prerequisites"></a>シナリオの前提条件
* Azure Active Directory Premium のライセンス
* フェデレーションまたは管理対象 Azure Active Directory テナント
* フェデレーション テナントでは、Multi-Factor Authentication を有効にする必要があります。

## <a name="configure-per-application-access-rules"></a>アプリケーションごとのアクセス規則の構成
ここでは、アプリケーションごとのアクセス規則を構成する方法について説明します。

1. Azure AD のグローバル管理者であるアカウントを使用して Azure クラシック ポータルにサインインします。
2. 左ウィンドウで、 **[Active Directory]**を選択します。
3. [ディレクトリ] タブで、ディレクトリを選択します。
4. **[アプリケーション]** タブを選択します。
5. 規則を設定するアプリケーションを選択します。
6. **[構成]** タブをクリックします。
7. 下へスクロールして、アクセス規則のセクションを表示します。 目的のアクセス規則を選択します。
8. 規則を適用するユーザーを指定します。
9. **[有効] チェック ボックスをオン**にして、ポリシーを有効にします。

## <a name="understanding-access-rules"></a>アクセス規則について
ここでは、Azure の条件付きアプリケーション アクセスでサポートされているアクセス規則について詳細に説明します。

### <a name="specifying-the-users-the-access-rules-apply-to"></a>アクセス規則を適用するユーザーの指定
既定では、このポリシーは、アプリケーションへのアクセスを持つすべてのユーザーに適用されます。 ただし、指定したセキュリティ グループのメンバーであるユーザーのみにポリシーを制限することもできます。 **[グループの追加]** を使用すると、グループ選択のダイアログ ボックスから、アクセス規則を適用するグループを 1 つまたは複数選択できます。 このダイアログ ボックスを使用して、選択したグループを削除することもできます。 グループに適用する規則を選択すると、アクセス規則は、指定したセキュリティ グループに属するユーザーにのみ課せられます。

**[除外]** をオンにし、1 つ以上のグループを指定することで、セキュリティ グループをポリシーから明示的に除外することもできます。 **除外** リストのグループのメンバーであるユーザーは、アクセス規則が適用されるグループのメンバーである場合でも、Multi-Factor Authentication の要件は適用されません。
以下に示すアクセス規則では、マネージャー グループのすべてのユーザーが、アプリケーションにアクセスするときに Multi-Factor Authentication を使用する必要があります。

![MFA による条件付きアクセス規則の設定](./media/active-directory-conditional-access-azuread-connected-apps/conditionalaccess-saas-apps.png)

## <a name="conditional-access-rules-with-mfa"></a>MFA による条件付きアクセス規則
ユーザーごとの Multi-Factor Authentication 機能を使用してユーザーが構成されている場合は、そのユーザーのこの設定がアプリの Multi-Factor Authentication 規則と組み合わされます。 これは、たとえ、それらのユーザーがアプリケーションの Multi-Factor Authentication 規則から除外されていたとしても、Multi-Factor Authentication を実行するには、ユーザーごとの Multi-Factor Authentication で構成されたユーザーが必要になることを意味します。 Multi-Factor Authentication とユーザーごとの設定に関する詳細を参照してください。

### <a name="access-rule-options"></a>アクセス規則のオプション
次のオプションがサポートされています。

* **[多要素認証を要求する]**: アクセス規則が適用されるユーザーは、ポリシーが適用されるアプリケーションにアクセスする前に Multi-Factor Authentication を完了する必要があります。
* **[社外ネットワークからの利用は、多要素認証を要求する]**: 信頼できる IP アドレスからアクセスするユーザーは、Multi-Factor Authentication を実行する必要はありません。 Multi-Factor Authentication の設定ページでは、信頼できる IP アドレスの範囲を構成できます。
* **[社外ネットワークからの利用は、禁止する]**: 信頼できる IP アドレス以外からアクセスするユーザーはブロックされます。 Multi-Factor Authentication の設定ページでは、信頼できる IP アドレスの範囲を構成できます。

### <a name="setting-rule-status"></a>規則の状態の設定
アクセス規則の状態により、規則を有効または無効にすることができます。 アクセス規則が無効の場合、Multi-Factor Authentication の要件は適用されません。

### <a name="access-rule-evaluation"></a>アクセス規則の評価
アクセス規則は、ユーザーが OAuth 2.0、OpenID Connect、SAML、または WS-Federation を使用するフェデレーション アプリケーションにアクセスした場合に評価されます。 また、OAuth 2.0 と OpenID Connect でアクセス トークンを取得するために更新トークンが使用されたときにもアクセス規則が評価されます。 更新トークンの使用時にポリシーの評価が失敗すると、**invalid_grant** エラーが返されます。これは、ユーザーがクライアントに対して再認証する必要があることを示します。

### <a name="configure-federation-services-to-provide-multi-factor-authentication"></a>Multi-Factor Authentication を実現するためのフェデレーション サービスの構成
フェデレーション テナントでは、MFA が Azure Active Directory またはオンプレミスの AD FS サーバーによって実行される場合があります。

既定では、MFA は Azure Active Directory によってホストされているページで実行されます。 オンプレミスの MFA を構成するには、Windows PowerShell の Azure AD モジュールを使用して、Azure Active Directory で **-SupportsMFA** プロパティを **true** に設定する必要があります。

次の例で、contoso.com テナントで [Set-MsolDomainFederationSettings コマンドレット](https://msdn.microsoft.com/library/azure/dn194088.aspx) を使用して、オンプレミスの MFA を有効にする方法を示します。

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

このフラグの設定に加えて、Multi-Factor Authentication が実行されるように、フェデレーション テナントの AD FS インスタンスを構成する必要があります。 [オンプレミスの Azure Multi-Factor Authentication のデプロイ](../multi-factor-authentication/multi-factor-authentication-get-started-server.md)の手順に従ってください。

## <a name="related-articles"></a>関連記事
* [Azure Active Directory に接続されている Office 365 とその他のアプリへのアクセスの保護](active-directory-conditional-access.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)


