---
title: 予約容量を使用して BLOB ストレージのコストを最適化する - Azure Storage
description: Azure Storage の予約容量を購入して、ブロック BLOB と Azure Data Lake Storage Gen2 リソースのコストを節約することについて説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/01/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: c06bbc412a51fc919b862aeb3f62ec58feec89cf
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/01/2020
ms.locfileid: "84259203"
---
# <a name="optimize-costs-for-blob-storage-with-reserved-capacity"></a>予約容量を使用して BLOB ストレージのコストを最適化する

Azure Storage の予約容量を使用して BLOB データのストレージ コストを節約できます。 Azure Storage の予約容量により、1 年間または 3 年間の予約を行うと、Standard ストレージ アカウントのブロック BLOB および Azure Data Lake Storage Gen2 データの容量を割引できます。 予約を行うと、予約期間に一定量のストレージ容量が提供されます。

Azure Storage の予約容量により、ブロック BLOB と Azure Data Lake Storage Gen2 データの容量コストを大幅に削減できます。 実現されるコスト削減は、予約の期間、予約対象として選択した合計容量、ストレージ アカウントに選択したアクセス層と冗長性の種類によって異なります。 予約容量では、割引が適用されても、Azure Storage リソースの状態は維持されます。

Azure Storage の容量予約の価格については、「[ブロック BLOB の価格](https://azure.microsoft.com/pricing/details/storage/blobs/)」と「[Azure Data Lake Storage Gen 2 の価格](https://azure.microsoft.com/pricing/details/storage/data-lake/)」を参照してください。

## <a name="reservation-terms-for-azure-storage"></a>Azure Storage の予約条件

以下のセクションでは、Azure Storage の予約条件について説明します。

### <a name="reservation-capacity"></a>予約容量

Azure Storage の予約容量は、1 年間または 3 年間の期間については月あたり 100 TB および 1 PB 単位で購入できます。

### <a name="reservation-scope"></a>予約スコープ

Azure Storage の予約容量は、単一サブスクリプションまたは複数のサブスクリプション (共有スコープ) に対して使用できます。 単一サブスクリプションを範囲としている場合、予約割引は選択したサブスクリプションにのみ適用されます。 複数のサブスクリプションを範囲としている場合、予約割引は顧客の請求コンテキスト内のサブスクリプションとの間で共有されます。

Azure Storage の予約容量を購入すると、ブロック BLOB と Azure Data Lake Storage Gen2 データの両方に対して予約を使用できます。 予約は購入済み範囲内の使用量に対して適用され、サブスクリプション内の特定のストレージ アカウント、コンテナー、オブジェクトに限定されるものではありません。

Azure Storage の予約は、サブスクリプションまたは共有リソース グループに格納されているデータ量のみを対象としています。 早期削除、操作、帯域幅、およびデータ転送の料金は予約に含まれていません。 予約を購入するとすぐに、予約の属性に一致する容量料金は、従量課金制ではなく割引価格で課金されます。 Azure の予約の詳細については、「[Azure の予約とは](/azure/billing/billing-save-compute-costs-reservations)」を参照してください。

### <a name="supported-account-types-tiers-and-redundancy-options"></a>サポートされているアカウントの種類、アクセス層、冗長性オプション

Azure Storage の予約容量は、汎用 v2 (GPv2) アカウントおよび Blob Storage アカウントを含む、Standard ストレージ アカウントのリソースについて使用できます。

すべてのアクセス層 (ホット、クール、アーカイブ) の予約がサポートされています。 アクセス層の詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](storage-blob-storage-tiers.md)」を参照してください。

すべての種類の冗長性の予約がサポートされています。 冗長オプションの詳細については、[Azure Storage の冗長性](../common/storage-redundancy.md)に関する記事を参照してください。

> [!NOTE]
> Azure Storage の予約容量は、Premium Storage アカウント、汎用 v1 (GPv1) ストレージ アカウント、Azure Data Lake Storage Gen1、ページ BLOB、Azure Queue storage、Azure Table Storage、または Azure Files では使用できません。  

