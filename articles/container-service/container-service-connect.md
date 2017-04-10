---
title: "Azure Container Service クラスターに接続する | Microsoft Docs"
description: "リモート コンピューターから Azure Container Service の Kubernetes、DC/OS、または Docker Swarm クラスターに接続します"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, DC/OS, Azure"
ms.assetid: ff8d9e32-20d2-4658-829f-590dec89603d
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: rogardle
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 3dfa2c56dd6d3e0fe7757995d284cebe172eabc4
ms.lasthandoff: 04/03/2017


---
# <a name="make-a-remote-connection-to-a-kubernetes-dcos-or-docker-swarm-cluster"></a>Kubernetes、DC/OS、または Docker Swarm クラスターへのリモート接続を確立する
Azure Container Service クラスターを作成したら、クラスターに接続してワークロードのデプロイと管理を行う必要があります。 この記事では、リモート コンピューターからクラスターのマスター VM に接続する方法について説明します。 

Kubernetes クラスター、DC/OS クラスター、Docker Swarm クラスターはローカルで HTTP エンドポイントを提供します。 Kubernetes では、このエンドポイントがセキュリティ保護されてインターネット上に公開されているため、インターネットに接続されている任意のコンピューターから `kubectl` コマンド ライン ツールを実行することによってアクセスできます。 

DC/OS と Docker Swarm では、内部システムに対する Secure Shell (SSH) トンネルを作成する必要があります。 トンネルが確立されたら、HTTP エンドポイントを使用してローカル システムからクラスターの Web インターフェイスを表示するコマンドを実行できます。 


## <a name="prerequisites"></a>前提条件

* [Azure Container Service にデプロイされている](container-service-deployment.md) Kubernetes、DC/OS、または Swarm クラスター。
* SSH RSA 秘密キー ファイル (デプロイ時にクラスターに追加された公開キーに対応)。 これらのコマンドは、SSH 秘密キーがコンピューター上の `$HOME/.ssh/id_rsa` に存在することを想定しています。 詳細については、[OS X と Linux 用](../virtual-machines/linux/mac-create-ssh-keys.md)または [Windows 用](../virtual-machines/linux/ssh-from-windows.md)の手順を参照してください。 SSH 接続が動作していない場合は、[SSH キーのリセット](../virtual-machines/linux/troubleshoot-ssh-connection.md)が必要になることがあります。

## <a name="connect-to-a-kubernetes-cluster"></a>Kubernetes クラスターへの接続

お使いのコンピューターに `kubectl` をインストールして構成するには、次の手順に従います。

> [!NOTE] 
> Linux または OS X では、`sudo` を使用してこのセクションのコマンドを実行することが必要になる場合があります。
> 

### <a name="install-kubectl"></a>kubectl のインストール
このツールをインストールする 1 つの方法は、`az acs kubernetes install-cli` Azure CLI 2.0 コマンドを使用することです。 このコマンドを実行するには、Azure CLI 2.0 の最新版が[インストール済み](/cli/azure/install-az-cli2)であることと、Azure アカウントにログインしていること (`az login`) を確認してください。

```azurecli
# Linux or OS X
az acs kubernetes install-cli [--install-location=/some/directory/kubectl]

# Windows
az acs kubernetes install-cli [--install-location=C:\some\directory\kubectl.exe]
```

