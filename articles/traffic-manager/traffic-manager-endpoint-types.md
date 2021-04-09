---
title: Traffic Manager エンドポイントの種類 | Microsoft Docs
description: この記事では、Azure Traffic Manager で使用できるさまざまなエンドポイントの種類について説明します
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: 7686f2f97da0113704216dcab741c063a80d3136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99051229"
---
# <a name="traffic-manager-endpoints"></a>Traffic Manager エンドポイント

Microsoft Azure Traffic Manager を使用すると、世界中のさまざまなデータ センターで実行されているアプリケーションのデプロイに、ネットワーク トラフィックを分散させる方法を制御できます。 Traffic Manager では、各アプリケーションのデプロイを 'エンドポイント' として構成します。 Traffic Manager が DNS 要求を受信すると、DNS 応答で返すことができるエンドポイントを選択します。 Traffic Manager は、現在のエンドポイントの状態とトラフィック ルーティング方法に基づいて選択を行います。 詳細については、「 [Traffic Manager のしくみ](traffic-manager-how-it-works.md)」をご覧ください。

Traffic Manager がサポートするエンドポイントには、次の 3 種類があります。

* **Azure エンドポイント** : Azure でホストされるサービスで使用されます。
* **外部エンドポイント**: IPv4/IPv6 アドレス、FQDN に対して、または Azure の外部でホストされるサービスに対して使用されます。 これらのサービスは、オンプレミスの場合と、異なるホスティング プロバイダーを使用している場合があります。
* **入れ子になったエンドポイント**: Traffic Manager プロファイルを組み合わせて、より柔軟なトラフィック ルーティング スキームを作成し、より大規模で複雑なデプロイのニーズに対応するために使用されます。

さまざまな種類のエンドポイントを 1 つの Traffic Manager プロファイルにまとめる方法に制限はありません。 各プロファイルでは、エンドポイントの種類を好きなように組み合わせることができます。

次のセクションでは、各エンドポイントの種類についてさらに詳しく説明します。

## <a name="azure-endpoints"></a>Azure エンドポイント

Azure エンドポイントは、Traffic Manager で Azure ベースのサービスに使用されます。 次の Azure リソースの種類がサポートされています。

* PaaS クラウド サービス。
* Web Apps
* Web アプリ スロット
* PublicIPAddress リソース (直接、または Azure Load Balancer を介して VM に接続可能) publicIpAddress には、Traffic Manager プロファイルで使用するために DNS 名を割り当てておく必要があります。

PublicIPAddress リソースは Azure Resource Manager のリソースです。 これらは、クラシック デプロイ モデルには存在せず、Traffic Manager の Azure Resource Manager エクスペリエンスでのみサポートされます。 その他の種類のエンドポイントは、リソース マネージャーとクラシック デプロイ モデルの両方でサポートされます。

