---
title: Azure Traffic Manager のパフォーマンスに関する考慮事項 | Microsoft Docs
description: Traffic Manager でのパフォーマンス、および Traffic Manager を使用したときの Web サイトのパフォーマンスをテストする方法について説明します。
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 5a0de772598877029649f8a985c79e28f5264535
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98186177"
---
# <a name="performance-considerations-for-traffic-manager"></a>Traffic Manager のパフォーマンスに関する考慮事項

このページでは、Traffic Manager を使用する場合のパフォーマンスに関する考慮事項について説明します。 以下のシナリオについて考えてみます。

WestUS と EastAsia のリージョンに Web サイトのインスタンスがあります。 一方のインスタンスが Traffic Manager のプローブの正常性チェックに失敗しました。 アプリケーションのトラフィックは、正常なリージョンの方へ送信されます。 このフェールオーバーは予期された動作ですが、リージョンに達するまでのトラフィックの伝達距離が長くなるため、待機時間という点でパフォーマンスの問題が発生する可能性があります。

## <a name="performance-considerations-for-traffic-manager"></a>Traffic Manager のパフォーマンスに関する考慮事項

Traffic Manager が Web サイトのパフォーマンスに対して影響を与えることができるのは、初期 DNS 参照のみです。 Traffic Manager プロファイルの名前の DNS 要求は trafficmanager.net ゾーンをホストする Microsoft DNS ルート サーバーによって処理されます。 Traffic Manager は、Traffic Manager ポリシーとプローブの結果に基づいて、Microsoft DNS ルート サーバーを設定し、定期的に更新します。 そのため、初期の DNS lookup 時であっても、DNS クエリが Traffic Manager に送信されることはありません。

Traffic Manager は、DNS ネーム サーバー、API サービス、ストレージ層、エンドポイント監視サービスなど、いくつかのコンポーネントで構成されます。 Traffic Manager のサービス コンポーネントでエラーが発生しても、Traffic Manager プロファイルに関連付けられた DNS 名への影響はありません。 Microsoft DNS サーバー内のレコードは変更されません。 ただし、エンドポイントの監視と DNS の更新は実行されません。 したがって、プライマリ サイトがダウンすると、Traffic Manager は DNS を更新してフェールオーバー サイトを指し示すことができません。

DNS 名前解決は短時間で処理され、結果はキャッシュに保存されます。 初期 DNS lookup の速度は、クライアントが名前解決に使用する DNS サーバーに依存します。 通常、クライアントは DNS lookup を約 50 ミリ秒以内で完了します。 lookup の結果は、DNS の Time-to-live (TTL) に設定されている時間キャッシュに保存されます。 Traffic Manager の TTL の既定値は 300 秒です。

トラフィックは Traffic Manager を通過しません。 DNS lookup が完了すると、クライアントは Web サイトのインスタンスの IP アドレスを持ちます。 クライアントは、Traffic Manager を通過せずに、そのアドレスに直接接続します。 ユーザーが選択した Traffic Manager のポリシーは、DNS のパフォーマンスに影響しません。 ただし、パフォーマンス ルーティング方法が、アプリケーションの動作に悪影響を与えることがあります。 たとえば、ポリシーによってトラフィックが北米からアジアでホストされているインスタンスにリダイレクトされる場合、それらのセッションのネットワーク待機時間がパフォーマンスの問題となることがあります。

## <a name="measuring-traffic-manager-performance"></a>Traffic Manager のパフォーマンスの測定

Traffic Manager プロファイルの動作とパフォーマンスを理解するために使用できる Web サイトが複数あります。 これらのサイトの多くは無料で利用できますが、制限事項がある場合があります。 一部のサイトでは、強化された監視とレポート機能を有料で提供しています。

これらのサイトのツールは DNS 待機時間を測定し、世界各地にあるクライアントの場所の解決された IP アドレスを表示します。 これらのツールのほとんどは DNS の結果をキャッシュに保存しません。 そのため、ツールではテストを実行するたびに完全な DNS lookup が表示されます。 独自のクライアントからテストする場合は、完全な DNS lookup のパフォーマンスを TTL 期間中に 1 回だけテストできます。

## <a name="sample-tools-to-measure-dns-performance"></a>DNS パフォーマンスを測定するサンプル ツール

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS には、多くのパフォーマンス ツールが備わっています。 DNS Comparison ツールでは、DNS 名を解決するのにかかる時間と他の DNS サービス プロバイダーとの比較が表示されます。

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    最も簡単なツールの 1 つは WebSitePulse です。 URL を入力すると、DNS 解決時間、最初のバイト、最後のバイト、その他のパフォーマンス統計が表示されます。 3 つの異なるテストの場所から選択できます。 今回の例では、最初の実行時には DNS lookup が 0.204 秒かかっていることが分かります。

    !["DNS" 参照の結果が強調して示されている "WebSitePulse" ツールを示すスクリーンショット。](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    結果がキャッシュに保存されるため、同じ Traffic Manager エンドポイントに対する 2 回目のテストでは、DNS lookup にかかった時間は 0.002 秒になっています。

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [CA App Synthetic Monitor](https://asm.ca.com/en/checkit.php)

    以前は Watchmouse Check Website と呼ばれていたツールです。複数の地理的な場所から同時に行った場合の DNS 解決時間を表示します。 URL を入力すると、DNS 解決時間、接続時間、および複数の地理的な場所からの速度が表示されます。 このテストを使用すると、世界中のさまざまな場所での、ホストされるサービスの応答の状態が確認できます。

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    このツールでは、Web ページの各要素のパフォーマンス統計値を確認できます。 [Page Analysis] タブには、DNS lookup にかかった時間の割合が表示されます。

* [What's My DNS?](https://www.whatsmydns.net/)

    このサイトは、20 の異なる場所から DNS 参照を実行して、結果を地図上に表示します。

* [Dig Web Interface](https://www.digwebinterface.com)

    このサイトでは CNAME や A レコードなど DNS のさらに詳細な情報が表示されます。 オプションで [Colorize output] と [Stats] を選択し、[Nameservers] で [All] を選択してください。

## <a name="next-steps"></a>次の手順

[Traffic Manager のトラフィック ルーティング方法について](traffic-manager-routing-methods.md)

[Traffic Manager の設定のテスト](traffic-manager-testing-settings.md)

[Traffic Manager の操作 (REST API リファレンス)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Azure Traffic Manager コマンドレット](/powershell/module/az.trafficmanager)