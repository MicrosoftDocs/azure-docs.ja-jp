---
title: Azure AD レポート API の概要 | Microsoft Docs
description: Azure Active Directory Reporting API の概要について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6ef122cd36c3d4e9135876647075f22158df0d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101653237"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory レポート API の概要

Azure Active Directory にはさまざまな[レポート](overview-reports.md)が用意されています。レポートには、SIEM システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに役立つ情報が掲載されています。 

Azure AD レポートに Microsoft Graph API を使用すると、一連の REST ベースの API を介してプログラムからデータにアクセスできます。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

この記事では、アクセス方法など、レポート API の概要について説明します。

問題が発生した場合は、「[Azure Active Directory のサポートを受ける方法](../fundamentals/active-directory-troubleshooting-support-howto.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

ユーザー操作の有無にかかわらず、レポート API にアクセスするには以下が必要です。

1. ロールを割り当てる (セキュリティ閲覧者、セキュリティ管理者、全体管理者)
2. アプリケーションを登録する
3. [アクセス許可の付与]
4. 構成設定を収集する

詳しい説明については、[Azure Active Directory レポート API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md)に関するページをご覧ください。 

## <a name="api-endpoints"></a>API エンドポイント 

監査ログ用の Microsoft Graph API エンドポイントは `https://graph.microsoft.com/v1.0/auditLogs/directoryAudits` であり、サインイン用の Microsoft Graph API エンドポイントは `https://graph.microsoft.com/v1.0/auditLogs/signIns` です。 詳細については、[監査 API リファレンス](/graph/api/resources/directoryaudit)と[サインイン API リファレンス](/graph/api/resources/signIn)のページを参照してください。

[Identity Protection リスク検出 API](/graph/api/resources/identityriskevent?view=graph-rest-beta) を使用すると、Microsoft Graph を使ってプログラムからセキュリティの検出にアクセスできます。 詳細については、「[Azure Active Directory Identity Protection と Microsoft Graph の基本](../identity-protection/howto-identity-protection-graph-api.md)」を参照してください。 
  
また、[プロビジョニング ログ API](/graph/api/resources/provisioningobjectsummary?view=graph-rest-beta) を使用して、テナント内のプロビジョニング イベントにプログラムからアクセスすることもできます。 

## <a name="apis-with-microsoft-graph-explorer"></a>Microsoft Graph Explorer を使用する API

[Microsoft Graph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)を使用してサインインを確認し、API データを監査できます。 Graph エクスプローラーの UI で、両方のサインイン ボタンを使用してアカウントにサインインし、次に示すようにテナントの **AuditLog.Read.All** および **Directory.ReadAll** のアクセス許可を設定します。   

![Graph エクスプローラー](./media/concept-reporting-api/graph-explorer.png)

![アクセス許可の変更 UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>証明書を使用して Azure AD レポート API にアクセスする 

ユーザーによる操作を必要としないでレポート データを取得することを計画している場合は、Azure AD レポート API と共に証明書を使ってください。

詳しくは、「[Azure Active Directory Reporting API と証明書を使用したデータの取得](tutorial-access-api-with-certificates.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

 * [Reporting API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md) 
 * [Azure AD Reporting API と証明書を使ってデータを取得する](tutorial-access-api-with-certificates.md)
 * [Azure Active Directory Reporting API でのエラーのトラブルシューティング](troubleshoot-graph-api.md)