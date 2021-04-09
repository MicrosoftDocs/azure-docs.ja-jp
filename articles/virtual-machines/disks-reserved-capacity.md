---
title: 予約を使用して Azure Disk Storage のコストを最適化する
description: Azure Disk Storage の予約を購入して、Premium SSD マネージド ディスクのコストを節約することについて説明します。
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: db6575894904e6ced2d4be48fec5961f5b8b8a54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602638"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Azure ディスク予約によるコストの削減

予約容量によって Azure Disk Storage の使用量を節約します。 Azure Disk Storage の予約を Azure Reserved Virtual Machine Instances と組み合わせると、仮想マシン (VM) の総コストを削減できます。 予約割引は、選択した予約スコープ内の一致するディスクに自動的に適用されます。 こうした自動適用により、割引を受けるためにマネージド ディスクに予約を割り当てる必要はありません。

割引は、ディスク使用量に応じて 1 時間ごとに適用されます。 使用されなかった予約容量は引き継がれません。 Azure Disk Storage の予約割引は、アンマネージド ディスク、Ultra Disk、またはページ BLOB の使用には適用されません。

## <a name="determine-your-storage-needs"></a>必要なストレージを決定する

予約を購入する前に、必要なストレージを決定します。 現在、Azure Disk Storage の予約は、一部の Azure Premium SSD の SKU にのみ使用できます。 Premium SSD の SKU によって、ディスクのサイズとパフォーマンスが決まります。

必要なストレージを決定するときは、ディスク数を容量のみに基づいて考えないでください。 たとえば、P40 ディスクの予約を購入し、それを使用して 2 台の小容量の P30 ディスクの支払いを行うことはできません。 予約を購入するときは、SKU ごとの合計ディスク数分の予約のみを購入します。

ディスク予約は、ディスク SKU ごとに行われます。 そのため、予約使用量は指定されたサイズではなくディスク SKU の単位に基づきます。

たとえば、プロビジョニングされたストレージ容量が 2 TiB の P40 ディスクを 1 台予約したとします。 同様に、2 台の P30 ディスクのみを割り当てたとします。 この場合の P40 の予約では P30 の使用量は考慮されず、P30 ディスクに対して従量課金制の料金が発生します。
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>購入に関する考慮事項

ディスク予約の購入を検討する場合は、次の方法が推奨されます。

- 購入する必要がある予約の決定に役立てるために、利用状況の情報を分析します。 プロビジョニング済みまたは使用済みのディスク容量ではなく、ディスク SKU の使用量を追跡するようにしてください。
- VM 予約と併せてディスク予約を検討します。 最大限の節約のために、VM 使用量とディスク使用量の両方について予約することを強くお勧めします。 最初に適切な VM 予約を決定し、次にディスク予約を評価できます。 一般に、それぞれのワークロードに標準的な構成があります。 たとえば、SQL Server サーバーに 2 台の P40 データ ディスクと 1 台の P30 オペレーティング システム ディスクが存在する場合があります。
  
  このようなパターンは、購入する予約量を決定するのに役立つことがあります。 このアプローチによって、評価プロセスが簡素化され、VM とディスクの両方について整合性のとれたプランが得られるようになります。 このプランには、サブスクリプションやリージョンなどの考慮事項が含まれています。

## <a name="purchase-restrictions"></a>購入の制限

現在、次のディスクには予約割引が利用できません。

- アンマネージド ディスクまたはページ BLOB。
- Standard SSD または Standard ハード ディスク ドライブ (HDD)。
- P30 より小さい Premium SSD の SKU:P1、P2、P3、P4、P6、P10、P15、および P20 SSD の SKU。
- Azure Government、Azure Germany、または Azure China リージョンのディスク。

まれなケースですが、リージョンのキャパシティが低下しているために、Azure によって新しい予約の購入がディスクSKU のサブセットに制限されることがあります。

## <a name="buy-a-disk-reservation"></a>ディスク予約を購入する