### <a name="security-requirements-for-purchase"></a>購入のセキュリティ要件

予約容量を購入するには:

- 少なくとも 1 つのエンタープライズ サブスクリプションまたは従量課金制料金の個々のサブスクリプションで**所有者**ロールである必要があります。
- Enterprise サブスクリプションの場合、EA ポータルで **[予約インスタンスを追加します]** を有効にする必要があります。 または、その設定が無効になっている場合は、ユーザーはサブスクリプションの EA 管理者である必要があります。
- クラウド ソリューション プロバイダー (CSP) プログラムの場合、管理者エージェントまたはセールス エージェントのみが Azure Blob Storage の予約容量を購入できます。

## <a name="determine-required-capacity-before-purchase"></a>購入前に必要な容量を確認する

Azure Storage の予約を購入する場合は、予約のリージョン、アクセス層、冗長性オプションを選択する必要があります。 予約はそのリージョン、アクセス層、および冗長性レベルに格納されるデータに対してのみ有効です。 たとえば、ゾーン冗長ストレージ (ZRS) を使用して、ホット アクセス層のデータの予約を米国西部で購入したとします。 米国東部のデータ、アーカイブ アクセス層のデータ、または geo 冗長ストレージ (GRS) のデータに同じ予約を使用することはできません。 ただし、追加のニーズに応じて別の予約を購入することもできます。  

予約は現在、100 TB または 1 PB のブロックでご利用いただくことができ、1 PB ブロックではより割引率が高くなります。 Azure Portal で予約を購入するとき、購入する予約を決定するのに役立つよう、以前の使用状況に応じて、Microsoft からの推奨が表示されることがあります。

## <a name="purchase-azure-storage-reserved-capacity"></a>Azure Storage の予約容量を購入する

