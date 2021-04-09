---
title: Azure Active Directory Reporting API でのエラーのトラブルシューティング | Microsoft Docs
description: Azure Active Directory Reporting API の呼び出し中のエラーの解決方法を提供します。
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0030c5a4-16f0-46f4-ad30-782e7fea7e40
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: abc8badf261e631dd6ceb7af9a6a0cb3676ae25d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017595"
---
# <a name="troubleshoot-errors-in-azure-active-directory-reporting-api"></a>Azure Active Directory Reporting API でのエラーのトラブルシューティング

この記事では、Microsoft Graph API を使用してアクティビティ レポートにアクセスする際に生じる可能性のある一般的なエラー メッセージと、その解決手順を示します。

### <a name="500-http-internal-server-error-while-accessing-microsoft-graph-v2-endpoint"></a>Microsoft Graph V2 エンドポイントへのアクセス中の 500 HTTP 内部サーバー エラー

現在、Microsoft Graph v2 エンドポイントはサポートされていません。必ず、Microsoft Graph v1 エンドポイントを使用して、アクティビティ ログにアクセスしてください。

### <a name="error-neither-tenant-is-b2c-or-tenant-doesnt-have-premium-license"></a>エラー: テナントが B2C ではなく、テナントに Premium ライセンスもありません

サインイン レポートへのアクセスには、Azure Active Directory Premium 1 (P1) ライセンスが必要です。 サインインへのアクセス中にこのようなエラー メッセージが表示された場合は、テナントに Azure AD P1 ライセンスがあることを確認してください。

### <a name="error-user-is-not-in-the-allowed-roles"></a>エラー: 許可されているロールのユーザーではありません 

API を使用して監査ログやサインインにアクセスしようとしたときにこのようなエラー メッセージが表示された場合は、ご利用のアカウントが、Azure Active Directory テナントの **セキュリティ閲覧者** または **レポート閲覧者** のロールに属していることを確認してください。 

### <a name="error-application-missing-aad-read-directory-data-permission"></a>エラー:アプリケーションに AAD の 'ディレクトリ データの読み取り' アクセス許可がありません 

「[Azure Active Directory レポート API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md)」の手順に従って、ご利用のアプリケーションが適切なアクセス許可セットを使用して実行されていることを確認してください。 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>エラー:アプリケーションに Microsoft Graph API の 'すべての監査ログ データの読み取り' アクセス許可がありません

「[Azure Active Directory レポート API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md)」の手順に従って、ご利用のアプリケーションが適切なアクセス許可セットを使用して実行されていることを確認してください。 

## <a name="next-steps"></a>次の手順

[監査 API リファレンスを使用する](/graph/api/resources/directoryaudit?view=graph-rest-beta)
[サインイン アクティビティ レポート API リファレンスを使用する](/graph/api/resources/signin?view=graph-rest-beta)