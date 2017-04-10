---
title: "Service Fabric アプリケーションのアップグレード | Microsoft Docs"
description: "この記事では、アップグレード モードの選択や正常性チェックの実行など、Service Fabric アプリケーションのアップグレードの概要を紹介します。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: 803c9c63-373a-4d6a-8ef2-ea97e16e88dd
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/02/2017
ms.author: subramar
translationtype: Human Translation
ms.sourcegitcommit: b4637922e7b280b0e9954c9e51788202e784b4f9
ms.openlocfilehash: 743223f78f279fedf33f73ff52b56f4a7358cd51
ms.lasthandoff: 02/13/2017


---
# <a name="service-fabric-application-upgrade"></a>Service Fabric アプリケーションのアップグレード
Service Fabric アプリケーションは、サービスのコレクションです。 アップグレードの際、Service Fabric は新しい [アプリケーション マニフェスト](service-fabric-application-model.md#describe-an-application) を以前のバージョンと比較し、アプリケーション内でアップグレードの必要があるサービスを決定します。 Service Fabric は、サービス マニフェスト内のバージョン番号を、以前のバージョンのバージョン番号と比較します。 サービスが変更されていない場合は、そのサービスはアップグレードされません。

## <a name="rolling-upgrades-overview"></a>ローリング アップグレードの概要
アプリケーションのローリング アップグレードでは、アップグレードは段階的に実行されます。 各段階で、アップグレードは、更新ドメインと呼ばれる、クラスター内のノードのサブセットに適用されます。 その結果、アプリケーションはアップグレード全体を通して引き続き使用できます。 アップグレード中に、クラスターに古いバージョンと新しいバージョンが混在することがあります。

そのため、その&2; つのバージョンには上位互換性と下位互換性がある必要があります。 互換性がない場合は、アプリケーション管理者が複数フェーズのアップグレードを担当して可用性を維持します。 複数フェーズのアップグレードでは、最初に、以前のバージョンと互換性のあるアプリケーションの中間バージョンにアップグレードします。 次に、更新前のバージョンとの互換性を破る最終バージョンにアップグレードします。この最終バージョンは、中間バージョンに対応しています。

更新ドメインは、クラスターを構成するときに、クラスター マニフェストで指定されます。 更新ドメインが更新を受け取る順序は特に決まってはいません。 更新ドメインは、アプリケーションのデプロイの論理単位です。 更新ドメインを使用すると、アップグレード中、サービスの可用性は高く維持されます。

アップグレードがクラスター内のすべてのノードに適用される場合 (アプリケーションに含まれている更新ドメインが&1; つのみの場合) は、非ローリング アップグレードを行うことができます。 このアプローチは推奨されません。サービスがダウンして、アップグレード時に利用可能にならないためです。 さらに、Azure では、クラスターが&1; つだけの更新ドメインでセットアップされた場合、一切保証しません。

## <a name="health-checks-during-upgrades"></a>アップグレード時の正常性チェック
アップグレードには、正常性ポリシーを設定する必要があります (既定値を使用することもできます)。 指定されたタイムアウト内にすべての更新ドメインがアップグレードされた場合や、すべての更新ドメインが正常であると判断された場合は、アップグレードが成功したことになります。  正常な更新ドメインとは、その更新ドメインが、正常性ポリシーで指定されているすべての正常性チェックに合格したことを意味します。 たとえば、正常性ポリシーでは、Service Fabric で正常性が定義されているように、アプリケーション インスタンス内のすべてのサービスが *正常*であることが要求される場合があります。

アップグレード中の Service Fabric による正常性ポリシーと正常性チェックはサービスとアプリケーションに依存しません。 つまり、サービス固有のテストは実行されません。  たとえば、サービスにスループット要件が必要になる場合がありますが、Service Fabric にはスループットを確認するための情報がありません。 実行されるチェックについては、 [正常性に関する記事](service-fabric-health-introduction.md) をご覧ください。 アップグレード中に実行されるチェックには、アプリケーション パッケージが正しくコピーされたかどうか、インスタンスが開始されたかどうかなどのテストが含まれます。

アプリケーションの正常性は、アプリケーションの子エンティティの集約です。 つまり、Service Fabric では、アプリケーションで報告された正常性によってアプリケーションの正常性が評価されます。 また、アプリケーションのすべてのサービスの正常性もそのように評価されます。 Service Fabric では、アプリケーション サービスの正常性が、サービスのレプリカなど、それらの子の正常性を集約することでさらに評価されます。 アプリケーションの正常性ポリシーが満たされると、アップグレードを続行できます。 正常性ポリシーに違反すると、アプリケーションのアップグレードは失敗します。

## <a name="upgrade-modes"></a>アップグレード モード
アプリケーションのアップグレードに推奨されるモードは、監視対象モードです。これは、よく使用されるモードです。 監視対象モードでは、1 つの更新ドメインでアップグレードを実行し、(指定されたポリシーに従って) すべての正常性チェックに合格すると、自動的に次の更新ドメインに移ります。  正常性チェックが失敗した場合やタイムアウトに達した場合は、更新ドメインのアップグレードはロールバックされるか、監視対象外手動モードに切り替えられます。 アップグレードを構成するときに、この&2; つのモードのいずれかを、アップグレードが失敗した場合のモードとして選択できます。 

監視対象外手動モードでは、更新ドメインですべてのアップグレードが行われた後、次の更新ドメインでアップグレードを開始するために、手動操作が必要になります。 Service Fabric の正常性チェックは実行されません。 管理者は、次の更新ドメインでアップグレードを開始する前に、正常性または状態のチェックを実行します。

## <a name="upgrade-default-services"></a>既定のサービスをアップグレードする
Service Fabric アプリケーション内の既定のサービスは、アプリケーションのアップグレード処理の間にアップグレードできます。 既定のサービスは、[アプリケーション マニフェスト](service-fabric-application-model.md#describe-an-application)で定義されています。 既定サービスのアップグレードの標準的な規則は次のとおりです。

1. クラスターに存在しない新しい[アプリケーション マニフェスト](service-fabric-application-model.md#describe-an-application)の既定のサービスは、作成されます。
> [!TIP]
> 次の規則を有効にするには、[EnableDefaultServicesUpgrade](service-fabric-cluster-fabric-settings.md#fabric-settings-that-you-can-customize) を true に設定する必要があります。 この機能は v5.5 からサポートされています。

2. 以前の[アプリケーション マニフェスト](service-fabric-application-model.md#describe-an-application)と新しいバージョンの両方に存在している既定のサービスは、更新されます。 新しいバージョンのサービス記述により、クラスター内の既存の記述が上書きされます。 既定のサービスの更新が失敗すると、アプリケーションのアップグレードは自動的にロールバックします。
3. 以前の[アプリケーション マニフェスト](service-fabric-application-model.md#describe-an-application)には存在し、新しいバージョンには存在しない既定のサービスは、削除されます。 **この既定サービスの削除は元に戻せないことに注意してください。**

アプリケーションのアップグレードがロールバックされる場合、既定のサービスはアップグレード開始前の状態に戻されます。 ただし、削除されたサービスを作成することはできません。

## <a name="application-upgrade-flowchart"></a>アプリケーション アップグレードのフローチャート
以下に示すフローチャートは、Service Fabric アプリケーションのアップグレード プロセスをわかりやすく示しています。 特に、このフローでは&1; つの更新ドメインのアップグレードが成功または失敗と見なされるときに、*HealthCheckStableDuration*、*HealthCheckRetryTimeout*、*UpgradeHealthCheckInterval* などのタイムアウトが制御にどのように役立つかを説明します。

![Service Fabric アプリケーションのアップグレード プロセス][image]

## <a name="next-steps"></a>次のステップ
[Visual Studio を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial.md) に関する記事では、Visual Studio を使用してアプリケーションをアップグレードする方法について説明します。

[PowerShell を使用したアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md) に関する記事では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

[高度なトピック](service-fabric-application-upgrade-advanced.md)を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。

「 [アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。

[image]: media/service-fabric-application-upgrade/service-fabric-application-upgrade-flowchart.png

