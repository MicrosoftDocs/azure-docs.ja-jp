---
title: (非推奨) Marathon UI を使用した Azure DC/OS クラスターの管理
description: Marathon Web UI を使用して Azure コンテナー サービスのクラスター サービスにコンテナーをデプロイします。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 16c16c0217a796ffbb57e10430f90cb4a7660ac6
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317614"
---
# <a name="deprecated-manage-an-azure-container-service-dcos-cluster-through-the-marathon-web-ui"></a>(非推奨) Marathon Web UI による Azure Container Service DC/OS クラスターの管理

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

DC/OS はクラスター化されたワークロードをデプロイし、スケールするための環境を提供すると共に、基礎となるハードウェアを抽象化します。 DC/OS に加え、コンピューティング ワークロードのスケジュールと実行を管理するフレームワークもあります。

一般的な各種ワークロードに対応したフレームワークがありますが、このドキュメントでは、Marathon を使ってコンテナーのデプロイを開始する方法について説明します。 


## <a name="prerequisites"></a>前提条件
これらの例を見ていく前に、Azure コンテナー サービスで構成された DC/OS クラスターが必要です。 また、このクラスターへのリモート接続も必要です。 これらの項目の詳細については、次の記事を参照してください。

* [Azure コンテナー サービス クラスターのデプロイ](container-service-deployment.md)
* [Azure コンテナー サービス クラスターに接続する](../container-service-connect.md)

> [!NOTE]
> この記事では、ローカル ポート 80 を介して DC/OS クラスターにトンネリングすることを前提とします。
>

## <a name="explore-the-dcos-ui"></a>DC/OS UI を操作する
Secure Shell (SSH) トンネルを[確立](../container-service-connect.md)したら、http:\//localhost/ に移動します。 DC/OS Web UI が読み込まれ、使用リソース、アクティブなエージェント、実行中のサービスなど、クラスターに関する情報が表示されます。

![DC/OS UI](./media/container-service-mesos-marathon-ui/dcos2.png)

## <a name="explore-the-marathon-ui"></a>Marathon UI について知る
Marathon UI を表示するには、http:\//localhost/marathon に移動します。 この画面から、Azure コンテナー サービス DC/OS クラスターで新しいコンテナーやその他のアプリケーションを開始できます。 コンテナーとアプリケーションの実行に関する情報も確認できます。  

![Marathon UI](./media/container-service-mesos-marathon-ui/dcos3.png)

## <a name="deploy-a-docker-formatted-container"></a>Docker 形式のコンテナーのデプロイ
Marathon を使用して新しいコンテナーをデプロイするには、**[Create Application (アプリケーションの作成)]** をクリックし、次の情報をフォームのタブに入力します。

| フィールド | 値 |
| --- | --- |
| ID |nginx |
| メモリ | 32 |
| Image |nginx |
| ネットワーク |ブリッジ |
| ホスト ポート |80 |
| プロトコル |TCP |

![New Application UI--General](./media/container-service-mesos-marathon-ui/dcos4.png)

![New Application UI--Docker Container](./media/container-service-mesos-marathon-ui/dcos5.png)

![New Application UI--Ports and Service Discovery](./media/container-service-mesos-marathon-ui/dcos6.png)

コンテナー ポートをエージェント上のポートに対して静的にマップするには、JSON モードを使用する必要があります。 トグル スイッチを使用して、New Application (新しいアプリケーション) ウィザードを **[JSON Mode (JSON モード)]** に切り替えてください。 その後、アプリケーション定義の `portMappings` セクションに次の設定を入力します。 この例では、コンテナーのポート 80 を DC/OS エージェントのポート 80 にバインドしています。 JSON モードで変更が済んだらウィザードを元のモードに切り替えてかまいません。

```none
"hostPort": 80,
```

![New Application UI--port 80 example](./media/container-service-mesos-marathon-ui/dcos13.png)

正常性チェックを有効にする場合は、**[Health Checks (正常性チェック)]** タブでパスを設定します。

![[New Application (新しいアプリケーション)] の UI - [Health Checks (正常性チェック)]](./media/container-service-mesos-marathon-ui/dcos_healthcheck.png)

DC/OS クラスターは、プライベート エージェントおよびパブリック エージェントのセットと共にデプロイされます。 クラスターがインターネットからアプリケーションにアクセスできるようにするには、アプリケーションをパブリック エージェントにデプロイする必要があります。 そのためには、新しいアプリケーション ウィザードの **[オプション]** タブを選択し、**[Accepted Resource Roles (承認されたリソース ロール)]** に「**slave_public**」と入力します。

その後、**[Create Application (アプリケーションの作成)]** をクリックします。

![New Application UI--public agent setting](./media/container-service-mesos-marathon-ui/dcos14.png)

Marathon メイン ページに戻ると、コンテナーのデプロイの状態を確認できます。 最初は、状態が "**Deploying (デプロイ中)**" と表示されます。 デプロイが成功すると、その状態は "**Running (実行中)**" に変わります。

![Marathon main page UI--container deployment status](./media/container-service-mesos-marathon-ui/dcos7.png)

DC/OS の Web UI (http:\//localhost/) に戻ると、タスク (この場合は Docker 形式のコンテナー) が DC/OS クラスターで実行されていることがわかります。

![DC/OS web UI--task running on the cluster](./media/container-service-mesos-marathon-ui/dcos8.png)

タスクが実行されているクラスター ノードを確認するには、**[Nodes (ノード)]** タブをクリックします。

![DC/OS web UI--task cluster node](./media/container-service-mesos-marathon-ui/dcos9.png)

## <a name="reach-the-container"></a>コンテナーへの到達

この例では、アプリケーションがパブリックのエージェント ノードで実行されています。 インターネットからアプリケーションに到達するには、クラスターのエージェント FQDN を参照します: `http://[DNSPREFIX]agents.[REGION].cloudapp.azure.com`。ここで、

* **DNSPREFIX** は、クラスターのデプロイ時に指定した DNS 接頭辞です。
* **REGION** は、リソース グループが置かれているリージョンです。

    ![インターネットから nginx へ](./media/container-service-mesos-marathon-ui/nginx.png)


## <a name="next-steps"></a>次の手順
* [DC/OS と Marathon API の使用](container-service-mesos-marathon-rest.md)

* Mesos と共に Azure Container Service を使用する方法の詳細

    > [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON203/player]
    > 
