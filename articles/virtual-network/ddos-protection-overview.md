---
title: "Azure DDoS Protection Standard の概要 | Microsoft Docs"
description: "Azure DDoS Protection サービスについて説明します。"
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 0130823b6a6f5a4883ad640c9bcefc89b82b2cd1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection Standard の概要

>[!IMPORTANT]
>Azure DDoS Protection Standard は、現在プレビュー段階にあります。 制限された数の Azure リソースが、選択されたいくつかの地域で DDoS Protection Standard をサポートします。 サブスクリプション用に DDoS Protection Standard を有効にするには、制限されたプレビュー中に[このサービスに登録する](http://aka.ms/ddosprotection)必要があります。 登録すると、Azure DDoS チームから連絡があり、有効化プロセスが案内されます。 DDoS Protection Standard は、米国東部、米国西部、米国中部の各地域で使用できます。 プレビュー中、サービスの使用に対して課金されることはありません。

分散型サービス拒否 (DDoS) 攻撃は、アプリケーションをクラウドに移行している顧客が直面する可用性とセキュリティに関する最大の関心事の 1 つです。 DDoS 攻撃は、アプリケーションのリソースを使い果たして、そのアプリケーションを正当なユーザーから使用不可にしようとします。 DDoS 攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。

Azure DDoS Protection をアプリケーション設計のベスト プラクティスと組み合わせることにより、これらの攻撃に対する防御が提供されます。 次の 2 つのサービス階層が提供されます。 

- **Azure DDoS Protection Basic** - Azure プラットフォームの一部として、追加料金なしで既に自動的に有効になっています。 常時オンのトラフィック監視および一般的なネットワーク レベル攻撃のリアルタイムの軽減策によって、Microsoft のオンライン サービスによって使用されるのと同じ防御が提供されます。  Azure のグローバル ネットワークのスケール全体を使用すると、各地域にまたがる攻撃トラフィックを分散および軽減できます。 
- **Azure DDoS Protection Standard** - 特に Virtual Network リソースに対してチューニングされた追加の軽減機能を提供します。 これを有効にすることは簡単であり、アプリケーションの変更は必要ありません。 保護ポリシーは、専用のトラフィック監視および機械学習アルゴリズムを通してチューニングされ、Load Balancer、Application Gateway、Service Fabric インスタンスなどの Virtual Network リソースに関連付けられたパブリック IP に適用されます。  攻撃中および履歴の表示のために、Azure Monitor ビューからリアルタイムのテレメトリを使用できます。 アプリケーション レイヤー保護は、[Application Gateway Web アプリケーション ファイアウォール](https://azure.microsoft.com/services/application-gateway/)経由で追加できます。 

![Azure DDoS Protection Standard](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

開発とテストのシナリオでは、自由に DDoS Protection Standard を試し、次のリソースを使用してエクスペリエンスに関するフィードバックを提供できます。
- [Microsoft Azure フォーラム上の Azure DDoS Protection](https://feedback.azure.com/forums/905032-azure-ddos-protection)。 
- [MSDN フォーラム上の Azure DDoS Protection](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [Stack Overflow 上の Azure DDoS Protection](https://stackoverflow.com/tags/azure-ddos/info)

サポートの問題については、[Azure サポート チケットを開く](../azure-supportability/how-to-create-azure-support-request.md)ことができます。

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS Protection Standard によって軽減される DDoS 攻撃の種類

DDoS Protection Standard は、次の種類の攻撃を軽減できます。

- **帯域幅消費型攻撃** - 攻撃の目標は、膨大な量の一見正当なトラフィックでネットワーク層をあふれさせることです。 これには、UDP フラッド、増幅フラッド、およびその他の偽装されたパケットのフラッドが含まれます。 DDoS Protection Standard は、この潜在的な数ギガバイトの攻撃を吸収して除去し、Azure のグローバル ネットワーク スケールを自動的に活用することによってこれらの攻撃を軽減します。 
- **プロトコル攻撃** - これらの攻撃は、レイヤー 3 とレイヤー 4 のプロトコル スタック内の弱点を悪用することによって、ターゲットをアクセス不可にします。 これには、SYN フラッド攻撃、リフレクション攻撃、およびその他のプロトコル攻撃が含まれます。 DDoS Protection Standard は、クライアントと対話して悪意のあるトラフィックと正当なトラフィックを区別し、悪意のあるトラフィックをブロックすることによってこれらの攻撃を軽減します。 
- **アプリケーション レイヤー攻撃** - これらの攻撃は、ホスト間のデータ転送を妨害するために Web アプリケーション パケットをターゲットにします。 これには、HTTP プロトコル違反、SQL インジェクション、クロスサイト スクリプティング、およびその他のレイヤー 7 攻撃が含まれます。 Application Gateway WAF を DDoS Protection Standard と共に使用することにより、これらの攻撃に対する防御が提供されます。 

DDoS Protection Standard は、VM、内部ロード バランサー、およびアプリケーション ゲートウェイに関連付けられたパブリック IP を含む仮想ネットワーク内のリソースを保護します。 Application Gateway WAF SKU と結合された場合、DDoS Protection Standard は L3 から L7 までの完全な軽減機能を提供できます。

## <a name="ddos-protection-standard-features"></a>DDoS Protection Standard の機能

![DDoS 機能](./media/ddos-protection-overview/ddos-overview-fig1.png)

DDoS Protection Standard の機能には、次のものが含まれます。 

- **ネイティブなプラットフォーム統合:** DDoS Protection Standard は Azure にネイティブに統合されており、Azure Portal および PowerShell 経由の構成を含みます。 DDoS Protection Standard は、ユーザーのリソースおよびリソース構成を理解しています。
- **常時オンのトラフィック監視:** DDoS 攻撃の兆候を検出するために、アプリケーションのトラフィック パターンが 24 時間 365 日監視されます。 軽減策は、保護ポリシーを超えた場合に実行されます。
- **ターンキー保護:** DDoS Protection Standard が有効になるとすぐに、簡略化された構成によって、仮想ネットワーク上のすべてのリソースが直ちに保護されます。 介入やユーザーの定義は必要ありません。攻撃が検出されると、DDoS Protection Standard はその攻撃を直ちに、かつ自動的に軽減します。
- **アダプティブ チューニング:** インテリジェント トラフィック プロファイリングが一定期間にわたってアプリケーションのトラフィックを学習し、そのサービスに最も適したプロファイルを選択して更新します。 このプロファイルは、時間の経過とともにトラフィックが変化すると調整されます。
- **アプリケーション ゲートウェイによる L3 から L7 までの保護:** Application Gateway WAF は、フル スタックの DDoS 保護を提供します。
- **広範囲にわたる軽減スケール:** 最も大きな既知の DDoS 攻撃から保護するためのグローバル容量により、60 種類を超える攻撃を軽減できます。 
- **攻撃メトリック:** 各攻撃から要約されたメトリックに Azure Monitor 経由でアクセスできます。
- **攻撃アラート:** 組み込みの攻撃メトリックを使用して、攻撃の開始時と停止時、およびその攻撃の期間にわたってアラートを構成できます。 アラートは、OMS、Splunk、Azure Storage、電子メール、Azure Portal などの運用ソフトウェアに統合されます。
- **コストの保証:** データ転送とアプリケーションは、ドキュメント化された DDoS 攻撃のサービス クレジットをスケールアウトします。

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection Standard の軽減策

Microsoft の DDoS Protection サービスは実際のトラフィック使用率を監視し、それを DDoS ポリシーで定義されたしきい値と常に比較します。 そのトラフィックしきい値を超えた場合は、DDoS 軽減策が自動的に開始されます。 トラフィックがしきい値未満に戻ると、その軽減策は削除されます。

軽減策の間、保護されたリソースに向けられたトラフィックは DDoS Protection サービスによってリダイレクトされ、いくつかのチェックが実行されます。 一般に、これらのチェックは次の機能を実行します。

- パケットがインターネット仕様に準拠しており、不正な形式でないことを確認します。
- クライアントと対話して、それが偽装されたパケットである可能性があるかどうかを判定します (たとえば、SYN Auth や SYN Cookie、またはソースへのパケットを削除してそれを再送信します)。
- 他の適用方法を実行できない場合は、パケットをレート制限します。

DDoS Protection は攻撃トラフィックをブロックし、残りのトラフィックを目的の宛先に転送します。 攻撃の検出から数分以内に、Azure Monitor メトリックを使用してユーザーに通知します。 DDoS Protection Standard テレメトリへのログ記録を構成することによって、そのログを将来の分析のための使用可能なオプションに書き込むことができます。 DDoS Protection Standard のための Azure Monitor 内のメトリック データは現在、30 日間保持されます。

顧客が独自の DDoS 攻撃をシミュレートすることはお勧めできません。 代わりに、顧客はサポート チャンネルを使用して、Azure のネットワークによって実行される DDoS 攻撃のシミュレーションを要求できます。 エンジニアがユーザーに連絡して、DDoS 攻撃の詳細 (ポート、プロトコル、ターゲット IP) やテストをスケジュールする時間を調整します。

## <a name="next-steps"></a>次のステップ

- [Azure PowerShell](ddos-protection-manage-ps.md) または [Azure Portal](ddos-protection-manage-portal.md) を使用した DDoS Protection Standard の管理の詳細を学習します。