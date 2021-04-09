---
title: Azure Service Fabric クラスターのアップグレード
description: Azure Service Fabric クラスターのバージョンまたは構成のアップグレードについて説明します。ここでは、クラスターの更新モードの設定、証明書のアップグレード、アプリケーション ポートの追加、OS 修正プログラムの実行、およびアップグレードの実行時に期待できる内容について説明します。
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: 028c91f85a6e318f7ea686c1bcd50262eb7c6bf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96571030"
---
# <a name="upgrading-and-updating-an-azure-service-fabric-cluster"></a>Azure Service Fabric クラスターのアップグレードと更新

最新のシステムでは、アップグレード性を考慮した設計を行うことが、製品の長期的な成功を達成する鍵となります。 Azure Service Fabric クラスターはお客様が所有するリソースですが、一部は Microsoft によって管理されます。 この記事では、何が自動的に管理され、何をお客様が構成できるかについて説明します。

## <a name="controlling-the-fabric-version-that-runs-on-your-cluster"></a>クラスター上で動作するファブリック バージョンの制御

クラスターでは常に、[サポートされているバージョンのファブリック](service-fabric-versions.md)が実行されている状態にします。 Microsoft が Service Fabric の新バージョン リリースをアナウンスするたびに、その日から最短で 60 日後には、以前のバージョンがサポート期間の終了として指定されます。 新しいリリースは、[Service Fabric チーム ブログ](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)で発表されます。

現在クラスターで実行されているバージョンの有効期間が終了する 14 日前に、正常性に関するイベントが生成され、クラスターの正常性が警告状態に移行します。 サポートされているバージョンのファブリックにアップグレードするまで、クラスターは警告状態のままとなります。

Microsoft からのリリース時に自動ファブリック アップグレードを受信するようにクラスターを設定するか、有効にするクラスターのサポートされるファブリック バージョンを選択することもできます。  詳細については、[クラスターの Service Fabric バージョンのアップグレード](service-fabric-cluster-upgrade-version-azure.md)に関するページをご覧ください。

## <a name="fabric-upgrade-behavior-during-automatic-upgrades"></a>自動アップグレード時の Fabric のアップグレード動作

Microsoft は、Azure クラスターで実行されるファブリック コードと構成を管理します。 必要に応じて、ソフトウェアに対して自動的な監視付きアップグレードを実行します。 これらのアップグレードは、コード、構成、またはその両方で行うことができます。 これらのアップグレードからアプリケーションが影響を受けない、またはその影響を最小限にするために、アップグレードは次のフェーズで行われます。

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>フェーズ 1:アップグレードが、すべてのクラスター正常性ポリシーを使用して実行される

このフェーズ中、アップグレード ドメインは 1 つずつ処理され、クラスターで実行されていたアプリケーションはダウンタイムなしで実行され続けます。 クラスター正常性ポリシー (ノードの正常性とアプリケーションの正常性) は、アップグレード中、遵守されます。

クラスター正常性ポリシーが満たされていない場合、アップグレードはロールバックされ、サブスクリプションの所有者に電子メールが送信されます。 この電子メールには以下の情報が含まれています。

* クラスターのアップグレードをロールバックする必要があるという通知
* 推奨される対応策 (ある場合)
* フェーズ 2 を実行するまでの日数 (*n*)。

インフラストラクチャに関する理由でアップグレードに失敗した場合は、同じアップグレードが数回実行されます。 電子メールの送信日から *n* 日後に、フェーズ 2 に進みます。

クラスター正常性ポリシーが満たされた場合は、アップグレードが成功したと見なされ、完了としてマークされます。 このフェーズの最初のアップグレードで成功することも、何回目かの再実行で成功することもあります。 実行が成功した場合、電子メールでの確認はありません。 これは、送信される電子メールが多くなりすぎないようにするためです。電子メールを受信するのは、正常でないことが起きた場合だけです。 クラスターのアップグレードの大半は、アプリケーションの可用性に影響することなく、成功すると思われます。

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>フェーズ 2:アップグレードが、既定の正常性ポリシーのみを使用して実行される

このフェーズでの正常性ポリシーは、アップグレードの開始時に正常だったアプリケーションの数が、アップグレード プロセス中も同じ数であり続けるように設定されています。 フェーズ 1 のように、フェーズ 2 のアップグレードではアップグレード ドメインが 1 つずつ処理され、クラスターで実行されていたアプリケーションはダウンタイムなしで実行され続けます。 クラスター正常性ポリシー (ノードの正常性と、クラスターで実行されているすべてのアプリケーションの正常性の組み合わせ) は、アップグレードの実行中、遵守されます。

有効なクラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 その後、サブスクリプションの所有者に電子メールが送信されます。 この電子メールには以下の情報が含まれています。

* クラスターのアップグレードをロールバックする必要があるという通知
* 推奨される対応策 (ある場合)
* フェーズ 3 を実行するまでの日数 (n)。

インフラストラクチャに関する理由でアップグレードに失敗した場合は、同じアップグレードが数回実行されます。 リマインダーの電子メールが、n 日の数日前に送信されます。 電子メールの送信日から n 日後に、フェーズ 3 に進みます。 フェーズ 2 で送信された電子メールは、真剣に受け止め、修復操作を実行する必要があります。

