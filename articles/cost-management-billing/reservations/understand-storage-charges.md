---
title: Azure Storage に予約割引が適用されるしくみについて | Microsoft Docs
description: Azure Storage の予約容量割引がブロック BLOB と Azure Data Lake Storage Gen2 リソースにどのように適用されるかについて説明します。
author: tamram
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: tamram
ms.openlocfilehash: fc1110c124f5e4e6d30c27de0911f86c186b67c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681636"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-storage"></a>Azure Storage に予約割引が適用されるしくみについて

Azure Storage の予約容量を購入すると、予約の条件に一致するブロック BLOB と Azure Data Lake Storage Gen2 リソースに予約割引が自動的に適用されます。 予約割引は、ストレージ容量にのみ適用されます。 帯域幅と要求レートは従量課金制で課金されます。

Azure Storage の予約容量の詳細については、[予約容量を使用して BLOB ストレージのコストを最適化する方法](../../storage/blobs/storage-blob-reserved-capacity.md)に関する記事を参照してください。

Azure Storage の容量予約の価格については、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」と「[Azure Data Lake Storage Gen 2 の価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)」を参照してください。

## <a name="how-the-reservation-discount-is-applied"></a>予約割引の適用方法

Azure Storage の予約容量割引は、ブロック BLOB と Azure Data Lake Storage Gen2 リソースに時間単位で適用されます。

Azure Storage の予約容量割引は、"使用しないと失われる" 割引です。 特定の 1 時間に予約の条件を満たすブロック BLOB または Azure Data Lake Storage Gen2 リソースがない場合、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースを削除すると、指定されたスコープ内の別の一致するリソースに予約割引が自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は失われます。

## <a name="discount-examples"></a>割引の例

以下の例は、デプロイに応じて、Azure Storage の予約容量割引がどのように適用されるかを示しています。

米国西部 2 リージョンで 1 年間 100 TB の予約容量を購入したとします。 この予約は、ホット アクセス層のローカル冗長ストレージ (LRS) に適用されます。

このサンプル予約のコストは 18,540 ドルであると仮定します。 次の 12 か月間について、全額を支払うか、月あたり 1,545 ドルの固定月額割賦払いを行うかを選択できます。

以下の例では、毎月の予約支払いプランに登録したと仮定します。 以下のシナリオでは、予約容量の使用量が過少または過多だった場合にどうなるかを説明します。

### <a name="underusing-your-capacity"></a>容量の使用量が過少だった場合

予約期間内の特定の 1 時間に、100 TB の予約容量のうち 80 TB のみを使用したとします。 残りの 20 TB は、その時間には適用されず、引き継がれません。

### <a name="overusing-your-capacity"></a>容量の使用量が過多だった場合

予約期間内の特定の 1 時間に、101 TB のストレージ容量を使用したとします。 100 TB のデータについては予約割引が適用され、残りの 1 TB についてはその時間の従量課金制料金で課金されます。 次の 1 時間の使用量が 100 TB に変わった場合は、すべての使用量が予約の対象になります。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [予約容量を使用して BLOB ストレージのコストを最適化する](../../storage/blobs/storage-blob-reserved-capacity.md)
- [Azure の予約とは](save-compute-costs-reservations.md)
