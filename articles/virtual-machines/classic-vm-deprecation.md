---
title: Azure VM (クラシック) は、2023 年 3 月 1 日に廃止になります。
description: この記事では、クラシック デプロイ モデルを使用して作成された VM の廃止に関する概要を説明します。
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 2fb710bab03d595d6e54bc8dd8fbda38c57123e7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668200"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>2023 年 3 月 1 日までに IaaS リソースを Azure Resource Manager に移行する 

2014 年に、[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) でサービスとしてのインフラストラクチャ (IaaS) を開始しました。 それ以降、機能を強化し続けてきました。 Azure Resource Manager には現在、完全な IaaS 機能とその他の強化機能が含まれるため、2020 年 2 月 28 日に [Azure Service Manager](./migration-classic-resource-manager-faq.md#what-is-azure-service-manager-and-what-does-it-mean-by-classic) (ASM) を介した IaaS 仮想マシン (VM) の管理を非推奨にしました。 この機能は、2023 年 3 月 1 日に完全に廃止される予定です。 

現在、IaaS VM の約 90% で Azure Resource Manager が使用されています。 ASM を介して IaaS リソースを使用している場合は、今すぐ移行の計画を開始してください。 [Azure Resource Manager](../azure-resource-manager/management/index.yml) を利用するには、2023 年 3 月 1 日までに完了してください。

クラシック デプロイ モデルを使用して作成された VM は、廃止のための[モダン ライフサイクル ポリシー](https://support.microsoft.com/help/30881/modern-lifecycle-policy) に従います。

## <a name="how-does-this-affect-me"></a>ユーザーへの影響 

- 2020 年 2 月に ASM を介して IaaS VM を利用しなかったお客様は、2020 年 2 月 28 日以降、VM (クラシック) を作成することはできません。 
- 2023 年 3 月 1 日には、ASM を使用して IaaS VM を起動することはできなくなります。 まだ実行または割り当てられているものは停止され、割り当てが解除されます。 
- 2023 年 3 月 1 日に、Azure Resource Manager に移行していないサブスクリプションには、残っている VM (クラシック) の削除に関するタイムラインをお知らせします。  

次の Azure サービスと機能は、この廃止による影響は "*ありません*"。 
- [Azure Cloud Services (クラシック)](../cloud-services/cloud-services-choose-me.md)
- VM (クラシック) で使用されて "*いない*" ストレージ アカウント 
- VM (クラシック) で使用されて"*いない*" 仮想ネットワーク 
- その他のクラシック リソース

## <a name="what-resources-are-available-for-this-migration"></a>この移行に使用できるリソース

- [Microsoft Q&A](/answers/topics/azure-virtual-machines-migration.html):移行に対する Microsoft とコミュニティによるサポート。

- [Azure の移行のサポート](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}):移行中の技術的支援のための専用サポート チーム。 テクニカル サポートを利用していないお客様は、この移行に特化して提供される[無料サポート機能](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%0A%20%20%20%20%22pesId%22%3A%20%22f3dc5421-79ef-1efa-41a5-42bf3cbb52c6%22%2C%0A%20%20%20%20%22supportTopicId%22%3A%20%22794bb734-af1b-e2d5-a757-dac7438009ab%22%2C%0A%20%20%20%20%22contextInfo%22%3A%20%22Migrate%20IAAS%20resources%20from%20Classic%20%28ASM%29%20to%20Azure%20Resource%20Manager%20%28ARM%29%22%2C%0A%20%20%20%20%22caller%22%3A%20%22NoSupportPlanASM2ARM%22%2C%0A%20%20%20%20%22severity%22%3A%20%222%22%0A%7D)を利用できます。 

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack): Fast Track は、対象となるお客様がこの移行を計画および実行することを支援します。 DC 移行プログラムに[自己推薦](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fprograms%2Fazure-fasttrack%2F%23nomination&data=02%7C01%7CTanmay.Gore%40microsoft.com%7C3e75bbf3617944ec663a08d85c058340%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637360526032558561&sdata=CxWTVQQPVWNwEqDZKktXzNV74pX91uyJ8dY8YecIgGc%3D&reserved=0)します。  

- 会社または組織が Microsoft と提携している場合、または Microsoft の担当者 (クラウド ソリューション アーキテクト (CSA)、テクニカル アカウント マネージャー (TAM) など) と連携している場合は、移行に関する追加のリソースを使用して一緒に作業してください。

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか? 

Azure Resource Manager への移行の計画をすぐに開始します。 

1. 影響を受けるすべての VM の一覧を作成します。 

   - [Azure portal の [VM] ペイン](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines)にある VM の種類、**仮想マシン (クラシック)** は、サブスクリプション内のすべての影響を受ける VM です。 
   - また、[ポータル](https://portal.azure.com/#blade/HubsExtension/ArgQueryBlade/query/resources%0A%7C%20where%20type%20%3D%3D%20%22microsoft.classiccompute%2Fvirtualmachines%22)または [PowerShell](../governance/resource-graph/concepts/work-with-data.md) を使用して Azure Resource Graph にクエリを実行し、選択したサブスクリプションのすべてのフラグ付き VM (クラシック) と関連情報の一覧を表示することもできます。 
   - 2020 年 2 月 8 日および 9 月 2 日に、「Azure Resource Manager への IaaS VM の移行の計画を開始します」という件名の電子メールをサブスクリプションの所有者に送信しました。 電子メールには、すべてのサブスクリプションと VM (クラシック) VM の一覧が記載されています。 この一覧を作成する際に使用してください。 

1. [Linux](./migration-classic-resource-manager-plan.md) および [Windows](./migration-classic-resource-manager-plan.md) の VM (クラシック) の Azure Resource Manager への移行については、[こちら](./migration-classic-resource-manager-overview.md)を参照してください。 詳細については、「[クラシックから Azure Resource Manager への移行に関してよく寄せられる質問](./migration-classic-resource-manager-faq.md)」を参照してください

1. 検証、準備、コミットという 3 つの簡単な手順を使用して既存の VM を移行するには、[プラットフォーム サポート移行ツール](./migration-classic-resource-manager-overview.md)を使用して計画を開始することをお勧めします。 このツールは、最小限のダウンタイムで VM を移行するように設計されています。 

   - 最初の手順である検証は、既存のデプロイに影響しません。また、移行についてサポートされていないすべてのシナリオの一覧が提供されます。 
   - [回避策の一覧](./migration-classic-resource-manager-overview.md#unsupported-features-and-configurations)を確認して、デプロイを修正し、移行に向けて準備を行います。 
   - すべての検証エラーを修正した後は、準備とコミットの手順で問題が発生しないようにすることが理想的です。 コミットが成功すると、デプロイは Azure Resource Manager にライブ マイグレーションされ、Azure Resource Manager によって公開される新しい API を使用して管理することができます。 

   この以降ツールが移行に適していない場合は、この移行に適した[他のコンピューティング オファリング](/azure/architecture/guide/technology-choices/compute-decision-tree)を調べることができます。 Azure のコンピューティング オファリングには数多くあり、それらは互いに異なっているため、プラットフォームでサポートされている移行パスを提供することができません。  

1. 技術的な質問、問題、および許可リストへのサブスクリプションに関するヘルプについては、[サポートにお問い合わせ](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})ください。

1. ビジネスへの影響を防ぐためにできるだけ早く移行を完了し、Azure Resource Manager の改善されたパフォーマンス、セキュリティ、および新しい機能を活用してください。 
