---
title: Azure Security Center でレコメンデーションを修復する | Microsoft Docs
description: このドキュメントでは、Azure リソースを保護してセキュリティ ポリシーを順守し続けるために、Azure Security Center でレコメンデーションを修復する方法について説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2019
ms.author: v-mohabe
ms.openlocfilehash: b947796d4af9def1a274f6e04f356c3173d29fff
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2019
ms.locfileid: "68778993"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Azure Security Center のセキュリティ レコメンデーション

レコメンデーションは、リソースのセキュリティを強化する方法を提案します。  レコメンデーションに記載されている修復手順に従って、レコメンデーションを実装します。 

## 修復手順 <a name="remediation-steps"></a>

すべてのレコメンデーションを確認したら、最初に修復するレコメンデーションを決定します。 [セキュリティ スコアの影響](security-center-recommendations.md#monitor-recommendations)を使用して、最初にすることの優先順位を決めることをお勧めします。

1. 一覧から、推奨事項をクリックします。
1. 「**修復の手順**」セクションの指示に従ってください。 レコメンデーションごとに、独自の指示のセットがあります。 HTTPS 経由のトラフィックのみを許可するようにアプリケーションを構成するための修復手順を次に示します。

    ![推奨事項の詳細](./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png)

1. 完了すると、修復が成功したかどうかを知らせる通知が表示されます。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Security Center でレコメンデーションを修復する方法について説明しました。 Security Center の詳細については、次のトピックを参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) -- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
