---
title: Azure Security Center でのセキュリティの警告の管理 | Microsoft Docs
description: このドキュメントは、Azure セキュリティ センターの機能を使用してセキュリティの警告の管理と対応することに役立ちます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 490f94c71611e07e765f5882cb4e3eec13033a6a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102099337"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Azure Security Center でのセキュリティ アラートの管理と対応

このトピックでは、Security Center のアラートを表示および処理し、リソースを保護する方法について説明します。

セキュリティ アラートをトリガーする高度な検出は、Azure Defender でのみ使用できます。 無料試用版が提供されています。 アップグレードするには、「[Azure Defender を有効にする](enable-azure-defender.md)」をご覧ください。

## <a name="what-are-security-alerts"></a>セキュリティの警告とは何か
Security Center は、真の脅威を検出し、偽陽性を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。 Security Center には、優先順位の付いたセキュリティの警告の一覧が表示されます。また、すぐに問題を調査する必要がある情報や、攻撃を受けたものを修復する方法についての推奨事項も表示されます。

さまざまな種類のアラートについては、「[セキュリティ アラート - リファレンス ガイド](alerts-reference.md)」をご覧ください。

Security Center によってアラートが生成される方法の概要については、「[Azure Security Center での脅威の検出と対応](security-center-alerts-overview.md)」をご覧ください。


## <a name="manage-your-security-alerts"></a>セキュリティ アラートの管理

1. Security Center の概要ページの上部にある **[セキュリティ アラート]** タイルまたはサイドバーのリンクを選択します。

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Azure Security Center の概要ページからの [セキュリティ アラート] ページの表示":::

    [セキュリティ アラート] ページが開きます。

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure Security Center のセキュリティ アラートの一覧":::

1. アラートの一覧をフィルター処理するには、関連するフィルターを選択します。 必要に応じて、 **[フィルターの追加]** オプションを使用して、さらにフィルターを追加することもできます。

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="アラート ビューへのフィルターの追加" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    この一覧は、選択したフィルター オプションに応じて更新されます。 フィルター処理はとても有益な機能です。 たとえば、システム内の潜在的な違反を調査するために、過去 24 時間以内に発生したセキュリティの警告を確認することができます。


## <a name="respond-to-security-alerts"></a>セキュリティの警告への対応

1. **[セキュリティ アラート]** リストから、アラートを選択します。 作業ウィンドウが開き、アラートとその影響を受けたすべてのリソースの説明が表示されます。 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="セキュリティ アラートのミニ詳細ビュー":::

    > [!TIP]
    > この作業ウィンドウを開いた状態で、キーボードの上矢印と下矢印を使用して、アラートの一覧をすばやく確認できます。

1. 詳細については、 **[すべての詳細を表示]** を選択します。

    セキュリティ アラート ページの左側のウィンドウには、セキュリティ アラートに関する概要情報 (タイトル、重要度、状態、アクティビティ時間、疑わしいアクティビティの説明、影響を受けるリソース) が表示されます。 影響を受けるリソースと共に、リソースに関連する Azure タグがあります。 これらを使用して、アラートを調査するときにリソースの組織コンテキストを推測します。

    右側のペインには、問題の調査に役立つアラートの詳細が含まれている **[アラートの詳細]** タブがあります。IP アドレス、ファイル、プロセスなど。
     
    ![セキュリティのアラートに対処する方法の推奨事項](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    また、右側のペインには **[アクションの実行]** タブがあります。このタブを使用して、セキュリティ アラートに関するその他のアクションを実行します。 次のようなアクションがあります。
    - *[Mitigate the threat]\(脅威の軽減\)* - このセキュリティ アラートに対する手動の修復手順を提供します
    - *[Prevent future attacks]\(将来の攻撃防止\)* - セキュリティに関する推奨事項を提供して、攻撃対象を減らし、セキュリティ体制を強化し、将来の攻撃を防ぐことができるようにします
    - *[Trigger automated response]\(自動応答のトリガー\)* - このセキュリティ アラートへの応答としてロジック アプリをトリガーするオプションを提供します
    - *[Suppress similar alerts]\(類似のアラートの抑制\)* - 組織に関連しないアラートの場合、同様の特性を持つ今後のアラートを抑制するオプションを提供します

    ![[アクションの実行] タブ](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>関連項目

このドキュメントでは、セキュリティ アラートを表示する方法について説明しました。 関連資料については、次のページを参照してください。

- [アラートの抑制ルールを構成する](alerts-suppression-rules.md)
- [Security Center のトリガーへの応答を自動化する](workflow-automation.md)
- [セキュリティ アラート - リファレンス ガイド](alerts-reference.md)
