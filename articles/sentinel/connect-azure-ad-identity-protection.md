---
title: Azure Sentinel Preview に Azure AD Identity Protection データを接続する | Microsoft Docs
description: Azure Sentinel に Azure AD Identity Protection データを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: c88c157c5f37bb0bd1e82225bdacfbd60806bbf8
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620637"
---
# <a name="connect-data-from-azure-ad-identity-protection"></a>Azure AD Identity Protection からデータを接続する

> [!IMPORTANT]
> 現在、Azure Sentinel はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

[Azure AD Identity Protection](https://docs.microsoft.com/azure/information-protection/reports-aip) から Azure Sentinel にログをストリーミングし、Azure Sentinel にストリーミングしたアラームのダッシュボードに表示し、カスタム アラームを作成し、調査を改善できます。 Azure Active Directory Identity Protection では、危険な状態のユーザー、リスク イベント、および脆弱性の統合ビューが用意されているほか、リスクを迅速に修復したり、今後のイベントを自動的に修復するようにポリシーを設定したりできます。 このサービスは、Microsoft のお客様の ID を保護する経験に基づいて構築されており、1 日 130 億を超えるログインからの信号によって高い精度を得ることができます。 


## <a name="prerequisites"></a>前提条件

- [Azure Active Directory Premium P1 または P2 ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)の所有
- グローバル理者またはセキュリティ管理者のアクセス許可を持つユーザー


## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD Identity Protection に接続する

Azure AD Identity Protection が既にある場合は、[お使いのネットワーク上で有効](../active-directory/identity-protection/enable.md)になっていることを確認します。
Azure AD Identity Protection がデプロイされ、データを取得している場合は、アラート データを Azure Sentinel に簡単にストリーミングできます。


1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure AD Identity Protection]** タイルをクリックします。

2. **[接続]** をクリックして、Azure AD Identity Protection のイベントの Azure Sentinel へのストリーミングを開始します。


6. Azure AD Identity Protection のアラートで Log Analytics の関連スキーマを使用するには、**IdentityProtectionLogs_CL** を検索します。

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure AD Identity Protection を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、以下の記事を参照してください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats.md)の概要。
