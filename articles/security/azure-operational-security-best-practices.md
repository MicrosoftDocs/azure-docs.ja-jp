---
title: Azure で運用可能なセキュリティに関するベスト プラクティス | Microsoft Docs
description: この記事は、Azure で運用可能なセキュリティに関するベスト プラクティスを提供します。
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: terrylan
ms.openlocfilehash: e2678eb7d75921f43a1e51b6a8cefc9925a9adc1
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56104603"
---
# <a name="azure-operational-security-best-practices"></a>Azure で運用可能なセキュリティに関するベスト プラクティス
Azure で運用可能なセキュリティとは、ユーザーが Azure 内のデータ、アプリケーション、その他の資産を保護するために使用できる、サービス、コントロール、機能を指します。 Azure で運用可能なセキュリティは、[セキュリティ開発ライフサイクル (SDL)](https://www.microsoft.com/sdl)、[Microsoft セキュリティ レスポンス センター](https://www.microsoft.com/msrc?rtc=1) プログラム、サイバー セキュリティの脅威状況に対する深い認識など、Microsoft に固有の機能から得られた知識が組み込まれたフレームワークを基盤としています。

この記事では、セキュリティに関するベスト プラクティスについて説明します。 このベスト プラクティスは、Azure のデータベース セキュリティに関して Microsoft が蓄積してきたノウハウと、ユーザーの皆様の経験に基づいています。

それぞれのベスト プラクティスについて、次の点を説明します。
-   ベスト プラクティスの内容
-   そのベスト プラクティスをお勧めする理由
-   そのベスト プラクティスを実践しなかった場合に発生する可能性がある事態
- そのベスト プラクティスを実践する方法

この「Azure で運用可能なセキュリティに関するベスト プラクティス」の記事は、この記事の執筆時点における共通認識と、Azure プラットフォームの機能および機能セットに基づいています。 共通認識とテクノロジは時間が経つにつれて変化するため、そのような変化に対応するために、この記事は定期的に更新されます。

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>予期しない動作変化の記憶域サービスを監視します。
クラウド環境でホストされる分散型アプリケーションの問題の診断およびトラブルシューティングは、従来の環境よりも複雑になる可能性があります。 アプリケーションは、PaaS や IaaS インフラストラクチャ、オンプレミス、モバイル デバイス、これらの環境を組み合わせたものにデプロイできます。 アプリケーションのネットワーク トラフィックがパブリックとプライベート ネットワークを走査可能性があり、アプリケーションが複数の記憶域テクノロジを使用して可能性があります。

アプリケーションが動作 (低速の応答時間) などの予期しない変更を使用するストレージ サービスを継続的に監視する必要があります。 詳細なデータを収集して、問題を徹底的に分析するログを使用します。 監視とログの両方から得られた診断情報を基に、アプリケーションで発生した問題の根本原因を特定できます。 そして、問題をトラブルシューティングし、問題を修復する適切な手順を決定できます。

[Azure Storage Analytics](../storage/storage-analytics.md) では、ログが記録され、Azure ストレージ アカウントのメトリック データを得ることができます。 このデータを使用して、要求のトレース、使用傾向の分析、ストレージ アカウントの問題の診断をすることをお勧めします。

## <a name="prevent-detect-and-respond-to-threats"></a>脅威の防止、検出、および対応
[Azure Security Center](../security-center/security-center-intro.md) を使用すると、Azure リソースのセキュリティを高度に視覚化 (および制御) しながら、脅威を回避し、検出し、それらに対応することができます。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、さまざまなセキュリティ ソリューションと連動します。

Security Center の Free レベルでは、Azure リソースに対してのみ限定的なセキュリティを提供します。 Standard レベルでは、オンプレミスおよび他のクラウドにこれらの機能を拡張します。 Security Center Standard は、セキュリティの脆弱性の検出と修正、悪意のあるアクティビティをブロックするためのアクセス制御とアプリケーション制御の適用、分析とインテリジェンスを使用した脅威の検出、攻撃を受けたときのすばやい対応を支援します。 Security Center Standard は最初の 60 日間、無料でお試しいただけます。 [Azure サブスクリプションでの Security Center Standard の利用開始](../security-center/security-center-get-started.md)をお勧めします。

Security Center では、すべての Azure リソースに関するセキュリティの状態を一元的に把握できます。 セキュリティの制御が適切かつ正しく構成されているかを一目で確認し、注意が必要なリソースを素早く特定できます。

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>エンド ツー エンドのシナリオ ベースのネットワーク監視
お客様は、仮想ネットワーク、ExpressRoute、Application Gateway、ロード バランサーなどのネットワーク リソースを組み合わせて Azure にエンド ツー エンド ネットワークを構築します。 各ネットワーク リソースは監視することができます。

[Azure Network Watcher](../network-watcher/network-watcher-monitoring-overview.md) は地域サービスです。 この診断ツールおよび視覚化ツールを使用して、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断します。

ネットワークの監視と使用可能なツールのベスト プラクティスを次に示します。

**ベスト プラクティス**: パケット キャプチャを使ってリモート ネットワーク監視を自動化する。  
**詳細**: Network Watcher を使用して、VM にログインすることなくネットワークの問題を監視して診断します。 アラートの設定により[パケット キャプチャ](../network-watcher/network-watcher-alert-triggered-packet-capture.md)をトリガーし、パフォーマンスに関する情報をパケット レベルでリアルタイムに確認可能です。 問題が起きた場合には、詳細に調査してより適切に診断できます。

**ベスト プラクティス**: フロー ログを使用して、ネットワーク トラフィックに関する分析情報を得る。  
**詳細**: [ネットワーク セキュリティ グループのフロー ログ](../network-watcher/network-watcher-nsg-flow-logging-overview.md)を使うと、ネットワーク トラフィックのパターンをより深く把握できます。 フロー ログの情報をもとに、コンプライアンスに関するデータを収集し、ネットワークのセキュリティ プロファイルを監査、監視できます。

**ベスト プラクティス**: VPN 接続の問題を診断する。  
**詳細**: Network Watcher を使用して、[VPN Gateway と接続に関する最も一般的な問題を診断します](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md)。 問題を識別するだけでなく、詳細なログを使用して、詳しい調査を行うこともできます。

## <a name="secure-deployment-by-using-proven-devops-tools"></a>実証済みの DevOps ツールによるセキュリティ保護されたデプロイ
次の DevOps のベスト プラクティスを使用して、エンタープライズとチームが、生産性と効率性があることを確認します。

**ベスト プラクティス**: サービスのビルドとデプロイを自動化する。  
**詳細**: [コードとしてのインフラストラクチャ](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code)は、モジュール式インフラストラクチャの構築と管理に関連する IT 担当者の日常作業の負担を軽減する、一連の技法とプラクティスです。 これにより、ソフトウェア開発者がアプリケーション コードを構築し保守するように、IT 担当者は最新のサーバー環境を構築、維持できます。

[Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) を使用して、宣言型のテンプレートを使ってアプリケーションをプロビジョニングすることができます。 1 つのテンプレートで、複数のサービスをその依存関係と共にデプロイできます。 アプリケーション ライフサイクルの各ステージで、同じテンプレートを使用してアプリケーションを繰り返しデプロイします。

**ベスト プラクティス**: Azure Web アプリまたはクラウド サービスに自動的にビルドし、デプロイする。  
**詳細**: Azure Pipelines を使用して、Azure Web アプリまたはクラウド サービスに[自動的にビルドし、デプロイする](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts)ことができます。 コードをチェックインするたびに、Azure Pipelines では、Azure へビルドを実行した後、バイナリを自動的にデプロイします。 パッケージのビルド プロセスは、Visual Studio の Package コマンドに相当し、発行手順は Visual Studio の Publish コマンドに相当します。

**ベスト プラクティス**: 継続的なデプロイを使用する。  
**詳細**: [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=vsts) は、複数の段階に分かれたデプロイを自動化し、リリース プロセスを管理するためのソリューションです。 継続的で管理されたデプロイメント パイプラインを作成し、すばやく、簡単に、頻繁にリリースできます。 Azure Pipelines を使用すると、リリース プロセスを自動化し、承認ワークフローを事前定義できます。 オンプレミスまたはクラウドに展開し、必要に応じて拡張、カスタマイズできます。

**ベスト プラクティス**: アプリを起動する前または更新プログラムを運用環境にデプロイする前に、パフォーマンスを確認する。  
**詳細**: Azure Test Plans を使用して、クラウド ベースの[ロード テスト](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)を実行します。

- アプリのパフォーマンスの問題を見つけます。
- デプロイの品質を向上します。
- アプリが常に使用可能であることを確認します。
- アプリが、次の立ち上げ、またはマーケティング キャンペーン時のトラフィックに対応できることを確認します。

**ベスト プラクティス**: アプリケーションのパフォーマンスを監視する。  
**詳細**: [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 Application Insights を使用して、実行中の Web アプリケーションを監視します。 パフォーマンスに異常があると、自動的に検出されます。 組み込まれている分析ツールを使えば、問題を診断し、ユーザーがアプリを使用して実行している操作を把握できます。 Application Insights は、パフォーマンスやユーザビリティを継続的に向上させるうえで役立つように設計されています。

## <a name="mitigate-and-protect-against-ddos"></a>DDoS に対する被害軽減と保護
分散型サービス拒否 (DDoS) は、アプリケーションのリソースを枯渇させようとする攻撃の種類です。 目的は、正当な要求を処理するアプリケーションの可用性と能力に影響を与えることです。 これらの攻撃は、いっそう高度なものになり、規模と影響も大きくなっています。 この攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。

DDoS に対する回復性を設計しビルドするときは、さまざまな障害モードに対応した計画と設計が必要です。

Azure の DDoS 回復性のサービスをビルドするためのベスト プラクティスを次に示します。

**ベスト プラクティス**: 設計と実装から、デプロイと運用まで、アプリケーションのライフ サイクル全体を通して、セキュリティを優先する必要があります。 アプリケーションには、比較的少量の要求によって大量のリソースを使用し、結果としてサービスを停止させるような、バグが含まれる可能性があります。  
**詳細**: Microsoft Azure で実行されるサービスを保護するには、お客様は、自分のアプリケーションのアーキテクチャをよく理解し、[ソフトウェア品質の 5 つの重要な要素](https://docs.microsoft.com/azure/architecture/guide/pillars)に注目する必要があります。 お客様は、標準的なトラフィックの量、アプリケーションと他のアプリケーションの間の接続モデル、パブリック インターネットに公開されるサービス エンドポイントについて知っておく必要があります。

アプリケーション自体を対象とするサービス拒否攻撃に対処するのに十分な回復力をアプリケーションに持たせることが、最も重要です。 Azure プラットフォームには、[セキュリティ開発ライフサイクル (Security Development Lifecycle: SDL)](https://www.microsoft.com/en-us/sdl) からセキュリティとプライバシーが組み込まれるようになっています。 SDL は、すべての開発段階でセキュリティに対処し、Azure がいっそう安全になるように継続的に更新されることを保証します。

**ベスト プラクティス**: [水平方向にスケーリングする](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out)ようにアプリケーションを設計し、増加した負荷の需要を満たす (特に、DDoS 攻撃の場合)。 アプリケーションがサービスの 1 つのインスタンスに依存する場合は、単一障害点が発生します。 複数のインスタンスをプロビジョニングすると、システムの回復力と拡張性が高まります。  
**詳細**: [Azure App Service](../app-service/app-service-value-prop-what-is.md) の場合は、複数のインスタンスを提供する [App Service プラン](../app-service/overview-hosting-plans.md)を選択してください。

Azure Cloud Services の場合は、[複数インスタンス](../cloud-services/cloud-services-choose-me.md)を使用するように各ロールを構成してください。

[Azure Virtual Machines](../virtual-machines/windows/overview.md) の場合は、VM アーキテクチャに 1 つ以上の VM が含まれていることと、[可用性セット](../virtual-machines/virtual-machines-windows-manage-availability.md)に各 VM が含まれていることを確認してください。 自動スケーリング機能には仮想マシン スケール セットを使うことをお勧めします。

**ベスト プラクティス**: アプリケーションのセキュリティ防御を多層化すると、攻撃が成功する可能性が減少します。 Azure プラットフォームの組み込み機能を使って、アプリケーションのセキュリティ保護設計を実装します。  
**詳細**: 攻撃のリスクは、アプリケーションの規模 (攻撃対象領域) と共に大きくなります。 公開されている IP アドレス空間をホワイト リストによって閉鎖し、ロード バランサー ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) と [Azure Application Gateway](../application-gateway/application-gateway-create-probe-portal.md)) に必要のないポートをリッスンすることで、攻撃対象領域を減らすことができます。

[ネットワーク セキュリティ グループ](../virtual-network/security-overview.md)も、攻撃対象領域を軽減する 1 つの手段です。 [サービス タグ](../virtual-network/security-overview.md#service-tags)および[アプリケーション セキュリティ グループ](../virtual-network/security-overview.md#application-security-groups)を使用して、セキュリティ規則作成の複雑さを軽減し、アプリケーションの構造の自然な延長としてネットワーク セキュリティを構成することができます。

お客様は、可能な限り、[仮想ネットワーク](../virtual-network/virtual-networks-overview.md)に Azure サービスをデプロイする必要があります。 このプラクティスを使用すると、サービス リソースはプライベート IP アドレスを通して通信できます。 仮想ネットワークからの Azure サービス トラフィックは、パブリック IP アドレスを発信元 IP アドレスとして既定で使用します。

[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を使用すると、サービス トラフィックは、仮想ネットワークから Azure サービスにアクセスするときに、仮想ネットワークのプライベート アドレスを発信元 IP アドレスとして使用するように切り替わります。

Azure 内のリソースと供に、お客様のオンプレミスのリソースが攻撃されることがよくあります。 オンプレミスの環境を Azure に接続している場合は、オンプレミスのリソースのパブリック インターネットへの露出を最小限にします。

Azure には、ネットワーク攻撃からの保護を提供する 2 つの DDoS [サービス オファリング](../virtual-network/ddos-protection-overview.md)があります。

- 基本保護は、追加コストなしに、既定で Azure に統合されます。 グローバルにデプロイされる Azure ネットワークのスケールと容量が、常時有効なトラフィック監視とリアルタイムのリスク軽減によって、一般的なネットワーク層攻撃からの保護を提供します。 基本保護では、ユーザーによる構成やアプリケーションの変更は必要なく、Azure DNS などの PaaS サービスを含むすべての Azure サービスを保護できます。
- 標準保護は、ネットワーク攻撃に対する高度な DDoS 軽減機能を提供します。 この機能は、お客様固有の Azure リソースを保護するために自動的に調整されます。 保護は、仮想ネットワークの作成時に簡単に有効にできます。 作成の後で行うこともでき、アプリケーションまたはリソースを変更する必要はありません。

## <a name="next-steps"></a>次の手順
Azure を使用してクラウド ソリューションを設計、デプロイ、管理するときに使用するセキュリティのベスト プラクティスの詳細については、「[Azure セキュリティのベスト プラクティスとパターン](security-best-practices-and-patterns.md)」を参照してください。

Azure のセキュリティとそれに関連する Microsoft サービスの一般情報については、以下のリソースを参照してください。
* [Azure セキュリティ チーム ブログ](https://blogs.msdn.microsoft.com/azuresecurity/) – Azure のセキュリティに関する最新情報を提供しています。
* [Microsoft セキュリティ レスポンス センター](https://technet.microsoft.com/library/dn440717.aspx) - このサイトでは、Azure に関する問題を含め、マイクロソフトのセキュリティの脆弱性を報告できます。メールの場合は、secure@microsoft.com 宛に報告してください。
