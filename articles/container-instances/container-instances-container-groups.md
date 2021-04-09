---
title: コンテナー グループの概要
description: Azure Container Instances のコンテナー グループ、ライフサイクルとリソース (CPU、ストレージ、ネットワークなど) を共有するインスタンスのコレクションについて説明します
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 72ebe6186da179bc5a1effddcc14327455eb7557
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89612415"
---
# <a name="container-groups-in-azure-container-instances"></a>Azure Container Instances のコンテナー グループ

Azure Container Instances の最上位のリソースは、*コンテナー グループ* です。 この記事では、コンテナー グループとは何か、およびそれらによってどのような種類のシナリオが有効になるかを説明します。

## <a name="what-is-a-container-group"></a>コンテナー グループとは

コンテナー グループは、同じホスト コンピューター上にスケジュール設定されるコンテナーのコレクションです。 コンテナー グループ内のコンテナーでは、ライフサイクル、リソース、ローカル ネットワーク、ストレージ ボリュームを共有します。 これは、[Kubernetes][kubernetes-pod] における *ポッド* の概念に似ています。

次の図は、複数のコンテナーを含むコンテナー グループの例を示しています。

![コンテナー グループ ダイアグラム][container-groups-example]

この例のコンテナー グループは:

* 単一のホスト コンピューター上にスケジュール設定されます。
* DNS 名ラベルが割り当てられます。
* 単一のパブリック IP アドレスを公開し、1 つの公開ポートを持ちます。
* 次の 2 つのコンテナーから構成されます。 片方のコンテナーはポート 80 でリッスンし、他方のポートはポート 5000 でリッスンします。
* ボリューム マウントとして 2 つの Azure ファイル共有が含まれています。各コンテナーは共有のいずれかをローカルにマウントします。

