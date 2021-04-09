---
title: Azure Sentinel に Azure Active Directory のデータを接続する | Microsoft Docs
description: Microsoft Azure Active Directory Domain Services からデータを収集し、Azure AD のサインイン ログ、監査ログ、プロビジョニング ログを Azure Sentinel にストリーミングする方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: f8931fedb380cf81d72b7b5280a5795498daaa57
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99251983"
---
# <a name="connect-azure-active-directory-azure-ad-data-to-azure-sentinel"></a>Azure Active Directory (Azure AD) データを Azure Sentinel に接続する

Azure Sentinel の組み込みコネクタを使用して、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) からデータを収集し、それを Azure Sentinel にストリーミングできます。 コネクタを使用すると、次の種類のログをストリーミングできます。

- [**サインイン ログ**](../active-directory/reports-monitoring/concept-all-sign-ins.md)には、ユーザーが認証要素を提供する [対話型ユーザー サインイン](../active-directory/reports-monitoring/concept-all-sign-ins.md#user-sign-ins)に関する情報が含まれています。

    Azure AD コネクタには、次の 3 つの追加カテゴリのサインイン ログが含まれるようになりました。現時点ではすべて **プレビュー** 段階です。
    
    - [**対話型ではないユーザー サインイン ログ**](../active-directory/reports-monitoring/concept-all-sign-ins.md#non-interactive-user-sign-ins)には、ユーザーからの操作や認証の要因なしで、ユーザーに代わってクライアントによって実行されるサインインに関する情報が含まれています。
    
    - [**サービス プリンシパルのサインイン ログ**](../active-directory/reports-monitoring/concept-all-sign-ins.md#service-principal-sign-ins)には、ユーザーが関与しないアプリとサービス プリンシパルによるサインインに関する情報が含まれています。 このサインインの場合、認証またはリソースへのアクセス用の資格情報が、アプリまたはサービスによって、それ自身のために提供されます。
    
    - [**マネージド ID サインイン ログ**](../active-directory/reports-monitoring/concept-all-sign-ins.md#managed-identity-for-azure-resources-sign-ins)には、Azure によってシークレットが管理されている Azure リソースによるサインインに関する情報が含まれています。 詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

- [**監査ログ**](../active-directory/reports-monitoring/concept-audit-logs.md)には、ユーザーとグループの管理、マネージド アプリケーション、およびディレクトリ アクティビティに関連するシステムの利用状況に関する情報が含まれています。

- [**プロビジョニング ログ**](../active-directory/reports-monitoring/concept-provisioning-logs.md) (**プレビュー**) には、Azure AD プロビジョニング サービスによってプロビジョニングされたユーザー、グループ、およびロールに関するシステムアクティビティ情報が含まれています。 

> [!IMPORTANT]
> 前述のように、使用可能なログの種類の一部は現在 **プレビュー** です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。
## <a name="prerequisites"></a>前提条件

- 任意の Azure AD ライセンス (Free、O365、P1、P2) があれば、Azure Sentinel にサインイン ログを取り込むことができます。 Azure Monitor (Log Analytics) と Azure Sentinel には、追加のギガバイト単位の料金が適用される場合があります。

- ユーザーには、ワークスペースの Azure Sentinel 共同作成者ロールを割り当てる必要があります。

- ユーザーには、ログをストリーミングするテナントの全体管理者またはセキュリティ管理者のロールを割り当てる必要があります。

- 接続の状態を確認できるようにするために、ユーザーは Azure AD 診断設定に対する読み取りおよび書き込みアクセス許可を持っている必要があります。 

## <a name="connect-to-azure-active-directory"></a>Azure Active Directory に接続する

1. Azure Sentinel で、ナビゲーション メニューから **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーで、 **[Azure Active Directory]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. Azure Sentinel にストリーミングする種類のログの隣のチェック ボックス (上記を参照) をオンにして、 **[接続]** をクリックします。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、次のテーブルの **[LogManagement]** セクションの下にある **[ログ]** にデータが表示されます。

- `SigninLogs`
- `AuditLogs`
- `AADNonInteractiveUserSignInLogs`
- `AADServicePrincipalSignInLogs`
- `AADManagedIdentitySignInLogs`
- `AADProvisioningLogs`

Azure AD ログを照会するには、クエ リ ウィンドウの上部に関連テーブル名を入力します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Active Directory を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
