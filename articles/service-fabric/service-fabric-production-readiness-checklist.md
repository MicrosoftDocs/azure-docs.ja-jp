---
title: Azure Service Fabric の運用環境の準備状況チェックリスト | Microsoft Docs
description: ベスト プラクティスに従って、Service Fabric アプリケーションとクラスターの運用準備をします。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/10/2018
ms.author: aljo
ms.openlocfilehash: e94280f9df1d4ac59856a73f6f6c2b7f7a0b9cc0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58107493"
---
# <a name="production-readiness-checklist"></a>運用環境の準備状況チェックリスト

お使いのアプリケーションとクラスターは、運用環境のトラフィックに対応する準備ができていますか。 アプリケーションとクラスターを実行してテストしたからといって、必ずしも運用環境に移行するための準備ができているというわけではありません。 次のチェックリストを検討して、アプリケーションとクラスターが円滑に実行されるようにします。 これらの項目すべてをチェック済みにすることを強くお勧めします。 当然ながら、特定の項目に代替ソリューションを使用することができます (独自の診断フレームワークなど)。


## <a name="pre-requisites-for-production"></a>運用環境の前提条件
1. [Azure Service Fabric のセキュリティに関するベスト プラクティス](https://docs.microsoft.com/azure/security/azure-service-fabric-security-best-practices): 
1. X.509 証明書を使用する
1. セキュリティ ポリシーを構成する
1. Azure Service Fabric の SSL を構成する
1. Azure Service Fabric にネットワークの分離とセキュリティを使用する
1. セキュリティ確保のために Azure Key Vault を設定する
1. ロールへの Microsoft.Network/loadBalancersAssign ユーザー
1. Actors プログラミング モデルを使用している場合に Reliable Actors のセキュリティ構成を実装する
1. 20 を超えるコアまたは 10 個を超えるノードを持つクラスターの場合は、システム サービス用に専用のプライマリ ノード タイプを作成します。 [配置の制約](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)を追加して、システム サービス用にプライマリ ノード タイプを予約します。
1. プライマリ ノード タイプには D2v2 または上位の SKU を使用します。 少なくとも 50 GB のハード ディスク容量を持つ SKU を選択することをお勧めします。
1. 運用環境クラスターは、[セキュリティで保護されている](service-fabric-cluster-security.md)必要があります。 セキュリティで保護されたクラスターの設定例については、この[クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG)を参照してください。 証明書の共通名を使用します。自己署名証明書は使用しないでください。
1. [コンテナーとサービスのリソース制約](service-fabric-resource-governance.md)を追加して、ノード リソースの消費が 75% を超えないようにします。 
1. [持続性レベル](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)を理解して設定します。 ステートフル ワークロードを実行しているノード タイプには、シルバー以上の持続性レベルを設定することをお勧めします。 プライマリ ノード タイプには、持続性レベルをシルバー以上に設定する必要があります。
1. ノード タイプの[信頼性レベル](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)を理解して選択します。 シルバー以上の信頼性をお勧めします。
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
1. 最新の変更が継続的にテストされるように、CI/CD パイプラインを確立します。 たとえば、[Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) または [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md) を使用して
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


## <a name="next-steps"></a>次の手順
* [Service Fabric Windows クラスターをデプロイする](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Service Fabric Linux クラスターをデプロイする](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Service Fabric の[アプリケーション ライフサイクル](service-fabric-application-lifecycle.md)について確認します。
