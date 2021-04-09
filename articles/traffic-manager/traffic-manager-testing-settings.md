---
title: Azure Traffic Manager の設定を検証する
description: この記事では、Traffic Manager 設定を確認し、トラフィック ルーティング方法をテストする方法について説明します。
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 4bb7458d4258dffb074d663e5cf712c7a0245cc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022328"
---
# <a name="verify-traffic-manager-settings"></a>Traffic Manager の設定の検証

Traffic Manager の設定をテストするには、場所が異なる複数のクライアントからテストを実行する必要があります。 次に、Traffic Manager プロファイルのエンドポイントを 1 つずつダウンさせていきます。

* DNS TTL を短い時間に設定して、変更がすぐに反映されるようにします (たとえば 30 秒に設定)。
* テストするプロファイル内の Azure クラウド サービスおよび Web サイトの IP アドレスを確認します。
* DNS 名を IP アドレスに解決してそのアドレスを表示できるツールを使用します。

DNS 名がプロファイル内のエンドポイントの IP アドレスに解決されることを確認します。 その名前は Traffic Manager プロファイルに定義されているものと同じトラフィック ルーティング方法で解決される必要があります。 DNS 名の解決には、**nslookup** や **dig** などのツールを使用できます。

以下の例では、Traffic Manager プロファイルをテストする方法について説明しています。

### <a name="check-traffic-manager-profile-using-nslookup-and-ipconfig-in-windows"></a>Windows で nslookup および ipconfig を使用して Traffic Manager プロファイルを確認します。

1. 管理者として、コマンド プロンプトまたは Windows PowerShell プロンプトを開きます。
2. 「`ipconfig /flushdns`」と入力して、DNS リゾルバー キャッシュをフラッシュします。
3. 「`nslookup <your Traffic Manager domain name>`」と入力します。 たとえば、次のコマンドは、プレフィックスが *myapp.contoso* のドメインを確認します。

    ```powershell
    nslookup myapp.contoso.trafficmanager.net
    ```

    通常の結果では、次の情報が表示されます。

    + この Traffic Manager ドメイン名を解決するためにアクセスされる DNS サーバーの DNS 名と IP アドレス。
    + コマンド ラインで "nslookup" の後に入力した Traffic Manager ドメイン名と、その Traffic Manager ドメインから解決された IP アドレス。 この 2 番目の IP アドレスを確認することが重要です。 この IP アドレスは、テスト対象の Traffic Manager プロファイルに含まれるいずれかのクラウド サービスまたは Web サイトのパブリック仮想 IP (VIP) アドレスと一致している必要があります。

## <a name="how-to-test-the-failover-traffic-routing-method"></a>フェールオーバーによるトラフィック ルーティング方法をテストする方法

1. すべてのエンドポイントを稼働状態にします。
2. 1 つのクライアントを使用し、nslookup または同様のユーティリティを使用して、会社のドメイン名の DNS 解決を要求します。
3. 解決された IP アドレスがプライマリ エンドポイントの IP アドレスに一致することを確認します。
4. プライマリ エンドポイントをダウンさせるか監視ファイルを削除することで、アプリケーションがダウンしていると Traffic Manager に認識させます。
5. Traffic Manager プロファイルの DNS 存続時間 (TTL) と追加の 2 分間待ちます。 たとえば、DNS TTL が 300 秒 (5 分) の場合は、7 分待つ必要があります。
6. DNS クライアント キャッシュをフラッシュし、nslookup を使用して DNS 解決を要求します。 Windows では、ipconfig /flushdns コマンドで、DNS キャッシュをフラッシュできます。
7. 解決された IP アドレスがセカンダリ エンドポイントの IP アドレスに一致することを確認します。
8. 処理を繰り返し、各エンドポイントを順番にダウンさせていきます。 DNS によって、一覧内の次のエンドポイントの IP アドレスが返されることを確認します。 すべてのエンドポイントを停止したら、プライマリ エンドポイントの IP アドレスが再度返されます。

## <a name="how-to-test-the-weighted-traffic-routing-method"></a>重み付けによるトラフィック ルーティング方法をテストする方法

1. すべてのエンドポイントを稼働状態にします。
2. 1 つのクライアントを使用し、nslookup または同様のユーティリティを使用して、会社のドメイン名の DNS 解決を要求します。
3. 解決された IP アドレスがいずれかのエンドポイントの IP アドレスに一致することを確認します。
4. DNS クライアント キャッシュをフラッシュし、各エンドポイントに手順 2 と 3 を繰り返します。 返された IP アドレスが、各エンドポイントに対応していることを確認します。

## <a name="how-to-test-the-performance-traffic-routing-method"></a>パフォーマンスによるトラフィック ルーティング方法をテストする方法

パフォーマンスによるトラフィック ルーティング方法を効果的にテストするには、世界各地のクライアントが必要になります。 さまざまな Azure リージョンにクライアントを作成し、そのクライアントでサービスをテストすることができます。 グローバル ネットワークがある場合は、世界中の他の場所にあるクライアントにリモートでサインインし、そこからテストを実行できます。

また、無料の Web ベースの DNS 検索と検出サービスも利用できます。 これらのツールの中には、世界中のさまざまな場所から DNS 名前解決を確認できるものもあります。 たとえば、"DNS lookup" の検索などを実行できます。 Gomez や Keynote のようなサード パーティのサービスを使用して、トラフィックがプロファイルに基づいて予期した通りに振り分けられることを確認できます。

## <a name="next-steps"></a>次のステップ

* [Traffic Manager のトラフィック ルーティング方法について](traffic-manager-routing-methods.md)
* [Traffic Manager のパフォーマンスに関する考慮事項](traffic-manager-performance-considerations.md)
* [Traffic Manager の機能低下状態のトラブルシューティング](traffic-manager-troubleshooting-degraded.md)
