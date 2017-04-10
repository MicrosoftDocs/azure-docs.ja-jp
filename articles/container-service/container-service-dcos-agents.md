---
title: "Azure Container Service の DC/OS エージェント プール | Microsoft Docs"
description: "パブリックおよびプライベートのエージェント プールが Azure Container Service の DC/OSクラスターで機能する仕組み"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker、コンテナー、マイクロ サービス、Mesos、Azure"
ms.assetid: 36d657c9-8845-4bf7-bed2-088323b67406
ms.service: container-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: be10411e735c5b96702ee8dcb893f1a48e479f6f
ms.openlocfilehash: cffc65e25ae8eab90a9879a0030b78b3b77890b7
ms.lasthandoff: 01/06/2017


---
# <a name="dcos-agent-pools-for-azure-container-service"></a>Azure Container Service の DC/OS エージェント プール
Azure Container Service の DC/OS クラスターでは、エージェント ノードを&2; つのプール (パブリック プールとプライベート プール) に格納します。 アプリケーションはどちらのプールにもデプロイできますが、コンテナー サービス内のコンピューター間のアクセスに影響があります。 コンピューターはインターネットに公開することも (パブリック)、内部だけで維持することも (プライベート) できます。 この記事では、パブリック プールとプライベート プールがある理由の概要について説明します。


* **プライベート Agents**: プライベート エージェント ノードは、ルーティング不可能なネットワークを介して実行されます。 このネットワークには、管理者ゾーンまたはパブリック ゾーン エッジ ルーターを介してのみアクセスできます。 既定では、DC/OS は、プライベート エージェント ノードでアプリを起動します。 

* **パブリック Agents**: パブリック エージェント ノードは、パブリックにアクセスできるネットワークを介して DC/OS アプリとサービスを実行します。 

DC/OS ネットワークのセキュリティの詳細については、[DC/OS のドキュメント](https://dcos.io/docs/1.7/administration/securing-your-cluster/)を参照してください。

## <a name="deploy-agent-pools"></a>エージェント プールのデプロイ

Azure Container Service の DC/OS エージェント プールは、次のように作成されます。

* **プライベート プール**には、[DC/OS クラスターのデプロイ](container-service-deployment.md)を実行するときに指定した数のエージェントノードが格納されます。 

* **パブリック プール**には、最初は、所定の数のエージェント ノードが格納されます。 このプールは、DC/OS クラスターがプロビジョニングされるときに自動的に追加されます。

プライベート プールとパブリック プールは、Azure 仮想マシンのスケール セットです。 これらのプールは、デプロイ後にサイズを変更できます。

## <a name="use-agent-pools"></a>エージェント プールの使用
既定では、 **Marathon** は新しいアプリケーションを *プライベート* エージェント ノードにデプロイします。 アプリケーションの作成中に、アプリケーションを *パブリック* ノードに明示的にデプロイする必要があります。 **[Optional (オプション)]** タブを選択し、**[Accepted Resource Roles (承認されたリソース ロール)]** の値として「**slave_public**」と入力します。 このプロセスについては、[こちら](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container)と [ DC\OS](https://dcos.io/docs/1.7/administration/installing/custom/create-public-agent/) のドキュメントに記載されています。

## <a name="next-steps"></a>次のステップ
* [DC/OS コンテナーの管理](container-service-mesos-marathon-ui.md)で詳細を確認します。

* DC/OS コンテナーへのパブリック アクセスが可能になるように Azure によって提供される[ファイアウォールを開く](container-service-enable-public-access.md)方法を確認します。


