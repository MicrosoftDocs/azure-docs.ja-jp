---
title: Azure Traffic Manager のリアル ユーザー測定
description: この概要では、Azure Traffic Manager Real User Measurements のしくみについて説明します。
services: traffic-manager
documentationcenter: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: duau
ms.custom: ''
ms.openlocfilehash: 618f8fff532da0f6ae315ad9e4cda35a289949d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98183712"
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager の Real User Measurements の概要

Traffic Manager プロファイルを設定して、パフォーマンスによるルーティング方法を使用する場合、サービスは DNS クエリ要求の送信元を確認し、その要求元を、待機時間が最短になる Azure リージョンに転送するルーティングの決定を行います。 これを実現するために、さまざまなエンドユーザー ネットワークに対して Traffic Manager が保持する、ネットワーク待機時間インテリジェンスが利用されます。

Real User Measurements を使用すると、エンド ユーザーが使用するクライアント アプリケーションから、Azure リージョンへのネットワーク待機時間を測定し、ルーティングの決定を行うときに、Traffic Manager も同様にその情報を考慮するよう指定できます。 Real User Measurements を選択することで、エンドユーザーが存在するネットワークからの要求のルーティングの精度を向上させることができます。 

## <a name="how-real-user-measurements-work"></a>Real User Measurements のしくみ

リアル ユーザー測定は、クライアント アプリケーションが使用されているエンドユーザー ネットワークから見るように、そのアプリケーションが Azure リージョンへの待機時間を測定することで機能します。 たとえば、さまざまな場所 (たとえば、北米リージョン) のユーザーがアクセスする Web ページがある場合、パフォーマンスによるルーティング方法を使用して、サーバー アプリケーションがホストされている最適な Azure リージョンへのユーザー アクセスを促すときに、リアル ユーザー測定の機能を使用できます。

最初に、Azure 提供の JavaScript (および、それに含まれる一意のキー) を Web ページに埋め込みます。 完了すると、ユーザーが Web ページにアクセスするたびに、JavaScript は Traffic Manager にクエリを実行して、測定する Azure リージョンに関する情報を取得します。 サービスは、一連のエンドポイントをスクリプトに返して、そのリージョンを連続して測定します。これを行うために、その Azure リージョンでホストされている 1 つのピクセル イメージをダウンロードし、要求が送信された時刻と、最初のバイトが受信された時刻の間の待機時間を記録します。 こうした測定値は、Traffic Manager サービスにレポートされます。

時間をかけて、多数のネットワークに対して、この処理を何回も実行すると、Traffic Manager が取得する、エンド ユーザーが存在するネットワークの待機時間特性に関する情報の精度が増します。 この情報は、Traffic Manager によるルーティングの決定の際に考慮されるようになります。 結果として、これが Real User Measurements に基づいた決定の精度向上につながります。

Real User Measurements を使用する場合は、Traffic Manager に送信された測定数に基づいて課金されます。 価格の詳細については、[Traffic Manager の価格ページ](https://azure.microsoft.com/pricing/details/traffic-manager/)をご覧ください。

## <a name="faqs"></a>FAQ

* [Real User Measurements のメリットは何ですか。](./traffic-manager-faqs.md#what-are-the-benefits-of-using-real-user-measurements)

* [Azure 以外のリージョンで Real User Measurements を使用できますか。](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-non-azure-regions)

* [どのルーティング方法が Real User Measurements のメリットを得られますか。](./traffic-manager-faqs.md#which-routing-method-benefits-from-real-user-measurements)

* [Real User Measurements は、プロファイルごとに個別に有効にする必要がありますか。](./traffic-manager-faqs.md#do-i-need-to-enable-real-user-measurements-each-profile-separately)

* [サブスクリプションの Real User Measurements をオフにするには、どうすればよいですか。](./traffic-manager-faqs.md#how-do-i-turn-off-real-user-measurements-for-my-subscription)

* [Web ページ以外のクライアント アプリケーションで Real User Measurements を使用できますか。](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-client-applications-other-than-web-pages)

* [Real User Measurements が有効な Web ページのレンダリング 1 回あたりの測定数はどのくらいですか。](./traffic-manager-faqs.md#how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered)

* [Web ページで Real User Measurements スクリプトが実行されるまでの間に、遅延が発生しますか。](./traffic-manager-faqs.md#is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage)

* [Real User Measurements を使用できるのは、測定対象の Azure リージョンでだけですか。](./traffic-manager-faqs.md#can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure)

* [測定数を特定の数値に制限できますか。](./traffic-manager-faqs.md#can-i-limit-the-number-of-measurements-made-to-a-specific-number)

* [Real User Measurements の一環としてクライアント アプリケーションによって取得される測定を表示できますか。](./traffic-manager-faqs.md#can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements)

* [Traffic Manager によって提供される測定スクリプトを変更できますか。](./traffic-manager-faqs.md#can-i-modify-the-measurement-script-provided-by-traffic-manager)

* [Real User Measurements で使用しているキーを、他のユーザーが見ることはできますか。](./traffic-manager-faqs.md#will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements)

* [自分の RUM キーを、他のユーザーが悪用することはできますか。](./traffic-manager-faqs.md#can-others-abuse-my-rum-key)

* [すべての Web ページに測定 JavaScript を配置する必要がありますか。](./traffic-manager-faqs.md#do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages)

* [Real User Measurements を使用している場合、Traffic Manager によってエンド ユーザーの情報を識別することは可能ですか。](./traffic-manager-faqs.md#can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements)

* [Real User Measurements を測定している Web ページは、ルーティング用に Traffic Manager を使用する必要がありますか。](./traffic-manager-faqs.md#does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing)

* [Real User Measurements と共に使用するために、Azure リージョンでホストしなければならないサービスはありますか。](./traffic-manager-faqs.md#do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements)

* [Real User Measurements を使用しているとき、Azure の帯域幅の使用量は増えますか。](./traffic-manager-faqs.md#will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements)

## <a name="next-steps"></a>次のステップ
- [Web ページで Real User Measurements](traffic-manager-create-rum-web-pages.md) を使用する方法を確認する
- [Traffic Manager のしくみ](traffic-manager-overview.md)
- [Mobile Center](/mobile-center/) について確認する
- Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認する。
- [Traffic Manager プロファイルの作成](./quickstart-create-traffic-manager-profile.md)