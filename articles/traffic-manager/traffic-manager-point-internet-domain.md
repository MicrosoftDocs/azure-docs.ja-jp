---
title: インターネット ドメインで Traffic Manager が参照されるようにする - Azure Traffic Manager
description: この記事は、会社のドメイン名で Traffic Manager ドメイン名が参照されるようにするために役立ちます。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: duau
ms.openlocfilehash: e0e2acfb0ec0068dcd08ae660e397f65e039a665
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183746"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>会社のインターネット ドメインで Azure Traffic Manager ドメインが参照されるようにする

Traffic Manager プロファイルを作成すると、Azure では、そのプロファイルに DNS 名が自動的に割り当てられます。 独自の DNS ゾーンの名前を使用するには、Traffic Manager プロファイルのドメイン名にマップされる CNAME DNS レコードを作成します。 Traffic Manager プロファイルの [構成] ページの **[全般]** セクションで、Traffic Manager ドメイン名を確認できます。

たとえば、`www.contoso.com` で Traffic Manager DNS 名 `contoso.trafficmanager.net` を参照させるには、次の DNS リソース レコードを作成します。

`www.contoso.com IN CNAME contoso.trafficmanager.net.`

これで、*www\.contoso.com* へのすべてのトラフィック要求が *contoso.trafficmanager.net* にリダイレクトされるようになります。

> [!IMPORTANT]
> *contoso.com* などの第 2 レベル ドメインで、Traffic Manager ドメインを参照させることはできません。 DNS プロトコルの標準では、第 2 レベル ドメイン名の CNAME レコードは許可されません。

## <a name="next-steps"></a>次のステップ

* [Traffic Manager のルーティング方法](traffic-manager-routing-methods.md)
* [Traffic Manager  - プロファイルの無効化、有効化、または削除](./traffic-manager-manage-profiles.md)
* [Traffic Manager - エンドポイントの無効化または有効化](./traffic-manager-manage-endpoints.md)