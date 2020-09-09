---
title: Azure Sentinel に Azure Active Directory のデータを接続する | Microsoft Docs
description: Azure Active Directory からデータを収集し、Azure AD のサインイン ログと監査ログを Azure Sentinel にストリーミングする方法について説明します。
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
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 279f54c3de964580cc37d1288a6e1b7726348e10
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208626"
---
# <a name="connect-data-from-azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD) からデータを接続する



Azure Sentinel の組み込みコネクタを使用して、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) からデータを収集し、それを Azure Sentinel にストリーミングできます。 コネクタを使用すると、[サインイン ログ](../active-directory/reports-monitoring/concept-sign-ins.md)と[監査ログ](../active-directory/reports-monitoring/concept-audit-logs.md)をストリーミングできます。

## <a name="prerequisites"></a>前提条件


- 任意の Azure AD ライセンス (Free、O365、P1、P2) があれば、Azure Sentinel にサインイン ログを取り込むことができます。 Azure Monitor (Log Analytics) と Azure Sentinel には、追加のギガバイト単位の料金が適用される場合があります。

- ユーザーには、ワークスペースの Azure Sentinel 共同作成者ロールを割り当てる必要があります。

- ユーザーには、ログをストリーミングするテナントの全体管理者またはセキュリティ管理者のロールを割り当てる必要があります。

- 接続の状態を確認できるようにするために、ユーザーは Azure AD 診断設定に対する読み取りおよび書き込みアクセス許可を持っている必要があります。 


## <a name="connect-to-azure-active-directory"></a>Azure Active Directory に接続する

1. Azure Sentinel で、ナビゲーション メニューから **[Data connectors]\(データ コネクタ\)** を選択します。

1. データ コネクタ ギャラリーで、 **[Azure Active Directory]** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. Azure Sentinel にストリーミングするログの隣のチェック ボックスをオンにして、 **[接続]** をクリックします。

1. Azure AD のアラートによって Azure Sentinel でインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents]\(インシデントの作成\)** で **[有効化]** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[分析]** でこのルールを編集してから、 **[Active rules]\(アクティブなルール\)** を選択します。

1. Azure AD のアラートのクエリを実行するために Log Analytics 内の関連スキーマを使用するには、クエリ ウィンドウで「`SigninLogs`」または「`AuditLogs`」と入力します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Azure Active Directory を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
