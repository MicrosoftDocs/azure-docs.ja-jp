---
title: "Azure Active Directory 接続アプリケーションに対するデバイス ベースの条件付きアクセス ポリシーを設定する方法 | Microsoft Docs"
description: "Azure AD 接続アプリケーションのデバイス ベースの条件付きアクセス ポリシーを設定します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a27862a6-d513-43ba-97c1-1c0d400bf243
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 1e7e764bdb1e883c28c137292de6a3ef8873e473
ms.openlocfilehash: e4b8622fc6d06be480a2de057070155b12746133


---
# <a name="set-device-based-conditional-access-policy-for-azure-active-directory-connected-applications"></a>Azure Active Directory 接続アプリケーションに対するデバイス ベースの条件付きアクセス ポリシーを設定する方法
Azure Active Directory (Azure AD) のデバイス ベースの条件付きアクセスを使用すると、次のような脅威から組織のリソースを保護することができます。

* 未確認のデバイスや管理下にないデバイスからのアクセスの試み。
* 組織のセキュリティ ポリシーを満たしていないデバイス。

条件付きアクセスの詳細については、「[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)」を参照してください。

デバイス ベースの条件付きアクセス ポリシーは、次のアプリケーションを保護するように設定できます。

* Office 365 SharePoint Online。組織のサイトとドキュメントを保護することができます。
* Office 365 Exchange Online。組織の電子メールを保護することができます。
* Azure AD への接続によって認証を行う SaaS (Software as a Service) アプリケーション。
* Azure AD アプリケーション プロキシ サービスを使用して発行されたオンプレミス アプリケーション。

デバイス ベースの条件付きアクセス ポリシーを Azure Portal で設定するには、そのディレクトリ内の対象アプリケーションにアクセスします。

  ![Azure Portal ディレクトリ内のアプリケーションの一覧](./media/active-directory-conditional-access-policy-connected-applications/01.png "Applications")

アプリケーションを選択し、**[構成]** タブをクリックして条件付きアクセス ポリシーを設定します。  

  ![アプリケーションの構成](./media/active-directory-conditional-access-policy-connected-applications/02.png "Device based access rules")

デバイス ベースの条件付きアクセス ポリシーを設定にするには、**[デバイス ベースのアクセス規則]** セクションの **[アクセス規則を有効にする]** を **[オン]** にします。

デバイス ベースの条件付きアクセス ポリシーには、次の 3 つの項目があります。

* **[適用対象]**。 ポリシーの適用先となるユーザーの範囲。
* **[デバイス ルール]**。 アプリケーションにアクセスするデバイスが満たすべき条件。
* **[アプリケーション強制]**。 ポリシーの適用先となるクライアント アプリケーション (ネイティブ アプリケーションとブラウザー アプリケーション)。
  
  ![デバイス ベースのアクセス ポリシーの 3 つの項目](./media/active-directory-conditional-access-policy-connected-applications/03.png "Device based access rules")

## <a name="select-the-users-the-policy-applies-to"></a>ポリシーの適用対象ユーザーの選択
**[適用対象]** セクションで、このポリシーを適用するユーザーの範囲を選択できます。

アクセス ポリシーの適用対象ユーザーの範囲は、次の 2 つの中から選ぶことができます。

* **[すべてのユーザー]**。 アプリケーションにアクセスするすべてのユーザーにポリシーを適用します。
* **[グループ]**。 特定のグループに属しているユーザーにポリシーを限定します。

![すべてのユーザーまたはグループにポリシーを適用](./media/active-directory-conditional-access-policy-connected-applications/11.png "Apply to")

 ポリシーからユーザーを除外するには、**[除外]** チェック ボックスをオンにします。 これを利用すると、アプリケーションに一時的にアクセスすることのできる権限を特定のユーザーに与えることができます。 一部のユーザーが条件付きアクセスの準備ができていないデバイスを持っている場合などにこのオプションを選択します。 デバイスがまだ登録されていなかったり、今後条件に適合しなくなったりする可能性があります。

## <a name="select-the-conditions-that-devices-must-meet"></a>デバイスが満たすべき条件の選択
アプリケーションにアクセスするデバイスが満たすべき条件は、**[デバイス ルール]** を使用して設定します。

デバイス ベースの条件付きアクセスは、次のタイプのデバイスで設定することができます。

* Windows 10 Anniversary Update、Windows 8.1、Windows 7
* Windows Server 2016、Windows Server 2012 R2、Windows Server 2012、Windows Server 2008 R2
* iOS デバイス (iPad、iPhone)
* Android デバイス

Mac のサポートは近日対応予定です。

  ![デバイスへのポリシーの適用](./media/active-directory-conditional-access-policy-connected-applications/04.png "Applications")

> [!NOTE]
> ドメイン参加済みデバイスと Azure AD 参加済みデバイスの違いについては、「[職場での Windows 10 デバイスの使用](active-directory-azureadjoin-windows10-devices.md)」を参照してください。
> 
> 

