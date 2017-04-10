---
title: "Azure Active Directory の条件付きアクセスに関するテクニカル リファレンス | Microsoft Docs"
description: "条件を利用してアクセスを制御する Azure Active Directory は、ユーザーの認証時、アプリケーションにアクセスを与える前に、選択された特定の条件を確認します。 条件が満たされていれば、ユーザーは承認され、アプリケーションにアクセスできます。"
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/28/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 4296bbc7123f6571ad564351612864a6315d8abf
ms.openlocfilehash: 450f3e001a0bc4a45fea4c4f0a81e676e9a80cc4
ms.lasthandoff: 03/02/2017


---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Azure Active Directory の条件付きアクセスに関するテクニカル リファレンス

## <a name="services-enabled-with-conditional-access"></a>条件付きアクセスで有効になっているサービス

条件付きアクセス規則は、さまざまな Azure AD アプリケーションの種類でサポートされています。 たとえば、次のような種類です。


* Azure アプリケーション プロキシに登録されているアプリケーション
* Azure Remote App
* Azure AD に登録されている、開発された基幹業務およびマルチ テナント アプリケーション
* Dynamics CRM
* Azure AD アプリケーション ギャラリーのフェデレーション アプリケーション
* Microsoft Office 365 Yammer
* Microsoft Office 365 Exchange Online
* Microsoft Office 365 SharePoint Online (OneDrive for Business を含む)
* Microsoft Power BI 
* Azure AD アプリケーション ギャラリーのパスワード SSO アプリケーション
* Visual Studio Online









## <a name="enable-access-rules"></a>アクセス規則の有効化
各規則は、アプリケーションごとに有効または無効にすることができます。 規則が **オン** の場合、その規則は有効になり、アプリケーションにアクセスするユーザーに適用されます。 **オフ** の場合、規則は使用されず、ユーザーのサインイン エクスペリエンスに影響しません。

## <a name="applying-rules-to-specific-users"></a>特定のユーザーへの規則の適用
規則は、 **[適用対象]**を設定することにより、セキュリティ グループに基づいて、ユーザーの特定のセットに適用できます。 **[適用対象]** には、**[すべてのユーザー]** または **[グループ]** を設定できます。 **[すべてのユーザー]** に設定すると、規則はアプリケーションへのアクセス権を持つすべてのユーザーに適用されます。 **[グループ]** オプションを使用すると、特定のセキュリティ グループと配布グループを選択でき、規則はそのグループにのみ適用されます。

規則をデプロイする場合、通常は、ユーザーの限定的なセット (パイロット テスト グループのメンバー) に最初に適用します。 完了したら、その規則は **[すべてのユーザー]**に適用できます。 これにより組織内のすべてのユーザーに規則が適用されます。

[グループの選択] で、 **[例外]** オプションを使用して、ポリシーから除外することもできます。 これらのグループのメンバーは、適用されるグループに含まれている場合でも、除外されます。

## <a name="at-work-networks"></a>"職場" ネットワーク
"職場" ネットワークを使用する条件付きアクセス規則は、Azure AD で構成されている信頼済み IP 範囲に依存するか、AD FS からの "企業ネットワーク内" 要求を使用します。 これらの規則は、次のようなものです。

* 作業中でない場合、多要素認証が必要です
* 作業中でない場合、アクセスをブロック

"職場" ネットワークを指定するためのオプション

1. [多要素認証の構成ページ](../multi-factor-authentication/multi-factor-authentication-whats-next.md)で、信頼済み IP 範囲を構成します。 条件付きアクセス ポリシーは、各認証要求とトークン発行に対して、構成済みの範囲を使用して規則を評価します。 
2. 企業ネットワーク内要求の使用を構成します。このオプションは、AD FS を使用して、フェデレーション ディレクトリで使用できます。 企業ネットワーク内要求について詳しくは、「[信頼できる IP](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips)」をご覧ください。


## <a name="rules-based-on-application-sensitivity"></a>アプリケーションの秘密度に基づく規則
規則は、アプリケーションごとに構成します。そうすることで、価値の高いサービスをセキュリティで保護し、その他のサービスへのアクセスには影響を与えないようにすることができます。 条件付きアクセス規則は、アプリケーションの **[構成]** タブで構成できます。 

現在、用意されている規則は、次のとおりです。

* **多要素認証が必要です**
  
  * このポリシーが適用されるユーザーはすべて、多要素認証によって少なくとも&1; 回は認証されなければなりません。
* **作業中でない場合、多要素認証が必要です**
  
  * このポリシーが適用されている場合、職場以外のリモートの場所からサービスにアクセスするすべてのユーザーが、多要素認証を少なくとも&1; 回実行している必要があります。 職場からリモートの場所に移動した場合は、サービスにアクセスするときに、多要素認証を実行する必要があります。
* **作業中でない場合、アクセスをブロック** 
  
  * ユーザーが職場からリモートの場所に移動した場合、"作業中でない場合、アクセスをブロック" ポリシーが適用されていると、そのユーザーはブロックされます。  職場に戻ると、アクセスは再度許可されます。

## <a name="related-topics"></a>関連トピック
* [Azure Active Directory に接続されている Office 365 とその他のアプリへのアクセスの保護](active-directory-conditional-access.md)
* [Article Index for Application Management in Azure Active Directory](active-directory-apps-index.md)


