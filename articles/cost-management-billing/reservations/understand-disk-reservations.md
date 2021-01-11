---
title: Azure ディスク ストレージに対する予約割引について
description: Azure Premium SSD マネージド ディスクに Azure ディスク予約割引が適用されるしくみについて説明します。
author: roygara
ms.author: rogarana
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: cd89c3ddc3c58de02f1104109ce7f243c4d1e6fd
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682452"
---
# <a name="understand-how-your-reservation-discount-is-applied-to-azure-disk-storage"></a>Azure のディスク ストレージに予約割引が適用されるしくみについて

Azure ディスク予約容量を購入すると、予約の条件に一致するディスク リソースに予約割引が自動的に適用されます。 予約割引は、ディスク SKU にのみ適用されます。 ディスク スナップショットは従量課金制で課金されます。

Azure ディスク予約の詳細については、[Azure ディスク予約によるコストの節約](../../virtual-machines/disks-reserved-capacity.md)に関するページをご覧ください。 Azure ディスク予約の価格の詳細については、[Azure Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)に関するページをご覧ください。

## <a name="how-the-reservation-discount-is-applied"></a>予約割引の適用方法

Azure ディスク予約の割引は、使用しないと失われる割引です。 これは時間単位でマネージド ディスク リソースに適用されます。 特定の時間において、予約条件を満たすマネージド ディスク リソースがない場合、その時間の予約数量は失われます。 未使用の時間を繰り越すことはできません。

リソースを削除すると、指定されたスコープ内の別の一致するリソースに予約割引が自動的に適用されます。 一致するリソースが見つからない場合、予約された時間は失われます。

## <a name="discount-examples"></a>割引の例

次の例は、デプロイに応じて、Azure ディスク予約割引がどのように適用されるかを示しています。

1 年間、米国西部 2 リージョンで 100 個の P30 ディスクを購入して予約したとします。 各ディスクには、約 1 TiB の記憶域があります。 この例の予約のコストは 140,100 ドルであると仮定します。 全額を前払いするか、または次の 12 か月間について固定月額分割払いで毎月 11,675 ドルを支払うかを選択できます。

次のシナリオでは、予約容量の過少使用、過多使用、または積み上げの場合にどうなるかを説明します。 以下の例では、毎月の予約支払いプランに登録したと仮定します。

### <a name="underusing-your-capacity"></a>容量の使用量が過少だった場合

100 個の予約済み Azure Premium ソリッドステート ドライブ (SSD) P30 ディスクのうち 99 個のみを予約期間内に 1 時間デプロイしたとします。 その時間について、残りの P30 ディスクには適用されません。 また、繰り越すこともできません。

### <a name="overusing-your-capacity"></a>容量の使用量が過多だった場合

予約期間内の特定の 1 時間に、101 個の Premium SSD P30 ディスクを使用したとします。 予約割引は、100 個の P30 ディスクにのみ適用されます。 その時間について、残りの P30 ディスクは従量課金制で課金されます。 次の 1 時間の使用量が 100 個の P30 ディスクに減少した場合は、すべての使用量が予約の対象になります。

### <a name="tiering-your-capacity"></a>容量の使用量を積み上げた場合

予約期間内の特定の 1 時間に、合計 200 個の P30 Premium SSD を使用したいとします。 また、最初の 30 分間に 100 個だけを使用するとします。 100 個の P30 ディスクを予約しているため、この間の使用量がすべて対象になります。 この後、最初の 100 の使用を中止し (このため、使用量はゼロになります)、残りの 30 分間に別の 100 の使用を開始すると、その使用量も予約の対象になります。

![容量の過少使用、過多使用、積み上げの例](media/understand-disk-reservations/reserved-disks-example-scenarios.png)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Azure ディスク予約によるコストの削減](../../virtual-machines/disks-reserved-capacity.md)
- [Azure の予約とは](save-compute-costs-reservations.md)