デバイス ルールには 2 つのオプションがあります。

* **すべてのデバイスに準拠を要求する**。 アプリケーションにアクセスするすべてのデバイス プラットフォームが準拠している必要があります。 デバイスの実行プラットフォームがデバイス ベースの条件付きアクセスに対応していない場合、そのデバイスのアクセスは拒否されます。
* **選択したデバイスにのみ準拠を要求する**。 特定のデバイスのプラットフォームのみ準拠が要求されます。 その他のプラットフォーム (つまりアプリケーションにアクセスできるその他のプラットフォーム) にはアクセス権が与えられます。
  
  ![デバイス ルールの適用範囲の設定](./media/active-directory-conditional-access-policy-connected-applications/05.png "Applications")

Azure AD 参加済みデバイスは、Intune または Azure AD と統合されたサード パーティのモバイル デバイス管理システムで "**準拠**" としてマークされている場合に準拠と見なされます。

ドメイン参加済みデバイスは、次の条件が満たされている場合に準拠と見なされます。

* Azure AD に登録されている場合。 ドメイン参加済みデバイスは、多くの組織で信頼済みデバイスとして扱われています。
* System Center Configuration Manager により Azure AD で "**準拠**" としてマークされている場合。
  
  ![準拠しているドメイン参加済みデバイス](./media/active-directory-conditional-access-policy-connected-applications/06.png "Device Rules")

個人用 Windows デバイスは、Intune または Azure AD と統合されたサード パーティのモバイル デバイス管理システムで "**準拠**" としてマークされている場合に準拠と見なされます。

Windows 以外のデバイスは、Intune によりディレクトリで "**準拠**" とマークされている場合に準拠と見なされます。

> [!NOTE]
> 各種管理システムで判断されたデバイスの準拠状況を使用するように Azure AD を設定する方法について詳しくは、[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)に関するページをご覧ください。
> 
> 

デバイス ベースのアクセス ポリシーの適用対象となるデバイス プラットフォームは選択することができます。 たとえば、Android、iOS、Windows Mobile (Windows 8.1 Phone と Windows 8.1 タブレット)、Windows (すべての Windows 10 デバイスを含め、Windows Mobile 以外の全 Windows デバイス) を選ぶことができます。
ポリシーの評価は選択したプラットフォームでのみ行われます。 アクセスを試みるデバイスの実行プラットフォームが、選択されたいずれのプラットフォームにも該当しない場合、ユーザーにアクセス権があれば、そのデバイスはアプリケーションにアクセスすることができます。 デバイス ポリシーは評価されません。

![デバイス ルールの適用対象プラットフォームを選択](./media/active-directory-conditional-access-policy-connected-applications/07.png "Device Rules")

## <a name="set-policy-evaluation-for-a-type-of-application"></a>特定の種類のアプリケーションに対してポリシーの評価を設定する
ユーザーまたはデバイスのアクセスに関してポリシーで評価するアプリケーションの種類は、**[アプリケーション強制]** セクションで設定します。

対象とするアプリケーションの種類として、次の 2 つのオプションがあります。

* ブラウザーおよびネイティブ アプリケーション
* ネイティブ アプリケーションのみ

![ブラウザーまたはネイティブ アプリケーションの選択](./media/active-directory-conditional-access-policy-connected-applications/08.png "Applications")

アプリケーションにアクセス ポリシーを適用するには、**[ブラウザーとネイティブ アプリケーション用]** を選択します。 この場合、次のアプリケーションが対象となります。

* ブラウザー (Windows 10 の Microsoft Edge、iOS の Safari など)。
* Active Directory Authentication Library (ADAL) (任意のプラットフォーム) または Web アカウント マネージャー (WAM) API (Windows 10) を使用するアプリケーション。

> [!NOTE]
> 証明機関によって保護されたデバイス ベースのアプリケーションにアクセスするユーザーのための考慮事項 (ブラウザーの対応など) について詳しくは、[Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)に関するページをご覧ください。
> 
> 

## <a name="help-protect-email-access-from-exchange-activesync-based-applications"></a>Exchange ActiveSync ベースのアプリケーションからの電子メールのアクセスを保護する
Office 365 Exchange Online アプリケーションでは、Exchange ActiveSync ベースのメール アプリケーションに対し、Exchange ActiveSync を使用して電子メール アクセスをブロックすることができます。

![Exchange ActiveSync 準拠オプション](./media/active-directory-conditional-access-policy-connected-applications/09.png "Applications")

![電子メールにアクセスするには、準拠デバイスが必要です](./media/active-directory-conditional-access-policy-connected-applications/10.png "Applications")

## <a name="next-steps"></a>次のステップ
* [Azure Active Directory の条件付きアクセス](active-directory-conditional-access.md)




<!--HONumber=Dec16_HO5-->


