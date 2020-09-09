---
title: Azure Cosmos DB における予約割引について
description: Azure Cosmos DB でプロビジョニングされるスループット (RU/秒) に予約割引を適用するしくみについて説明します。
author: bandersmsft
ms.author: banders
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: bfa84a993fafe9240ba765de8d87ea4afb69cbd5
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2020
ms.locfileid: "84015949"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-cosmos-db"></a>Azure Cosmos DB に予約割引が適用されるしくみについて

Azure Cosmos DB の予約容量を購入すると、予約の属性や数量に合致する Azure Cosmos DB リソースに予約割引が自動的に適用されます。 予約では、Azure Cosmos DB リソース用にプロビジョニングされたスループットがカバーされます。 ソフトウェア、ネットワーク、ストレージ、定義済みコンテナーの料金はカバーされません。

## <a name="how-reservation-discount-is-applied"></a>予約割引の適用方法

予約割引は、"*使用しないと失われます*"。 したがって、ある時間、一致するリソースがない場合は、その時間に対する予約量は失われます。 未使用の予約済み時間を繰り越すことはできません。

リソースをシャットダウンすると、予約割引は、指定されたスコープ内の別の一致するリソースに自動的に適用されます。 指定したスコープ内に一致するリソースが見つからない場合、予約済み時間は "*失われます*"。

## <a name="reservation-discount-applied-to-azure-cosmos-db-accounts"></a>Azure Cosmos DB アカウントに適用される予約割引

予約割引は[プロビジョニングされたスループット](../../cosmos-db/request-units.md) (RU/秒) に時間単位で適用されます。 Azure Cosmos DB リソースがまる 1 時間実行されない場合、予約属性に合致する他の Azure Cosmos DB リソースに予約割引が自動的に適用されます。 この割引は、同時に実行されている Azure Cosmos DB リソースに適用できます。 Azure Cosmos DB リソースがまる 1 時間実行されず、予約属性に合致するリソースが他にない場合、その時間については、予約割引の特典が完全に活用されません。

割引は階層化されています。 予約の要求ユニットの数が多ければ、それだけ大きな割引が受けられます。