Azure Storage の予約容量は [Azure portal](https://portal.azure.com) を使用して購入できます。 予約の支払いは、前払いまたは月払いで行います。 月払いでの購入の詳細については、「[前払いまたは月払いで Azure の予約を購入する](/azure/billing/billing-monthly-payments-reservations)」を参照してください。

お客様のシナリオに適した予約条件を特定する方法については、[Azure Storage の予約容量の割引のしくみ](../../cost-management-billing/reservations/understand-storage-charges.md)に関する記事を参照してください。

予約容量を購入するには、次の手順に従います。

1. Azure portal の [[購入予約]](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) ペインに移動します。  
1. 新しい予約を購入する **[Azure Blob Storage]** を選択します。  
1. 次の表で説明するように、必須フィールドに入力します。

    ![予約容量の購入方法を示すスクリーンショット](media/storage-blob-reserved-capacity/select-reserved-capacity.png)

   |フィールド  |説明  |
   |---------|---------|
   |**スコープ**   |  予約に関連づけられた課金の特典を使用できるサブスクリプションの数を指定します。 また、特定のサブスクリプションに予約を適用する方法も制御します。 <br/><br/> **[共有]** を選択すると、予約割引は、課金のコンテキスト内にある任意のサブスクリプションの Azure Storage 容量に適用されます。 課金のコンテキストは、Azure に対するサインアップ方法に基づきます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープはアカウント管理者が作成するすべての個別の従量課金制サブスクリプションを含みます。  <br/><br/>  **[単一サブスクリプション]** を選択すると、予約割引は選択したサブスクリプションの Azure Storage 容量に適用されます。 <br/><br/> **[1 つのリソース グループ]** を選択した場合、予約割引は、選択したサブスクリプションおよびそのサブスクリプション内の選択したリソース グループ内の Azure Storage 容量に適用されます。 <br/><br/> 予約を購入した後で、予約のスコープを変更できます。  |
   |**サブスクリプション**  | Azure Storage の予約の支払いに使用するサブスクリプションです。 選択したサブスクリプションの支払方法が、コストの課金で使用されます。 サブスクリプションは、次のいずれかの種類である必要があります。 <br/><br/>  マイクロソフト エンタープライズ契約 (オファー番号:MS-AZR-0017P または MS-AZR-0148P):エンタープライズ サブスクリプションの場合、登録の年額コミットメント残高から料金が差し引かれるか、超過料金として課金されます。 <br/><br/> 従量課金制料金の個別サブスクリプション (オファー番号:MS-AZR-0003P または MS-AZR-0023P):従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。    |
   | **リージョン** | 予約が有効になっているリージョン。 |
   | **アクセス層** | 予約が有効になっているアクセス層。 オプションには、*ホット*、*クール*、*アーカイブ* があります。 アクセス層の詳細については、「[Azure Blob Storage: ホット、クール、アーカイブ ストレージ層](storage-blob-storage-tiers.md)」を参照してください。 |
   | **冗長性** | 予約の冗長オプション。 オプションには、*LRS*、*ZRS*、*GRS*、*GZRS*、*RA-GRS*、*RA- GZRS* があります。 冗長オプションの詳細については、[Azure Storage の冗長性](../common/storage-redundancy.md)に関する記事を参照してください。 |
   | **請求頻度** | アカウントが予約に対して課金される頻度を示します。 オプションには *[月 1 回]* または *[前払い]* があります。 |
   | **[サイズ]** | 予約が有効になっているリージョン。 |
   |**用語**  | 1 年間または 3 年間。   |

1. 予約のパラメーターを選択すると、Azure portal にコストが表示されます。 ポータルには、従量課金制の割引率も表示されます。

1. **[購入予約]** ペインで、予約の料金総額を確認します。 また、予約の名前も指定することができます。

    ![予約の購入方法を示すスクリーンショット](media/storage-blob-reserved-capacity/purchase-reservations.png)

予約を購入すると、予約の条件に一致する既存の Azure Storage ブロック BLOB または Azure Data Lake Storage Gen2 リソースに予約が自動的に適用されます。 Azure Storage リソースをまだ作成していない場合は、予約の条件に一致するリソースを作成するたびに予約が適用されます。 どちらの場合にも、予約の条件は正常な購入の直後に開始されます。

## <a name="exchange-or-refund-a-reservation"></a>予約の交換または返金

一定の制限付きで、予約の交換または返金を行うことができます。 以降のセクションでは、これらの制限について説明します。

予約を交換または返金するには、Azure portal の予約の詳細に移動します。 **[Exchange]\(交換\)** または **[払戻]** を選択し、指示に従ってサポート リクエストを送信します。 要求が処理されると、Microsoft は要求の完了を確認する電子メールを送信します。

Azure の予約ポリシーの詳細については、「[Azure の予約のセルフサービスによる交換と払戻](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

### <a name="exchange-a-reservation"></a>予約を交換する

予約を交換すると、予約の未使用部分に基づいて日割り返金を受けることができます。 その後、新しい Azure Storage 予約の購入価格に返金を適用できます。

交換回数に制限はありません。 さらに、交換に伴う手数料はありません。 購入する新しい予約の値は、元の予約の日割りで計算されたクレジットと同じかそれ以上である必要があります。 Azure Storage 予約の交換は、他の Azure サービスの予約ではなく別の Azure Storage 予約に対してのみ行うことができます。

### <a name="refund-a-reservation"></a>予約を返金する

Azure Storage 予約はいつでも取り消すことができます。 取り消しを行うと、予約の残りの期間に基づいて日割り計算された返金金額から 12% の早期終了料金を差し引いた金額を受け取ります。 1 年あたりの最大の返金は 5 万ドルです。

予約を取り消すと予約はすぐに終了し、残りの月が Microsoft に返されます。 なお、残りは日割りの払い戻し額から手数料を引いた額が当初の購入形式に戻されます。

## <a name="expiration-of-a-reservation"></a>予約の有効期限

予約の有効期限が切れると、その予約の下で使用している Azure Storage の容量は、従量課金制の料金で課金されます。 予約は自動更新されません。

予約の有効期限の 30 日前と有効期限当日に通知メールが送信されます。 予約によって提供されるコスト削減を引き続き利用するには、有効期限日より後に更新してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Azure の予約とは](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Storage に予約割引が適用されるしくみについて](../../cost-management-billing/reservations/understand-storage-charges.md)
