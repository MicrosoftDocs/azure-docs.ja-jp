---
title: Azure DDoS Protection の機能
description: Azure DDoS Protection の機能について説明します
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 602bb98f2cdc8a96874eba8dadfa33f3267d19ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97746560"
---
# <a name="azure-ddos-protection-standard-features"></a>Azure DDoS Protection Standard の機能

以降のセクションでは、Azure DDoS Protection Standard サービスの主な機能について説明します。

## <a name="always-on-traffic-monitoring"></a>常時接続のトラフィック監視:

DDoS Protection Standard は実際のトラフィック使用率を監視し、それを DDoS ポリシーで定義されたしきい値と常に比較します。 そのトラフィックしきい値を超えた場合は、DDoS 軽減策が自動的に開始されます。 トラフィックがしきい値未満に戻ると、その軽減策は停止されます。

![Azure DDoS Protection Standard の軽減策](./media/ddos-protection-overview/mitigation.png)

軽減策の実行中、保護されたリソースに送信されたトラフィックは DDoS Protection サービスによってリダイレクトされ、次のようないくつかのチェックが実行されます。

- パケットがインターネット仕様に準拠しており、不正な形式でないことを確認します。
- クライアントと対話して、それが偽装されたパケットである可能性があるかどうかを判定します (たとえば、SYN Auth や SYN Cookie、またはソースへのパケットを削除してそれを再送信します)。
- 他に適用できる方法がない場合は、パケットをレート制限します。

DDoS Protection によって、攻撃トラフィックがドロップされ、残りのトラフィックがその目的の宛先に転送されます。 攻撃の検出から数分以内に、Azure Monitor メトリックを使用してユーザーに通知します。 DDoS Protection Standard テレメトリへのログ記録を構成することによって、そのログを将来の分析のための使用可能なオプションに書き込むことができます。 DDoS Protection Standard のための Azure Monitor 内のメトリック データは、30 日間保持されます。

## <a name="adaptive-real-time-tuning"></a>アダプティブ リアルタイム チューニング

Azure DDoS Protection Basic サービスは、お客様のシステムの保護と、他のお客様への影響を防ぐのに役立ちます。 たとえば、インフラストラクチャ全体にわたる DDoS Protection ポリシーの "*トリガー レート*" より小さい正規の着信トラフィックの一般的な量に対してサービスがプロビジョニングされている場合、そのお客様のリソースに対する DDoS 攻撃は認識されない可能性があります。 より一般的には、最近の攻撃 (たとえば、複数ベクター DDoS) の複雑さと、テナントのアプリケーション固有の動作により、お客様ごとに保護ポリシーを調整する必要があります。 このサービスは、次の 2 つのインサイト手法を通じてこれを実現します。

- お客様ごと (パブリック IP ごと) に、レイヤー 3 および 4 のトラフィック パターンを自動学習する。

- 誤検出を最小限に減らす (大量のトラフィックを吸収できる Azure のスケールを活用したもの)。

![DDoS Protection Standard のしくみを示した図 (「ポリシーの世代」が丸で囲まれています)](./media/ddos-best-practices/image-5.png)

## <a name="ddos-protection-telemetry-monitoring-and-alerting"></a>DDoS 保護のテレメトリ、監視、アラート

DDoS Protection Standard では、[Azure Monitor](../azure-monitor/overview.md) を通じて豊富なテレメトリが公開されています。 お客様は、DDoS Protection で使用される任意の Azure Monitor メトリックについて、アラートを構成することができます。 また、ログを Splunk (Azure Event Hubs)、Azure Monitor ログ、Azure Storage と統合し、Azure Monitor 診断インターフェースを介して高度な分析を行うこともできます。

### <a name="ddos-mitigation-policies"></a>DDoS 軽減ポリシー

Azure Portal で **[監視]**  >  **[メトリック]** を選択します。 **[メトリック]** ウィンドウで、リソース グループを選択し、**パブリック IP アドレス** のリソースの種類を選択して、Azure のパブリック IP アドレスを選択します。 DDoS のメトリックが、**使用可能なメトリック** のウィンドウに表示されます。

