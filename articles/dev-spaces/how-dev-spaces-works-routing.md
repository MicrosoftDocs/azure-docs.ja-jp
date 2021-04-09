---
title: Azure Dev Spaces でルーティングがどのように機能するか
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Azure Dev Spaces を動作させるプロセスと、ルーティングがどのように機能するかについて説明します
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 2d2c6f336222b4ae0907d6579289a8cad8d73aa6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91977971"
---
# <a name="how-routing-works-with-azure-dev-spaces"></a>Azure Dev Spaces でルーティングがどのように機能するか

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces には、Kubernetes アプリケーションを迅速に反復処理してデバッグし、Azure Kubernetes Service (AKS) クラスターでチームと共同作業を行うための複数の方法が用意されています。 開発スペースでプロジェクトの実行が開始されると、Azure Dev Spaces は、プロジェクトに追加のネットワーキングとルーティングの機能を提供します。

この記事では、Dev Spaces でルーティングがどのように機能するかについて説明します。

## <a name="how-routing-works"></a>ルーティングのしくみ

開発スペースは AKS 上に構築され、同じ[ネットワークの概念](../aks/concepts-network.md)を使用します。 Azure Dev Spaces には一元化された *ingressmanager* サービスがあり、独自のイングレス コントローラーを AKS クラスターにデプロイします。 *ingressmanager* サービスでは、AKS クラスターが開発スペースと共に監視され、クラスター内の Azure Dev Spaces イングレス コントローラーをイングレス オブジェクトで強化してアプリケーション ポッドへのルーティングを実現します。 各ポッド内の devspaces-proxy コンテナーでは、HTTP トラフィック用の `azds-route-as` HTTP ヘッダーが URL に基づいて開発スペースに追加されます。 たとえば、URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* に対する要求では、`azds-route-as: azureuser` を含む HTTP ヘッダーが取得されます。 `azds-route-as` ヘッダーが既に存在する場合は、devspaces-proxy コンテナーによってヘッダーが追加されません。

クラスタの外部からサービスに対して行われた HTTP 要求はイングレス コントローラーに進みます。 イングレス コントローラーでは、そのイングレス オブジェクトと規則に基づいて要求を適切なポッドに直接ルーティングします。 ポッド内の devspaces-proxy コンテナーが要求を受信し、URLに基づいて `azds-route-as` ヘッダーを追加して、要求をアプリケーション コンテナーにルーティングします。

クラスター内のあるサービスに対して別のサービスから行われた HTTP 要求は、呼び出し元のサービスの devspaces-proxy コンテナーを最初に通過します。 devspaces-proxy コンテナーでは、HTTP 要求を調べて `azds-route-as` ヘッダーをチェックします。 devspaces-proxy コンテナーでは、ヘッダーに基づいて、そのヘッダー値に関連付けられているサービスの IP アドレスを検索します。 IP アドレスが見つかった場合、devspaces-proxy コンテナーは要求をその IP アドレスに再ルーティングします。 IP アドレスが見つからなかった場合、devspaces-proxy コンテナーは要求を親アプリケーション コンテナーにルーティングします。

たとえば、アプリケーション *serviceA* および *serviceB* が *default* という名前の親開発スペースにデプロイされるとします。 *serviceA* は *serviceB* に依存しており、そのサービスに対する HTTP 呼び出しを行います。 Azure ユーザーは、*default* スペースに基づいて、*azureuser* という子開発スペースを作成します。 また、Azure ユーザーは、独自のバージョンの *serviceA* をその子スペースにデプロイします。 *http://azureuser.s.default.serviceA.fedcba09...azds.io* に対する要求は以下のような手順で行われます。

![Azure Dev Spaces のルーティング](media/how-dev-spaces-works/routing.svg)

