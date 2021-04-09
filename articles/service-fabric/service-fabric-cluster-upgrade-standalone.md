---
title: Azure Service Fabric スタンドアロン クラスターをアップグレードする
description: Azure Service Fabric スタンドアロン クラスターのバージョンまたは構成のアップグレードについて説明します。  T
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 519a9163d16fda2dd9fcf49cf22fe4ad4a272e09
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86260978"
---
# <a name="upgrading-and-updating-a-service-fabric-standalone-cluster"></a>Service Fabric スタンドアロン クラスターのアップグレードと更新

最新のシステムでは、アップグレード性を考慮した設計を行うことが、製品の長期的な成功を達成する鍵となります。 Azure Service Fabric スタンドアロン クラスターは、ユーザーが所有するリソースの 1 つです。 この記事では、アップグレードまたは更新できるものについて説明します。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>クラスター上で動作するファブリック バージョンの制御
クラスターで [Service Fabric のサポートされているバージョン](service-fabric-versions.md)が常に実行されるようにする必要があります。 Microsoft が Service Fabric の新しいバージョンのリリースを発表すると、前のバージョンのサポート期間の終了が発表日から最短で 60 日後に設定されます。 新しいリリースは、[Service Fabric チーム ブログ](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)で発表されます。 その時点で新しいリリースを選択できるようになります。

Microsoft からのリリース時に自動ファブリック アップグレードを受信するようにクラスターを設定するか、有効にするクラスターのサポートされるファブリック バージョンを手動で選択することもできます。 詳細については、「[Windows Server でのスタンドアロン Azure Service Fabric クラスターのアップグレード](service-fabric-cluster-upgrade-windows-server.md)」をご覧ください。

## <a name="customize-configuration-settings"></a>構成設定をカスタマイズする

クラスターの信頼性レベルやノードのプロパティなど、*ClusterConfig.json* ファイルでは多くの異なる [構成設定](service-fabric-cluster-manifest.md)を設定できます。  詳細については、「[Upgrade the configuration of a standalone cluster](service-fabric-cluster-config-upgrade-windows-server.md)」(スタンドアロン クラスターの構成をアップグレードする) をご覧ください。  他の多くのさらに高度な設定もカスタマイズできます。  詳細については、[Service Fabric クラスターのファブリック設定](service-fabric-cluster-fabric-settings.md)に関するページを参照してください。

## <a name="define-node-properties"></a>ノードのプロパティの定義
場合によっては、特定のワークロードが、クラスター内の特定のノードの種類だけで確実に実行されるようにしたいことがあります。 たとえば、ワークロードの中に GPU や SSD を必要とするものとしないものが混在している場合があります。 クラスター内のノードの種類ごとに、カスタム ノードのプロパティをクラスター ノードに追加できます。 配置の制約は、1 つまたは複数のノードのプロパティに選択される個々のサービスに接続されるステートメントです。 配置の制約で、サービスを実行する場所を定義します。

配置の制約、ノードのプロパティの使用、およびプロパティの定義方法の詳細については、「[ノードのプロパティと配置の制約](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)」を参照してください。
 

## <a name="add-capacity-metrics"></a>容量メトリックの追加
ノードの種類ごとに、アプリケーションで負荷をレポートするために使用するカスタム容量メトリックを追加できます。 負荷をレポートする容量メトリックの使用方法については、Service Fabric クラスター リソース マネージャー ドキュメントの [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md) および [メトリックと負荷](service-fabric-cluster-resource-manager-metrics.md) に関するページをご覧ください。

## <a name="patch-the-os-in-the-cluster-nodes"></a>クラスター ノードで OS に修正プログラムを適用する
パッチ オーケストレーション アプリケーション (POA) は、ダウンタイムなしで、Service Fabric クラスターでのオペレーティング システムへのパッチの適用を自動化する Service Fabric アプリケーションです。 [Windows 用のパッチ オーケストレーション アプリケーション](service-fabric-patch-orchestration-application.md)をクラスターにデプロイすることにより、常時サービスの稼働状態を維持しながら、調整された方法でパッチをインストールすることができます。 


## <a name="next-steps"></a>次のステップ
* [Service Fabric クラスターのファブリック設定](service-fabric-cluster-fabric-settings.md)
* [クラスターのスケールアップとスケールダウン](service-fabric-cluster-scale-in-out.md)
* [アプリケーションのアップグレード](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
