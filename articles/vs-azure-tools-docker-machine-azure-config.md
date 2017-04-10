---
title: "Docker マシンを利用し、Azure で Docker ホストを作成する | Microsoft Docs"
description: "Docker マシンを利用し、Azure で Docker ホストを作成する方法について説明します。"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 7a3ff6e1-fa93-4a62-b524-ab182d2fea08
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a5b845a93f318b991e14705f0fadea3acd802ced
ms.lasthandoff: 03/21/2017


---
# <a name="create-docker-hosts-in-azure-with-docker-machine"></a>Docker マシンを利用し、Azure で Docker ホストを作成する
[Docker](https://www.docker.com/) コンテナーを実行するには、Docker デーモンを実行しているホスト VM が必要です。
このトピックでは、 [docker-machine](https://docs.docker.com/machine/) コマンドを使用して、Docker デーモンで構成され、Azure で実行される新しい Linux VM を作成する方法について説明します。 

**注:** 

* *この記事は docker-machine バージョン 0.9.0-rc2 以上に依存します*
* *Windows コンテナーは今後の docker-machine でサポートされる予定です*

## <a name="create-vms-with-docker-machine"></a>Docker マシンで VM を作成する
`azure` ドライバーを使用する `docker-machine create` コマンドを使用して、Azure に Docker ホスト VM を作成します。 

Azure ドライバーにはサブスクリプション ID が必要になります。 [Azure CLI](cli-install-nodejs.md) または [Azure Portal](https://portal.azure.com) を使用して、Azure サブスクリプションを取得することができます。 

**Azure ポータルの使用**

* 左側のナビゲーション ページからサブスクリプションを選択し、サブスクリプション ID をコピーします。

**Azure CLI の使用**

* 「 ```azure account list``` 」と入力し、サブスクリプション ID をコピーします。

「 `docker-machine create --driver azure` 」と入力し、オプションとその既定値を表示します。
[Docker の Azure ドライバー ドキュメント](https://docs.docker.com/machine/drivers/azure/) で詳細を確認することもできます。 

次の例では[既定値](https://github.com/docker/machine/blob/master/drivers/azure/azure.go#L22)を使用していますが、必要に応じて以下の値を設定します。 

* パブリック IP に関連付けられている名前の azure-dns と生成される証明書。  これにより、VM は動的 IP を安全に停止およびリリースすることができ、新しい IP で VM が再起動された後に再接続することが可能になります。  名前のプレフィックスは、そのリージョン UNIQUE_DNSNAME_PREFIX.westus.cloudapp.azure.com に対して一意である必要があります。
* 発信インターネット アクセス用に VM 上で開かれているポート 80
* より高速な Premium Storage を利用するための VM のサイズ
* VM ディスクに使用する Premium Storage

```
docker-machine create -d azure --azure-subscription-id <Your AZURE_SUBSCRIPTION_ID> --azure-dns <Your UNIQUE_DNSNAME_PREFIX> --azure-open-port 80 --azure-size Standard_DS1_v2 --azure-storage-type "Premium_LRS" mydockerhost 
```

## <a name="choose-a-docker-host-with-docker-machine"></a>docker-machine で Docker ホストを選択する
ホストの docker-machine に入力が行われていると、docker コマンドを実行する際に既定のホストを設定することができます。

## <a name="using-powershell"></a>PowerShell の使用
```powershell
docker-machine env MyDockerHost | Invoke-Expression 
```

## <a name="using-bash"></a>Bash の使用
```bash
eval $(docker-machine env MyDockerHost)
```

指定したホストに対して docker コマンドを実行できます

```
docker ps
docker info
```

## <a name="run-a-container"></a>コンテナーを実行する
ホストを構成すると、単純な Web サーバーを実行して、ホストが正しく構成されているかどうかをテストすることができます。
ここでは標準の nginx イメージを使用し、ポート 80 で待機することと、ホスト VM の再起動時にコンテナーも再起動することを指定します (`--restart=always`)。 

```bash
docker run -d -p 80:80 --restart=always nginx
```

出力は次のようになります。

```
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
efd26ecc9548: Pull complete
a3ed95caeb02: Pull complete
83f52fbfa5f8: Pull complete
fa664caa1402: Pull complete
Digest: sha256:12127e07a75bda1022fbd4ea231f5527a1899aad4679e3940482db3b57383b1d
Status: Downloaded newer image for nginx:latest
25942c35d86fe43c688d0c03ad478f14cc9c16913b0e1c2971cb32eb4d0ab721
```

## <a name="test-the-container"></a>コンテナーをテストする
`docker ps` を利用し、実行中のコンテナーを調べます。

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                         NAMES
d5b78f27b335        nginx               "nginx -g 'daemon off"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 443/tcp   goofy_mahavira
```

実行中のコンテナーを確認します。「`docker-machine ip <VM name>`」と入力すると、ブラウザーに入力する IP アドレスが見つかります。

```
PS C:\> docker-machine ip MyDockerHost
191.237.46.90
```

![実行中の ngnix コンテナー](./media/vs-azure-tools-docker-machine-azure-config/nginxsuccess.png)

## <a name="summary"></a>概要
docker-machine を使用して、個々の Docker ホストの検証のために Azure に Docker ホストを簡単にプロビジョニングできます。
コンテナーの運用ホスティングについては、「 [Azure Container Service の概要](http://aka.ms/AzureContainerService)

Visual Studio を使用した .NET Core アプリケーションの開発については、「 [Docker Tools for Visual Studio (Visual Studio 用 Docker ツール)](http://aka.ms/DockerToolsForVS)


