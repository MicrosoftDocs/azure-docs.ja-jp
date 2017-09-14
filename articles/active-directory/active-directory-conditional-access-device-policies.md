---
title: "Office 365 サービス用の Azure Active Directory の条件付きアクセスのデバイス ポリシー | Microsoft Docs"
description: "企業リソースをよりセキュリティ保護された状態にしながら、ユーザー コンプライアンスやサービスへのアクセスを維持するのに役立つように条件付きアクセスのデバイス ポリシーをプロビジョニングする方法について説明します。"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8664c0bb-bba1-4012-b321-e9c8363080a0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: markvi
ms.reviewer: calebb
ms.translationtype: Human Translation
ms.sourcegitcommit: 43aab8d52e854636f7ea2ff3aae50d7827735cc7
ms.openlocfilehash: 2d0794781946195fc6fbab413299012e6949a4bc
ms.contentlocale: ja-jp
ms.lasthandoff: 06/03/2017

---
# <a name="active-directory-conditional-access-device-policies-for-office-365-services"></a>Office 365 サービス用の Active Directory の条件付きアクセスのデバイス ポリシー

条件付きアクセスでは、複数の部分が機能することが必要です。 特に、多要素認証されたユーザー、認証されたデバイス、および準拠デバイスが必要になります。 この記事では、主に組織が Office 365 サービスへのアクセスを制御しやすくするために使用できるデバイス ベースの条件に焦点を絞ります。 

企業ユーザーは、個人のデバイスから勤務先または学校にある Exchange や SharePoint Online などの Office 365 サービスにアクセスすることを希望します。 そのアクセスをセキュリティ保護する必要があります。 企業リソースをよりセキュリティ保護された状態にしながら、準拠デバイスを使用しているユーザーにサービスへのアクセス権を付与するのに役立つように、条件付きアクセスのデバイス ポリシーをプロビジョニングできます。 Office 365 に対する条件付きアクセス ポリシーは、Microsoft Intune の条件付きアクセス ポータルで設定できます。

Azure Active Directory (Azure AD) は、Office 365 サービスへのセキュリティ保護されたアクセスに役立つように条件付きアクセス ポリシーを適用します。 非準拠のデバイスを使用しているユーザーが Office 365 サービスにアクセスできないようにブロックする条件付きアクセス ポリシーを作成できます。 ユーザーは、サービスへのアクセス権が付与される前に、会社のデバイス ポリシーに従う必要があります。 あるいは、ユーザーが Office 365 サービスへのアクセスを取得するには自分のデバイスを登録する必要があるポリシーを作成できます。 ポリシーは、組織内のすべてのユーザーに適用することも、いくつかのターゲット グループに制限することもできます。 時間の経過とともに、より多くのターゲット グループをポリシーに追加できます。

デバイス ポリシーを適用するための前提条件として、ユーザーは自分のデバイスを Azure AD デバイス登録サービスに登録する必要があります。 Azure AD デバイス登録サービスに登録するデバイスの多要素認証を有効にすることを選択できます。 Azure Active Directory デバイス登録サービスには多要素認証が推奨されます。 多要素認証が有効になっている場合、自分のデバイスを Azure AD デバイス登録サービスに登録するユーザーには第 2 要素認証が実行されます。

## <a name="how-does-a-conditional-access-policy-work"></a>条件付きアクセス ポリシーのしくみ

ユーザーがサポートされているデバイス プラットフォームから Office 365 サービスへのアクセスを要求すると、Azure AD はそのユーザーとデバイスを認証します。 Azure AD は、ユーザーがそのサービスに対して設定されているポリシーに従っている場合にのみ、そのサービスへのアクセス権を付与します。 登録されていないデバイス上のユーザーには、登録し、会社の Office 365 サービスへのアクセスに準拠する方法に関する手順が示されます。 iOS および Android デバイス上のユーザーは、Intune Company Portal アプリケーションを使用して自分のデバイスを登録する必要があります。 ユーザーがデバイスを登録すると、そのデバイスは Azure AD に登録され、さらにデバイスの管理とコンプライアンスに対して登録されます。 Office 365 サービスに対するモバイル デバイスの管理を行うには、Azure AD デバイス登録サービスを Microsoft Intune と共に使用する必要があります。 デバイス ポリシーが適用されている場合、ユーザーが Office 365 サービスにアクセスするにはデバイスの登録が必要です。

ユーザーがデバイスを正常に登録すると、そのデバイスと信頼済みになります。 Azure AD は、認証されたユーザーに会社のアプリケーションへのシングル サインオン アクセスを提供します。 Azure AD は、ユーザーが初めてアクセスを要求したときだけでなく、アクセスへの要求を更新するたびに、サービスへのアクセス権を付与するための条件付きアクセス ポリシーを適用します。 サインインの資格情報が変更された場合、デバイスが紛失するか盗まれた場合、または更新が要求された時点でポリシーの条件が満たされていない場合、ユーザーはサービスへのアクセスを拒否されます。

## <a name="deployment-considerations"></a>デプロイに関する考慮事項

デバイスを登録するには、Azure AD デバイス登録サービスを使用する必要があります。

オンプレミスのユーザーが認証されようとしている場合は、Active Directory フェデレーション サービス (AD FS) (バージョン 1.0 以降のバージョン) が必要です。 ID プロバイダーが多要素認証を実行できない場合、社内参加のための多要素認証は失敗します。 たとえば、AD FS 2.0 では多要素認証を使用できません。 Azure AD デバイス登録サービスのための多要素認証を有効にする前に、オンプレミスの AD FS が多要素認証で動作すること、および有効な多要素認証方法が設定されていることを確認してください。 たとえば、Windows Server 2012 R2 上の AD FS には多要素認証機能があります。 また、Azure AD デバイス登録サービスのための多要素認証を有効にする前に、AD FS サーバー上で追加の有効な認証 (多要素認証) 方法を設定することも必要です。 AD FS でサポートされている多要素認証方法の詳細については、「[AD FS の追加の認証方法の構成](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)」を参照してください。

## <a name="next-steps"></a>次のステップ

*   一般的な質問への答えについては、「[Azure Active Directory conditional access FAQs (Azure Active Directory の条件付きアクセスのよく寄せられる質問)](active-directory-conditional-faqs.md)」を参照してください。