Azure Disk Storage の予約は [Azure portal](https://portal.azure.com/) を使用して購入できます。 予約の支払いは、前払いまたは月払いで行うことができます。 月払いでの購入の詳細については、「[月払いで予約を購入する](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments)」を参照してください。

予約容量を購入するには、次の手順に従います。

1. Azure portal の [[購入予約]](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) ペインに移動します。

1. **[Azure Managed Disks]** を選択して予約を購入します。

    ![予約を購入するためのペイン](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. 次の表で説明するように、必要な値を指定します。

   |要素  |説明  |
   |---------|---------|
   |**スコープ**   |  予約に関連付けられた課金の特典を使用できるサブスクリプションの数。 この値は、特定のサブスクリプションに予約を適用する方法も指定します。 <br/><br/> **[共有]** を選択すると、予約割引は、課金のコンテキスト内にあるすべてのサブスクリプションの Azure Storage 容量に適用されます。 課金のコンテキストは、Azure に対するサインアップ方法に基づきます。 エンタープライズのお客様の場合、共有スコープが対象の登録であり、登録内のすべてのサブスクリプションが含まれます。 従量課金制のお客様の場合、共有スコープはアカウント管理者が作成するすべての個別の従量課金制サブスクリプションを含みます。  <br/><br/>  **[単一サブスクリプション]** を選択すると、予約割引は選択したサブスクリプションの Azure Storage 容量に適用されます。 <br/><br/> **[1 つのリソース グループ]** を選択すると、予約割引は選択したサブスクリプションおよびそのサブスクリプションの選択したリソース グループ内の Azure Storage 容量に適用されます。 <br/><br/> 予約を購入した後で、予約のスコープを変更できます。  |
   |**サブスクリプション**  | Azure Storage の予約の支払いに使用するサブスクリプション。 選択したサブスクリプションの支払方法が、コストの課金で使用されます。 サブスクリプションは、次のいずれかの種類である必要があります。<br/><ul><li> マイクロソフト エンタープライズ契約 (オファー番号: MS-AZR-0017P と MS-AZR-0148P)。 Enterprise サブスクリプションの場合、登録の Azure 前払い (旧称: 年額コミットメント) の残高から料金が差し引かれるか、超過分として課金されます。</li><br/><li>従来課金制料金の個別サブスクリプション (オファー番号: MS-AZR-0003P と MS-AZR-0023P)。 従量課金制料金の個々のサブスクリプションの場合、クレジット カードまたはサブスクリプションの請求書に記載されている支払方法に料金が課金されます。</li></ul>    |
   | **ディスク** | 作成する SKU。 |
   | **リージョン** | 予約が有効になっているリージョン。 |
   | **請求頻度** | アカウントが予約に対して課金される頻度。 オプションには **[月 1 回]** と **[前払い]** があります。 |

    ![購入する製品を選択するためのペイン (.png)](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. 予約の値を指定すると、Azure portal にコストが表示されます。 ポータルには、従量課金制の割引率も表示されます。 **[次へ]** を選択して、 **[購入予約]** ペインに進みます。

1. **[購入予約]** ペインで、予約の名前を指定し、予約の合計数量を選択できます。 予約の数は、ディスクの数にマップされます。 たとえば、100 台のディスクを予約する場合は、 **[Quantity]\(数量\)** に値「**100**」を入力します。

1. 予約の総コストを確認します。

    ![[購入予約] ペイン](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

予約を購入すると、予約条件に一致する既存の Disk Storage リソースに予約が自動的に適用されます。 Disk Storage リソースをまだ作成していない場合は、予約条件に一致するリソースを作成するたびに予約が適用されます。 どちらの場合も、予約期間は購入が正常に行われた直後から開始されます。

## <a name="cancel-exchange-or-refund-reservations"></a>予約の取り消し、交換、または返金

一定の制限内で予約の取り消し、交換、または返金を行うことができます。 詳しくは、「[Azure の予約のセルフサービスによる交換と払戻](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)」を参照してください。

## <a name="expiration-of-a-reservation"></a>予約の有効期限

予約の有効期限が切れると、その予約の下で使用する Azure Disk Storage の容量は、従量課金制の料金で課金されます。 予約は自動更新されません。

予約の有効期限の 30 日前と有効期限当日に通知メールが送信されます。 予約によって提供されるコスト削減を引き続き利用するには、有効期限日より後に更新してください。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次のステップ

- [Azure の予約とは](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure Disk Storage に予約割引が適用されるしくみについて](../cost-management-billing/reservations/understand-disk-reservations.md)