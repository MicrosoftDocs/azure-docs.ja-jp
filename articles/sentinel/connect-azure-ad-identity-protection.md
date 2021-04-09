---
title: Azure Sentinel に Azure AD Identity Protection データを接続する方法
description: Azure AD Identity Protection から Azure Sentinel にログとアラートをストリーミングしてダッシュボードを表示し、カスタム アラートを作成し、調査を改善する方法を説明します。
author: yelevin
manager: rkarlin
ms.assetid: 91c870e5-2669-437f-9896-ee6c7fe1d51d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 06/24/2020
ms.author: yelevin
ms.openlocfilehash: f420e80f10072c440f5401c042d6370a061b1a7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98632243"
---
# <a name="connect-data-from-azure-active-directory-azure-ad-identity-protection"></a>Azure Active Directory (Azure AD) Identity Protection からデータを接続する

[Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) から Azure Sentinel にログをストリーミングし、Azure Sentinel にストリーミングしたアラームのダッシュボードに表示し、カスタム アラームを作成し、調査を改善できます。 Azure Active Directory Identity Protection では、危険な状態のユーザー、リスク検出、および脆弱性の統合ビューが用意されているほか、リスクを迅速に修復したり、今後のイベントを自動的に修復するようにポリシーを設定したりできます。 このサービスは、Microsoft のお客様の ID を保護する経験に基づいて構築されており、1 日 130 億を超えるログインからの信号によって高い精度を得ることができます。 

## <a name="prerequisites"></a>前提条件

- [Azure AD Premium P2 サブスクリプション](https://azure.microsoft.com/pricing/details/active-directory/)が必要です。
- グローバル管理者またはセキュリティ管理者のアクセス許可を持つユーザーが必要です。

## <a name="connect-to-azure-ad-identity-protection"></a>Azure AD Identity Protection に接続する

Azure AD Premium P2 サブスクリプションを使用している場合は、Azure AD Identity Protection が含まれます。 [ポリシーが有効](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)になっており、アラートが生成されている場合は、アラート データを簡単に Azure Sentinel にストリーミングできます。

1. Azure Sentinel で **[Data connectors]\(データ コネクタ\)** を選択し、 **[Azure AD Identity Protection]** タイルをクリックします。

1. **[接続]** をクリックして、Azure AD Identity Protection のイベントの Azure Sentinel へのストリーミングを開始します。

1. Azure AD Identity Protection のアラートで Log Analytics の関連スキーマを使用するには、**SecurityAlert** を検索します。

コネクタをテストする場合は、[検出をシミュレート](../active-directory/identity-protection/howto-identity-protection-simulate-risk.md)して、Azure Sentinel にストリーミングされるサンプル アラートを生成することができます。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure AD Identity Protection を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