1. イングレス コントローラーが URL に関連付けられているポッド (*serviceA.azureuser*) の IP を確認します。
1. イングレス コントローラーが Azure ユーザーの開発スペースでポッドの IP を検索し、要求を *serviceA.azureuser* ポッドにルーティングします。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが要求を受信し、HTTP ヘッダーとして `azds-route-as: azureuser` を追加します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが *serviceA.azureuser* ポッド内の *serviceA* アプリケーション コンテナーに要求をルーティングします。
1. *serviceA.azureuser* ポッド内の *serviceA* アプリケーションが *serviceB* の呼び出しを行います。 また、*serviceA* アプリケーションには、既存の `azds-route-as` ヘッダー (ここでは `azds-route-as: azureuser`) を保持するためのコードが含まれています。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが要求を受信し、`azds-route-as` ヘッダーの値に基づいて *serviceB* の IP を確認します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーは *serviceB.azureuser* の IP を検索しません。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが親スペース内の *serviceB* (*serviceB.default*) の IP を検索します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが *serviceB.default* の IP を検索し、要求を *serviceB.default* ポッドにルーティングします。
1. *serviceB.default* ポッド内の devspaces-proxy コンテナーが要求を受信し、*serviceB.default* ポッド内の *serviceB* アプリケーション コンテナーに要求をルーティングします。
1. *serviceB.default* ポッド内の *serviceB* アプリケーションが *serviceA.azureuser* ポッドに応答を返します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが応答を受信し、*serviceA.azureuser* ポッド内の *serviceA* アプリケーション コンテナーに応答をルーティングします。
1. *serviceA* アプリケーションが応答を受信し、その独自の応答を返します。
1. *serviceA.azureuser* ポッド内の devspaces-proxy コンテナーが *serviceA* アプリケーション コンテナーから応答を受信し、クラスターの外部に存在する元の呼び出し元に応答をルーティングします。

HTTP ではないその他の TCP トラフィックはすべて、未変更の状態でイングレス コントローラーと devspaces-proxy コンテナーを通過します。

## <a name="sharing-a-dev-space"></a>開発スペースを共有する

チームで作業している場合は、チーム全体で開発スペースを共有したり、派生の開発スペースを作成したりできます。 開発スペースは、その開発スペースのリソース グループへの共同作成者アクセス権を持つユーザーが使用できます。

別の開発スペースから派生した新しい開発スペースを作成することもできます。 派生の開発スペースを作成すると、その開発スペースの名前空間に *azds.io/parent-space=PARENT-SPACE-NAME* ラベルが追加されます。 また、親開発スペースからのすべてのアプリケーションが派生の開発スペースと共有されます。 更新バージョンのアプリケーションを派生の開発スペースにデプロイする場合、そのアプリケーションは派生の開発スペースにのみ存在し、親開発スペースには影響しません。 最大で 3 レベルの派生の開発スペースまたは "*親の親*" であるスペースを使用できます。

また、派生の開発スペースでは、その独自のアプリケーションと親から共有されるアプリケーションの間で要求がインテリジェントにルーティングされます。 ルーティングの際は、派生の開発スペースでアプリケーションに対する要求のルーティングが試行され、親開発スペースから共有アプリケーションにフォールバックされます。 アプリケーションが親スペースにない場合は、親の親であるスペース内の共有アプリケーションにルーティングがフォールバックします。

次に例を示します。
* 開発スペース *default* にアプリケーション *serviceA* および *serviceB* があります。
* 開発スペース *azureuser* は *default* から派生したものです。
* *serviceA* の更新バージョンは *azureuser* にデプロイされます。

*azureuser* の使用時には、*serviceA* に対するすべての要求が *azureuser* 内の更新バージョンにルーティングされます。 *serviceB* に対する要求は、最初に *azureuser* バージョンの *serviceB* へのルーティングが試行されます。 これが存在しないため、*default* バージョンの *serviceB* にルーティングされます。 *azureuser* バージョンの *serviceA* が削除されると、*serviceA* に対するすべての要求は *default* バージョンの *serviceA* を使用するようにフォールバックされます。

## <a name="next-steps"></a>次のステップ

Azure Dev Spaces でルーティングを使用して迅速な反復と開発を行う方法の例については、「[Azure Dev Spaces でのコードのリモート デバッグのしくみ][how-it-works-remote-debugging]」をご覧ください。


[how-it-works-remote-debugging]: how-dev-spaces-works-remote-debugging.md