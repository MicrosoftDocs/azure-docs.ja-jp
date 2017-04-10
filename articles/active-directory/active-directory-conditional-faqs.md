---
title: "Azure Active Directory の条件付きアクセスに関する FAQ | Microsoft Docs"
description: "条件付きアクセスに関してよく寄せられる質問  "
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/06/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 3980b99206c9f0c81ba3d03778777878e25db7c8
ms.lasthandoff: 12/29/2016


---
# <a name="azure-active-directory-conditional-access-faq"></a>Azure Active Directory の条件付きアクセスに関する FAQ
## <a name="which-applications-work-with-conditional-access-policies"></a>条件付きアクセス ポリシーを使用するアプリケーションにはどのようなものがありますか?
**A:**[条件付きアクセスでサポートされるアプリケーション](active-directory-conditional-access-supported-apps.md)に関するトピックを参照してください。

## <a name="are-conditional-access-policies-enforced-for-b2b-collaboration-and-guest-users"></a>条件付きアクセス ポリシーは、B2B コラボレーション ユーザーおよびゲスト ユーザーには適用されますか?
**A:** B2B コラボレーション ユーザーにはポリシーが適用されます。 ただし、ユーザーがポリシー要件を満たすことができない場合もあります。たとえば、組織が多要素認証をサポートしていない場合などです。 

現在、SharePoint ゲスト ユーザーにはポリシーが適用されません。 ゲストの関係は、SharePoint 内で維持されます。 ゲスト ユーザー アカウントには、認証サーバーでのアクセス ポリシーは適用されません。 ゲスト アクセスは、SharePoint で管理できます。

## <a name="does-a-sharepoint-online-policy-also-apply-to-onedrive-for-business"></a>SharePoint Online ポリシーは、OneDrive for Business にも適用されますか?
**A:** はい。

## <a name="why-cant-i-set-a-policy-on-client-apps-like-word-or-outlook"></a>Word や Outlook などのクライアント アプリにポリシーを設定できないのですが、なぜでしょうか?
**A:** 条件付きアクセス ポリシーは、サービスへのアクセスの要件を設定し、そのサービスへの認証を実行するときに適用されます。 ポリシーはクライアント アプリケーションで直接設定されるのではなく、サービスを呼び出すときに適用されます。 たとえば、SharePoint で設定したポリシーは SharePoint を呼び出すクライアントに適用され、Exchange で設定したポリシーは Outlook に適用されます。

## <a name="does-a-conditional-access-policy-apply-to-service-accounts"></a>条件付きアクセス ポリシーはサービス アカウントに適用されますか?
**A:** 条件付きアクセス ポリシーは、すべてのユーザー アカウントに適用されます。 これには、サービス アカウントとして使用されるユーザー アカウントも含まれます。 多くの場合、自動的に実行されるサービス アカウントはポリシーを満たすことができません。 例として、MFA が必要となる場面が挙げられます。 このような場合、条件付きアクセス ポリシーの管理設定を使用して、サービス アカウントをポリシーから除外できます。 ここで、ユーザーへのポリシーの適用についてさらに詳しく説明します。


