---
title: "Privileged Identity Management サブスクリプション | Microsoft Docs"
description: "テナントで Azure AD Privileged Identity Management を管理し使用するためのサブスクリプションとライセンスの要件を説明します。"
services: active-directory
documentationcenter: 
author: barclayn
manager: mbaldwin
editor: mwahl
ms.assetid: 34367721-8b42-4fab-a443-a2e55cdbf33d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2017
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: 9364a1449ba17568c82832bc1e97d40febbb30ab
ms.openlocfilehash: c6aea0b7280ad8f1365c04203c78a1499ba0aa58


---

# <a name="azure-active-directory-privileged-identity-management-subscription-requirements"></a>Azure Active Directory Privileged Identity Management サブスクリプションの要件

Azure AD Privileged Identity Management は、Azure AD の Premium P2 エディションに付属しています。 Premium P2 のその他の機能と Premium P1 との違いの詳細については、「[Azure Active Directory のエディション](../active-directory-editions.md)」を参照してください。

>[!NOTE]
Azure Active Directory (Azure AD) Privileged Identity Management のプレビュー版では、テナントでサービスを試用するときにライセンスがチェックされませんでした。  Azure AD Privileged Identity Management が一般公開で利用できるようになったため、2016 年 12 月以降、Privileged Identity Management の使用を継続するには、試用版または有料サブスクリプションがテナントに存在する必要があります。
  

## <a name="confirm-your-trial-or-paid-subscription"></a>試用版または有料サブスクリプションを確認する

組織で試用版を使用しているのか、サブスクリプションを購入済みなのかが不明な場合は、Windows PowerShell V1 の Azure Active Directory モジュールに含まれるコマンドを使用して、テナントにサブスクリプションが存在するかどうかを確認できます。 
1. PowerShell ウィンドウを開きます。
2. 「`Connect-MsolService`」を入力して、テナントのユーザーとして認証します。
3. 「`Get-MsolSubscription | ft SkuPartNumber,IsTrial,Status`」を入力します。

このコマンドによって、テナントのサブスクリプションの一覧が取得されます。 行が返されない場合、Azure AD Privileged Identity Management を使用するには、Azure AD Premium P2 の試用版を入手するか、Azure AD Premium P2 サブスクリプションまたは EMS E5 を購入する必要があります。  試用版を入手して Azure AD Privileged Identity Management を使い始めるには、「[Azure AD Privileged Identity Management の使用](../active-directory-privileged-identity-management-getting-started.md)」を参照してください。

このコマンドで行が返され、その行の SkuPartNumber が "AAD_PREMIUM_P2" または "EMSPREMIUM" であり、IsTrial が "True" である場合は、Azure AD Premium P2 の試用版がテナントに存在することになります。  サブスクリプションの状態が有効でなく Azure AD Premium P2 または EMS E5 のサブスクリプションを購入していない場合に、Azure AD Privileged Identity Management を使用し続けるには、Azure AD Premium P2 サブスクリプションまたは EMS E5 サブスクリプションを購入する必要があります。

Azure AD Premium P2 は、[Microsoft Enterprise Agreement](https://www.microsoft.com/en-us/licensing/licensing-programs/enterprise.aspx)、[Open Volume License プログラム](https://www.microsoft.com/en-us/licensing/licensing-programs/open-license.aspx)、[Cloud Solution Providers プログラム](https://partner.microsoft.com/en-US/cloud-solution-provider)を通してご利用いただけます。 Azure および Office 365 の加入者も、Azure AD Premium P2 をオンラインで購入できます。  Azure AD Premium の価格とオンラインでの注文方法の詳細については、「[Azure Active Directory の価格](https://azure.microsoft.com/en-us/pricing/details/active-directory/)」に掲載されています。

## <a name="azure-ad-privileged-identity-management-is-not-available-in-tenant"></a>Azure AD Privileged Identity Management をテナントで使用できない

Azure AD Privileged Identity Management は、次の場合にテナントで使用できなくなります。
- 組織でプレビュー版の Azure AD Privileged Identity Management を使用していて、Azure AD Premium P2 サブスクリプションまたは EMS E5 サブスクリプションを購入していなかった場合。
- 組織で使用している Azure AD Premium P2 または EMS E5 の試用期限が切れている場合。
- 組織で購入したサブスクリプションの有効期限が切れている場合。

Azure AD Premium P2 サブスクリプションまたは EMS E5 サブスクリプションの有効期限が切れたときや、プレビュー版の Azure AD Privileged Identity Management を使用していた組織が Azure AD Premium P2 または EMS E5 サブスクリプションを入手していない場合は次のようになります。

- Azure AD ロールへの永続的なロールの割り当てには影響しません。
- 特権ロールのアクティブ化、特権アクセスの管理、または特権ロールのアクセス レビューの実行のために、Azure Portal の Azure AD Privileged Identity Management 拡張機能と、Azure AD Privileged Identity Management の Graph API コマンドレットおよび PowerShell インターフェイスを利用することはできなくなります。
- ユーザーが今後特権ロールをアクティブ化できなくなるため、Azure AD ロールの候補ロールの割り当ては削除されます。
- Azure AD ロールのすべての実行中のアクセス レビューが終了し、Azure AD Privileged Identity Management の構成設定が削除されます。
- ロールの割り当てを変更しても、Azure AD Privileged Identity Management から電子メールが送信されなくなります。

## <a name="next-steps"></a>次のステップ

- [Azure AD Privileged Identity Management の使用](../active-directory-privileged-identity-management-getting-started.md)
- [Azure AD Privileged Identity Management におけるロール](../active-directory-privileged-identity-management-roles.md)



<!--HONumber=Jan17_HO4-->


