---
title: Azure Data Factory データ フローに対する予約割引について | Microsoft Docs
description: 実行中の ADF データ フローに予約割引がどのように適用されるかについて説明します。 割引は、これらのデータ フローに時間単位で適用されます。
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: makromer
ms.openlocfilehash: 12b640fd97f48e293320593b33ab2fdc54980c0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716297"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-data-factory-data-flows"></a>Azure Data Factory データ フローに対する予約割引の適用方法

ADF データ フローの予約容量を購入した後は、予約のコンピューティングの種類とコア数に一致する Azure 統合ランタイムを使用するデータ フローに対して、予約割引が自動的に適用されます。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、"*使用しないと失われます*"。 したがって、一致する Azure 統合リソースがある時間に使用されていない場合、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

データ フローに対する統合ランタイムの使用を停止すると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は "*失われます*"。

## <a name="discount-applied-to-adf-data-flows"></a>ADF データ フローに適用される割引

ADF データ フローの予約容量割引は、統合ランタイムの実行に対して時間単位で適用されます。 購入した予約は、お使いのデータ フローの統合ランタイムで使用されるコンピューティングの種類と照合されます。 実行時間が 1 時間に満たないデータ フローの場合、その予約は予約の属性に一致する他のデータ フローに自動的に適用されます。 この割引は、同時に実行されているデータ フローにも適用できます。 予約の属性に一致するデータ フローのうち、実行時間が 1 時間を超えるデータ フローがない場合は、その時間について予約割引の特典を完全に活用することができません。

次の例は、購入したコア数と実行する時間に応じて、ADF データ フローの予約容量割引がどのように適用されるかを示しています。

- シナリオ 1:メモリ最適化のコンピューティングの種類の数量として「80」を入力して、ADF データ フロー用の 1 時間の 80 コアでのメモリ最適化コンピューティングの予約を購入します。 Azure 統合ランタイムを 144 コアのメモリ最適化に設定して、1 時間データ フローを実行します。 1 時間のデータ フロー使用量のうち 64 コア分には従量課金制の料金が適用されます。 80 コアのメモリ最適化使用量の 1 時間分には予約割引が適用されます。
- シナリオ 2: 汎用のコンピューティングの種類の数量として「32」を入力して、ADF データ フロー用の 1 時間の 32 コアでの汎用コンピューティングの予約を購入します。 32 コアの一般コンピューティングの Azure 統合ランタイムを使用して 1 時間データ フローをデバッグします。 使用した時間全体に対して予約割引が適用されます。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[Azure の予約の使用状況](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)に関するページを参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート要求を作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)