---
title: Azure Red Hat OpenShift 4 クラスターのサポート ポリシー
description: Red Hat OpenShift 4 のサポート ポリシー要件について説明します
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 30579536b8051e9a045c217751871287636a3976
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102454280"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Azure Red Hat OpenShift のサポート ポリシー

Azure Red Hat OpenShift 4 クラスターの一部の構成は、クラスターのサポートの可否に影響する可能性があります。 Azure Red Hat OpenShift 4 では、クラスター管理者が内部クラスター コンポーネントを変更できますが、すべての変更がサポートされているわけではありません。 以下のサポート ポリシーでは、ポリシーに違反する変更と、Microsoft および Red Hat からのサポートが無効になる変更を共有します。

> [!NOTE]
> OpenShift Container Platform で Technology Preview とマークされている機能は、Azure Red Hat OpenShift ではサポートされていません。

## <a name="cluster-configuration-requirements"></a>クラスター構成の要件

* すべての OpenShift Cluster オペレーターは、管理された状態のままである必要があります。 `oc get clusteroperators` を実行して、クラスター オペレーターの一覧を返すことができます。
* クラスターには少なくとも 3 つのワーカー ノードと 3 つのマネージャー ノードが必要です。 OpenShift コンポーネントのスケジューリングを妨げることがないようにしてください。 クラスター ワーカーをゼロに変更しないでください。また、クラスターをグレースフル シャットダウンしないでください。
* クラスターの Prometheus および Alertmanager サービスを削除または変更しないでください。
* Service Alertmanager ルールを削除しないでください。
* ネットワーク セキュリティ グループを削除したり変更したりしないでください。
* Azure Red Hat OpenShift サービス ログ (mdsd ポッド) を削除または変更しないでください。
* 'arosvc.azurecr.io' クラスターのプル シークレットを削除または変更しないでください。
* すべてのクラスター仮想マシンには、少なくとも Azure Resource Manager (ARM) とサービス ログ (Geneva) エンドポイントへの直接送信インターネット アクセスが必要です。  HTTPS プロキシの形式はサポートされていません。
* クラスターの仮想ネットワークの DNS 構成は変更しないでください。 既定の Azure DNS リゾルバーを使用する必要があります。
* クラスターの MachineConfig オブジェクト (kubelet 構成など) は、どのような方法でもオーバーライドしないでください。
* unsupportedConfigOverrides オプションを設定しないでください。 これらのオプションを設定すると、マイナー バージョンのアップグレードができなくなります。
* Azure Red Hat OpenShift サービスからはプライベート リンク サービスを介してクラスターにアクセスします。  サービス アクセスを削除または変更しないでください。
* 非 RHCOS コンピューティング ノードはサポートされていません。 たとえば、RHEL コンピューティング ノードを使用することはできません。

## <a name="supported-virtual-machine-sizes"></a>サポートされる仮想マシンのサイズ

Azure Red Hat OpenShift 4 は、次の仮想マシン サイズのワーカー ノード インスタンスをサポートしています。

### <a name="general-purpose"></a>汎用

|系列|サイズ|vCPU|メモリ:GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>メモリ最適化

|系列|サイズ|vCPU|メモリ:GiB|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>コンピューティング最適化

|系列|サイズ|vCPU|メモリ:GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>マスター ノード

|系列|サイズ|vCPU|メモリ:GiB|
|-|-|-|-|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
