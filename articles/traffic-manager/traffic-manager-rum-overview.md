---
title: "Azure Traffic Manager の Real User Measurements | Microsoft Docs"
description: "Traffic Manager の Real User Measurements の概要"
services: traffic-manager
documentationcenter: traffic-manager
author: KumudD
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/19/2017
ms.author: kumud
ms.custom: 
ms.openlocfilehash: a7e8ae605b6234341d9ab8b790f4c54d8627f29f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="traffic-manager-real-user-measurements-overview"></a>Traffic Manager の Real User Measurements の概要

>[!NOTE]
>Traffic Manager の Real User Measurements 機能はパブリック プレビューであり、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 機能はサポート対象ではなく、機能が制限されることもあります。また、Azure の場所によっては、利用できない場合もあります。 この機能の可用性と状態に関する最新の通知については、[Azure Traffic Manager の更新情報](https://azure.microsoft.com/updates/?product=traffic-manager)に関するページをご覧ください。

Traffic Manager プロファイルを設定して、パフォーマンスによるルーティング方法を使用する場合、サービスは DNS クエリ要求の送信元を確認し、その要求元を、待機時間が最短になる Azure リージョンに転送するルーティングの決定を行います。 これを実現するために、さまざまなエンド ユーザー ネットワークに対して Traffic Manager が保持する、ネットワーク待機時間インテリジェンスが利用されます。

Real User Measurements を使用すると、エンド ユーザーが使用するクライアント アプリケーションから、Azure リージョンへのネットワーク待機時間を測定し、ルーティングの決定を行うときに、Traffic Manager も同様にその情報を考慮するよう指定できます。 Real User Measurements を選択することで、エンドユーザーが存在するネットワークからの要求のルーティングの精度を向上させることができます。 

## <a name="how-real-user-measurements-work"></a>Real User Measurements のしくみ

Real User Measurements は、クライアント アプリケーションが使用されているエンド ユーザー ネットワークから見るように、そのアプリケーションが Azure リージョンへの待機時間を測定することで機能します。 たとえば、さまざまな場所 (たとえば、北米リージョン) のユーザーがアクセスする Web ページがある場合、パフォーマンスによるルーティング方法を使用して、サーバー アプリケーションがホストされている最適な Azure リージョンへのユーザー アクセスを促すときに、Real User Measurements の機能を活用できます。

最初に、Azure 提供の JavaScript (および、それに含まれる一意のキー) を Web ページに埋め込みます。 完了すると、ユーザーが Web ページにアクセスするたびに、JavaScript は Traffic Manager にクエリを実行して、測定する Azure リージョンに関する情報を取得します。 サービスは、一連のエンドポイントをスクリプトに返して、そのリージョンを連続して測定します。これを行うために、その Azure リージョンでホストされている 1 つのピクセル イメージをダウンロードし、要求が送信された時刻と、最初のバイトが受信された時刻の間の待機時間を記録します。 こうした測定値は、Traffic Manager サービスにレポートされます。

時間をかけて、多数のネットワークに対して、この処理を何回も実行すると、Traffic Manager が取得する、エンド ユーザーが存在するネットワークの待機時間特性に関する情報の精度が増します。 この情報は、Traffic Manager によるルーティングの決定の際に考慮されるようになります。 結果として、これが Real User Measurements に基づいた決定の精度向上につながります。

Real User Measurements を使用する場合は、Traffic Manager に送信された測定数に基づいて課金されます。 価格の詳細については、[Traffic Manager の価格ページ](https://azure.microsoft.com/pricing/details/traffic-manager/)をご覧ください。

## <a name="next-steps"></a>次のステップ
- [Web ページで Real User Measurements](traffic-manager-create-rum-web-pages.md) を使用する方法を確認する
- [Traffic Manager のしくみ](traffic-manager-overview.md)
- [Mobile Center](https://docs.microsoft.com/mobile-center/) について確認する
- Traffic Manager でサポートされている [トラフィック ルーティング方法](traffic-manager-routing-methods.md) の詳細を確認する。
- [Traffic Manager プロファイルの作成](traffic-manager-create-profile.md)

