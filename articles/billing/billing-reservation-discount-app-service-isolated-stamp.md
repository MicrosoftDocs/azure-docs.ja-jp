---
title: Azure App Service Isolated スタンプに予約割引を適用する方法
description: Azure App Service Isolated スタンプに予約割引を適用する方法について説明します。
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: banders
ms.openlocfilehash: cb4b371deadb1ae9e6ae048c3157809aff857c9d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "68298163"
---
# <a name="how-reservation-discounts-apply-to-azure-app-service-isolated-stamps"></a>Azure App Service Isolated スタンプに予約割引を適用する方法

App Service Isolated スタンプ料金の予約容量を購入すると、そのリージョンのスタンプ料金に予約割引が自動的に適用されます。 予約割引は、Isolated スタンプ料金の測定から生成される使用量に適用されます。 worker、追加のフロント エンド、およびスタンプに関連するその他のリソースは、通常の料金で引き続き課金されます。

## <a name="reservation-discount-application"></a>適用される予約割引

App Service Isolated スタンプ料金の割引は、1 時間ごとのIsolated スタンプの実行に適用されます。 スタンプを 1 時間デプロイしないと、その 1 時間の予約容量は無駄になります。 持ち越すことはできません。

購入後、購入した予約は、特定のリージョンで実行されている Isolated スタンプと照合されます。 そのスタンプをシャットダウンすると、そのリージョンで実行されている他のスタンプには予約割引が自動的に適用されます。 スタンプが存在しない場合、予約はそのリージョンで作成された次のスタンプに適用されます。

スタンプが丸 1 時間実行されない場合、予約は同じ時間内に同じリージョン内の他の一致するスタンプに自動的に適用されます。

## <a name="choose-a-stamp-type---windows-or-linux"></a>スタンプの種類を選択する - Windows または Linux

空の Isolated スタンプからは、既定で Windows スタンプの測定が生成されます。 たとえば、worker がデプロイされていない場合などです。 Windows worker をデプロイしている場合は、引き続き測定が出力されます。 Linux worker をデプロイしている場合、測定は Linux スタンプの測定に変わります。 Linux と Windows の両方の worker をデプロイしている場合、スタンプからは Windows の測定が生成されます。

その結果、スタンプの有効期間中、スタンプの測定は Windows と Linux とで変わる可能性があります。 一方、予約はオペレーティング システム固有です。 スタンプにデプロイする予定の worker をサポートする予約を購入する必要があります。 Windows 専用スタンプと混合スタンプには、Windows 予約を使用します。 Linux worker のみのスタンプには、Linux 予約を使用します。

Linux 予約を購入する必要があるのは、スタンプに Linux worker _のみ_を使用する予定の場合だけです。

## <a name="discount-examples"></a>割引の例

次の例は、デプロイに応じて、Isolated スタンプ料金の予約インスタンス割引がどのように適用されるかを示しています。

- **例 1**:App Service Isolated スタンプのないリージョンで、Isolated 予約スタンプ容量の 1 インスタンスを購入します。 そのリージョンに新しいスタンプをデプロイし、そのスタンプの予約料金を支払います。
- **例 2**:App Service Isolated スタンプが既にデプロイされているリージョンで、Isolated 予約スタンプ容量の 1 インスタンスを購入します。 デプロイしたスタンプの予約料金の受け取りを開始します。
- **例 3**:App Service Isolated スタンプが既にデプロイされているリージョンで、Isolated 予約スタンプ容量の 1 インスタンスを購入します。 デプロイしたスタンプの予約料金の受け取りを開始します。 後でスタンプを削除し、新しいスタンプをデプロイします。 新しいスタンプの予約料金を受け取ります。 スタンプがデプロイされていない期間の割引は繰り越されません。
- **例 4**:あるリージョンで Isolated Linux 予約スタンプ容量の 1 インスタンスを購入してから、そのリージョンに新しいスタンプをデプロイします。 スタンプが最初にワーカーなしでデプロイされると、Windows スタンプの測定が生成されます。 割引は適用されません。 最初の Linux worker がスタンプをデプロイされると、Linux スタンプの測定が生成され、予約割引が適用されます。 Windows worker が後でスタンプにデプロイされると、スタンプの測定は Windows に戻ります。 Isolated Linux 予約スタンプの予約に対する割引は適用されなくなります。

## <a name="next-steps"></a>次の手順

- 予約を管理する方法については、「[Azure の予約の管理](billing-manage-reserved-vm-instance.md)」をご覧ください。
- App Service Isolated スタンプの予約容量を事前に購入してコストを削減する方法の詳細については、「[Prepay for Azure App Service Isolated Stamp Fee with reserved capacity (予約容量を含む Azure App Service Isolated スタンプ料金の前払い)](billing-prepay-app-service-isolated-stamp.md)」を参照してください。
- Azure の予約の詳細については、次の記事を参照してください。
  - [Azure の予約とは](billing-save-compute-costs-reservations.md)
  - [Azure での予約の管理](billing-manage-reserved-vm-instance.md)
  - [従量課金制料金のサブスクリプションの予約の使用状況について](billing-understand-reserved-instance-usage.md)
  - [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)