クラスター正常性ポリシーが満たされた場合は、アップグレードが成功したと見なされ、完了としてマークされます。 このフェーズの最初のアップグレードで成功することも、何回目かの再実行で成功することもあります。 実行が成功した場合、電子メールでの確認はありません。

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>フェーズ 3: アップグレードが、アグレッシブな正常性ポリシーを使用して実行される

このフェーズでのこれらの正常性ポリシーは、アプリケーションの正常性よりもアップグレードの完了のために調整されています。 このフェーズで終了するクラスター アップグレードは、ほとんどありません。 クラスターがこのフェーズに達すると、アプリケーションが正常な状態でなくなるか、可用性が失われたりする確率が高くなります。

他の 2 つのフェーズと同様に、フェーズ 3 でもアップグレード ドメインが 1 つずつ処理されます。

クラスター正常性ポリシーが満たされていない場合は、アップグレードがロールバックされます。 インフラストラクチャに関する理由でアップグレードに失敗した場合は、同じアップグレードが数回実行されます。 その後、そのクラスターはサポートやアップグレードを受信しなくなるように固定されます。

この情報と修復操作が記載された電子メールが、サブスクリプションの所有者に送信されます。 クラスターがフェーズ 3 でも失敗する状態になることは、想定していません。

クラスター正常性ポリシーが満たされた場合は、アップグレードが成功したと見なされ、完了としてマークされます。 このフェーズの最初のアップグレードで成功することも、何回目かの再実行で成功することもあります。 実行が成功した場合、電子メールでの確認はありません。

## <a name="manage-certificates"></a>証明書の管理

Service Fabric では、クラスターの作成時に指定した [X.509 server certificates](service-fabric-cluster-security.md) を使用して、クラスター ノード間の通信をセキュリティで保護し、クライアントを認証します。 [Azure Portal](https://portal.azure.com) からか、PowerShell または Azure CLI を使用して、クラスターとクライアントに対して証明書を追加、更新、または削除することができます。  詳細については、[証明書の追加または削除](service-fabric-cluster-security-update-certs-azure.md)に関するページを参照してください。

## <a name="open-application-ports"></a>アプリケーション ポートを開く

アプリケーション ポートは、ノードの種類に関連付けられた Load Balancer リソースのプロパティを変更することで変更できます。 Azure Portal を使用するか、PowerShell または Azure CLI を使用できます。 詳細については、[クラスターのアプリケーション ポートを開く](create-load-balancer-rule.md)ことに関するページを参照してください。

## <a name="define-node-properties"></a>ノードのプロパティの定義

場合によっては、特定のワークロードが、クラスター内の特定のノードの種類だけで確実に実行されるようにしたいことがあります。 たとえば、ワークロードの中に GPU や SSD を必要とするものとしないものが混在している場合があります。 クラスター内のノードの種類ごとに、カスタム ノードのプロパティをクラスター ノードに追加できます。 配置の制約は、1 つまたは複数のノードのプロパティに選択される個々のサービスに接続されるステートメントです。 配置の制約で、サービスを実行する場所を定義します。

配置の制約、ノードのプロパティの使用、およびプロパティの定義方法の詳細については、「[ノードのプロパティと配置の制約](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)」を参照してください。

## <a name="add-capacity-metrics"></a>容量メトリックの追加

ノードの種類ごとに、アプリケーションで負荷をレポートするために使用するカスタム容量メトリックを追加できます。 負荷をレポートする容量メトリックの使用方法については、Service Fabric クラスター リソース マネージャー ドキュメントの [クラスターの説明](service-fabric-cluster-resource-manager-cluster-description.md) および [メトリックと負荷](service-fabric-cluster-resource-manager-metrics.md) に関するページをご覧ください。

## <a name="set-health-policies-for-automatic-upgrades"></a>自動アップグレードに正常性ポリシーを設定する

ファブリックのアップグレードには、カスタム正常性ポリシーを指定できます。 指定したポリシーは、クラスターのファブリック アップグレードが Automatic に設定されている場合、自動ファブリック アップグレードのフェーズ 1 に適用されます。
クラスターのファブリック アップグレードを Manual に設定した場合は、新しいバージョンを選択するたびにこれらのポリシーが適用され、クラスターのファブリック アップグレードが開始されます。 ポリシーをオーバーライドしていない場合、既定の設定が使用されます。

カスタム正常性ポリシーを指定したり、現在の設定を確認したりするには、[ファブリック アップグレード] ブレードでアップグレードの詳細設定を選択します。 具体的な方法については、次の図を参照してください。

![Manage custom health policies][HealthPolices]

## <a name="customize-fabric-settings-for-your-cluster"></a>クラスターのファブリック設定のカスタマイズ

クラスターとノードのプロパティの信頼性レベルなど、多くの異なる構成設定は、クラスター上でカスタマイズできます。 詳細については、[Service Fabric クラスターのファブリック設定](service-fabric-cluster-fabric-settings.md)に関するページを参照してください。

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
