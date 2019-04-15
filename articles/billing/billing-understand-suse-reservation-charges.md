---
title: SUSE の予約プラン割引と使用状況を理解する - Azure | Microsoft Docs
description: SUSE プランの割引が仮想マシン上の SUSE ソフトウェアにどのように適用されるかを学習します。
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
ms.date: 08/28/2018
ms.author: banders
ms.openlocfilehash: 4305db991a8129b0ae4205300051391df893c52c
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917789"
---
# <a name="understand-how-the-suse-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>SUSE Linux Enterprise ソフトウェアの予約プラン割引が Azure に適用されるしくみについて説明します

SUSE Linux プランを購入すると、割引は、予約に一致するデプロイされた SUSE 仮想マシン (VM) に自動的に適用されます。 SUSE Linux プランでは、Azure VM 上で SUSE ソフトウェアを実行するコストが対象になります。

適切な SUSE Linux プランを購入するためには、実行する SUSE VM と、その VM 上で実行する vCPU の数を理解する必要があります。 以降のセクションは、使用状況 CSV ファイルに基づいて購入するプランを識別するうえで役に立ちます。

## <a name="discount-applies-to-different-vm-sizes"></a>異なる VM サイズに対する割引の適用

予約 VM インスタンスと同様に、SUSE プランの購入は、インスタンス サイズの柔軟性を提供します。 つまり、異なる vCPU 数の VM をデプロイする場合でも、割引が適用されます。 割引は、ソフトウェア プラン内の異なる VM サイズに適用されます。

割引額は、以降の表に記載されている比率によって異なります。 比率は、そのグループ内の各測定の相対的フットプリントを比較します。 比率は、VM の vCPU に依存します。 比率の値を使用すると、SUSE Linux プランの割引が適用される VM インスタンスの数を計算できます。

たとえば、3 つまたは 4 つの vCPU を搭載する VM に対して SUSE Linux Enterprise Server for HPC Priority のプランを購入する場合、予約の比率は 2 です。 この場合、次の SUSE ソフトウェア コストが割引の対象になります。

- 1 つまたは 2 つの vCPU を使用する、2 つのデプロイされた VM
- 3 つまたは 4 つの vCPU を使用する、1 つのデプロイされた VM
- または、5 つ以上の vCPU を使用する VM の 0.77 または約 77%。

5 つ以上の vCPU の場合、比率は 2.6 です。 したがって、5 つ以上の vCPU を使用する VM に対して SUSE を予約した場合、ソフトウェア コストの一部のみ (約 77%) が対象になります。

## <a name="understand-suse-vm-usage-before-you-buy"></a>購入前における SUSE VM の使用状況の把握

次の表は、予約を購入できるソフトウェア プラン、関連する使用量メーター、およびそれぞれの比率を示しています。

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server for HPC Priority

Azure portal マーケットプレース名:

- SLES 12 SP3 for HPC (Priority)

|SUSE VM | MeterId| 比率| VM サイズの例|
| -------| ------------------------| --- |--- |
|SLES for HPC 1-2 vCPU|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SLES for HPC 3-4 vCPU|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SLES for HPC 5+ vCPU|4edcd5a5-8510-49a8-a9fc-c9721f501913|2.6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server for HPC Standard

Azure portal マーケットプレース名:

- SLES 12 SP3 for HPC

|SUSE VM | MeterId | 比率|VM サイズの例|
| ------- | --- | ------------------------| --- |
|SLES for HPC 1-2 vCPU |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SLES for HPC 3-4 vCPU|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1.92308|D4s_v3|
|SLES for HPC 5+ vCPU |907a85de-024f-4dd6-969c-347d47a1bdff|2.92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-priority"></a>SUSE Linux Enterprise Server for SAP Priority

Azure portal マーケットプレース名:

- SLES for SAP 15 (Priority)
- SLES for SAP 12 SP3 (Priority)
- SLES for SAP 12 SP2 (Priority)

|SUSE VM | MeterId | 比率|VM サイズの例|
| ------- |------------------------| --- | --- |
|SLES for SAP Priority 1-2 vCPU|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SLES for SAP Priority 3-4 vCPU |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SLES for SAP Priority 5+ vCPU |18ae79cd-dfce-48c9-897b-ebd3053c6058|2.41176|D8s_v3|

### <a name="suse-linux-enterprise-server-priority"></a>SUSE Linux Enterprise Server Priority

Azure portal マーケットプレース名:

- SLES 15 (PRIORITY)
- SLES 12 SP3 (Priority)
- SLES 11 SP4 (Priority)

|SUSE VM | MeterId | 比率|VM サイズの例|
| ------- |------------------------| --- |--- |
|SLES 1 vCPU|462cd632-ec6b-4663-b79f-39715f4e8b38|1|B1ms|
|SLES 2-4 vCPU |924bee71-5eb8-424f-83ed-a58823c33908|2|D4s_v3|
|SLES 2-4 vCPU |60b3ae9d-e77a-46b2-9cdf-92fa87407969|2|D4s_v3|
|SLES 6 vCPU |e8862232-6131-4dbe-bde4-e2ae383afc6f|3||
|SLES 8 vCPU |e11331a8-fd32-4e71-b60e-4de2a818c67a|3.2|D8s_v3|
|SLES 12 コア vCPU |a5afd00d-d3ef-4bcd-8b42-f158b2799782|3.2||
|SLES 16 vCPU |bb21066f-fe46-46d3-8006-b326b1663e52|3.2| D16s_v3|
|SLES 20 vCPU |c5228804-1de6-4bd4-a61c-501d9003acc8|3.2| |
|SLES 24 コア vCPU |-005d-4075-ac11-822ccde9e8f6|3.2| ND24s|
|SLES 32 vCPU |180c1a0a-b0a5-4de3-a032-f92925a4bf90|3.2| D32s_v3|
|SLES 40 コア vCPU |a161d3d3-0592-4956-9b64-6829678b6506|3.2||
|SLES 64 vCPU |7f5a36ed-d5b5-4732-b6bb-837dbf0fb9d8|3.2| D64s_v3|
|SLES 72 コア vCPU |93329a72-24d7-4faa-93d9-203f367ed334|3.2|F72s_v2|
|SLES 96 コア vCPU |2018c3a8-ff13-41f8-b64d-9558c5206547|3.2||
|SLES 128 コア vCPU |ac27e4d7-44b5-4fee-bc1a-78ac5b4abaf7|3.2| M128ms|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server Standard

Azure portal マーケットプレース名:

- SLES 15
- SLES 15 (Standard)
- SLES 12 SP3 (Standard)

|SUSE VM | MeterId | 比率|VM サイズの例|
| ------- |------------------------| --- |--- |
|SLES 1-2 コア vCPU |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|SLES 3-4 コア vCPU |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1.92308|D4s_v3|
|SLES 5+ vCPU |7b349b65-d906-42e5-833f-b2af38513468|2.30769| D8s_v3|

## <a name="next-steps"></a>次の手順

予約について詳しくは、次の記事を参照してください。

- [Azure の予約とは](billing-save-compute-costs-reservations.md)
- [Azure の予約による SUSE ソフトウェア プランの前払い](../virtual-machines/linux/prepay-suse-software-charges.md)
- [Azure Reserved VM Instances による仮想マシンの前払い](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure の予約を管理する](billing-manage-reserved-vm-instance.md)
- [従量課金制サブスクリプションの予約使用量について](billing-understand-reserved-instance-usage.md)
- [エンタープライズ加入契約の予約使用量について](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせ

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
