---
title: Azure Service Fabric の運用環境の準備状況チェックリスト
description: ベスト プラクティスに従って、Service Fabric アプリケーションとクラスターの運用準備をします。
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: a03df40a8ce213c5de9ed7017d47713c4de3449d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87835869"
---
# <a name="production-readiness-checklist"></a>運用環境の準備状況チェックリスト

お使いのアプリケーションとクラスターは、運用環境のトラフィックに対応する準備ができていますか。 アプリケーションとクラスターを実行してテストしたからといって、必ずしも運用環境に移行するための準備ができているというわけではありません。 次のチェックリストを検討して、アプリケーションとクラスターが円滑に実行されるようにします。 これらの項目すべてをチェック済みにすることを強くお勧めします。 当然ながら、特定の項目に代替ソリューションを使用することができます (独自の診断フレームワークなど)。


## <a name="prerequisites-for-production"></a>運用環境の前提条件
1. Azure Service Fabric のベスト プラクティス:[アプリケーションの設計](./service-fabric-best-practices-applications.md)、[セキュリティ](./service-fabric-best-practices-security.md)、[ネットワーク](./service-fabric-best-practices-networking.md)、[キャパシティ プランニングとスケール](./service-fabric-best-practices-capacity-scaling.md)、[コードとしてのインフラストラクチャ](./service-fabric-best-practices-infrastructure-as-code.md)、および[監視と診断](./service-fabric-best-practices-monitoring.md)。 
1. Reliable Actors プログラミング モデルを使用していて、セキュリティで保護されたサービス間通信が必要な場合は、[FabricTransport 設定を構成します](./service-fabric-reliable-actors-fabrictransportsettings.md)。
1. 20 を超えるコアまたは 10 個を超えるノードを持つクラスターの場合は、システム サービス用に専用のプライマリ ノード タイプを作成します。 [配置の制約](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)を追加して、システム サービス用にプライマリ ノード タイプを予約します。
1. プライマリ ノード タイプには D2v2 または上位の SKU を使用します。 少なくとも 50 GB のハード ディスク容量を持つ SKU を選択することをお勧めします。
1. 運用環境クラスターは、[セキュリティで保護されている](service-fabric-cluster-security.md)必要があります。 セキュリティで保護されたクラスターの設定例については、この[クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)を参照してください。 証明書の共通名を使用します。自己署名証明書は使用しないでください。
1. [コンテナーとサービスのリソース制約](service-fabric-resource-governance.md)を追加して、ノード リソースの消費が 75% を超えないようにします。 
1. [持続性レベル](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)を理解して設定します。 ステートフル ワークロードを実行しているノード タイプには、シルバー以上の持続性レベルを設定することをお勧めします。
1. ノード タイプの[信頼性レベル](service-fabric-cluster-capacity.md#reliability-characteristics-of-the-cluster)を理解して選択します。 シルバー以上の信頼性をお勧めします。
1. クラスターの[容量の要件](service-fabric-cluster-capacity.md)を特定するため、ワークロードのロード テストとスケール テストを実行します。 
1. アラートを使用して、サービスとアプリケーションが監視され、アプリケーション ログが生成され、格納されています。 例については、「[Service Fabric アプリケーションにログ記録を追加する](service-fabric-how-to-diagnostics-log.md)」と「[Monitor containers with Azure Monitor logs (Azure Monitor ログによるコンテナーの監視)](service-fabric-diagnostics-oms-containers.md)」を参照してください。
1. クラスターがアラート ([Azure Monitor ログ](service-fabric-diagnostics-event-analysis-oms.md)など) で監視されています。 
1. 基になる仮想マシン スケール セット インフラストラクチャが、アラート ([Azure Monitor ログ](service-fabric-diagnostics-oms-agent.md)など) で監視されています。
1. ロックアウトされないように、クラスターには、常に[プライマリ証明書とセカンダリ証明書](service-fabric-cluster-security-update-certs-azure.md)があります。
1. 開発、ステージング、および運用環境用に個別のクラスターを維持します。 
1. [アプリケーションのアップグレード](service-fabric-application-upgrade.md)と[クラスターのアップグレード](service-fabric-tutorial-upgrade-cluster.md)は、最初に開発クラスターとステージング クラスターでテストされます。 
1. 自動アップグレードは、運用環境クラスターではオフにして、開発環境クラスターとステージング クラスターではオンにします (必要に応じてロールバック)。 
1. サービスの目標復旧時点 (RPO) を確立し、[ディザスター リカバリー プロセス](service-fabric-disaster-recovery.md)を設定してそれをテストします。
1. クラスターの手動またはプログラムを使用した[スケーリング](service-fabric-cluster-scaling.md)を計画します。
1. クラスター ノードへの[修正プログラムの適用](service-fabric-patch-orchestration-application.md)を計画します。 
1. 最新の変更が継続的にテストされるように、CI/CD パイプラインを確立します。 たとえば、[Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) または [Jenkins](/azure/developer/jenkins/deploy-to-service-fabric-cluster) を使用して
1. [Fault Analysis Service](service-fabric-testability-overview.md) と制御された[混乱](service-fabric-controlled-chaos.md)の誘発を使用して、負荷の下で開発クラスターとステージング クラスターをテストします。 
1. アプリケーションの[スケーリング](service-fabric-concepts-scalability.md)を計画します。 


Service Fabric の Reliable Services または Reliable Actors プログラミング モデルを使用している場合は、次の項目をチェック済みにする必要があります。
1. ローカル開発中にアプリケーションをアップグレードして、サービス コードが `RunAsync` メソッドでキャンセル トークンを受け入れること、およびカスタム通信リスナーを終了することを確認します。
1. Reliable Collection を使用する場合は、[よくある落とし穴](service-fabric-work-with-reliable-collections.md)を回避します。
1. ロード テストの実行時に、.NET CLR メモリ パフォーマンス カウンターを監視して、高いレートのガベージ コレクションやランナウェイ ヒープの増加を確認します。
1. [Reliable Services と Reliable Actors](service-fabric-reliable-services-backup-restore.md) のオフライン バックアップを保持して、復元プロセスをテストします。
1. プライマリ NodeType 仮想マシン インスタンス数は理想的には、クラスターの信頼性レベルの最小と等しくする必要があります。レベルの最小を超えることが適切な場合の条件は、プライマリ NodeTypes 仮想マシン スケール セット SKU を垂直方向にスケーリングする場合の一時的なものなどです。

## <a name="optional-best-practices"></a>省略可能なベスト プラクティス

上記のリストは、運用に入るための前提条件ですが、次の項目も考慮する必要があります。
1. 組み込みの正常性評価とレポートを拡張するため、[Service Fabric の正常性モデル](service-fabric-health-introduction.md)にプラグインします。
1. [リソース分散](service-fabric-cluster-resource-manager-balancing.md)のためにアプリケーションとレポートの[負荷](service-fabric-cluster-resource-manager-metrics.md)を監視しているカスタム ウォッチドッグをデプロイします。 


## <a name="next-steps"></a>次のステップ
* [Service Fabric Windows クラスターをデプロイする](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Service Fabric Linux クラスターをデプロイする](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。