DDoS Protection Standard は、DDoS が有効になっている仮想ネットワーク内で、保護されたリソースのパブリック IP ごとに、3 つの自動調整された軽減ポリシー (TCP SYN、TCP、UDP) を適用します。 ポリシーのしきい値は、メトリック **[Inbound packets to trigger DDoS mitigation]\(DDoS 軽減をトリガーする着信パケット数\)** を選択することで確認できます。

![使用可能なメトリックとメトリックのグラフ](./media/ddos-best-practices/image-7.png)

ポリシーのしきい値は、機械学習ベースのネットワーク トラフィック プロファイルを使用して自動的に構成されます。 DDoS の軽減は、ポリシーのしきい値を超過した場合にのみ、攻撃を受けている IP アドレスに対して行われます。

### <a name="metric-for-an-ip-address-under-ddos-attack"></a>DDoS 攻撃を受けた IP アドレスの メトリック

パブリック IP アドレスが攻撃を受けると、メトリック **[Under DDoS attack or not]\(DDoS 攻撃中かどうか\)** の値が 1 に切り替わり、攻撃トラフィックに対する軽減措置が実行されます。

![[Under DDoS attack or not]\(DDoS 攻撃中かどうか\) メトリックとグラフ](./media/ddos-best-practices/image-8.png)

このメトリックには、アラートを構成することをお勧めします。 そうすると、パブリック IP アドレスでアクティブな DDoS 軽減が実行されたときに通知が送られます。

詳しくは、「[Azure Portal を使用した Azure DDoS Protection Standard の管理](manage-ddos-protection.md)」をご覧ください。

## <a name="web-application-firewall-for-resource-attacks"></a>リソース攻撃用の Web アプリケーション ファイアウォール

アプリケーション レイヤーでのリソース攻撃に限り、お客様は Web アプリケーション ファイアウォール (WAF) を構成して Web アプリケーションのセキュリティ保護を強化する必要があります。 WAF は、着信 Web トラフィックを検査して、SQL インジェクション、クロスサイト スクリプティング、DDoS、その他のレイヤー 7 攻撃をブロックします。 Azure は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する [Application Gateway の機能として WAF](../web-application-firewall/ag/ag-overview.md) を提供します。 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?search=WAF&page=1) では Azure パートナーのその他の WAF プランを利用でき、ニーズにより適したものが見つかる可能性があります。

Web アプリケーション ファイアウォールを構成しても、帯域幅消費型攻撃や状態枯渇攻撃を受ける可能性があります。 WAF 仮想ネットワーク上で DDoS Protection Standard を有効にして、帯域幅消費型攻撃やプロトコル攻撃に対する保護を強化することを強くお勧めします。 詳しくは、「[DDoS Protection の参照アーキテクチャ](ddos-protection-reference-architectures.md)」セクションをご覧ください。

## <a name="protection-planning"></a>保護の計画

計画と準備は、DDoS 攻撃中にシステムがどのように動作するかを理解するために重要です。 この取り組みには、インシデント管理対応計画の設計が含まれます。

DDoS Protection Standard を利用している場合は、インターネットに接続するエンドポイントの仮想ネットワークで同サービスが有効になっていることを確認する必要があります。 DDoS アラートを構成すると、インフラストラクチャに対する攻撃の可能性を常時監視するのに役立ちます。 

お使いのアプリケーションを独自に監視してください。 アプリケーションの通常の動作を把握し、 DDoS 攻撃時にアプリケーションが想定の動作をしなくなった場合の対応を準備しましょう。 

[シミュレーションを通じてテストを行う](test-through-simulations.md)ことで、ご利用のサービスが攻撃にどのように対応するかを学習します。

## <a name="next-steps"></a>次のステップ

- [DDoS 保護プランの作成](manage-ddos-protection.md)方法について説明します。