> [!NOTE]
> 現在、複数コンテナー グループでサポートされているのは、Linux コンテナーのみです。 Windows コンテナーの場合、Azure Container Instances では、1 つのコンテナー インスタンスのデプロイのみをサポートします。 Microsoft ではすべての機能を Windows コンテナーにも取り入れるように取り組んでいますが、現在のプラットフォームの違いは、サービスの[概要](container-instances-overview.md#linux-and-windows-containers)に関するページで確認できます。

## <a name="deployment"></a>デプロイ

複数コンテナー グループをデプロイするには、一般的な方法が 2 つあります: [Resource Manager テンプレート][resource-manager template]または [YAML ファイル][yaml-file]を使用します。 コンテナー インスタンスのデプロイ時に追加の Azure サービス リソース ([Azure Files 共有][azure-files]など) をデプロイする必要がある場合は、Resource Manager テンプレートをお勧めします。 YAML フォーマットは簡潔であるため、デプロイにコンテナー インスタンスのみが含まれている場合は、YAML ファイルをお勧めします。 設定できるプロパティの詳細については、「[Resource Manager テンプレート リファレンス](/azure/templates/microsoft.containerinstance/containergroups)」または「[YAML リファレンス](container-instances-reference-yaml.md)」のドキュメントを参照してください。

コンテナー グループの構成を保持するために、Azure CLI の [az container export][az-container-export] コマンドを使用して、YAML ファイルに構成をエクスポートできます。 エクスポートによって、バージョン コントロールでコンテナー グループの構成を「コードとしての構成」として格納できます。 または、エクスポートしたファイルを YAML で新しい構成を作成する際の出発点として使用します。



## <a name="resource-allocation"></a>リソース割り当て

Azure Container Instances では、グループにインスタンスの[リソース要求][resource-requests]を追加することで、CPU、メモリ、必要に応じて [GPU][gpus] (プレビュー) などのリソースをマルチコンテナー グループに割り当てます。 たとえば、CPU リソースを例に挙げると、それぞれが 1 CPU を要求する 2 つのコンテナー インスタンスを持つ 1 つのコンテナー グループを作成すると、コンテナー グループに 2 CPU が割り当てられます。

### <a name="resource-usage-by-container-instances"></a>コンテナー インスタンス別のリソース使用量

グループ内の各コンテナー インスタンスには、そのリソース要求で指定されたリソースが割り当てられます。 ただし、省略可能な[リソース制限][resource-limits]プロパティを構成している場合、グループ内のコンテナー インスタンスに使用される最大リソースが異なる場合があります。 コンテナー インスタンスのリソース制限は、必須の[リソース要求][resource-requests]プロパティ以上にする必要があります。

* リソース制限を指定しない場合、コンテナー インスタンスの最大リソース使用量はそのリソース要求と同じになります。

* コンテナー インスタンスの制限を指定した場合、インスタンスの最大使用量は要求よりも大きくなり、設定した上限に達する可能性があります。 それに応じて、グループ内の他のコンテナー インスタンスによるリソースの使用量が減少する可能性があります。 コンテナー インスタンスに設定できるリソース制限の最大値は、グループに割り当てられているリソース合計になります。
    
たとえば、あるグループに属する 2 つのコンテナー インスタンスがそれぞれ 1 CPU を要求するとき、あるコンテナーで実行されるワークロードが他のコンテナーのそれと比べ、多くの CPU 実行を要求することがあります。

このシナリオでは、コンテナー インスタンスに対して最大 2 つの CPU のリソース制限を設定できます。 この構成にすると、コンテナー インスタンスには最大 2 つの CPU (使用可能な場合) を使用できます。

> [!NOTE]
> コンテナー グループの少量のリソースは、サービスの基になるインフラストラクチャによって使用されます。 コンテナーでは、グループに割り当てられているほとんどのリソースにアクセスできますが、すべてのリソースにアクセスできるわけではありません。 このため、グループ内のコンテナーのリソースを要求するときは、小さいリソース バッファーを計画してください。

### <a name="minimum-and-maximum-allocation"></a>最小値と最大値の割り当て

* **最小値** の 1 CPU と 1 GB メモリをコンテナー グループに割り当てます。 グループ内の個々のコンテナー インスタンスは、1 CPU と 1 GB メモリ未満でプロビジョニングできます。 

* コンテナー グループ内の **最大** リソースについては、デプロイ リージョンでの Azure Container Instances に対する [リソースの可用性][region-availability]に関するページを参照してください。

## <a name="networking"></a>ネットワーク

コンテナー グループは、外部に接続する IP アドレス、その IP アドレス上の 1 つ以上のポート、完全修飾ドメイン名 (FQDN) を持つ DNS ラベルを共有することができます。 外部クライアントがグループ内のコンテナーにアクセスできるようにするには、IP アドレスのポートをコンテナーから公開する必要があります。 コンテナー グループの IP アドレスと FQDN は、コンテナー グループを削除すると解放されます。 

コンテナー グループ内では、コンテナー インスタンスは任意のポートの localhost を通じて相互にアクセスできます。それらのポートがグループの IP アドレスまたはコンテナーで外部に公開されていなくてもかまいません。

必要に応じて、[Azure 仮想ネットワーク][virtual-network]にコンテナー グループをデプロイして、コンテナーで仮想ネットワーク内の他のリソースと安全に通信できるようにします。

## <a name="storage"></a>ストレージ

コンテナー グループにマウントする外部ボリュームを指定できます。 サポートされるボリュームは次のとおりです。
* [Azure ファイル共有][azure-files]
* [シークレット][secret]
* [空のディレクトリ][empty-directory]
* [複製された Git リポジトリ][volume-gitrepo]

これらのボリュームは、グループの個別のコンテナーの特定のパスにマップできます。 

## <a name="common-scenarios"></a>一般的なシナリオ

複数コンテナー グループは、1 つの機能タスクを少数のコンテナー イメージに分割する場合に便利です。 これらのイメージは複数のチームによって配布可能であり、異なるリソース要件を持っています。

次のような使用例が考えられます。

* Web アプリケーションにサービスを提供するコンテナーとソース管理から最新のコンテンツをプルするコンテナー。
* アプリケーション コンテナーとログ記録コンテナー。 ログ記録コンテナーは、メイン アプリケーションによって出力されるログとメトリックを収集し、長期保存される記憶域に書き込みます。
* アプリケーション コンテナーと監視コンテナー。 監視コンテナーは、要求を定期的にアプリケーションに送信して、アプリケーションが実行中であり、正常に応答していることを確認します。そうでない場合はアラートを発生させます。
* フロントエンド コンテナーとバックエンド コンテナー。 フロントエンドは、データを取得するためにサービスを実行しているバックエンドと共に、Web アプリケーションを提供する可能性があります。 

## <a name="next-steps"></a>次のステップ

Azure Resource Manager テンプレートを使用してマルチコンテナーのコンテナー グループをデプロイする方法を確認します。

> [!div class="nextstepaction"]
> [コンテナー グループのデプロイ][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-virtual-network-concepts.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
