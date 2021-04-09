---
title: Azure Security Center でのセキュリティ インシデントの管理 | Microsoft Docs
description: このドキュメントは、Azure Security Center を使用してセキュリティ インシデントを処理するために役立ちます。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 722a508679c74f9d62df07575ffa1006528f4398
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652108"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Azure Security Center でのセキュリティ インシデントの管理

セキュリティ アラートのトリアージと調査は、熟練のセキュリティ アナリストでさえ長時間を要することのある作業です。 多くのユーザーにとっては、どこから始めればよいかを判断することも困難です。 

Security Center では、[分析](./security-center-alerts-overview.md)を使用して個別の[セキュリティ アラート](security-center-managing-and-responding-alerts.md)間の情報を接続します。 これらの接続を使用することで、Security Center では攻撃者の行動や影響を受けるリソースを理解するのに役立つ、攻撃活動とその関連アラートの単一ビューを作成します。

このページでは、Security Center のインシデントの概要について説明します。

## <a name="what-is-a-security-incident"></a>セキュリティ インシデントとは

Security Center でのセキュリティ インシデントとは、 [キル チェーン](alerts-reference.md#intentions) のパターンに一致するリソースに関するすべての警告を集約したものです。 インシデントは [[セキュリティ アラート]](security-center-managing-and-responding-alerts.md) ページの一覧に表示されます。 インシデントを選択すると、関連するアラートと詳細情報が表示されます。

## <a name="managing-security-incidents"></a>セキュリティ インシデントの管理

1. Security Center のアラート ページで、 **[フィルターの追加]** ボタンを使用してアラート名によってフィルター処理し、アラート名 **[複数のリソースでセキュリティ インシデントが検出されました]** に絞り込みます。 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="Azure Security Center のアラート ページでインシデントを特定する":::

    一覧がフィルター処理され、インシデントのみが表示されるようになりました。 セキュリティ インシデントには、セキュリティ アラートとは異なるアイコンが付いています。

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Azure Security Center のアラート ページのインシデント一覧":::

1. インシデントの詳細を表示するには、一覧から 1 つを選択します。 サイド ペインに、インシデントの詳細が表示されます。

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="インシデントの詳細を示すサイド ペイン":::

1. 詳細を表示するには、 **[すべての詳細を表示]** を選択します。

    [![Azure Security Center でのセキュリティ インシデントへの対応](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    セキュリティ インシデント ページの左側のウィンドウには、セキュリティ インシデントに関する概要情報 (タイトル、重要度、状態、アクティビティ時間、説明、影響を受けるリソース) が表示されます。 影響を受けるリソースの横に、関連する Azure タグが表示されます。 これらのタグを使用して、アラートを調査するときにリソースの組織コンテキストを推測します。

    右側のウィンドウには、このインシデントの一部として関連付けられたセキュリティ アラートを含む **[アラート]** タブが表示されます。 

    >[!TIP]
    > 具体的なアラートの詳細については、そのアラートを選択してください。 

    [![インシデントの [アクションの実行] タブ](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    **[アクションの実行]** タブに切り替えるには、右側のウィンドウの下部にあるタブまたはボタンを選択します。 このタブを使用すると、次のような操作を実行できます。
    - *[Mitigate the threat]\(脅威の軽減\)* - このセキュリティ インシデントに対する手動の修復手順を提供します
    - *[Prevent future attacks]\(将来の攻撃防止\)* - セキュリティに関する推奨事項を提供して、攻撃対象を減らし、セキュリティ体制を強化し、将来の攻撃を防ぐことができるようにします
    - *[Trigger automated response]\(自動応答のトリガー\)* - このセキュリティ インシデントへの応答としてロジック アプリをトリガーするオプションを提供します
    - *[Suppress similar alerts]\(類似のアラートの抑制\)* - 組織に関連しないアラートの場合、同様の特性を持つ今後のアラートを抑制するオプションを提供します 

   > [!NOTE]
   > 同じアラートが 1 つのインシデントに含まれることもあれば、スタンドアロン アラートとして表示されることもあります。

1. インシデントの脅威を修復するには、各アラートに対する修復手順に従います。


## <a name="next-steps"></a>次のステップ

このページでは、Security Center のセキュリティ インシデント機能について説明しました。 関連情報については、次のページを参照してください。

- [Security Center のセキュリティ アラート](security-center-alerts-overview.md)
- [セキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)