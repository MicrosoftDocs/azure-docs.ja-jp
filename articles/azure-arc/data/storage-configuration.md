---
title: ストレージの構成
description: Azure Arc 対応データ サービスのストレージ構成オプションについて説明します
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 7b683029b7fd05078755d4e8cd027f55c805f991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97107262"
---
# <a name="storage-configuration"></a>ストレージの構成

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Kubernetes ストレージの概念

Kubernetes は、基盤となる仮想化技術スタック (オプション) とハードウェアにインフラストラクチャ抽象化レイヤーを提供します。 Kubernetes がストレージを抽象化する方法は、 **[ストレージ クラス](https://kubernetes.io/docs/concepts/storage/storage-classes/)** です。 ポッドをプロビジョニングするときに、各ボリュームに使用するストレージ クラスを指定できます。 ポッドがプロビジョニングされると、ストレージ クラス **[プロビジョナー](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** が呼び出されてストレージがプロビジョニングされ、プロビジョニングされたストレージ上に **[永続ボリューム](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** が作成された後、ポッドが **[永続ボリューム要求](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)** によって永続ボリュームにマウントされます。

Kubernetes は、Kubernetes を拡張するドライバー ("アドオン" とも呼ばれます) をストレージ インフラストラクチャ プロバイダーがプラグインする手段を提供します。 ストレージのアドオンは、 **[Container Storage Interface 標準](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)** に準拠している必要があります。 この明確でない **[CSI ドライバーのリスト](https://kubernetes-csi.github.io/docs/drivers.html)** に含まれているアドオンも多数あります。 どの CSI ドライバーを使用するかは、クラウドでホストされたマネージド Kubernetes サービスで実行しているかどうか、またはハードウェアに使用している OEM プロバイダーなどの要因によって異なります。

次のコマンドを実行して、Kubernetes クラスターで構成されているストレージ クラスを表示できます。

```console
kubectl get storageclass
```

Azure Kubernetes Service (AKS) クラスターからの出力例:

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

ストレージ クラスの詳細を取得するには、次のコマンドを実行します。

```console
kubectl describe storageclass/<storage class name>
```

例:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

次のコマンドを実行して、現在プロビジョニングされている永続ボリュームと永続ボリューム要求を確認できます。

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

永続ボリュームを表示する例:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

永続ボリューム要求を表示する例:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>ストレージ構成を選択する際に考慮する要素

適切なストレージ クラスを選択することは、データの回復性とパフォーマンスにとって重要です。 間違ったストレージ クラスを選択すると、ハードウェア障害が発生した場合にデータが失われたり、パフォーマンスが低下したりする可能性があります。

一般に、ストレージには次の 2 種類があります。

- **ローカル ストレージ** - 特定のノードのローカル ハード ドライブにプロビジョニングされているストレージ。 この種類のストレージはパフォーマンスに関しては理想的ですが、データを複数のノードにレプリケートすることによって、データの冗長性を確保するために特別に設計する必要があります。
- **リモートの共有ストレージ** - 一部のリモート記憶装置 (例えば、SAN、NAS、または EBS や Azure Files などのクラウド ストレージ サービス) でプロビジョニングされているストレージ。 通常、この種類のストレージはデータの冗長性を自動的に提供しますが、ローカル ストレージの場合ほど高速ではありません。

> [!NOTE]
> 現時点では、NFS を使用する場合は、Azure Arc データ コントローラーをデプロイする前に、デプロイ プロファイル ファイルで allowRunAsRoot を true に設定する必要があります。

### <a name="data-controller-storage-configuration"></a>データ コントローラーのストレージ構成

データ サービス用の Azure Arc の一部のサービスは、データをレプリケートする機能がないため、リモートの共有ストレージを使用するように構成する必要があります。 これらのサービスは、データ コントローラー ポッドのコレクションにあります。

|**サービス**|**永続ボリューム要求**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**コントローラー SQL インスタンス**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**コントローラー API サービス**|`<namespace>/data-controller`|

データ コントローラーがプロビジョニングされるときに、これらの各永続ボリュームに使用されるストレージ クラスは、`azdata arc dc create` コマンドに --storage-class | -sc パラメーターを渡すか、使用する control.json 展開テンプレート ファイルでストレージ クラスを設定することによって指定されます。

初期状態で提供される展開テンプレートでは、ターゲット環境に適した既定のストレージ クラスが指定されていますが、デプロイ時にオーバーライドできます。 デプロイ時にデータ コントローラー ポッドのストレージ クラス構成を変更するには、[デプロイ プロファイルを変更する](create-data-controller.md)ための詳細な手順を参照してください。

--storage-class | -sc パラメーターを使用してストレージ クラスを設定した場合、そのストレージ クラスは、ログとデータの両方のストレージ クラスに使用されます。 展開テンプレート ファイルでストレージ クラスを設定した場合は、ログとデータに異なるストレージ クラスを指定できます。

データ コントローラー ポッドのストレージ クラスを選択する際に考慮する必要がある重要な要素は次のとおりです。

- データの持続性を確保するために、リモートの共有ストレージ クラスを使用する **必要があります**。これにより、ポッドが復旧したときにポッドまたはノードが停止した場合、永続ボリュームに再び接続できるようになります。
- コントローラー SQL インスタンス、メトリック DB、ログ DB に書き込まれるデータは、通常、非常に少ないボリュームであり、待機時間の影響を受けないため、超高速パフォーマンス ストレージは重要ではありません。 ユーザーが Grafana インターフェイスと Kibana インターフェイスを頻繁に使用していて、データベース インスタンスが多数ある場合、ユーザーはより高速なストレージの恩恵を受ける可能性があります。
- データベース インスタンスごとにログとメトリックが収集されるので、必要なストレージ容量は、配置したデータベース インスタンスの数による変数になります。 データは、消去される前に、ログとメトリック DB に 2 週間保持されます。 
- デプロイ後にストレージ クラスを変更することは困難であり、ドキュメント化されておらず、サポートもされていません。 デプロイ時には、ストレージ クラスを正しく選択してください。

> [!NOTE]
> ストレージ クラスが指定されていない場合は、既定のストレージ クラスが使用されます。 既定のストレージ クラスは、Kubernetes クラスターごとに 1 つしか存在できません。 [既定のストレージ クラスを変更する](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)ことができます。

### <a name="database-instance-storage-configuration"></a>データベース インスタンスのストレージ構成

各データベース インスタンスには、データ、ログ、およびバックアップの永続ボリュームがあります。 これらの永続ボリュームのストレージ クラスは、デプロイ時に指定できます。 ストレージ クラスが指定されていない場合は、既定のストレージ クラスが使用されます。

`azdata arc sql mi create` または `azdata arc postgres server create` を使用してインスタンスを作成する場合は、使用してストレージ クラスの設定に使用できるパラメーターが 2 つあります。

> [!NOTE]
> これらのパラメーターの一部は開発中であり、今後のリリースでは `azdata arc sql mi create` および `azdata arc postgres server create` で使用できるようになります。

|パラメーター名、短い名前|使用目的|
|---|---|
|`--storage-class-data`, `-scd`|トランザクション ログ ファイルを含むすべてのデータ ファイルのストレージ クラスを指定するために使用されます。|
|`--storage-class-logs`, `-scl`|すべてのログ ファイルのストレージ クラスを指定するために使用されます。|
|`--storage-class-data-logs`, `-scdl`|データベース トランザクション ログ ファイルのストレージ クラスを指定するために使用されます。 **注意事項: まだ使用できません。**|
|`--storage-class-backups`, `-scb`|すべてのバックアップ ファイルのストレージ クラスを指定するために使用されます。 **注意事項: まだ使用できません。**|

次の表は、データとログの永続ボリュームにマップされる Azure SQL Managed Instance コンテナー内のパスを示しています。

|パラメーター名、短い名前|mssql-miaa コンテナー内のパス|説明|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|mssql インストールおよびその他のシステム プロセスのディレクトリが含まれています。 mssql ディレクトリには、既定のデータ (トランザクション ログを含む)、エラー ログ、バックアップのディレクトリが含まれています。|
|`--storage-class-logs`, `-scl`|/var/log|コンソール出力 (stderr、stdout) やコンテナー内のプロセスのその他のログ情報を格納するディレクトリが含まれています。|

次の表は、データとログの永続ボリュームにマップされる PostgreSQL インスタンス コンテナー内のパスを示しています。

|パラメーター名、短い名前|postgres コンテナー内のパス|説明|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|postgres インストールのデータとログのディレクトリが含まれています。|
|`--storage-class-logs`, `-scl`|/var/log|コンソール出力 (stderr、stdout) やコンテナー内のプロセスのその他のログ情報を格納するディレクトリが含まれています。|

各データベース インスタンスには、データ ファイル、ログ、およびバックアップ用に個別の永続ボリュームがあります。 これは、ボリューム プロビジョナーがストレージをプロビジョニングする方法に応じて、これらの種類のファイルごとに I/O が分離されることを意味します。 各データベース インスタンスには、独自の永続ボリューム要求と永続ボリュームがあります。

特定のデータベース インスタンスに複数のデータベースが存在する場合、すべてのデータベースは同じ永続ボリューム要求、永続ボリューム、およびストレージ クラスを使用します。 すべてのバックアップ (差分ログ バックアップと完全バックアップの両方) で、同じ永続ボリューム要求と永続ボリュームが使用されます。 データベース インスタンス ポッドの永続ボリューム要求を次に示します。

|**インスタンス**|**永続ボリューム要求**|
|---|---|
|**Azure SQL Managed Instance**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Azure Database for PostgreSQL インスタンス**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL HyperScale**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(序数の範囲は 0 ～ W、W はワーカーの数)*|

データベース インスタンス ポッドのストレージ クラスを選択する際に考慮する必要がある重要な要素は次のとおりです。

- データベース インスタンスは、単一のポッド パターンまたは複数のポッド パターンでデプロイできます。 単一のポッド パターンの例として、Azure SQL マネージド インスタンスの開発者インスタンスや汎用価格レベルの Azure SQL マネージド インスタンスがあります。 複数のポッド パターンの例としては、高可用性 Business Critical 価格レベルの Azure SQL マネージド インスタンスが挙げられます (注: 価格レベルは開発中であり、お客様はまだ利用できません)。単一のポッド パターンでデプロイされたデータベース インスタンスは、データの持続性を確保するために、リモートの共有ストレージ クラスを使用する **必要があります**。これにより、ポッドが復旧したときにポッドまたはノードが停止した場合、永続ボリュームに再び接続できるようになります。 これに対し、高可用性 Azure SQL マネージド インスタンスでは Always On 可用性グループを使用して、同期または非同期で 1 つのインスタンスから別のインスタンスにデータをレプリケートします。 特にデータが同期的にレプリケートされる場合、データのコピーは常に複数存在します (通常は 3 つのコピー)。 このため、データ ファイルとログ ファイルにローカル ストレージまたはリモートの共有ストレージ クラスを使用することができます。 ローカル ストレージを使用している場合は、ポッド、ノード、またはストレージのハードウェアに障害が発生した場合でもデータが保持されます。 この柔軟性を考慮して、パフォーマンスを向上させるためにローカル ストレージを使用することもできます。
- データベースのパフォーマンスは、主に特定の記憶装置の I/O スループットの関数です。 データベースの読み取りまたは書き込みが多い場合は、その種類のワークロード向けに設計されたハードウェアを備えたストレージ クラスを選択する必要があります。 たとえば、データベースがほとんど書き込みに使用されている場合は、RAID 0 を使用したローカル ストレージを選択できます。 データベースが主として少量の "ホット データ" の読み取りに使用されるが、コールド データの全体的なストレージ ボリュームが大きい場合は、階層化されたストレージを持つ SAN デバイスを選択できます。 適切なストレージ クラスを選択することは、データベースに使用するストレージの種類を選択することとあまり違いはありません。
- ローカル ストレージ ボリューム プロビジョナーを使用している場合は、ディスク I/O の競合を避けるために、データ、ログ、およびバックアップ用にプロビジョニングされるローカル ボリュームが、基になる別々の記憶装置にそれぞれ配置されているようにします。 OS も、別のディスクにマウントされているボリューム上にある必要があります。 これは基本的に、物理ハードウェア上のデータベース インスタンスの場合と同じガイダンスです。
- 特定のインスタンス上のすべてのデータベースが永続ボリューム要求と永続ボリュームを共有するため、ビジー状態のデータベース インスタンスを同じデータベース インスタンス上に併置しないようにしてください。 可能であれば、I/O の競合を避けるために、ビジー状態のデータベースをそれぞれのデータベース インスタンスに分割します。 さらに、ノード ラベル ターゲット設定を使用してデータベース インスタンスを別々のノードに配置することで、全体的な I/O トラフィックを複数のノードに分散させることができます。 仮想化を使用している場合は、ノード レベルだけでなく、特定の物理ホスト上のすべてのノード VM によって発生する、結合された I/O アクティビティも、I/O トラフィックを分散することを検討してください。

## <a name="estimating-storage-requirements"></a>ストレージ要件の推定
このリリースでは、ステートフル データを含むすべてのポッドは、2 つの永続ボリューム (データ用の永続ボリュームとログ用の永続ボリューム) を使用します。 次の表は、単一のデータ コントローラー、Azure SQL マネージド インスタンス、Azure Database for PostgreSQL インスタンス、および Azure PostgreSQL HyperScale インスタンスに必要な永続ボリュームの数を示しています。

|リソースの種類|ステートフル ポッドの数|必要な永続ボリュームの数|
|---|---|---|
|データ コントローラー|4 (`control`、`controldb`、`logsdb`、`metricsdb`)|4 * 2 = 8|
|Azure SQL Managed Instance|1|2|
|Azure Database for PostgreSQL インスタンス|1| 2|
|Azure PostgreSQL HyperScale|1 + W (W = ワーカーの数)|2 * (1 + W)|

次の表は、サンプルのデプロイに必要な永続ボリュームの合計数を示しています。

|リソースの種類|インスタンスの数|必要な永続ボリュームの数|
|---|---|---|
|データ コントローラー|1|4 * 2 = 8|
|Azure SQL Managed Instance|5|5 * 2 = 10|
|Azure Database for PostgreSQL インスタンス|5| 5 * 2 = 10|
|Azure PostgreSQL HyperScale|2 (ワーカーの数 = インスタンスあたり 4)|2 * 2 * (1 + 4) = 20|
|***永続ボリュームの合計数***||8 + 10 + 10 + 20 = 48|

この計算を使用して、ストレージのプロビジョナーまたは環境に基づいて Kubernetes クラスターのストレージを計画することができます。 たとえば、5 つのノードを持つ Kubernetes クラスターにローカル ストレージ プロビジョナーが使用されている場合、上記のサンプル デプロイでは、各ノードに少なくとも 10 個の永続ボリュームのストレージが必要です。 同様に、5 つのノードを持つ Azure Kubernetes Service (AKS) クラスターをプロビジョニングする場合は、10 個のデータ ディスクを接続できるように、ノード プールに適した VM サイズを選択することが重要です。 AKS ノードのストレージのニーズに応じてノードのサイズを変更する方法の詳細については、[こちら](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)を参照してください。

## <a name="choosing-the-right-storage-class"></a>適切なストレージ クラスの選択

### <a name="on-premises-and-edge-sites"></a>オンプレミスとエッジ サイト

Microsoft とその OEM、OS、Kubernetes パートナーは、Azure Arc データ サービスの認定プログラムに取り組んでいます。 このプログラムは、お客様に認定テスト ツールキットと同等のテスト結果を提供します。 テストでは、機能の互換性、ストレス テストの結果、パフォーマンスとスケーラビリティが評価されます。 これらの各テスト結果は、使用されている OS、使用されている Kubernetes ディストリビューション、使用されているハードウェア、使用されている CSI アドオン、および使用されているストレージ クラスを示します。 これにより、お客様は要件に合わせて最適なストレージ クラス、OS、Kubernetes ディストリビューション、およびハードウェアを選択できます。 このプログラムと初期テスト結果の詳細は、Azure Arc データ サービスの一般提供が近づいたときに提供されます。

#### <a name="public-cloud-managed-kubernetes-services"></a>パブリック クラウド、マネージド Kubernetes サービス

パブリック クラウドベースのマネージド Kubernetes サービスについては、次の推奨事項を使用できます。

|パブリック クラウド サービス|推奨|
|---|---|
|**Azure Kubernetes Service (AKS)**|Azure Kubernetes Service (AKS) には、2 種類のストレージ (Azure Files と Azure Managed Disks) があります。 それぞれの種類のストレージには、Standard (HDD) と Premium (SSD) の 2 つの価格/パフォーマンス レベルがあります。 したがって、AKS では、`azurefile` (Azure Files Standard レベル)、`azurefile-premium` (Azure Files Premium レベル)、`default` (Azure ディスク Standard レベル)、および `managed-premium` (Azure ディスク Premium レベル) の 4 つのストレージ クラスが提供されます。 既定のストレージ クラスは `default` (Azure ディスク Standard レベル) です。 種類とレベルの間には相当な **[価格の違い](https://azure.microsoft.com/en-us/pricing/details/storage/)** があり、決定時に考慮する必要があります。 高パフォーマンスの要件を持つ運用環境のワークロードの場合は、すべてのストレージ クラスに `managed-premium` を使用することをお勧めします。 開発/テスト ワークロード、概念実証などでコストを考慮する場合は、`azurefile` が最もコストのかからないオプションです。 4 つのオプションはすべて、Azure 内のネットワークに接続された記憶装置であるため、リモートの共有ストレージを必要とする状況に使用できます。 AKS Storage の詳細については、[こちら](../../aks/concepts-storage.md)をご覧ください。|
|**AWS Elastic Kubernetes Service (EKS)**| Amazon の Elastic Kubernetes Service には、[EBS CSI ストレージ ドライバー](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)に基づく 1 つのプライマリ ストレージ クラスがあります。 運用環境のワークロードにはこれをお勧めします。 EKS クラスターに追加できる新しいストレージ ドライバー ([EFS CSI ストレージ ドライバー](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html)) もありますが、現在はベータ段階にあり、変更される可能性があります。 AWS はこのストレージ ドライバーが運用環境でサポートされると発表していますが、まだベータ版であり、変更される可能性があるため、使用しないことをお勧めします。 既定は EBS ストレージ クラスであり、`gp2` と呼ばれます。 EKS Storage の詳細については、[こちら](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)をご覧ください。|
|**Google Kubernetes Engine (GKE)**|Google Kubernetes Engine (GKE) には、[GCE 永続ディスク](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk)に使用される `standard` と呼ばれるストレージ クラスが 1 つだけあります。 唯一のものであり、既定値でもあります。 GKE 用の[ローカルの静的なボリューム プロビジョナー](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner)があり、直接接続された SSD で使用できますが、Google によって保守またはサポートされていないため、使用しないことをお勧めします。 GKE ストレージの詳細については、[こちら](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes)をご覧ください。
