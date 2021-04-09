---
title: Azure Service Fabric と API Management の概要
description: この記事では、Azure API Management を Service Fabric アプリケーションへのゲートウェイとして使用する方法の概要を示します。
ms.topic: conceptual
ms.date: 06/22/2017
ms.openlocfilehash: 32f47d62cc9dda7cc88421dbf616bf69ffe152fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575688"
---
# <a name="service-fabric-with-azure-api-management-overview"></a>Azure Service Fabric と API Management の概要

通常、クラウド アプリケーションには、ユーザー、デバイス、またはその他のアプリケーションに単一の受信ポイントを提供するフロントエンド ゲートウェイが必要です。 Service Fabric では、[ASP.NET Core アプリケーション](service-fabric-reliable-services-communication-aspnetcore.md)などの任意のステートレス サービスをゲートウェイとして使用できますが、[Event Hubs](../event-hubs/index.yml)、[IoT Hub](../iot-hub/index.yml)、[Azure API Management](../api-management/index.yml) など、トラフィック受信用に設計された別のサービスを使用することもできます。

この記事では、Azure API Management を Service Fabric アプリケーションへのゲートウェイとして使用する方法の概要を示します。 API Management は Service Fabric と直接統合されるので、バックエンドの Service Fabric サービスへのルーティング規則を豊富に備えた API を公開することができます。

## <a name="availability"></a>可用性

> [!IMPORTANT]
> この機能は、必要な仮想ネットワーク サポートのために API Management の **Premium** および **Developer** レベルで使用できます。

## <a name="architecture"></a>Architecture

一般的な Service Fabric アーキテクチャでは、HTTP API を公開するバックエンド サービスへの HTTP 呼び出しを行う、単一 ページの Web アプリケーションを使用します。 [Service Fabric getting-started サンプル アプリケーション](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)に、このアーキテクチャの例が示されています。

このシナリオでは、ステートレス Web サービスが Service Fabric アプリケーションへのゲートウェイとして機能します。 このアプローチでは、下図に示すように、HTTP 要求をバックエンド サービスにプロキシすることができる Web サービスを記述する必要があります。

![ステートレス Web サービスが Service Fabric アプリケーションへのゲートウェイとしてどのように機能するかを示す図。][sf-web-app-stateless-gateway]

アプリケーションの複雑さが増すにつれ、多数のバックエンド サービスの前面で API を公開しなければならないゲートウェイも複雑になります。 Azure API Management は、ルーティング規則、アクセスの制御、レート制限、監視、イベント ログ、および応答キャッシュに対応する複雑な API を、最小限のユーザー操作で処理することができるよう設計されています。 また、Service Fabric サービスの検出、パーティション解決、およびレプリカ選択をサポートし、要求を直接 Service Fabric のバックエンド サービスにインテリジェントにルーティングできるので、独自のステートレス API ゲートウェイを記述する必要がありません。 

このシナリオでは、下図に示すように、Web UI が Web サービスを通じて引き続き使用される一方、HTTP API 呼び出しは Azure API Management によって管理およびルーティングされます。

![Web UI が Web サービスを通じて引き続き使用される一方、HTTP API 呼び出しは Azure API Management によって管理およびルーティングされる方法を示す図。][sf-apim-web-app]

## <a name="application-scenarios"></a>アプリケーションのシナリオ

Service Fabric のサービスはステートレスかステートフルのいずれかで、シングルトン、int-64 範囲、名前付きのいずれかの構成でパーティション分割されています。 サービス エンドポイントを解決するには、特定のサービス インスタンスの特定のパーティションを識別する必要があります。 サービス エンドポイントを解決する際は、シングルトン パーティションの場合を除き、サービス インスタンス名 (`fabric:/myapp/myservice` など) と、サービスの特定のパーティションの両方を指定する必要があります。

Azure API Management は、ステートレス サービス、ステートフル サービス、および任意のパーティション構成のどの組み合わせでも使用できます。

## <a name="send-traffic-to-a-stateless-service"></a>ステートレス サービスにトラフィックを送信する

最も簡単なケースでは、トラフィックはステートレス サービスのインスタンスに転送されます。 そのため API Management 操作には、Service Fabric バックエンドの特定のステートレス サービス インスタンスに要求をマップする Service Fabric バックエンドの受信処理ポリシーが含まれています。 このサービスに送信された要求は、サービスのランダムなインスタンスに送信されます。

**例**

次のシナリオでは、Service Fabric アプリケーションに `fabric:/app/fooservice` という名前のステートレス サービスがあり、そこで内部 HTTP API を公開しています。 サービス インスタンスの名前は既にわかっているので、API Management の受信処理ポリシーに直接ハードコードすることができます。 

![Service Fabric アプリケーションに、内部 HTTP API を公開しているステートレス サービスがあることを示す図。][sf-apim-static-stateless]

## <a name="send-traffic-to-a-stateful-service"></a>ステートフル サービスにトラフィックを送信する

ステートレス サービスのシナリオと同様に、トラフィックはステートフル サービスのインスタンスに転送されます。 この場合、API Management 操作には、特定の *ステートフル* サービス インスタンスの特定のパーティションに要求をマップする Service Fabric バックエンドの受信処理ポリシーが含まれます。 各要求がマップされるパーティションは、受信 HTTP 要求からの入力 (URL パス内の値など) を使用して、ラムダ メソッドで計算されます。 このポリシーは、プライマリ レプリカだけに要求を送信するよう構成することも、読み取り操作のためにランダムなレプリカに要求を送信するよう構成することもできます。

**例**

