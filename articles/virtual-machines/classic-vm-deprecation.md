---
title: 2023 年 3 月 1 日に Azure クラシック VM の提供が終了となります。
description: この記事では、クラシック VM の提供終了の概要を示します
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: tagore
ms.openlocfilehash: 0b2b995a6fe4cedd14b2e4ceeddc5747ec2423cf
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754805"
---
# <a name="migrate-your-iaas-resources-to-azure-resource-manager-by-march-1-2023"></a>2023 年 3 月 1 日までに IaaS リソースを Azure Resource Manager に移行する 

2014 年に Azure Resource Manager で IaaS を起動し、その後ずっと機能を強化してきました。 [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) には現在、完全な IaaS 機能とその他の強化機能が含まれるため、2020 年 2 月 28 日に [Azure Service Manager](https://docs.microsoft.com/azure/virtual-machines/windows/migration-classic-resource-manager-faq#what-is-azure-service-manager-and-what-does-it-mean-by-classic) を介した IaaS VM の管理が非推奨となり、2023 年 3 月 1 日にこの機能の提供は完全に終了となります。 

現在、IaaS VM の約 90% で Azure Resource Manager が使用されています。 Azure Service Manager (ASM) を介して IaaS リソースを使用する場合は、今すぐ移行の計画を開始し、2023 年 3 月 1 日までに完了して [Azure Resource Manager](../azure-resource-manager/management/index.yml)をご利用ください。

クラシック VM は、[最新のライフサイクル ポリシー](https://support.microsoft.com/help/30881/modern-lifecycle-policy)に従って廃止されます。

## <a name="how-does-this-affect-me"></a>どのような影響がありますか? 

- 2020 年 2 月 28 日以降、2020 年 2 月中に Azure Service Manager (ASM) を介して IaaS VM をご利用にならなかったお客様は、クラシック VM を作成できなくなります。 
- 2023 年 3 月 1 日に、お客様は Azure Service Manager を使用して IaaS VM を起動できなくなります。また、まだ実行中であるか、割り当て済みのものはすべて停止され、割り当てが解除されます。 
- 2023 年 3 月 1 日に、Azure Resource Manager に移行していないサブスクリプションには、残っているクラシック VM の削除に関するタイムラインをお知らせします。  

次の Azure サービスと機能は、この提供終了の影響を**受けません**: 
- Cloud Services 
- クラシック VM で使用されて**いない**ストレージ アカウント 
- クラシック VM で使用されて**いない**仮想ネットワーク (VNet)。 
- その他のクラシック リソース

## <a name="what-actions-should-i-take"></a>どのような対応が必要ですか? 

- Azure Resource Manager への移行の計画をすぐに開始します。 

- 影響を受けるすべての VM の一覧を作成します。 [Azure portal VM ブレード](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ClassicCompute%2FVirtualMachines)の VM の種類 "Virtual Machines (クラシック)" は、サブスクリプション内のすべての影響を受ける VM です。 

- [Linux](./linux/migration-classic-resource-manager-plan.md) および [Windows](./windows/migration-classic-resource-manager-plan.md) のクラシック VM を Azure Resource Manager に移行する場合の[詳細について学習](./windows/migration-classic-resource-manager-overview.md)します。

- 詳細については、「[クラシックから Azure Resource Manager への移行に関してよく寄せられる質問](./migration-classic-resource-manager-faq.md)」を参照してください

- 技術的な質問、問題、および許可リストへのサブスクリプションの追加については、[サポートにお問い合わせ](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"8a82f77d-c3ab-7b08-d915-776b4ff64ff4"})ください。

- FAQ に含まれていないその他の質問およびフィードバックについては、下にコメントを残してください。

- ビジネスへの影響を防ぐためにできるだけ早く移行を完了し、Azure Resource Manager によって提供される改善されたパフォーマンス、セキュリティ、および新しい機能を活用してください。 

## <a name="what-resources-are-provided-to-me-for-this-migration"></a>この移行に関してどのようなリソースが提供されますか?

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-virtual-machines-migration.html):移行に関する Microsoft とコミュニティによるサポート

- [Azure の移行のサポート](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/{"pesId":"6f16735c-b0ae-b275-ad3a-03479cfa1396","supportTopicId":"1135e3d0-20e2-aec5-4ef0-55fd3dae2d58"}):移行中の技術的支援のための専用サポート チーム

- [Microsoft Fast Track](https://www.microsoft.com/fasttrack):Microsoft Fast Track チームは、移行中に対象ユーザーに対して技術的なサポートを提供できます。 

- 会社/組織が Microsoft と提携している場合、または Microsoft の担当者 (クラウド ソリューション アーキテクト (CSA)、テクニカル アカウント マネージャー (TAM) など) と連携している場合は、移行に関する追加のリソースを使用して一緒に作業してください。 

