---
title: Azure AD データを Azure Sentinel に接続する | Microsoft Docs
description: Azure Sentinel に Azure Active Directory データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0a8f4a58-e96a-4883-adf3-6b8b49208e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 43405bd3e97dbee325f0a5ed82c5848880775eee
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240810"
---
# <a name="connect-data-from-azure-active-directory"></a>Azure Active Directory からデータを接続する



Azure Sentinel では、[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) からデータを収集して、それを Azure Sentinel にストリーミングできます。 ストリームは[サインイン ログ](../active-directory/reports-monitoring/concept-sign-ins.md) と [監査ログ](../active-directory/reports-monitoring/concept-audit-logs.md) を選択できます。

## <a name="prerequisites"></a>前提条件

- サインイン データを Active Directory からエクスポートするには、Azure AD P1 または P2 ライセンスが必要です。

- ログからストリーミングするテナントに対してグローバル管理者アクセス許可またはセキュリティ管理者アクセス許可を持つユーザー。

- 接続の状態を確認できるようにするには、Azure AD の診断ログにアクセスするためのアクセス許可が必要です。 


## <a name="connect-to-azure-ad"></a>Azure への接続

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure Active Directory]** タイルをクリックします。

1. Azure Sentinel にストリーミングするログの隣の **[接続する]** をクリックします。

1. Azure AD のアラートによって Azure Sentinel で自動的にインシデントが生成されるようにするかどうかを選択できます。 **[Create incidents]\(インシデントの作成\)** で **[有効化]** を選択して、接続されたセキュリティ サービスで生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 次に、 **[分析]** でこのルールを編集してから、 **[Active rules]\(アクティブなルール\)** を選択します。

1. Azure AD アラートで Log Analytics 内の関連スキーマを使用するには、**SigninLogs** と **AuditLogs** を検索します。




## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure AD を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
