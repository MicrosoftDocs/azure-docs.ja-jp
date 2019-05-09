---
title: Red Hat の予約プラン割引と使用状況を理解する - Azure | Microsoft Docs
description: Red Hat プランの割引が仮想マシン上の Red Hat ソフトウェアにどのように適用されるかを学習します。
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2019
ms.author: cwatson
ms.openlocfilehash: fe0d0f0baa2b3d1c08e871541dce1511e00f7f87
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917058"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Red Hat Linux Enterprise ソフトウェアの予約プラン割引が Azure に適用されるしくみについて説明します

Red Hat Linux プランを購入すると、割引は、予約に一致するデプロイされた Red Hat 仮想マシン (VM) に自動的に適用されます。 Red Hat Linux プランでは、Azure VM 上で Red Hat ソフトウェアを実行するコストが対象になります。

適切な Red Hat Linux プランを購入するためには、実行する Red Hat VM と、その VM 上で実行する vCPU の数を理解する必要があります。 以降のセクションは、使用状況 CSV ファイルに基づいて購入するプランを識別するうえで役に立ちます。

## <a name="discount-applies-to-different-vm-sizes"></a>異なる VM サイズに対する割引の適用

予約 VM インスタンスと同様に、Red Hat プランの購入は、インスタンス サイズの柔軟性を提供します。 つまり、異なる vCPU 数の VM をデプロイする場合でも、割引が適用されます。 割引は、ソフトウェア プラン内の異なる VM サイズに適用されます。

割引額は、以降の表に記載されている比率によって異なります。 比率は、そのグループ内の各測定の相対的フットプリントを比較します。 比率は、VM の vCPU に依存します。 比率の値を使用すると、Red Hat Linux プランの割引が適用される VM インスタンスの数を計算できます。

たとえば、3 つまたは 4 つの vCPU を備える VM に対して Red Hat Linux Enterprise Server のプランを購入する場合、その予約の比率は 2 です。 次の Red Hat ソフトウェア コストが割引の対象になります。

- 1 つまたは 2 つの vCPU を使用する、2 つのデプロイされた VM
- 3 つまたは 4 つの vCPU を使用する、1 つのデプロイされた VM
- または、5 つ以上の vCPU を使用する VM の 0.77 または約 77%。

5 つ以上の vCPU の場合、比率は 2.6 です。 そのため、5 つ以上の vCPU を使用する VM に対して Red Hat を予約した場合、ソフトウェア コストの一部のみ (約 77%) が対象になります。

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>購入前における Red Hat VM の使用状況の把握

次の表は、予約を購入できるソフトウェア プラン、関連する使用量メーター、およびそれぞれの比率を示しています。

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure portal マーケットプレース名:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (最新の lvm)

|Red Hat VM | MeterId| 比率| VM サイズの例|
| -------| ------------------------| --- |--- |
|1-4 vCPU VM ライセンス|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1-4 vCPU VM ライセンス|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1-4 vCPU VM ライセンス|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|5+ vCPU VM ライセンス|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|037eddc0-fedd-4d73-b5d8-92fba9edb831|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|794dcb90-0793-43e6-9909-70d29974e56d|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|86c35ec3-0a48-426a-9625-22d80e6ea55b|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2.166666667|D8s_v3|
|5+ vCPU VM ライセンス|d09f877e-03b4-48b2-b11a-782b965cff19|2.166666667|D8s_v3|
|44 vCPU VM ライセンス|6f44ae85-a70e-44be-83ec-153a0bc23979|2.166666667||
|60 vCPU VM ライセンス|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2.166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>HA 対応 Red Hat Enterprise Linux for SAP

Azure portal マーケットプレース名:

|Red Hat VM | MeterId | 比率|VM サイズの例|
| ------- | --- | ------------------------| --- | --- |
|1-4 vCPU VM ライセンス |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|5+ vCPU VM ライセンス|6dfb482b-23ea-487f-810c-e66360f025de|2.333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>HA 対応 Red Hat Enterprise Linux

Azure portal マーケットプレース名:

|Red Hat VM | MeterId | 比率|VM サイズの例|
| ------- |------------------------| --- | --- |
|1-4 vCPU VM ライセンス|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|5+ vCPU VM ライセンス|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Azure portal マーケットプレース名:

