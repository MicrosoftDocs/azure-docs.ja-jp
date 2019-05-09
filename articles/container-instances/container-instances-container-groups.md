---
title: Azure Container Instances のコンテナー グループ
description: Azure Container Instances の複数コンテナー グループのしくみを理解します
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/20/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f4bbea8acd447a731cf5c56f9876baf9183735ea
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784995"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances のコンテナー グループ

Azure Container Instances の最上位のリソースは、*コンテナー グループ*です。 この記事では、コンテナー グループとは何か、およびそれらによってどのような種類のシナリオが有効になるかを説明します。

## <a name="how-a-container-group-works"></a>コンテナー グループのしくみ

コンテナー グループは、同じホスト コンピューター上にスケジュール設定されるコンテナーのコレクションです。 コンテナー グループ内のコンテナーでは、ライフサイクル、リソース、ローカル ネットワーク、ストレージ ボリュームを共有します。 これは、[Kubernetes][kubernetes-pod] における*ポッド*の概念に似ています。

次の図は、複数のコンテナーを含むコンテナー グループの例を示しています。

![コンテナー グループ ダイアグラム][container-groups-example]

この例のコンテナー グループは:

* 単一のホスト コンピューター上にスケジュール設定されます。
* DNS 名ラベルが割り当てられます。
* 単一のパブリック IP アドレスを公開し、1 つの公開ポートを持ちます。
* 次の 2 つのコンテナーから構成されます。 片方のコンテナーはポート 80 でリッスンし、他方のポートはポート 5000 でリッスンします。
* ボリューム マウントとして 2 つの Azure ファイル共有が含まれています。各コンテナーは共有のいずれかをローカルにマウントします。

