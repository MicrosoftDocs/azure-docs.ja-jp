---
title: (非推奨) Azure Container Service の DC/OS エージェント プール
description: パブリックおよびプライベートのエージェント プールが Azure Container Service の DC/OSクラスターで機能する仕組み
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 03cacda1aa405cb2d0ded579c8ddb5f6011ce3bb
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113789"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(非推奨) Azure Container Service の DC/OS エージェント プール

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Azure Container Service の DC/OS クラスターでは、エージェント ノードを 2 つのプール (パブリック プールとプライベート プール) に格納します。 アプリケーションはどちらのプールにもデプロイできますが、コンテナー サービス内のコンピューター間のアクセスに影響があります。 コンピューターはインターネットに公開することも (パブリック)、内部だけで維持することも (プライベート) できます。 この記事では、パブリック プールとプライベート プールがある理由の概要について説明します。


* **プライベート エージェント**:プライベート エージェント ノードは、ルーティング不可能なネットワークを介して実行されます。 このネットワークには、管理者ゾーンまたはパブリック ゾーン エッジ ルーターを介してのみアクセスできます。 既定では、DC/OS は、プライベート エージェント ノードでアプリを起動します。 

* **パブリック エージェント**:パブリック エージェント ノードは、パブリックにアクセスできるネットワークを介して DC/OS アプリとサービスを実行します。 

DC/OS ネットワークのセキュリティの詳細については、[DC/OS のドキュメント](https://docs.mesosphere.com/)を参照してください。

## <a name="deploy-agent-pools"></a>エージェント プールのデプロイ

Azure Container Service の DC/OS エージェント プールは、次のように作成されます。

* **プライベート プール**には、[DC/OS クラスターのデプロイ](container-service-deployment.md)を実行するときに指定した数のエージェントノードが格納されます。 

* **パブリック プール**には、最初は、所定の数のエージェント ノードが格納されます。 このプールは、DC/OS クラスターがプロビジョニングされるときに自動的に追加されます。

プライベート プールとパブリック プールは、Azure 仮想マシンのスケール セットです。 これらのプールは、デプロイ後にサイズを変更できます。

## <a name="use-agent-pools"></a>エージェント プールの使用
既定では、 **Marathon** は新しいアプリケーションを *プライベート* エージェント ノードにデプロイします。 アプリケーションの作成中に、アプリケーションを *パブリック* ノードに明示的にデプロイする必要があります。 **[Optional (オプション)]** タブを選択し、**[Accepted Resource Roles (承認されたリソース ロール)]** の値として「**slave_public**」と入力します。 このプロセスについては、[こちら](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)と [ DC\OS](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) のドキュメントに記載されています。

## <a name="next-steps"></a>次の手順
* [DC/OS コンテナーの管理](container-service-mesos-marathon-ui.md)で詳細を確認します。

* DC/OS コンテナーへのパブリック アクセスが可能になるように Azure によって提供される[ファイアウォールを開く](container-service-enable-public-access.md)方法を確認します。