- Red Hat Enterprise Linux 6.8 for SAP Business Apps
- Red Hat Enterprise Linux 7.3 for SAP Business Apps
- Red Hat Enterprise Linux 7.4 for SAP
- Red Hat Enterprise Linux 7.5 for SAP

|Red Hat VM | MeterId | 比率|VM サイズの例|
| ------- |------------------------| --- |--- |
|1 vCPU VM ライセンス|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 vCPU VM ライセンス|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 vCPU VM ライセンス|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 vCPU VM ライセンス|69a140fa-e08e-415c-85f2-48158e4c73a0|2.166666667||
|8 vCPU VM ライセンス|777a5a74-22d6-48c9-9705-ac38fe05a278|2.166666667|D8s_v3|
|12 vCPU VM ライセンス|d6b8917a-5127-497a-9f48-1e959df98812|2.166666667||
|16 vCPU VM ライセンス|03667e82-e009-425a-83f7-8ebddbca5af4|2.166666667|D16s_v3|
|20 vCPU VM ライセンス|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2.166666667||
|24 vCPU VM ライセンス|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2.166666667|ND24s|
|32 vCPU VM ライセンス|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2.166666667|D32s_v3|
|40 vCPU VM ライセンス|737142c3-8e4f-4fc1-aa41-05b1661edff8|2.166666667||
|44 vCPU VM ライセンス|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2.166666667||
|60 vCPU VM ライセンス|a22bb342-ba9a-4529-a178-39a92ce770b6|2.166666667||
|64 vCPU VM ライセンス|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2.166666667|64s_v3|
|72 vCPU VM ライセンス|14341b96-e92c-4dca-ba66-322c88a79aa6|2.166666667|F72s_v2|
|96 vCPU VM ライセンス|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2.166666667||
|128 vCPU VM ライセンス|9b198a68-974a-47a7-9013-49169ac0f2e9|2.166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Azure portal マーケットプレース名:

- Red Hat Enterprise Linux 6.7 for SAP HANA
- Red Hat Enterprise Linux 7.2 for SAP HANA
- Red Hat Enterprise Linux 7.3 for SAP HANA

|Red Hat VM | MeterId | 比率|VM サイズの例|
| ------- |------------------------| --- |--- |
|1 vCPU VM ライセンス|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 vCPU VM ライセンス|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 vCPU VM ライセンス|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 vCPU VM ライセンス|a5963812-0f5a-4053-8ace-2b5babd15ed8|2.166666667||
|8 vCPU VM ライセンス|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2.166666667|D8s_v3|
|12 vCPU VM ライセンス|0e3bc72d-a888-4bcf-8437-119f763a3215|2.166666667||
|16 vCPU VM ライセンス|b40e95d8-3176-42f0-967c-497785c031b2|2.166666667|D16s_v3|
|20 vCPU VM ライセンス|81f34277-499d-40a3-a634-99adc08e2d45|2.166666667||
|24 vCPU VM ライセンス|e03f1906-d35d-4084-b2cd-63281869c8ee|2.166666667|ND24s|
|32 vCPU VM ライセンス|0a58c082-ceb8-4327-9b64-887c30dddb23|2.166666667|D32s_v3|
|40 vCPU VM ライセンス|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2.166666667||
|44 vCPU VM ライセンス|378b8125-d8a5-4e09-99bc-c1462534ffb0|2.166666667||
|60 vCPU VM ライセンス|5d7db11a-54e9-404e-aaa8-509fac7c0638|2.166666667||
|64 vCPU VM ライセンス|3c8157b2-a57d-45ce-ba02-bd86e9209795|2.166666667|64s_v3|
|72 vCPU VM ライセンス|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2.166666667|F72s_v2|
|96 vCPU VM ライセンス|b13895fc-0d06-4de9-b860-627c471cd247|2.166666667||
|128 vCPU VM ライセンス|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2.166666667| M128ms|

## <a name="next-steps"></a>次の手順

予約について詳しくは、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [Azure の予約による Red Hat ソフトウェア プランの前払い](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure の予約の管理](billing-manage-reserved-vm-instance.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)してください。