予約購入では、リージョンのオンデマンド価格に等しい率ですべてのリージョンに割引が適用されます。 各リージョンの予約割引率については、この記事の「[リージョン別の予約割引](#reservation-discount-per-region)」セクションをご覧ください。

## <a name="reservation-discount-per-region"></a>リージョン別の予約割引
予約割引は、時間単位で Azure Cosmos DB スループットに適用されます。 単一サブスクリプションまたは登録済み/アカウントのスコープのいずれかで適用されます。 予約割引は、次の比率で、さまざまなリージョンのメーター使用量に適用されます。

|メーターの説明  |リージョン |比率  |
|---------|---------|---------|
|Azure Cosmos DB - 100 RU/秒/時間 - アジア太平洋南東部  |  アジア太平洋南東部    |   1      |
|Azure Cosmos DB - 100 RU/秒/時間 - アジア太平洋東部 |   アジア太平洋東部   |    1     |
|Azure Cosmos DB - 100 RU/秒/時間 - 北ヨーロッパ|  北ヨーロッパ       |    1     |
|Azure Cosmos DB - 100 RU/秒/時間 - 韓国南部|    韓国南部     |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 西ヨーロッパ|    西ヨーロッパ     |      1   |
|Azure Cosmos DB - 100 RU/秒/時間 - 韓国中部|   韓国中部    |       1  |
|Azure Cosmos DB - 100 RU/秒/時間 - 英国南部|   英国南部      |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 英国西部|   英国西部      |    1     |
|Azure Cosmos DB - 100 RU/秒/時間 - 英国北部 |   英国北部    |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 英国南部 2|   英国南部 2      |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国東部 2|  米国東部 2     |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国中北部|   米国中北部      |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国西部|   米国西部      |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国中部| 米国中部        |     1    |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国西部 2|   米国西部 2      |      1   |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国中西部|   米国中西部      |       1  |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国東部|   米国東部      |  1       |
|Azure Cosmos DB - 100 RU/秒/時間 - 南アフリカ北部|     南アフリカ北部    |   1      |
|Azure Cosmos DB - 100 RU/秒/時間 - 南アフリカ西部 |    南アフリカ西部      |    1     |
|Azure Cosmos DB - 100 RU/秒/時間 - インド南部|    インド南部     |    1.0375    |
|Azure Cosmos DB - 100 RU/秒/時間 - カナダ東部|   カナダ東部      |    1.1      |
|Azure Cosmos DB - 100 RU/秒/時間 - 東日本|   東日本      |    1.125     |
|Azure Cosmos DB - 100 RU/秒/時間 - 西日本|     西日本    |   1.125       |
|Azure Cosmos DB - 100 RU/秒/時間 - インド西部|     インド西部    |    1.1375     |
|Azure Cosmos DB - 100 RU/秒/時間 - インド中部|    インド中部     |  1.1375       |
|Azure Cosmos DB - 100 RU/秒/時間 - オーストラリア東部|     オーストラリア東部    |   1.15       |
|Azure Cosmos DB - 100 RU/秒/時間 - カナダ中部|  カナダ中部       |   1.2       |
|Azure Cosmos DB - 100 RU/秒/時間 - フランス中部|   フランス中部      |    1.25      |
|Azure Cosmos DB - 100 RU/秒/時間 - ブラジル南部|  ブラジル南部       |   1.5      |
|Azure Cosmos DB - 100 RU/秒/時間 - オーストラリア中部|   オーストラリア中部      |   1.5      |
|Azure Cosmos DB - 100 RU/秒/時間 - オーストラリア中部 2| オーストラリア中部 2        |    1.5     |
|Azure Cosmos DB - 100 RU/秒/時間 - フランス南部|    フランス南部     |    1.625     |

## <a name="scenarios-that-show-how-the-reservation-discount-is-applied"></a>予約割引が適用されるしくみを示すシナリオ

予約に次の要件があるとします。

* 必要なスループット:50,000 RU/秒  
* 使用されるリージョン:2

この場合、この 2 つのリージョンで 100 RU/秒メーターの数量が 500 になり、それに対して合計オンデマンド料金が請求されます。 RU/秒の合計消費量は 1 時間あたり 100,000 です。

**シナリオ 1**

たとえば、米国中北部と米国西部のリージョンで Azure Cosmos DB のデプロイが必要であるとします。 各リージョンのスループット消費量は 50,000 RU/秒です。 100,000 RU/秒を予約購入すると、オンデマンド料金が完全に相殺されます。

予約で与えられる割引は (スループット消費量 * そのリージョンの予約割引率) として計算されます。 米国中北部リージョンと米国西部リージョンの場合、予約割引率は 1 です。 そのため、合計割引 RU/秒は 100,000 です。 この値は次のように計算されます。50,000 * 1 + 50,000 * 1 = 100,000 RU/秒。 通常の従量課金制の料金では追加料金を支払う必要はありません。

|メーターの説明 | リージョン |スループット消費量 (RU/秒) |RU/秒に適用される予約割引 |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/秒/時間 - 米国中北部  |   米国中北部  | 50,000  | 50,000  |
|Azure Cosmos DB - 100 RU/秒/時間 - 米国西部  |  米国西部   |  50,000  |  50,000 |

**シナリオ 2**

たとえば、オーストラリア中部 2 とフランス南部のリージョンで Azure Cosmos DB のデプロイが必要であるとします。 各リージョンのスループット消費量は 50,000 RU/秒です。 100,000 RU/秒の予約購入は次のように適用されます ("オーストラリア中部 2" の使用量が先に割引されるとします)。

|メーターの説明 | リージョン |スループット消費量 (RU/秒) |RU/秒に適用される予約割引 |
|---------|---------|---------|---------|
|Azure Cosmos DB - 100 RU/秒/時間 - オーストラリア中部 2  |  オーストラリア中部 2   |  50,000  |  50,000   |
|Azure Cosmos DB - 100 RU/秒/時間 - フランス南部  |  フランス南部   |  50,000 |  15,384  |

オーストラリア中部 2 リージョンの 50,000 ユニットの使用量は、75,000 RU/秒の課金対象使用量 (または正規化された使用量) に相当します。 この値は、スループット消費量 * そのリージョンの予約割引率として計算されます。 計算は、課金対象使用量または正規化された使用量の 75,000 RU/秒と等しくなります。 この値は次のように計算されます。50,000 * 1.5 = 75,000 RU/秒。

100,000 RU/秒の予約購入でオーストラリア中部 2 の 75,000 RU/秒が相殺されます。 25,000 RU/秒がフランス南部リージョンのために残ります。 残りの 25,000 RU/秒から、15,384 RU/秒の予約割引がフランス南部リージョンに適用されます。 この割引値は次のように計算されます。25,000 / 1.625 = 15,384 RU/秒。 フランス南部リージョンの残りの 34,616 RU/秒は通常の従量課金制料金で請求されます。

Azure 請求システムでは、予約構成に一致し、処理される最初のインスタンスに予約の課金特典が割り当てられます。 たとえば、今回のケースではオーストラリア中部 2 です。

Azure の予約の適用状況を把握し、課金の使用状況レポートで確認する方法については、[Azure の予約の使用状況](understand-reserved-instance-usage-ea.md)に関するページを参照してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

Azure の予約の詳細については、次の記事を参照してください。

* [Azure の予約とは](save-compute-costs-reservations.md)  
* [Azure Cosmos DB の容量が予約された Azure Cosmos DB リソースの前払い](../../cosmos-db/cosmos-db-reserved-capacity.md)  
* [Azure SQL Database の予約容量を使用した SQL Database 計算リソースの前払い](../../azure-sql/database/reserved-capacity-overview.md)  
* [Azure の予約の管理](manage-reserved-vm-instance.md)  
* [従量課金制サブスクリプションの予約使用量について](understand-reserved-instance-usage.md)  
* [エンタープライズ加入契約の予約使用量について](understand-reserved-instance-usage-ea.md)
* [CSP サブスクリプションの予約の使用状況について](https://docs.microsoft.com/partner-center/azure-reservations)