Azure エンドポイントを使用しているとき、Traffic Manager は、Web アプリがいつ停止され、いつ開始されたかを検出します。 この状態は、エンドポイントの状態に反映されます。 詳細については、[Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md#endpoint-and-profile-status)に関する記事をご覧ください。 基になるサービスが停止すると、Traffic Manager でエンドポイントの正常性チェックや、エンドポイントへのトラフィック転送が行われなくなります。 停止したインスタンスに対しては、Traffic Manager の課金イベントは発生しません。 サービスが再起動されると課金が再開され、エンドポイントはトラフィックを受け取れるようになります。 この検出機能は、PublicIpAddress エンドポイントには適用されません。

## <a name="external-endpoints"></a>外部エンドポイント

IPv4/IPv6 アドレス、FQDN、または Azure 外部のサービスには、外部エンドポイントが使用されます。 IPv4/IPv6 アドレスのエンドポイントを使用すると、Traffic Manager は、エンドポイントの DNS 名を必要とせずに、エンドポイントの正常性を確認できます。 その結果、Traffic Manager は、応答でそのエンドポイントを返すときに、A/AAAA レコードでクエリに応答できます。 Azure の外部のサービスとは、オンプレミスや他のプロバイダーでホストされているサービスなどです。 外部エンドポイントは個別に使用することも、同じ Traffic Manager プロファイルで Azure エンドポイントと組み合わせることもできます。 ただし、IPv4 または IPv6 のアドレスとして指定されているエンドポイントは例外で、外部エンドポイントとしてのみ存在できます。 Azure エンドポイントと外部エンドポイントを組み合わせると、次のようにさまざまなシナリオが可能になります。

* Azure を使用して、アクティブ/アクティブまたはアクティブ/パッシブ フェールオーバー モデルで、既存のオンプレミス アプリケーションの冗長性を向上させます。 
* DNS 名が関連付けられていないエンドポイントにトラフィックをルーティングします。 また、返された DNS 名の IP アドレスを取得するために 2 番目の DNS クエリを実行する必要がないため、DNS ルックアップ全体の待機時間が減ります。
* 世界各地のユーザーのアプリケーションの待機時間を減らし、Azure で既存のオンプレミス アプリケーションを他の主要地域に拡張します。 詳細については、[Traffic Manager の 'パフォーマンス' によるトラフィック ルーティング](traffic-manager-routing-methods.md#performance)に関する記事をご覧ください。
* Azure を使用して、既存のオンプレミス アプリケーションの容量を、継続的に、または "クラウドへのバースト" ソリューションとして追加し、需要の急増に対処します。

場合によっては、外部エンドポイントを使用して Azure サービスを参照すると便利です。 例については、「[FAQ](traffic-manager-faqs.md#traffic-manager-endpoints)」を参照してください。 正常性チェックは、外部エンドポイントの料金ではなく、Azure エンドポイントの料金で課金されます。 Azure エンドポイントとは異なり、基になるサービスを停止または削除しても、正常性チェックは引き続き課金されます。 Traffic Manager でエンドポイントを無効にするか削除すると、課金は停止します。

## <a name="nested-endpoints"></a>入れ子になったエンドポイント

入れ子になったエンドポイントでは、複数の Traffic Manager プロファイルを組み合わせて、柔軟なトラフィック ルーティング スキームを作成し、大規模で複雑なデプロイのニーズに対応できます。 入れ子になったエンドポイントでは、’子’ プロファイルがエンドポイントとして ’親’ プロファイルに追加されます。 子と親の両方のプロファイルには、入れ子になった他のプロファイルなど、あらゆる種類の他のエンドポイントを含めることができます。 

詳細については、「 [入れ子になった Traffic Manager プロファイル](traffic-manager-nested-profiles.md)」をご覧ください。

## <a name="web-apps-as-endpoints"></a>エンドポイントとしての Web アプリ

Traffic Manager で Web アプリをエンドポイントとして構成するときの追加の考慮事項を示します。

1. Web Apps を Traffic Manager で使用するには、その SKU が ’Standard’ 以上である必要があります。 SKU が低い Web アプリケーションの追加の試みが失敗します。 既存の Web アプリケーションの SKU をダウングレードすると、Traffic Manager がその Web アプリケーションにトラフィックを送信しなくなります。 サポートされているプランの詳細については、「[App Service のプラン](https://azure.microsoft.com/pricing/details/app-service/plans/)」を参照してください。
2. エンドポイントが HTTP 要求を受信すると、その要求の 'host' ヘッダーを使用して、どの Web アプリケーションで要求を処理するかを判断します。 ホスト ヘッダーには、"contosoapp.azurewebsites.net" など、要求を開始するために使用される DNS 名が含まれています。 別の DNS 名を Web アプリケーションで使用するには、その DNS 名を、アプリのカスタム ドメイン名として登録する必要があります。 Web アプリケーション エンドポイントを Azure エンドポイントとして追加すると、Traffic Manager プロファイルの DNS 名は、そのアプリに自動的に登録されます。 この登録は、エンドポイントが削除されると、自動的に削除されます。
3. 各 Traffic Manager プロファイルでは、Azure リージョンごとに最大 1 つの Web アプリ エンドポイントを使用できます。 この制約を回避するには、Web アプリケーションを外部エンドポイントとして構成します。 詳細については、[FAQ](traffic-manager-faqs.md#traffic-manager-endpoints) をご覧ください。

## <a name="enabling-and-disabling-endpoints"></a>エンドポイントの有効化と無効化

Traffic Manager でエンドポイントを無効にすると、メンテナンス モードのエンドポイントや再デプロイされるエンドポイントから一時的にトラフィックを削除するときに便利です。 エンドポイントが再度稼働状態になったら、もう一度有効にできます。

Traffic Manager ポータル、PowerShell、CLI、または REST API を使用して、エンドポイントを有効または無効にできます。

> [!NOTE]
> エンドポイントの無効化は、デプロイメント状態とは関係ありません。 Azure サービス (VM または Web アプリケーションなど) は実行状態のままとなり、Traffic Manager で無効になってもトラフィックを受信できます。 トラフィックは、Traffic Manager プロファイルの DNS 名を介してではなく、サービス インスタンスに直接送信できます。 詳細については、「 [Traffic Manager のしくみ](traffic-manager-how-it-works.md)」をご覧ください。

現在、各エンドポイントがトラフィックを受信できるかどうかは、次の要因によって決まります。

* プロファイルの状態 (有効または無効)
* エンドポイントの状態 (有効または無効)
* そのエンドポイントの正常性チェックの結果

詳細については、 [Traffic Manager のエンドポイント監視](traffic-manager-monitoring.md#endpoint-and-profile-status)に関する記事をご覧ください。

> [!NOTE]
> Traffic Manager は DNS レベルで動作するため、いずれかのエンドポイントとの既存の接続に影響を与えることはありません。 エンドポイントが利用できない場合、Traffic Manager は新しい接続を使用可能な別のエンドポイントへ誘導します。 ただし、無効または異常なエンドポイントの外側にあるホストは、セッションが終了するまで既存の接続を介してトラフィックを受信し続ける可能性があります。 トラフィックが既存の接続に転送されないようにするには、セッション期間をアプリケーションで制限する必要があります。

プロファイル内のすべてのエンドポイントまたはプロファイル自体が無効になっている場合、Traffic Manager は、新しい DNS クエリに対して "NXDOMAIN" 応答を送信します。

## <a name="faqs"></a>FAQ

* [複数のサブスクリプションのエンドポイントで Traffic Manager を使用できますか。](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions)

* [クラウド サービス 'Staging' スロットで Traffic Manager を使用できますか。](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-cloud-service-staging-slots)

* [Traffic Manager では IPv6 エンドポイントはサポートされていますか。](./traffic-manager-faqs.md#does-traffic-manager-support-ipv6-endpoints)

* [同じリージョン内の複数の Web アプリで Traffic Manager を使用できますか。](./traffic-manager-faqs.md#can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region)

* [Traffic Manager プロファイルの Azure エンドポイントを別のリソース グループに移動する操作方法を教えてください。](./traffic-manager-faqs.md#how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription)

## <a name="next-steps"></a>次のステップ

* [Traffic Manager のしくみ](traffic-manager-how-it-works.md)を確認します。
* Traffic Manager の [エンドポイントの監視と自動フェールオーバー](traffic-manager-monitoring.md)を確認します。
* Traffic Manager の [トラフィック ルーティング方法](traffic-manager-routing-methods.md)を確認します。