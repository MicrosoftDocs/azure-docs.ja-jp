---
title: Azure Lighthouse と Cloud Solution Provider プログラム
description: Azure の委任されたリソース管理を使用する場合、セキュリティとアクセスの制御を考慮することが大切です。
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 399d2cb829c0425e3c9ee70a61cafde8568f903b
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012110"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse と Cloud Solution Provider プログラム

[CSP (クラウド ソリューション プロバイダー)](https://docs.microsoft.com/partner-center/csp-overview) パートナーである場合、CSP プログラムを通じて顧客用に作成された Azure サブスクリプションには、[代理で管理 (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 機能を使用してあらかじめアクセスできるようになっています。 このアクセスにより、顧客のサブスクリプションを直接サポートし、構成し、管理することができます。

Azure Lighthouse では、Azure の委任されたリソース管理と AOBO を併用できます。 より粒度の細かいアクセス許可をユーザーに使用できるようになるため、セキュリティが向上すると共に、不要なアクセスを減らすことにつながります。 また、ユーザーは、テナントに対する 1 つのログインを使用して、複数の顧客サブスクリプションにまたがって作業できるため、効率とスケーラビリティも向上します。

## <a name="administer-on-behalf-of-aobo"></a>代理で管理 (AOBO)

AOBO を使用すると、テナント内の[管理エージェント](https://docs.microsoft.com/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) ロールを持つすべてのユーザーが、CSP プログラムを通じて貴社が作成した Azure サブスクリプションへの AOBO アクセス権を持つことになります。 顧客のサブスクリプションにアクセスする必要のあるすべてのユーザーは、このグループのメンバーである必要があります。 AOBO では、異なる顧客と共同作業を行う別々のグループを柔軟に作成したり、グループやユーザーに対して個別のロールを有効にしたりすることはできません。

![AOBO を使用したテナント管理](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Azure の委任されたリソース管理

Azure の委任されたリソース管理を使用すると、次の図に示すように、さまざまなグループをさまざまな顧客またはロールに割り当てることができます。 ユーザーは Azure の委任されたリソース管理によって適切なレベルのアクセス権を持つことになるため、管理エージェント ロールを持つ (つまり、完全な AOBO アクセス権を持つ) ユーザーの数を減らすことができます。 これにより、顧客のリソースへの不要なアクセスが制限されるので、セキュリティの強化につながります。 また、より柔軟に、複数の顧客を大規模に管理できます。

CSP プログラムを使用して作成したサブスクリプションをオンボードするには、[Azure の委任されたリソース管理へのサブスクリプションのオンボード](../how-to/onboard-customer.md)に関する記事で説明されている手順を参照してください。 テナント内に管理エージェント ロールを持つすべてのユーザーが、このオンボードを実行できます。

![AOBO と Azure の委任されたリソース管理を使用したテナント管理](../media/csp-2.jpg)

## <a name="next-steps"></a>次の手順

- [テナント間の管理エクスペリエンス](cross-tenant-management-experience.md)について学習します。
- [Azure の委任されたリソース管理にサブスクリプションをオンボード](../how-to/onboard-customer.md)する方法について説明します。
- [Cloud Solution Provider プログラム](https://docs.microsoft.com/partner-center/csp-overview)について学習します。
