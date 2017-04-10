---
title: "Office 365 サービス用条件付きアクセスのデバイス ポリシー | Microsoft Docs"
description: "デバイスに基づく条件が Office 365 サービスへのアクセスを制御するしくみの詳細です。 インフォメーション ワーカー (IW) は、個人のデバイスから勤務先または学校の Exchange や SharePoint Online などの Office 365 サービスにアクセスすることを望んでおり、それに対して、IT 管理者はそのサービスに対するアクセスをセキュリティで保護したいと考えます。IT 管理者は、条件付きアクセスのデバイス ポリシーをプロビジョニングすることで、会社リソースをセキュリティで保護し、同時に IW が準拠デバイスでサービスにアクセスできるようになります。"
services: active-directory
documentationcenter: 
author: femila
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 613e2447c1e47fcd80f2e9ded9ad2669f8283c3d


---
# <a name="conditional-access-device-policies-for-office-365-services"></a>Office 365 サービス用条件付きアクセスのデバイス ポリシー
"条件付きアクセス" という用語には、多要素認証されたユーザー、認証されたデバイス、準拠デバイスなどの、さまざまな条件が関連付けられています。このトピックでは、主に Office 365 サービスへのアクセスを制御するデバイスに基づく条件について取り上げます。 インフォメーション ワーカー (IW) は、個人のデバイスから勤務先または学校の Exchange や SharePoint Online などの Office 365 サービスにアクセスすることを望んでおり、それに対して、IT 管理者はそのサービスに対するアクセスをセキュリティで保護したいと考えます。 IT 管理者は、条件付きアクセスのデバイス ポリシーをプロビジョニングすることで、会社リソースをセキュリティで保護し、同時に IW が準拠デバイスでサービスにアクセスできるようになります。 Office 365 に対する条件付きアクセス ポリシーは、Microsoft Intune の条件付きアクセス ポータルで構成することができます。

Azure Active Directory により、条件付きアクセス ポリシーが適用されて、Office 365 サービスへのアクセスがセキュリティで保護されます。 テナント管理者は、ユーザーが非準拠デバイスを使用して O365 サービスにアクセスするのをブロックする条件付きアクセス ポリシーを作成できます。 サービスへのアクセスが許可されるようにするには、ユーザーが会社のデバイスのポリシーに準拠している必要があります。 また、管理者は、O365 サービスにアクセスできるデバイスの登録のみをユーザーに求めるポリシーを作成することもできます。 ポリシーを組織のすべてのユーザーに適用することも、いくつかのターゲット グループに制限して、時間の経過に伴って他のターゲット グループにも適用されるように拡張することもできます。

デバイス ポリシーを適用するための前提条件として、ユーザーが個人のデバイスを Azure Active Directory Device Registration サービスに登録する必要があります。 デバイスを Azure Active Directory Device Registration サービスに登録するにあたり、Multi-Factor Authentication (MFA) を有効にすることができます。 Azure Active Directory Device Registration サービスを使用する場合は、MFA を有効にすることをお勧めします。 MFA が有効な場合は、Azure Active Directory Device Registration サービスにデバイスを登録するユーザーには、第 2 の要素の認証が実行されます。

## <a name="how-does-conditional-access-policy-work"></a>条件付きアクセス ポリシーのしくみ
サポート対象のデバイス プラットフォームから O365 サービスへのアクセスをユーザーが要求すると、Azure Active Directory によって、そのユーザーと、ユーザーが要求の際に使用したデバイスが認証されます。サービスに設定されているポリシーにユーザーが準拠している場合にのみ、サービスへのアクセスが許可されます。 デバイスを登録していないユーザーには、会社の O365 サービスにアクセスするための登録方法とポリシーに準拠する方法に関する対応情報が提供されます。 iOS デバイスと Android デバイスのユーザーは、ポータル サイト アプリケーションを使用してデバイスを登録する必要があります。 ユーザーがデバイスを登録すると、デバイスは Azure Active Directory に登録され、デバイス管理と準拠の対象として登録されます。 Office 365 サービスのモバイル デバイス管理を有効にする場合は、お客様が Azure Active Directory Device Registration サービスと Microsoft Intune を組み合わせて使用する必要があります。 デバイス ポリシーが適用されている場合に、ユーザーが Office 365 サービスにアクセスするには、デバイスの登録が前提条件となります。

ユーザーがデバイスを正しく登録すると、そのデバイスは信頼済みになります。 Azure Active Directory により、会社のアプリケーションにアクセスするためのシングル サインオンが提供され、サービスへのアクセスを許可する条件付きアクセス ポリシーが適用されます。条件付きアクセス ポリシーは、ユーザーがアクセスを初めて要求したときだけではなく、アクセスの更新を要求するたびに適用されます。 サインインの資格情報が変更された場合、デバイスが紛失または盗難に遭った場合、または更新の要求時にポリシーが満たされていない場合、ユーザーによるサービスへのアクセスが拒否されます。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項:
デバイスを登録するには、Azure Active Directory Device Registration サービスを使用する必要があります。

ユーザーをオンプレミスで認証する場合は、Active Directory フェデレーション サービス (AD FS) (1.0 以降) が必要です。 ID プロバイダーが多要素認証に対応していない場合、社内参加用の Multi-Factor Authentication は失敗します。 たとえば、AD FS 2.0 は多要素認証に対応していません。 テナント管理者は、Azure Active Directory Device Registration サービスで MFA を有効にする前に、オンプレミスの AD FS が MFA に対応していて、かつ有効な MFA 方法が有効になっていることを確認する必要があります。 たとえば、Windows Server 2012 R2 の AD FS には MFA 機能があります。 また、ユーザーが、Azure Active Directory Device Registration サービスで MFA を有効にする前に、AD FS サーバーで追加の有効な認証 (MFA) 方法を有効にする必要があります。 AD FS でサポートされている MFA 方法の詳細については、「AD FS の追加の認証方法の構成」を参照してください。

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)
Q: サポートされていないデバイス プラットフォームに対する既定の除外ポリシーとは何ですか。

A: 現在のところ、条件付きアクセス ポリシーは、iOS デバイスおよび Android デバイスのユーザーに選択的に適用されます。 その他のデバイス プラットフォームのアプリケーションは、既定では、iOS デバイスおよび Android デバイスの条件付きアクセス ポリシーの影響を受けません。 ただし、テナント管理者は、サポートされていないプラットフォームのユーザーへのアクセスを許可しないようにグローバル ポリシーを上書きすることができます。
今後、条件付きアクセス ポリシーが拡張されて、Windows を含むその他のプラットフォームのユーザーにもポリシーが適用される予定です。

Q: Office 365 サービスに対する条件付きアクセス ポリシーがブラウザー ベースのアプリ (OWA、ブラウザー ベースの SharePoint など) に拡張されるのはいつですか。

A: 現在のところ、Office 365 サービスへの条件付きのアクセスは、デバイスの機能豊富なアプリケーションに制限されます。 今後、条件付きアクセス ポリシーが拡張されて、ブラウザーからサービスにアクセスするユーザーにもポリシーが適用される予定です。




<!--HONumber=Feb17_HO2-->