次のシナリオでは、Service Fabric アプリケーションに `fabric:/app/userservice` という名前のパーティション分割されたステートフル サービスがあり、そこで内部 HTTP API を公開しています。 サービス インスタンスの名前は既にわかっているので、API Management の受信処理ポリシーに直接ハードコードすることができます。  

サービスは、2 つのパーティションとキー範囲 (`Int64.MinValue` ～ `Int64.MaxValue`) を使用する Int64 パーティション構成でパーティション分割されます。 分割キーの計算には任意のアルゴリズムを使用できますが、ここでは、バックエンド ポリシーが URL 要求パスで指定された値 `id` を 64 ビットの整数に変換することで、範囲内でパーティション分割キーを計算します。 

![Service Fabric と Azure API Management のトポロジの概要][sf-apim-static-stateful]

## <a name="send-traffic-to-multiple-stateless-services"></a>複数のステートレス サービスにトラフィックを送信する

より高度なシナリオでは、複数のサービス インスタンスに要求をマップする API Management 操作を定義できます。 この場合、各操作には、受信 HTTP 要求からの値 (URL パスやクエリ文字列など) に基づいて特定のサービス インスタンスに要求をマップし、ステートフル サービスの場合はサービス インスタンス内のパーティションに要求をマップするポリシーが含まれています。

そのため API Management 操作には、受信 HTTP 要求から取得した値に基づいて Service Fabric バックエンドのステートレス サービス インスタンスに要求をマップする Service Fabric バックエンドの受信処理ポリシーが含まれています。 サービスへの要求は、サービスのランダムなインスタンスに送信されます。

**例**

この例では、アプリケーションのユーザーごとに新しいステートレス サービス インスタンスが作成され、次の式を使用して動的に生成された名前が付けられます。

- `fabric:/app/users/<username>`

  各サービスには一意の名前が付けられますが、サービスはユーザーや管理者の入力に対する応答として作成されるので、事前にその名前を知ることはできません。そのため、APIM ポリシーやルーティング規則にハードコードすることはできません。 代わりに、要求を送信するサービスの名前は、URL 要求パスで指定された値 `name` から、バックエンド ポリシー定義内で生成されます。 次に例を示します。

  - `/api/users/foo` への要求はサービス インスタンス `fabric:/app/users/foo` にルーティングされる
  - `/api/users/bar` への要求はサービス インスタンス `fabric:/app/users/bar` にルーティングされる

![アプリケーションのユーザーごとに新しいステートレス サービス インスタンスが、動的に生成された名前を付けて作成される例を示す図。][sf-apim-dynamic-stateless]

## <a name="send-traffic-to-multiple-stateful-services"></a>複数のステートフル サービスにトラフィックを送信する

ステートレス サービスの例と同様に、API Management 操作は複数の **ステートフル** サービスに要求をマップできますが、このとき、ステートフル サービス インスタンスごとにパーティション解決が必要になることもあります。

そのため API Management 操作には、受信 HTTP 要求から取得した値に基づいて Service Fabric バックエンドのステートフル サービス インスタンスに要求をマップする Service Fabric バックエンドの受信処理ポリシーが含まれています。 要求は特定のサービス インスタンスのほか、そのサービス インスタンス内の特定のパーティションにもマップすることができます。さらに、任意でそのパーティション内のプライマリ レプリカかランダム セカンダリ レプリカのいずれにマップすることも可能です。

**例**

この例では、アプリケーションのユーザーごとに新しいステートフル サービス インスタンスが作成され、次の式を使用して動的に生成された名前が付けられます。

- `fabric:/app/users/<username>`

  各サービスには一意の名前が付けられますが、サービスはユーザーや管理者の入力に対する応答として作成されるので、事前にその名前を知ることはできません。そのため、APIM ポリシーやルーティング規則にハードコードすることはできません。 代わりに、要求を送信するサービスの名前は、URL 要求パスで指定された値 `name` から、バックエンド ポリシー定義内で生成されます。 次に例を示します。

  - `/api/users/foo` への要求はサービス インスタンス `fabric:/app/users/foo` にルーティングされる
  - `/api/users/bar` への要求はサービス インスタンス `fabric:/app/users/bar` にルーティングされる

各サービス インスタンスもまた、2 つのパーティションとキー範囲 (`Int64.MinValue` ～ `Int64.MaxValue`) を使用する Int64 パーティション構成でパーティション分割されます。 分割キーの計算には任意のアルゴリズムを使用できますが、ここでは、バックエンド ポリシーが URL 要求パスで指定された値 `id` を 64 ビットの整数に変換することで、範囲内でパーティション分割キーを計算します。 

![各サービス インスタンスもまた、2 つのパーティションとキー範囲 (Int64.MinValue から Int64.MaxValue) を使用する Int64 パーティション構成でパーティション分割されることを示す図。][sf-apim-dynamic-stateful]

## <a name="next-steps"></a>次のステップ

[チュートリアル](service-fabric-tutorial-deploy-api-management.md)に従って最初の Service Fabric クラスターと API Management を設定し、要求が API Management を通じてサービスに流れるようにします。

<!-- links -->

<!-- pics -->
[sf-apim-web-app]: ./media/service-fabric-api-management-overview/sf-apim-web-app.png
[sf-web-app-stateless-gateway]: ./media/service-fabric-api-management-overview/sf-web-app-stateless-gateway.png
[sf-apim-static-stateless]: ./media/service-fabric-api-management-overview/sf-apim-static-stateless.png
[sf-apim-static-stateful]: ./media/service-fabric-api-management-overview/sf-apim-static-stateful.png
[sf-apim-dynamic-stateless]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateless.png
[sf-apim-dynamic-stateful]: ./media/service-fabric-api-management-overview/sf-apim-dynamic-stateful.png
