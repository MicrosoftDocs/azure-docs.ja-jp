---
title: スマート検出の通知変更 - Azure Application Insights
description: スマート検出の既定の通知受信者を変更します。 スマート検出では、Azure Application Insights を利用し、トレース テレメトリに異常なパターンがないか、アプリケーション トレースを監視できます。
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8e2bf4e451ebc3c9ebba2c01dae6703fc79aa606
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324133"
---
# <a name="smart-detection-e-mail-notification-change"></a>スマート検出の電子メール通知に関する変更

お客様からのフィードバックに基づいて、2019 年 4 月 1 日付けで、スマート検出から電子メール通知を受信する既定のロールが変更されます。

## <a name="what-is-changing"></a>何が変わるのですか?

現在、スマート検出の電子メール通知は、既定では _サブスクリプションの所有者_、_サブスクリプションの共同作成者_、および _サブスクリプションの閲覧者_ の各ロールに送信されています。 これらのロールには、多くの場合、監視に積極的に関与していないユーザーが含まれているため、多数のユーザーが不必要に通知を受信する原因になっています。 このエクスペリエンスを改善するために、電子メール通知は既定では[監視閲覧者](../../role-based-access-control/built-in-roles.md#monitoring-reader)ロールと[監視共同作業者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)ロールのみに送信されるようにするための変更が進行しています。

## <a name="scope-of-this-change"></a>この変更の範囲

この変更は、以下を除くすべてのスマート検出ルールに影響します。

* プレビューとしてマークされているスマート検出ルール。 これらのスマート検出ルールでは、現在、電子メール通知はサポートされていません。

* 失敗の異常ルール。 このルールは、クラシック アラートから統合アラート プラットフォームに移行された後、新しい既定のロールへの適用が開始されます (詳細については、[こちら](../platform/monitoring-classic-retirement.md)をご覧ください)。

## <a name="how-to-prepare-for-this-change"></a>この変更に備える方法は?

スマート検出のメール通知が関連するユーザーに確実に送信されるようにするには、それらのユーザーをサブスクリプションの[監視閲覧者](../../role-based-access-control/built-in-roles.md#monitoring-reader)ロールまたは[監視共同作成者](../../role-based-access-control/built-in-roles.md#monitoring-contributor)ロールに割り当てる必要があります。

Azure portal を使用してユーザーを監視閲覧者または監視共同作業者に割り当てるには、「[ロールの割り当てを追加する](../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment)」で説明されている手順に従います。 ユーザーが割り当てられるロールとして、必ず "_監視閲覧者_" または "_監視共同作業者_" を選択してください。

> [!NOTE]
> ルール設定の "_追加の電子メール受信者_" オプションを使用して構成されるスマート検出通知の特定の受信者は、この変更による影響を受けません。 これらの受信者は、引き続き電子メール通知を受信します。

この変更に関する質問や懸念がある場合は、ご遠慮なく[お問い合わせ](mailto:smart-alert-feedback@microsoft.com)ください。

## <a name="next-steps"></a>次のステップ

スマート検出の詳細を確認します。

- [失敗の異常](./proactive-failure-diagnostics.md)
- [メモリ リーク](./proactive-potential-memory-leak.md)
- [パフォーマンスの異常](./proactive-performance-diagnostics.md)

