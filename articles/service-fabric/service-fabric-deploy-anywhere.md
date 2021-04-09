---
title: Azure とスタンドアロン Service Fabric クラスターの概要
description: Windows Server または Linux を実行する任意の VM またはコンピューター上に Service Fabric クラスターを作成できます。 つまり、オンプレミスか、Microsoft Azure か、クラウド プロバイダーかに関係なく、相互接続された一連の Windows Server コンピューターまたは Linux コンピューターがある任意の環境に、Service Fabric アプリケーションをデプロイして実行できます。
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: d83ec139c367d9a539db173a42ae909f57d5d71a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96576130"
---
# <a name="comparing-azure-and-standalone-service-fabric-clusters-on-windows-server-and-linux"></a>Windows Server と Linux での Azure とスタンドアロン Service Fabric クラスターの比較

Service Fabric クラスターは、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターに属しているコンピューターまたは VM をクラスター ノードといいます。 クラスターは多数のノードにスケールできます。 新しいノードがクラスターに追加されると、Service Fabric は、増加したノード数全体で、サービスのパーティションのレプリカとインスタンスのバランスを再調整します。 アプリケーション全体のパフォーマンスが向上し、メモリへのアクセスの競合が減少します。 クラスター内のノードが効率的に使用されていない場合、クラスター内のノードの数を削減できます。 Service Fabric は、各ノードのハードウェアを効率的に利用できるように、減らされたノード数全体で、再度パーティションのレプリカとインスタンスのバランスを再調整します。

Service Fabric を使用すると、Windows Server または Linux を実行するすべての VM またはコンピューター上に Service Fabric クラスターを作成できます。 つまり、オンプレミスか、Microsoft Azure か、クラウド プロバイダーかに関係なく、相互接続された一連の Windows Server コンピューターまたは Linux コンピューターがある任意の環境に、Service Fabric アプリケーションをデプロイして実行できます。

## <a name="benefits-of-clusters-on-azure"></a>Azure 上のクラスターの利点

Azure では、他の Azure の機能やサービスとの統合が提供されているため、クラスターの操作と管理が容易で信頼性が高くなります。

* **Azure portal:** Azure portal を使用すると、クラスターの作成と管理が容易になります。
* **Azure Resource Manager:** Azure Resource Manager を使用すると、クラスターでユニットとして使用されているリソースの管理が容易になり、コストの追跡と要求を簡素化できます。
* **Azure リソースとしての Service Fabric クラスター** : Service Fabric クラスターは Azure リソースなので、Azure の他の Azure リソースと同じようにモデル化できます。
* **Azure インフラストラクチャとの統合** : Service Fabric は、OS、ネットワーク、およびその他のアップグレードに関して基になる Azure インフラストラクチャと連携するため、アプリケーションの可用性と信頼性が向上します。  
* **診断**:Azure では、Azure Diagnostics および Azure Monitor ログとの統合が提供されます。
* **自動スケール:** Azure 上のクラスターの場合、仮想マシン スケール セットを通じて、組み込みの自動スケール機能が提供されます。 オンプレミスおよび他のクラウド環境では、自動スケール機能を独自に構築するか、Service Fabric で公開されているクラスター スケーリング用 API を手動で使用してスケールする必要があります。

## <a name="benefits-of-standalone-clusters"></a>スタンドアロン クラスターの利点

* クラスターをホストするクラウド プロバイダーを自由に選択できます。
* Service Fabric アプリケーションは、一度記述すると、複数のホスティング環境で最小限の変更で実行できます。
* Service Fabric アプリケーションの構築に関する知識は、ホスティング環境が変わっても適用できます。
* Service Fabric クラスターの実行と管理に関する運用経験は、環境が変わっても活かせます。
* ホスティング環境による制約がないため、広範な顧客にアプローチできます。
* データ センターまたはクラウド プロバイダーにブラックアウトが発生した場合でもサービスを別のデプロイ環境に移行できるため、広範囲に及ぶサービス停止に対する信頼性と保護が強化されています。

## <a name="next-steps"></a>次のステップ

* [Azure 上の Service Fabric クラスター](service-fabric-azure-clusters-overview.md)の概要を読む
* [Service Fabric スタンドアロン クラスター](service-fabric-standalone-clusters-overview.md)の概要を読む
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。