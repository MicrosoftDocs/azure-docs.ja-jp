---
title: インクルード ファイル
description: インクルード ファイル
services: load balancer
author: kumudd
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 1d3ce900f7354b31e999c12b8e1eb0e23d391fcb
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181596"
---
| | Standard SKU | Basic SKU |
| --- | --- | --- |
| バックエンド プールのサイズ | 最大 1,000 インスタンスをサポート。 | 最大 100 インスタンスをサポート。 |
| バックエンド プール エンドポイント | 仮想マシン、可用性セット、仮想マシン スケール セットの組み合わせを含む、単一の仮想ネットワーク内の任意の仮想マシン。 | 単一の可用性セットまたは仮想マシン スケール セット内の仮想マシン。 |
| [正常性プローブ](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP、HTTP、HTTPS | TCP、HTTP |
| [正常性プローブ ダウン動作](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | インスタンス プローブがダウンし、__かつ__ すべてのプローブがダウンしても TCP 接続は存続。 | インスタンス プローブがダウンしても TCP 接続は存続。 すべてのプローブがダウンした場合、TCP 接続は終了。 |
| 可用性ゾーン | Standard SKU では、受信と送信に対するゾーン冗長とゾーン フロントエンド、送信フロー マッピングによりゾーン障害に耐久、ゾーン間の負荷分散。 | 使用できません。 |
| 診断 | Azure Monitor、バイト カウンターとパケット カウンターを含む多次元メトリック、正常性プローブの状態、接続試行 (TCP SYN)、送信接続の正常性 (SNAT 成功および失敗のフロー)、アクティブなデータ プレーン測定 | パブリック ロード バランサーに対する Azure Log Analytics のみ、SNAT 枯渇アラート、バックエンド プール正常性カウント。 |
| HA ポート | 内部ロード バランサー | 使用できません。 |
| 既定でのセキュリティ保護 | パブリック IP、パブリック ロード バランサー エンドポイント、内部ロード バランサー エンドポイントへのインバウンド フローは、ネットワーク セキュリティ グループによってホワイトリストに登録されない限り禁止されます。 | 既定でオープン、ネットワーク セキュリティ グループは任意。 |
| [送信接続 (アウトバウンド接続)](../articles/load-balancer/load-balancer-outbound-connections.md) | [送信規則](../articles/load-balancer/load-balancer-outbound-rules-overview.md)を使用して、プールに基づく送信 NAT を明示的に定義できます。 負荷分散規則のオプトアウトごとに複数のフロントエンドを使用できます。仮想マシン、可用性セット、仮想マシン スケール セットが送信接続 (アウトバウンド接続) を使用するためには、送信シナリオを明示的に作成する "_必要があります_"。  仮想ネットワーク サービス エンドポイントは、送信接続 (アウトバウンド接続) を定義しなくても到達でき、処理されたデータにはカウントされません。  VNet サービス エンドポイントとして使用できない Azure PaaS サービスなどのすべてのパブリック IP アドレスは、送信接続 (アウトバウンド接続) を介して到達する必要があり、処理されたデータにカウントされます。 仮想マシン、可用性セット、または仮想マシン スケール セットの負荷分散を担うのが内部ロード バランサーだけであるときは、既定の SNAT による送信接続は利用できません。代わりに [アウトバウンド規則](../articles/load-balancer/load-balancer-outbound-rules-overview.md)を使用してください。 送信 SNAT プログラミングは、受信負荷分散ルールのプロトコルに基づくトランスポート プロトコル固有です。 | 単一のフロントエンド。複数のフロントエンドが存在する場合は、ランダムに選ばれます。  仮想マシン、可用性セット、または仮想マシン スケール セットの負荷分散を担うのが内部ロード バランサーだけであるときは、既定の SNAT が使用されます。 |
| [送信規則](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | 宣言型の送信 NAT 構成、パブリック IP アドレスかパブリック IP プレフィックスまたは両方の使用、構成可能な送信アイドル タイムアウト (4 - 120 分)、カスタム SNAT ポートの割り当て | 使用できません。 |
|  [アイドルの TCP リセット](../articles/load-balancer/load-balancer-tcp-reset.md) | 任意のルールの TCP アイドル タイムアウト に対する TCP リセット (TCP RST) を有効にする | 使用できません。 |
| [複数のフロントエンド](../articles/load-balancer/load-balancer-multivip-overview.md) | 受信および[送信](../articles/load-balancer/load-balancer-outbound-connections.md) | 受信のみ |
| 管理操作 | ほとんどの操作は 30 秒未満 | 一般に 60 ～ 90 秒以上。 |
| SLA | 2 つの正常な仮想マシンが存在するデータ パスで 99.99%。 | 適用不可。 | 
| 価格 | ルールの数、リソースに関連付けられた受信および送信で処理されたデータに基づいて課金。  | 課金なし。 |
|  |  |  |
