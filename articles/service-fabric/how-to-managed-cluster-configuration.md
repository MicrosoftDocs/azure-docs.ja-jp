---
title: Service Fabric マネージド クラスター (プレビュー) を構成する
description: OS の自動アップグレード、NSG ルールなどのために Service Fabric マネージド クラスターを構成する方法について説明します。
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: 44b1b949fe314231cb44f190c31b53903e47a904
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732634"
---
# <a name="service-fabric-managed-cluster-preview-configuration-options"></a>Service Fabric マネージド クラスター (プレビュー) の構成オプション

クラスターの作成時に [Service Fabric マネージド クラスターの SKU](overview-managed-cluster.md#service-fabric-managed-cluster-skus) を選択するのに加えて、他にもさまざまな構成方法があります。 現在のプレビューでは、次のことができます。

* クラスターの[ネットワーク オプション](how-to-managed-cluster-networking.md)を構成する
* ノードの種類に[仮想マシン スケール セット拡張機能](how-to-managed-cluster-vmss-extension.md)を追加する
* ノードの種類に[マネージド ID](how-to-managed-identity-managed-cluster-virtual-machine-scale-sets.md) を構成する
* ノードの [OS の自動アップグレード](how-to-managed-cluster-configuration.md#enable-automatic-os-image-upgrades)を有効にする
* ノードで [OS とデータ ディスクの暗号化](how-to-enable-managed-cluster-disk-encryption.md)を有効にする

## <a name="enable-automatic-os-image-upgrades"></a>OS の自動アップグレードを有効にする

マネージド クラスター ノードが実行されている仮想マシンに対して、OS イメージの自動アップグレードを有効にすることができます。 仮想マシン スケール セットのリソースは、ユーザーに代わって Service Fabric マネージド クラスターによって管理されますが、クラスター ノードに対して OS イメージの自動アップグレードを有効にするかどうかはユーザーが選択できます。 [従来の Service Fabric](service-fabric-best-practices-infrastructure-as-code.md#azure-virtual-machine-operating-system-automatic-upgrade-configuration) クラスターと同様に、クラスターに対する意図しない中断を防ぐために、マネージド クラスター ノードは既定ではアップグレードされません。

OS の自動アップグレードを有効にするには、次のようにします。

* `2021-01-01-preview` (またはそれ以降の) バージョンの *Microsoft ServiceFabric/managedclusters* リソースと *Microsoft servicefabric/managedclusters/nodetypes* リソースを使用する
* クラスターのプロパティ `enableAutoOSUpgrade` を *true* に設定する
* クラスターの nodeTypes のリソースのプロパティ `vmImageVersion` を *latest* に設定する

次に例を示します。

```json
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters",
      ...
      "properties": {
        ...
        "enableAutoOSUpgrade": true
      },
    },
    {
      "apiVersion": "2021-01-01-preview",
      "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
       ...
      "properties": {
        ...
        "vmImageVersion": "latest",
        ...
      }
    }
}

```

有効にすると、Service Fabric によってマネージド クラスター内で OS イメージのバージョンの照会と追跡が開始されます。 新しい OS バージョンが利用可能な場合、クラスター ノードの種類 (仮想マシン スケール セット) が一度に 1 つずつアップグレードされます。 Service Fabric ランタイムのアップグレードは、クラスター ノードの OS イメージのアップグレードが進行中でないことを確認した後にのみ実行されます。

アップグレードが失敗した場合、Service Fabric によって 24 時間後に再試行され、最大で 3 回再試行されます。 従来の (アンマネージド) Service Fabric のアップグレードと同様に、異常なアプリやノードによって OS イメージのアップグレードがブロックされることがあります。

イメージのアップグレードの詳細については、「[Azure 仮想マシン スケール セットを使用した OS イメージの自動アップグレード](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Service Fabric マネージド クラスターの概要](overview-managed-cluster.md)

[Service Fabric クラスター テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates)
