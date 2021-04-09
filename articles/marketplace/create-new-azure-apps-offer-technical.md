---
title: Azure アプリケーション オファーの技術的な詳細を追加する方法
description: パートナー センターで、Azure アプリケーション オファーの技術的な詳細を追加する方法について説明します。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 11/06/2020
ms.openlocfilehash: a8658242bca48ee490e7fd44582f46e7c9c78e5b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94369799"
---
# <a name="how-to-add-technical-details-for-your-azure-application-offer"></a>Azure アプリケーション オファーの技術的な詳細を追加する方法

この記事では、Microsoft コマーシャル マーケットプレースから貴社のソリューションに接続する場合の技術的な詳細を入力する方法について説明します。 貴社のオファーを顧客が取得および管理することを選択した場合、Microsoft ではこの接続を使用して、顧客にオファーをプロビジョニングします。

オファーに、[Marketplace の従量制課金 API](partner-center-portal/marketplace-metering-service-apis.md) を使用して測定イベントを生成するマネージド アプリケーションが含まれており、Azure AD セキュリティ トークンを使用して認証する予定のサービスがある場合にのみ、このセクションを完了してください。 詳細については、さまざまな認証オプションに関する「[Marketplace の測定サービスの認証方法](partner-center-portal/marketplace-metering-service-authentication.md)」を参照してください。

## <a name="technical-configuration-offer-level"></a>技術的な構成 (オファー レベル)

**[技術的な構成]** タブが適用されるのは、[Marketplace の従量制課金 API](partner-center-portal/marketplace-metering-service-apis.md) を使用して測定イベントを生成するマネージド アプリケーションを作成する場合のみです。 その場合は、次の手順を完了します。 そうでなければ、[次のステップ](#next-steps)に進んでください。 

これらのフィールドの詳細については、「[コマーシャル マーケットプレース用の Azure アプリケーション オファーを計画する](plan-azure-application-offer.md#technical-configuration)」を参照してください。

1. **[技術的な構成]** タブで、2 つのサービス間の接続が、認証された通信の背後にあることを検証するために使用される、**Azure Active Directory テナント ID** と **Azure Active Directory アプリケーション ID** を指定します。

1. **[下書きの保存]** を選択してから、次の[プランの概要] タブに移動します。

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション オファーのプランを作成する方法](create-new-azure-apps-offer-plans.md)