または、[Kubernetes リリース ページ](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG.md)から最新のクライアントを直接ダウンロードすることもできます。 詳細については、「[Installing and Setting up kubectl (kubectl のインストールとセットアップ)](https://kubernetes.io/docs/user-guide/prereqs/)」を参照してください。

### <a name="download-cluster-credentials"></a>クラスターの資格情報のダウンロード
`kubectl` をインストールしたら、クラスターの資格情報をコンピューターにコピーする必要があります。 資格情報を取得する 1 つの方法は、`az acs kubernetes get-credentials` コマンドを使用することです。 リソース グループの名前とコンテナー サービス リソースの名前を渡します。


```azurecli
az acs kubernetes get-credentials --resource-group=<cluster-resource-group> --name=<cluster-name>
```

このコマンドにより、`kubectl` が求める配置場所である `$HOME/.kube/config` にクラスターの資格情報がダウンロードされます。

または、`scp` を使用して、マスター VM の `$HOME/.kube/config` からローカル コンピューターにファイルを安全にコピーすることもできます。 次に例を示します。

```console
mkdir $HOME/.kube
scp azureuser@<master-dns-name>:.kube/config $HOME/.kube/config
```

Windows を使用している場合は、Bash on Ubuntu on Windows、PuTTy セキュア ファイル コピー クライアント、または同様のツールを使用する必要があります。



### <a name="use-kubectl"></a>kubectl の使用

`kubectl` を構成したら、クラスター内のノードを一覧表示することで接続をテストできます。

```console
kubectl get nodes
```

他の `kubectl` コマンドを試すことができます。 たとえば、Kubernetes ダッシュボードを表示できます。 まず、Kubernetes API サーバーに対してプロキシを実行します。

```console
kubectl proxy
```

Kubernetes UI は現在、`http://localhost:8001/ui` で利用できます。

詳細については、[Kubernetes のクイック スタート](http://kubernetes.io/docs/user-guide/quick-start/)に関するページを参照してください。

## <a name="connect-to-a-dcos-or-swarm-cluster"></a>DC/OS または Swarm クラスターへの接続

Azure Container Service によってデプロイされた DC/OS と Docker Swarm クラスターを使用するには、次の手順に従って、ローカルの Linux、OS X、または Windows システムから Secure Shell (SSH) トンネルを作成してください。 

> [!NOTE]
> ここでの手順では、SSH 経由の TCP トラフィックのトンネリングに重点を置いています。 また、内部クラスター管理システムの 1 つを使用して対話型の SSH セッションを開始することもできますが、これはお勧めしません。 内部システムで直接作業すると、意図せずに構成を変更してしまうおそれがあります。  
> 

### <a name="create-an-ssh-tunnel-on-linux-or-os-x"></a>Linux または OS X での SSH トンネルの作成
Linux または OS X で SSH トンネルを作成するにはまず、負荷分散されたマスターのパブリック DNS 名を特定する必要があります。 次の手順に従います。


1. [Azure Portal](https://portal.azure.com) で、コンテナー サービス クラスターを含むリソース グループに移動します。 各リソースが表示されるようにリソース グループを展開します。 

2. コンテナー サービス リソースをクリックし、**[概要]** をクリックします。 クラスターの **[Master FQDN (マスター FQDN)]** が **[要点]** の下に表示されます。 後で使用するためにこの名前を保存します。 

    ![Public DNS name](media/pubdns.png)

    または、コンテナー サービスに対して `az acs show` コマンドを実行します。 コマンドの出力で **Master Profile:fqdn** プロパティを探します。

3. ここでシェルを開き、次の値を指定して `ssh` コマンドを実行します。 

    **LOCAL_PORT** は、トンネルのサービス側にある接続先の TCP ポートです。 Swarm の場合、これを 2375 に設定します。 DC/OS の場合、これを 80 に設定します。  
    **REMOTE_PORT** は、公開するエンドポイントのポートです。 Swarm の場合、ポート 2375 を使用します。 DC/OS の場合、ポート 80 を使用します。  
    **USERNAME** は、クラスターのデプロイ時に指定したユーザー名です。  
    **DNSPREFIX** は、クラスターのデプロイ時に指定した DNS 接頭辞です。  
    **REGION** は、リソース グループが置かれているリージョンです。  
    **PATH_TO_PRIVATE_KEY** (省略可能) は、クラスターの作成時に指定した公開キーに対応する秘密キーへのパスです。 このオプションは、`-i` フラグと共に使用します。

    ```bash
    ssh -fNL LOCAL_PORT:localhost:REMOTE_PORT -p 2200 [USERNAME]@[DNSPREFIX]mgmt.[REGION].cloudapp.azure.com 
    ```
    > [!NOTE]
    > SSH 接続ポートは、標準ポート 22 ではなく、2200 です。 複数のマスター VM を含むクラスターでは、これが最初のマスター VM への接続ポートです。
    > 



DC/OS と Swarm の例については、以降のセクションを参照してください。    

### <a name="dcos-tunnel"></a>DC/OS トンネル
DC/OS エンドポイントのトンネルを開くには、次のようなコマンドを実行します。

```bash
sudo ssh -fNL 80:localhost:80 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com 
```

> [!NOTE]
> ポート 80 以外のローカル ポート (ポート 8888 など) を指定できます。 ただし、このポートを使用すると、一部の Web UI リンクは動作しない可能性があります。

これで、次の URL を使用してローカル システムから DC/OS エンドポイントにアクセスできます (ローカル ポート 80 を想定)。

* DC/OS: `http://localhost:80/`
* Marathon: `http://localhost:80/marathon`
* Mesos: `http://localhost:80/mesos`

同様に、各アプリケーションの REST API にはこのトンネルを経由して到達できます。

### <a name="swarm-tunnel"></a>Swarm トンネル
Swarm エンドポイントへのトンネルを開くには、次のようなコマンドを実行します。

```bash
ssh -fNL 2375:localhost:2375 -p 2200 azureuser@acsexamplemgmt.japaneast.cloudapp.azure.com
```

これで、DOCKER_HOST 環境変数を次のように設定できます。 通常どおり、Docker コマンド ライン インターフェイス (CLI) を使用し続けることができます。

```bash
export DOCKER_HOST=:2375
```

### <a name="create-an-ssh-tunnel-on-windows"></a>Windows の SSH トンネルの作成
Windows では、さまざまな方法で SSH トンネルを作成できます。 このセクションでは、PuTTY を使用してトンネルを作成する方法について説明します。

1. Windows システムに [PuTTY をダウンロード](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)します。

2. アプリケーションを実行します。

3. ホスト名を入力します。ホスト名は、クラスター管理者のユーザー名とクラスターの第 1 マスターのパブリック DNS 名で構成されます。 **[ホスト名]** は `adminuser@PublicDNSName` のようになります。 **[ポート]** に「2200」と入力します。

    ![PuTTY configuration 1](media/putty1.png)

4. **[SSH]、[認証]** の順に選択します。 認証用の秘密キー ファイル (.ppk 形式) のパスを追加します。 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) などのツールを使用すると、クラスターの作成に使用された SSH キーからこのファイルを生成できます。

    ![PuTTY configuration 2](media/putty2.png)

5. **[SSH]、[トンネル]** の順に選択し、次のように転送ポートを構成します。

    * **[源ポート]** には、DC/OS の場合は 80、Swarm の場合は 2375 を指定します。
    * **[ターゲット]** には、DC/OS の場合は localhost:80、Swarm の場合は localhost:2375 を指定します。

    次の例は DC/OS で構成されていますが、Docker Swarm でも同様です。

    > [!NOTE]
    > このトンネルの作成時にはポート 80 を使用しないでください。
    > 

    ![PuTTY configuration 3](media/putty3.png)

6. 完了したら、**[セッション]、[保存]** の順にクリックして接続の構成を保存します。

7. PuTTY セッションに接続するには、**[開く]** をクリックします。 接続すると、PuTTY のイベント ログでポート構成を確認できます。

    ![PuTTY event log](media/putty4.png)

DC/OS のトンネルを構成したら、関連するエンドポイントに次のアドレスでアクセスできます。

* DC/OS: `http://localhost/`
* Marathon: `http://localhost/marathon`
* Mesos: `http://localhost/mesos`

Docker Swarm のトンネルを構成したら、Windows の設定を開き、`DOCKER_HOST` という名前のシステム環境変数を `:2375` という値で構成します。 これで、Docker CLI を使用して Swarm クラスターにアクセスできます。

## <a name="next-steps"></a>次のステップ
クラスターにコンテナーをデプロイして管理します。

* [Azure Container Service と Kubernetes の使用](container-service-kubernetes-ui.md)
* [Azure コンテナー サービスと DC/OS の使用](container-service-mesos-marathon-rest.md)
* [Azure コンテナー サービスと Docker Swarm の使用](container-service-docker-swarm.md)