> [!NOTE]
> 現在、複数コンテナー グループでサポートされているのは、Linux コンテナーのみです。 Windows コンテナーの場合、Azure Container Instances では、1 つのインスタンスのデプロイのみをサポートします。 Microsoft ではすべての機能を Windows コンテナーにも取り入れるように取り組んでいますが、現在のプラットフォームの違いは、サービスの[概要](container-instances-overview.md#linux-and-windows-containers)に関するページで確認できます。

## <a name="deployment"></a>Deployment

複数コンテナー グループをデプロイするには、一般的な方法が 2 つあります: [Resource Manager テンプレート][resource-manager template]または [YAML ファイル][yaml-file]を使用します。 コンテナー インスタンスのデプロイ時に追加の Azure サービス リソース ([Azure Files 共有][azure-files]など) をデプロイする必要がある場合は、Resource Manager テンプレートをお勧めします。 YAML フォーマットは簡潔であるため、デプロイにコンテナー インスタンスのみが含まれている場合は、YAML ファイルをお勧めします。

コンテナー グループの構成を保持するために、Azure CLI の [az container export][az-container-export] コマンドを使用して、YAML ファイルに構成をエクスポートできます。 エクスポートによって、バージョン コントロールでコンテナー グループの構成を「コードとしての構成」として格納できます。 または、エクスポートしたファイルを YAML で新しい構成を作成する際の出発点として使用します。

## <a name="resource-allocation"></a>リソースの割り当て

Azure Container Instances では、グループにインスタンスの[リソース要求][resource-requests]を追加することで、CPU、メモリ、必要に応じて [GPU][gpus] (プレビュー) などのリソースをコンテナー グループに割り当てます。 たとえば、CPU リソースを例に挙げると、それぞれが 1 CPU を要求する 2 つのインスタンスを持つ 1 つのコンテナー グループを作成すると、コンテナー グループに 2 CPU が割り当てられます。

コンテナー グループに利用できる最大リソースは、デプロイに使用した [Azure リージョン][region-availability]によって異なります。

### <a name="container-resource-requests-and-limits"></a>コンテナー リソースの要求と制限

* 既定では、グループ内のコンテナー インスタンスで、グループの要求されたリソースを共有します。 それぞれが 1 CPU を要求する 2 つのインスタンスを含むグループでは、全体としてグループで 2 CPU にアクセスできます。 各インスタンスでは最大 2 CPU まで使用することができ、インスタンスは実行中に CPU リソースを取得するために競合する可能性があります。

* グループ内の 1 インスタンスによるリソース使用率を制限するには、必要に応じてインスタンスに[リソース制限][resource-limits]を設定します。 1 CPU を要求するインスタンスが 2 つあるグループでは、コンテナーのどちらかがもう一方よりも多く CPU を実行することを要求する可能性があります。

  このシナリオでは、1 つのインスタンスに対してリソース制限を 0.5 CPU に設定し、2 つ目のインスタンスに 2 CPU の制限を設定することができます。 この構成では、1 つ目のコンテナーのリソース使用率を 0.5 CPU に制限し、利用可能な場合は、2 つ目のコンテナーが最大 2 CPU をすべて使用することを許可します。

詳細については、コンテナー グループ REST API の [ResourceRequirements][resource-requirements] プロパティを参照してください。

### <a name="minimum-and-maximum-allocation"></a>最小値と最大値の割り当て

* **最小値**の 1 CPU と 1 GB メモリをコンテナー グループに割り当てます。 グループ内の個々のコンテナー インスタンスは、1 CPU と 1 GB メモリ未満でプロビジョニングできます。 

* コンテナー グループの**最大**リソースについては、デプロイ リージョンの Azure Container Instances の [リソースの可用性][aci-region-availability] に関するページを参照してください。

## <a name="networking"></a>ネットワーク

コンテナー グループは、IP アドレスと、その IP アドレス上のポートの名前空間を共有します。 外部クライアントがグループ内のコンテナーにアクセスできるようにするには、IP アドレスのポートをコンテナーから公開する必要があります。 グループ内のコンテナーがポートの名前空間を共有するため、ポートのマッピングはサポートされません。 グループ内のコンテナーは、ポートの localhost 経由で相互にアクセスできます。これは、これらのポートがグループの IP アドレスで外部的に公開されていない場合でも同じです。

必要に応じて、[Azure 仮想ネットワーク][virtual-network] (プレビュー) にコンテナー グループをデプロイして、コンテナーで仮想ネットワーク内の他のリソースと安全に通信することを許可します。

## <a name="storage"></a>Storage

コンテナー グループにマウントする外部ボリュームを指定できます。 これらのボリュームは、グループの個別のコンテナーの特定のパスにマップできます。

## <a name="common-scenarios"></a>一般的なシナリオ

複数コンテナー グループは、1 つの機能タスクを少数のコンテナー イメージに分割する場合に便利です。 これらのイメージは複数のチームによって配布可能であり、異なるリソース要件を持っています。

次のような使用例が考えられます。

* Web アプリケーションにサービスを提供するコンテナーとソース管理から最新のコンテンツをプルするコンテナー。
* アプリケーション コンテナーとログ記録コンテナー。 ログ記録コンテナーは、メイン アプリケーションによって出力されるログとメトリックを収集し、長期保存される記憶域に書き込みます。
* アプリケーション コンテナーと監視コンテナー。 監視コンテナーは、要求を定期的にアプリケーションに送信して、アプリケーションが実行中であり、正常に応答していることを確認します。そうでない場合はアラートを発生させます。
* フロントエンド コンテナーとバックエンド コンテナー。 フロントエンドは、データを取得するためにサービスを実行しているバックエンドと共に、Web アプリケーションを提供する可能性があります。 

## <a name="next-steps"></a>次の手順

Azure Resource Manager テンプレートを使用してマルチコンテナーのコンテナー グループをデプロイする方法を確認します。

> [!div class="nextstepaction"]
> [コンテナー グループのデプロイ][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/pod/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-vnet.md
[gpus]: container-instances-gpu.md
[az-container-export]: /cli/azure/container#az-container-export
