---
title: インクルード ファイル
description: インクルード ファイル
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/29/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 1f6c65d6d8da275d6c52ac2802092dbb5e4e507d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "73182238"
---
### <a name="what-is-expressroute-direct"></a>ExpressRoute Direct とは何ですか?

ExpressRoute Direct では、世界中に戦略的に分散されたピアリングの場所で Microsoft のグローバル ネットワークに直接接続する機能がお客様に提供されます。 ExpressRoute Direct では、大規模なアクティブ/アクティブ接続をサポートするデュアル 100 または 10 Gbps 接続が提供されます。 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>ExpressRoute Direct に接続するにはどうすればよいですか? 

お客様が ExpressRoute Direct を利用するには、ローカル通信事業者やコロケーション プロバイダーと協力して、ExpressRoute ルーターに接続する必要があります。

### <a name="what-locations-currently-support-expressroute-direct"></a>現在どの場所で ExpressRoute Direct がサポートされていますか? 

[場所のページ](../articles/expressroute/expressroute-locations-providers.md)で提供状況を確認してください。 

### <a name="what-is-the-sla-for-expressroute-direct"></a>ExpressRoute Direct の SLA は何ですか?

ExpressRoute Direct は同じ [ExpressRoute のエンタープライズ グレード](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/)を使用します。 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>ExpressRoute Direct ではどのようなシナリオを検討する必要がありますか?  

ExpressRoute Direct では、お客様に、Microsoft グローバル バックボーンへの直接 100 または 10 Gbps ポート ペアが提供されます。 最大のメリットをお客様に提供するシナリオとしては、大量のデータ インジェスト、規制市場向けの物理的な分離、バースト シナリオ専用の容量などがあります。 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>ExpressRoute Direct の請求モデルは何ですか? 

ExpressRoute Direct では、固定金額でポート ペアに対して請求されます。 Standard 回線は追加時間なしで含まれ、Premium はわずかな追加料金があります。 送信は、ピアリングの場所のゾーンに基づいて回線ごとに課金されます。

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>ExpressRoute Direct ポート ペアの課金はいつ始まりますか?

ExpressRoute Direct のポート ペアは、ExpressRoute Direct リソースが作成されてから 45 日目、あるいは 1 つまたは両方のリンクが有効にされたときの、どちらか早い方の時点で、課金されるようになります。 45 日の猶予期間は、お客様がコロケーション プロバイダーとの相互接続プロセスを完了できるように設けられています。
