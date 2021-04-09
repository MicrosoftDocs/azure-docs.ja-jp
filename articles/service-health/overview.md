---
title: Azure Service Health とは
description: お使いの Azure アプリが現在および将来の Azure サービスの問題やメンテナンスの影響をどのように受けるかに関するカスタマイズした情報。
ms.topic: overview
ms.date: 05/10/2019
ms.openlocfilehash: cb1eb8eb75a18a73e8f024f02217df37f45981fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87499774"
---
# <a name="what-is-azure-service-health"></a>Azure Service Health とは

Azure では、クラウド リソースの正常性についての最新情報を提供するエクスペリエンスのスイートが提供されています。 この情報には、サービスに影響するイベント、計画的なメンテナンス、可用性に影響する可能性のあるその他の変更など、現在および今後の問題が含まれています。

Azure Service Health は、3 つの異なる小さなサービスが組み合わされたものです。

[Azure の状態](azure-status-overview.md)では、Azure サービスの停止に関する情報が **[[Azure の状態] ページ](https://status.azure.com)** で提供されます。 そのページは、すべての Azure リージョンの全 Azure サービスの正常性を示すグローバル ビューです。 状態ページは、影響が広範囲に及ぶインシデントの有無を調べるときには良い参考になります。しかし、現在 Azure を使用しているユーザーには、Azure Service Health を利用して Azure のインシデントとメンテナンスに関する最新情報を入手することを強くお勧めします。

[Service Health](service-health-overview.md) では、使用している Azure サービスとリージョンの正常性についてのカスタマイズされたビューが提供されます。 認証された Service Health のエクスペリエンスではユーザーが現在使用中のサービスとリソースが認識されるので、停止、計画的メンテナンス活動、その他の正常性に関するアドバイザリについてのサービスに影響を及ぼすお知らせを確認するのに最適な場所です。 Service Health の最善の利用方法は、Service Health アラートを設定し、サービスの問題、計画メンテナンス、その他の変更がご利用中の Azure サービスとリージョンに影響を及ぼすおそれがある場合に、お好きな通信チャネルを使って通知を受け取れるようにすることです。

[Resource Health](resource-health-overview.md) では、特定の仮想マシン インスタンスなど、個々のクラウド リソースの正常性に関する情報を入手できます。 さらに、Azure Monitor を使えば、クラウド リソースの可用性が変動したときに通知するアラートを構成することもできます。 Resource Health と Azure Monitor による通知を組み合わせれば、リソースの可用性を分単位で把握すると共に、問題がお客様側の原因によるものか、Azure プラットフォームのイベントに関連するものかをすばやく評価できるようになります。

ここに挙げたエクスペリエンスが一体となって、お客様にとって最も意味のある精度で Azure の正常性を包括的に確認できます。

**Azure の状態ページ、Azure Service Health、および Azure Resource Health の概要を見る**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OgX6]

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]