---
title: "Azure AD レポート API の概要 | Microsoft Docs"
description: "Azure Active Directory Reporting API の概要について説明します"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/18/2017
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 9944cbd2b1b7c4acb18d37da1394c0bbc170f77d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory Reporting API の概要

Azure Active Directory は多様なレポートを提供しています。 これらのレポートのデータは、SIEM システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに非常に役立ちます。 Azure AD レポート API は、一連の REST ベースの API を使用してプログラムによってデータにアクセスできるようにします。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

この記事では、Azure AD のレポート API の使用を開始するために必要な情報を提供します。
次のセクションでは、監査 API およびサインイン API の使用に関して詳しく説明します。 

よく寄せられる質問については、こちらの [FAQ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-faq) を参照してください。 問題については、[サポート チケットを提出](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)してください。

## <a name="learning-map"></a>学習マップ
1. **準備** - API サンプルをテストするには、事前に、 [Azure AD Reporting API にアクセスするための前提条件](active-directory-reporting-api-prerequisites-azure-portal.md)を完了する必要があります。
2. **さらに詳しく** - レポート API の概要を取得します。
   
   * [監査 API のサンプルの使用](active-directory-reporting-api-audit-samples.md) 
   * [サインイン アクティビティ レポート API のサンプルの使用](active-directory-reporting-api-sign-in-activity-samples.md)
3. **カスタマイズ** - 独自のソリューションを作成します。 
   
   * [監査 API リファレンスの使用](active-directory-reporting-api-audit-reference.md) 
   * [サインイン アクティビティ レポート API リファレンスの使用](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>次のステップ
提供されているすべての Azure AD Graph API エンドポイントを確認するには、次のリンクを使用してください。[https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta](https://graph.windows.net/tenant-name/activities/$metadata?api-version=beta)

