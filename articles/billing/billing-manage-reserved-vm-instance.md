---
title: Azure の予約を管理する | Microsoft Docs
description: サブスクリプション スコープを変更し、Azure の予約に対するアクセス権を管理する方法について説明します。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2019
ms.author: banders
ms.openlocfilehash: 1edc15261520d1c2cbf9bf85a62249826edc045b
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58904443"
---
# <a name="manage-reservations-for-azure-resources"></a>Azure リソースに対する予約を管理する

Azure の予約を購入した後、必要に応じて、別のサブスクリプションに予約を適用する、予約を管理できるユーザーを変更する、または予約の範囲を変更します。 また、1 つの予約を 2 つの予約に分割して、購入したインスタンスの一部を別のサブスクリプションに適用することもできます。

Azure Reserved Virtual Machine Instances を購入した場合は、予約の最適化設定を変更できます。 予約割引は、同じシリーズの VM に適用するか、特定の VM サイズのデータセンター容量を予約することもできます。


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="change-the-scope-for-a-reservation"></a>予約のスコープを変更する

 予約割引は、実際の予約に該当し、かつ予約のスコープ内で実行される仮想マシン、SQL データベース、Azure Cosmos DB、またはその他のリソースに適用されます。 課金コンテキストは、予約の購入に使用されたサブスクリプションによって決まります。

予約のスコープを更新するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** > **[予約]** を選択します。
3. 予約を選択します。
4. **[設定]** > **[構成]** を選択します。
5. スコープを変更します。

共有スコープから単一スコープに変更する場合、選択できるのは自分が所有者であるサブスクリプションだけです。 選択できるのは、予約と同じ課金コンテキスト内のサブスクリプションに限られます。

スコープの対象となるのは、従量課金制オファー MS-AZR-0003P または MS-AZR-0023P、Enterprise オファー MS-AZR-0017P または MS-AZR-0148P、または CSP サブスクリプションの種類のみです。

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>予約を管理できるユーザーを追加または変更する

予約のロールに人を追加することで予約の管理を委任することができます。 既定では、予約の購入者とアカウント管理者に予約の所有者ロールが割り当てられています。

予約へのアクセス権は、その予約割引を取得したサブスクリプションとは別個に管理することができます。 予約を管理するためのアクセス許可をだれかに与えても、サブスクリプションを管理する権限は与えられません。 また、予約のスコープ内のサブスクリプションを管理するアクセス許可をだれかに与えても、予約を管理する権限は与えられません。

予約のアクセス管理を委任するには、次の手順に従います。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** > **[予約]** の順に選択し、自分にアクセス権がある予約を一覧表示します。
3. どの予約のアクセス権を他のユーザーに委任するかを選択します。
4. **[アクセス制御 (IAM)]** を選択します。
5. **[ロールの割り当ての追加]** > **[ロール]** > **[所有者]** を選択します。 また、制限付きアクセス権を付与する場合は、別のロールを選択します。
6. 所有者として追加するユーザーの電子メール アドレスを入力します。
7. ユーザーを選択し、**[保存]** を選択します。

## <a name="split-a-single-reservation-into-two-reservations"></a>1 つの予約を 2 つの予約に分割する

 予約内で複数のリソース インスタンスを購入した後、その予約内のインスタンスを異なるサブスクリプションに割り当てることができます。 既定では、すべてのインスタンスに 1 つのスコープ (1 つのサブスクリプションまたは共有のいずれか) があります。 たとえば 10 個の予約インスタンスを購入し、そのスコープをサブスクリプション A に指定したとします。その後、7 個の予約について、そのスコープをサブスクリプション A に変更し、残りの 3 つをサブスクリプション B に変更することができます。予約を分割してインスタンスを配分することで細かなスコープ管理が可能となります。 共有スコープを選択することでサブスクリプションへの割り当てを単純化することができます。 ただし、コスト管理や予算編成の都合上、特定のサブスクリプションに数量を割り当てることもできます。

 PowerShell、CLI、または API を通じて、1 つの予約を 2 つの予約に分割することができます。

### <a name="split-a-reservation-by-using-powershell"></a>PowerShell を使用して予約を分割する

1. 次のコマンドを実行して予約の発注 ID を取得します。

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. 予約の詳細を取得します。

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. 予約を 2 つに分割してインスタンスを配分します。

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. 次のコマンドを実行してスコープを更新することができます。

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancellations-and-exchanges"></a>キャンセルと交換

予約の種類に応じて、予約の取り消しや交換を行える場合があります。 詳細については、次のトピック内で取り消しと交換に関するセクションをご覧ください。

- [Azure Reserved VM Instances による仮想マシンの前払い](..//virtual-machines/windows/prepay-reserved-vm-instances.md#cancellations-and-exchanges)
- [Azure の予約からの SUSE ソフトウェア プランの前払い](../virtual-machines/linux/prepay-suse-software-charges.md#cancellation-and-exchanges-not-allowed)
- [Azure SQL Database の予約容量を使用した SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md#cancellations-and-exchanges)

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>予約 VM インスタンスの最適化設定を変更する

 予約 VM インスタンスを購入する場合は、インスタンス サイズの柔軟性または容量の優先度を選択します。 インスタンス サイズの柔軟性によって、予約割引が、同じ [VM サイズ グループ](https://aka.ms/RIVMGroups)内の他の VM に適用されます。 容量の優先度では、デプロイ用のデータ センターの容量が優先されます。 このオプションにより、必要なときに VM インスタンスを起動する能力に対する信頼が高まります。

予約のスコープが共有されている場合、既定でインスタンス サイズの柔軟性はオンです。 VM の展開では、データ センターの容量は優先されません。

スコープが単一の予約の場合は、VM インスタンス サイズの柔軟性ではなく、容量の優先度の予約を最適化できます。

予約の最適化設定を更新するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. **[すべてのサービス]** > **[予約]** を選択します。
3. 予約を選択します。
4. **[設定]** > **[構成]** を選択します。
5. **[最適化の対象]** 設定を変更します。

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

Azure の予約の詳細については、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)

サービス プランの購入:
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database の予約容量を使用した SQL Database 計算リソースの前払い](../sql-database/sql-database-reserved-capacity.md)
- [Azure Cosmos DB の容量が予約された Azure Cosmos DB リソースの前払い](../cosmos-db/cosmos-db-reserved-capacity.md)

ソフトウェア プランの購入:
- [Azure の予約からの Red Hat ソフトウェア プランの前払い](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure の予約からの SUSE ソフトウェア プランの前払い](../virtual-machines/linux/prepay-suse-software-charges.md)

割引と使用状況の理解:
- [VM 予約割引の適用方法](billing-understand-vm-reservation-charges.md)
- [Red Hat Enterprise Linux ソフトウェア プランの割引の適用方法](../billing/billing-understand-rhel-reservation-charges.md)
- [SUSE Linux Enterprise ソフトウェア プランの割引の適用方法を理解する](../billing/billing-understand-suse-reservation-charges.md)
- [その他の予約割引の適用方法](billing-understand-reservation-charges.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
- [予約に含まれない Windows ソフトウェアのコスト](billing-reserved-instance-windows-software-costs.md)
