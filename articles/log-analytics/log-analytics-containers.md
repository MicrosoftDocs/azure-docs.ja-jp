---
title: "Azure Log Analytics のコンテナー監視ソリューション | Microsoft Docs"
description: "Log Analytics のコンテナー監視ソリューションを使用すると、Docker と Windows のコンテナー ホストを 1 か所で表示して管理できます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: magoedte;banders
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: b2e03531ee401f4552198e5dd50fbfe1d970f0e5
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="container-monitoring-solution-in-log-analytics"></a>Log Analytics のコンテナー監視ソリューション

![コンテナーのシンボル](./media/log-analytics-containers/containers-symbol.png)

この記事では、Docker と Windows のコンテナー ホストを 1 か所で表示して管理できる、Log Analytics のコンテナー監視ソリューションを設定して使用する方法について説明します。 Docker は、IT インフラストラクチャへのソフトウェアのデプロイを自動化するコンテナーを作成するために使用されるソフトウェア仮想化システムです。

このソリューションは、どのコンテナーが実行中か、何のコンテナー イメージが実行中か、コンテナーがどこで実行中かを表示します。 コンテナーで使用されるコマンドを示す詳細な監査情報を確認できます。 また、Docker または Windows ホストをリモートで確認しなくても、一元化されたログを表示および検索して、コンテナーのトラブルシューティングを行うことができます。 ホストで余分なリソースを使用しているコンテナーや、ノイズが大きいコンテナーを特定できます。 また、コンテナーについて、CPU、メモリ、ストレージ、ネットワークの使用量とパフォーマンスに関する情報を一元的に確認できます。 Windows を実行しているコンピューターでは、Windows Server、Hyper-V、Docker の各コンテナーのログを一元化して比較できます。 このソリューションは、次のコンテナー オーケストレーターをサポートしています。

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift


次のダイアグラムは、OMS を使用するさまざまなコンテナー ホストとエージェント間の関係を示しています。

![コンテナー ダイアグラム](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements"></a>システム要件

始める前に、次の詳細を確認し、前提条件が満たされていることを確認してください。

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Docker Orchestrator と OS プラットフォームのコンテナー監視ソリューションのサポート
次の表は、Log Analytics によるコンテナー インベントリ、パフォーマンス、およびログの Docker オーケストレーションとオペレーティング システムの監視サポートの概要を示しています。   

| | ACS | Linux | Windows | コンテナー<br>インベントリ | イメージ<br>インベントリ | ノード<br>インベントリ | コンテナー<br>パフォーマンス | コンテナー<br>イベント | イベント<br>ログ | コンテナー<br>ログ |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| サービス<br>Fabric | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Open<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(スタンドアロン) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Linux サーバー<br>(スタンドアロン) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Linux でサポートされている Docker のバージョン

- Docker 1.11 ～ 1.13
- Docker CE と EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>コンテナー ホストとしてサポートされている x64 Linux ディストリビューション

- Ubuntu 14.04 LTS と 16.04 LTS
- CoreOS (Stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 と 7.3
- SLES 12
- RHEL 7.2 と 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 と 3.5
- ACS Mesosphere DC/OS 1.7.3 ～ 1.8.8
- ACS Kubernetes 1.4.5 ～ 1.6
- ACS Docker Swarm

### <a name="supported-windows-operating-system"></a>サポートされている Windows オペレーティング システム

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional または Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Windows でサポートされている Docker のバージョン

- Docker 1.12 と 1.13
- Docker 17.03.0 以降

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

1. コンテナー監視ソリューションを OMS ワークスペースに追加します。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) から追加するか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページで説明されている手順に従って追加してください。

2. OMS エージェントで Docker をインストールして使用します。  オペレーティング システムにより、次の方法から選択できます。

  * サポートされている Linux オペレーティング システムでは、Docker をインストールして実行し、[OMS エージェント for Linux](log-analytics-agent-linux.md) をインストールして構成します。  
  * CoreOS では、OMS Agent for Linux を実行できません。 代わりに、OMS Agent for Linux のコンテナー化されたバージョンを実行します。 Azure Government Cloud のコンテナーで作業をしている場合は、[CoreOS を含む Linux コンテナー ホスト](#for-all-linux-container-hosts-including-coreos)または [CoreOS を含む Azure Government Linux コンテナー](#for-all-azure-government-linux-container-hosts-including-coreos)に関する記事をご覧ください。
  * Windows Server 2016 および Windows 10 では、Docker エンジンとクライアントをインストールした後、エージェントを接続して情報を収集し、Log Analytics に送信します。  

### <a name="container-services"></a>コンテナー サービス

- Red Hat OpenShift 環境がある場合は、[Red Hat OpenShift の OMS エージェントの構成](#configure-an-oms-agent-for-red-hat-openshift)に関する記事をご覧ください。
- Azure Container Service を使用する Kubernetes クラスターがある場合は、[Microsoft Operations Management Suite (OMS) を使用した Azure Container Service クラスターの監視](../container-service/kubernetes/container-service-kubernetes-oms.md)に関する記事をご覧ください。
- Azure Container Service DC/OS クラスターがある場合、詳細については、「[Operations Management Suite を使用した Azure Container Service DC/OS クラスターの監視](../container-service/dcos-swarm/container-service-monitoring-oms.md)」をご覧ください。
- Docker Swarm モード環境がある場合、詳細については、「[Docker Swarm 用の OMS エージェントを構成する](#configure-an-oms-agent-for-docker-swarm)」をご覧ください。
- Service Fabric でコンテナーを使用する場合、詳しくは「[Azure Service Fabric の概要](../service-fabric/service-fabric-overview.md)」をご覧ください。
- Windows を実行しているコンピューターに Docker エンジンをインストールして構成する方法の詳細については、「[Windows 上の Docker エンジン](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)」をご覧ください。

> [!IMPORTANT]
> Docker は、[OMS Agent for Linux](log-analytics-agent-linux.md) をコンテナー ホストにインストールする**前**に実行しておく必要があります。 Docker をインストールするより先にエージェントをインストールしてある場合は、OMS Agent for Linux を再インストールする必要があります。 Docker の詳細については、[Docker の Web サイト](https://www.docker.com)を参照してください。


## <a name="linux-container-hosts"></a>Linux コンテナー ホスト

Docker をインストールした後で、コンテナー ホストの次の設定を使用して、Docker で使用するためにエージェントを構成します。 まず、OMS のワークスペース ID とキーが必要です。これらは Azure Portal で見つけることができます。 ワークスペースで **[クイック スタート]** > **[コンピューター]** をクリックして、**ワークスペース ID** と**主キー**を表示します。  両方をコピーしてお使いのエディターに貼り付けます。

### <a name="for-all-linux-container-hosts-except-coreos"></a>CoreOS を除くすべての Linux コンテナー ホスト

- OMS Agent for Linux のインストール方法の詳細と手順については、「[Linux コンピューターを Operations Management Suite (OMS) に接続する](log-analytics-agent-linux.md)」をご覧ください。

### <a name="for-all-linux-container-hosts-including-coreos"></a>CoreOS を含むすべての Linux コンテナー ホスト

監視する OMS コンテナーを起動します。 次の例に変更を加えて使用してください。

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

### <a name="for-all-azure-government-linux-container-hosts-including-coreos"></a>CoreOS を含むすべての Azure Government Linux コンテナー ホスト

監視する OMS コンテナーを起動します。 次の例に変更を加えて使用してください。

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

### <a name="switching-from-using-an-installed-linux-agent-to-one-in-a-container"></a>インストール済みの Linux エージェントからコンテナー内のエージェントの使用への切り替え
これまで直接インストールされたエージェントを使用しており、今後はコンテナーで実行されているエージェントを使用したい場合は、まず OMS Agent for Linux を削除する必要があります。 エージェントを正しくアンインストール方法について詳しくは、「[OMS Agent for Linux のアンインストール](log-analytics-agent-linux.md#uninstalling-the-oms-agent-for-linux)」をご覧ください。  

### <a name="configure-an-oms-agent-for-docker-swarm"></a>Docker Swarm 用の OMS エージェントを構成する

Docker Swarm で、OMS エージェントをグローバル サービスとして実行できます。 次の情報を使用して、OMS Agent サービスを作成します。 OMS のワークスペース ID と主キーを挿入する必要があります。

- マスター ノードで、次を実行します。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>Red Hat OpenShift 用の OMS エージェントを構成する
Red Hat OpenShift に OMS エージェントを追加してコンテナーの監視データの収集を開始するには、次の 3 つの方法が用意されています。

* OpenShift の各ノードに直接 [OMS Agent for Linux をインストールする](log-analytics-agent-linux.md)  
* Azure 内に存在する OpenShift の各ノードで [Log Analytics VM 拡張機能を有効にする](log-analytics-azure-vm-extension.md)  
* OMS エージェントを OpenShift デーモン セットとしてインストールする  

このセクションでは、OMS エージェントを OpenShift デーモン セットとしてインストールするために必要な手順を説明します。  

1. OpenShift のマスター ノードにサインオンし、yaml ファイル [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) を GitHub からマスター ノードにコピーして、値を OMS のワークスペース ID と 主キーに変更します。
2. 次のコマンドを実行して OMS のプロジェクトを作成し、ユーザー アカウントを設定します。

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. デーモン セットをデプロイするには、次の手順を実行します。

    `oc create -f ocp-omsagent.yaml`

5. 構成と動作が正しいことを確認するには、次を入力します。

    `oc describe daemonset omsagent`  

    出力は次のようになります。

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

OMS Agent デーモン セットの yaml ファイルを使用するときにシークレットを使用して OMS のワークスペース ID と主キーをセキュリティで保護するには、次の手順を実行します。

1. OpenShift のマスター ノードにサインオンして、yaml ファイル [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) とシークレットを生成するスクリプト [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) を GitHub からコピーします。  このスクリプトにより OMS のワークスペース ID と主キーのシークレット yaml ファイルが生成され、秘密情報がセキュリティで保護されます。  
2. 次のコマンドを実行して OMS のプロジェクトを作成し、ユーザー アカウントを設定します。 シークレットを生成するスクリプトは OMS のワークスペース ID <WSID> と主キー <KEY> を要求し、完了すると ocp-secret.yaml ファイルが作成されます。  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. 次のコマンドを実行して、シークレット ファイルをデプロイします。

    `oc create -f ocp-secret.yaml`

5. 次を実行して、デプロイを確認します。

    `oc describe secret omsagent-secret`  

    出力は次のようになります。  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. 次を実行して、OMS エージェントのデーモン セット yaml ファイルをデプロイします。

    `oc create -f ocp-ds-omsagent.yaml`  

7. 次を実行して、デプロイを確認します。

    `oc describe ds oms`

    出力は次のようになります。

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

### <a name="secure-your-secret-information-for-docker-swarm-and-kubernetes"></a>Docker Swarm と Kubernetes の秘密情報の安全を確保する

Docker Swarm と Kubernetes コンテナー サービス用のシークレットの OMS のワークスペース ID と主キーをセキュリティで保護できます。

#### <a name="secure-secrets-for-docker-swarm"></a>Docker Swarm 用のシークレットを保護する

Docker Swarm では、ワークスペース ID と主キーのシークレットの作成後に、OMSagent 用の Docker サービスの作成を実行できます。 次の情報を使用して、シークレット情報を作成します。

1. マスター ノードで、次を実行します。

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. シークレットが正しく作成されたことを確認します。

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. 次のコマンドを実行して、シークレットをコンテナー化された OMS エージェントにマウントします。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="secure-secrets-for-kubernetes-with-yaml-files"></a>Kubernetes 用のシークレットを yaml ファイルを使用して保護する

Kubernetes では、スクリプトを使用して、ワークスペース ID と主キーのシークレット .yaml ファイルを生成します。 [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) ページに、シークレット情報を使用して、または使用せずに使うことができるファイルがあります。

- シークレット情報がない既定の OMS Agent DaemonSet (omsagent.yaml)
- シークレット情報 (omsagent-ds-secrets.yaml) と、シークレット yaml (omsagentsecret.yaml) ファイルを生成するシークレット生成スクリプトを使用する OMS Agent DaemonSet yaml ファイル。

omsagent DaemonSet は、シークレットを使用して作成するか使用せずに作成するかを選択できます。

##### <a name="default-omsagent-daemonset-yaml-file-without-secrets"></a>シークレットを使用しない既定の OMSagent DaemonSet yaml ファイル

- 既定の OMS Agent DaemonSet yaml ファイルでは、`<WSID>` と `<KEY>` を自分の WSID と KEY に置き換えます。 ファイルをマスター ノードにコピーし、次を実行します。

    ```
    sudo kubectl create -f omsagent.yaml
    ```

##### <a name="default-omsagent-daemonset-yaml-file-with-secrets"></a>シークレットを使用する既定の OMSagent DaemonSet yaml ファイル

1. シークレット情報を使用して OMS Agent DaemonSet を使用するには、まずシークレットを作成します。
    1. スクリプトとシークレット テンプレート ファイルをコピーし、それらが同じディレクトリにあることを確認します。
        - シークレット生成スクリプト: secret-gen.sh
        - シークレット テンプレート: secret-template.yaml
    2. 次の例のように、スクリプトを実行します。 このスクリプトでは、OMS のワークスペース ID と主キーの入力を求められます。それらを入力すると、シークレット yaml ファイルが作成され、実行できるようになります。   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. 次のコマンドを実行して、シークレット ポッドを作成します。
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. 検証するには、次のコマンドを実行します。

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        出力は、次のようになるはずです。

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        出力は、次のようになるはずです。

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. ``` sudo kubectl create -f omsagent-ds-secrets.yaml ``` を実行して、omsagent daemon-set を作成します。

2. 次のように、OMS Agent DaemonSet が実行されていることを確認します。

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Kubernetes では、スクリプトを使用して、ワークスペース ID と主キー用のシークレット yaml ファイルを生成します。 [omsagent yaml ファイル](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml)で次の例の情報を使用して、シークレット情報を保護します。

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

## <a name="windows-container-hosts"></a>Windows コンテナー ホスト

### <a name="preparation-before-installing-windows-agents"></a>Windows エージェントをインストールする前の準備

Windows を実行しているコンピューターにエージェントをインストールする前に、Docker サービスを構成する必要があります。 構成により、Windows エージェントまたは Log Analytics 仮想マシン拡張機能は、エージェントが Docker デーモンにリモートでアクセスできるように Docker TCP ソケットを使用できるようになり、監視用のデータをキャプチャすることが可能になります。

#### <a name="to-start-docker-and-verify-its-configuration"></a>Docker を起動して構成を確認するには

Windows Server の TCP 名前付きパイプのセットアップに必要な手順があります。

1. Windows PowerShell で、TCP パイプと名前付きパイプを有効にします。

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. TCP パイプと名前付きパイプ用の構成ファイルを使用して、Docker を構成します。 構成ファイルは C:\ProgramData\docker\config\daemon.json にあります。

    daemon.json ファイルで、以下を実行する必要があります。

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Windows コンテナーで使用する Docker デーモン構成の詳細については、「[Windows 上の Docker エンジン](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon)」をご覧ください。


### <a name="install-windows-agents"></a>Windows エージェントのインストール

Windows および Hyper-V コンテナーの監視を有効にするには、コンテナー ホストである Windows コンピューターに Microsoft Monitoring Agent (MMA) をインストールします。 Windows を実行しているオンプレミス環境のコンピューターの場合、[Log Analytics への Windows コンピューターの接続](log-analytics-windows-agents.md)に関する記事をご覧ください。 Azure で実行されている仮想マシンの場合、[仮想マシン拡張機能](log-analytics-azure-vm-extension.md)を使用して Log Analytics に接続します。

Service Fabric で実行されている Windows コンテナーを監視できます。 ただし、現在 Service Fabric でサポートされているのは、[Azure で実行される仮想マシン](log-analytics-azure-vm-extension.md)と[オンプレミス環境で Windows を実行するコンピューター](log-analytics-windows-agents.md)のみです。

Windows でコンテナー監視ソリューションが正しく設定されていることを確認できます。 管理パックが正常にダウンロードされているかどうかを確認するには、*ContainerManagement.xxx* を探します。 ファイルは C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs フォルダーにあります。


## <a name="solution-components"></a>ソリューションのコンポーネント

Windows エージェントを使用している場合、このソリューションを追加するときに、各コンピューターにエージェントと共に次の管理パックがインストールされます。 この管理パックは構成や保守が不要です。

- *ContainerManagement.xxx* は、C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs にインストールされます。

## <a name="container-data-collection-details"></a>コンテナーのデータ収集の詳細
コンテナー監視ソリューションでは、有効化されたエージェントを使用して、コンテナー ホストとコンテナーからさまざまなパフォーマンス メトリックとログ データを収集します。

データは、次のエージェントの種類によって 3 分ごとに収集されます。

- [OMS Agent for Linux](log-analytics-linux-agents.md)
- [Windows エージェント](log-analytics-windows-agents.md)
- [Log Analytics VM 拡張機能](log-analytics-azure-vm-extension.md)


### <a name="container-records"></a>コンテナー レコード

次の表に、コンテナー監視ソリューションによって収集されるレコードと、ログ検索結果に表示されるデータ型の例を示します。

| データ型 | ログ検索のデータ型 | フィールド |
| --- | --- | --- |
| ホストとコンテナーのパフォーマンス | `Type=Perf` | Computer、ObjectName、CounterName (%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network)、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| コンテナー インベントリ | `Type=ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| コンテナー イメージ インベントリ | `Type=ContainerImageInventory` | TimeGenerated、Computer、Image、ImageTag、ImageSize、VirtualSize、Running、Paused、Stopped、Failed、SourceSystem、ImageID、TotalContainer |
| コンテナー ログ | `Type=ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| コンテナー サービス ログ | `Type=ContainerServiceLog`  | TimeGenerated、Computer、TimeOfCommand、Image、Command、SourceSystem、ContainerID |
| コンテナー ノード インベントリ | `Type=ContainerNodeInventory_CL`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| Kubernetes インベントリ | `Type=KubePodInventory_CL` | TimeGenerated、Computer、PodLabel_deployment_s、PodLabel_deploymentconfig_s、PodLabel_docker_registry_s、Name_s、Namespace_s、PodStatus_s、PodIp_s、PodUid_g、PodCreationTimeStamp_t、SourceSystem |
| コンテナー プロセス | `Type=ContainerProcess_CL` | TimeGenerated、Computer、Pod_s、Namespace_s、ClassName_s、InstanceID_s、Uid_s、PID_s、PPID_s、C_s、STIME_s、Tty_s、TIME_s、Cmd_s、Id_s、Name_s、SourceSystem |
| Kubernetes イベント | `Type=KubeEvents_CL` | TimeGenerated、Computer、Name_s、ObjectKind_s、Namespace_s、Reason_s、Type_s、SourceComponent_s、SourceSystem、Message |

*PodLabel* データ型に付加されるラベルは、独自のカスタム ラベルです。 表に示されている、付加された PodLabel ラベルは一例です。 そのため、`PodLabel_deployment_s`、`PodLabel_deploymentconfig_s`、`PodLabel_docker_registry_s` はご利用の環境のデータ セットによって異なり、一般的には `PodLabel_yourlabel_s` に似ています。


## <a name="monitor-containers"></a>コンテナーの監視
OMS ポータルでソリューションを有効にすると、コンテナー ホストと、ホストで実行されているコンテナーに関する概要情報が **[コンテナー]** タイルに表示されます。

![[コンテナー] タイル](./media/log-analytics-containers/containers-title.png)

このタイルには、環境内に存在するコンテナーの数と、それらのコンテナーの状態 (失敗、実行中、停止) の概要が示されます。

### <a name="using-the-containers-dashboard"></a>コンテナー ダッシュボードの使用
**[コンテナー]** タイルをクリックします。 ここに表示される情報は、次の項目で整理されます。

- **コンテナー イベント** - コンテナーの状態と、失敗したコンテナーがあるコンピューターを示します。
- **コンテナー ログ** - 生成されたコンテナー ログ ファイルの一定期間のグラフと、ログ ファイル数が最も多いコンピューターの一覧を示します。
- **Kubernetes イベント** - 生成された Kubernetes イベントの一定期間のグラフと、ポッドがイベントを生成した理由の一覧を示します。 *このデータ セットは、Linux 環境でのみ使用します。*
- **Kubernetes 名前空間のインベントリ** - 名前空間とポッドの数、およびそれらの階層を示します。 *このデータ セットは、Linux 環境でのみ使用します。*
- **コンテナー ノードのインベントリ** - コンテナー ノード/ホストで使用されるオーケストレーションの種類の数を示します。 コンピューター ノード/ホストも、コンテナーの数として一覧表示されます。 *このデータ セットは、Linux 環境でのみ使用します。*
- **コンテナー イメージのインベントリ** - 使用されるコンテナー イメージの総数と、イメージの種類の数を示します。 イメージの数も、イメージ タグで一覧表示されます。
- **コンテナーの状態** - 実行中のコンテナーがあるコンテナー ノード/ホスト コンピューターの総数を示します。 コンピューターも、実行中のホストの数として一覧表示されます。
- **コンテナー プロセス** - 実行されているコンテナー プロセスの一定期間の折れ線グラフを示します。 コンテナーも、コンテナー内で実行中のコマンド/プロセスとして一覧表示されます。 *このデータ セットは、Linux 環境でのみ使用します。*
- **コンテナー CPU のパフォーマンス** - コンピューター ノード/ホストの平均 CPU 使用率の一定期間の折れ線グラフを示します。 平均 CPU 使用率に基づいてコンピューター ノード/ホストも一覧表示されます。
- **コンテナー メモリのパフォーマンス** - メモリ使用量の一定期間の折れ線グラフを示します。 インスタンス名に基づいてコンピューターのメモリ使用率も一覧表示されます。
- **コンピューターのパフォーマンス** - CPU パフォーマンスの一定期間のパーセント、メモリ使用量の一定期間のパーセント、一定期間の空きディスク領域のメガバイトについての折れ線グラフを示します。 グラフの線にマウス オーバーすると詳細を表示できます。


ダッシュボードの各エリアは、収集されたデータに対して実行された検索の結果を視覚的に表したものです。

![コンテナー ダッシュボード](./media/log-analytics-containers/containers-dash01.png)

![コンテナー ダッシュボード](./media/log-analytics-containers/containers-dash02.png)

**[コンテナーの状態]** エリアで、上部のエリアをクリックすると次のように表示されます。

![コンテナーの状態](./media/log-analytics-containers/containers-status.png)

[ログ検索] が開き、コンテナーの状態に関する情報が表示されます。

![コンテナーの [ログ検索]](./media/log-analytics-containers/containers-log-search.png)

ここで検索クエリを編集して、関心のある情報のみが見つかるように変更できます。 ログ検索の詳細については、「[Log Analytics におけるログの検索](log-analytics-log-searches.md)」を参照してください。

## <a name="troubleshoot-by-finding-a-failed-container"></a>失敗したコンテナーを特定してトラブルシューティングを行う

ゼロ以外の終了コードで終了したコンテナーは、Log Analytics によって **[失敗]** とマークされます。 **[失敗したコンテナー]** エリアで、環境におけるエラーと失敗の概要を確認できます。

### <a name="to-find-failed-containers"></a>失敗したコンテナーを特定するには
1. **[コンテナーの状態]** エリアをクリックします。  
   ![コンテナーの状態](./media/log-analytics-containers/containers-status.png)
2. [ログ検索] が開き、次のようにコンテナーの状態が表示されます。  
   ![コンテナーの状態](./media/log-analytics-containers/containers-log-search.png)
3. 次に、失敗したコンテナーの集計値をクリックして、追加情報を表示します。 **[表示数を増やす]** を展開してイメージ ID を表示します。  
   ![失敗したコンテナー](./media/log-analytics-containers/containers-state-failed.png)  
4. 次に、検索クエリに以下を入力します。 `Type=ContainerInventory <ImageID>` により、停止したイメージと失敗したイメージのサイズと数など、イメージに関する詳細が表示されます。  
   ![失敗したコンテナー](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>コンテナー データのログの検索
特定のエラーのトラブルシューティングを実行する際には、環境のどこでそのエラーが発生しているのかを確認すると役立つ場合があります。 次のログの種類は、目的の情報を返すクエリを作成するうえで役立ちます。


- **ContainerImageInventory** – この種類は、イメージ別に整理された情報を見つける場合や、イメージの ID やサイズなどのイメージ情報を確認する場合に使用します。
- **ContainerInventory** – この種類は、コンテナーの場所、コンテナーの名前、実行中のイメージに関する情報が必要な場合に使用します。
- **ContainerLog** – この種類は、特定のエラー ログの情報やエントリを見つける場合に使用します。
- **ContainerNodeInventory_CL** – コンテナーが置かれているホスト/ノードに関する情報が必要な場合に使用します。 これにより、Docker のバージョン、オーケストレーションの種類、ストレージ、ネットワーク情報を取得できます。
- **ContainerProcess_CL** – コンテナー内で実行中のプロセスをすぐに確認したいときに使用します。
- **ContainerServiceLog** – この種類は、開始、停止、削除、プルのコマンドなど、Docker デーモンの監査証跡情報を見つける場合に使用します。
- **KubeEvents_CL** – Kubernetes イベントを確認するときに使用します。
- **KubePodInventory_CL** – クラスター階層に関する情報を把握したいときに使用します。


### <a name="to-search-logs-for-container-data"></a>コンテナー データのログを検索するには
* 最近失敗したことがわかっているイメージを選択し、そのエラー ログを見つけます。 まず、**ContainerInventory** 検索で、そのイメージを実行しているコンテナー名を特定します。 たとえば、`Type=ContainerInventory ubuntu Failed` を検索します。  
    ![Ubuntu コンテナーの検索](./media/log-analytics-containers/search-ubuntu.png)

  **[名前]** の横にあるコンテナーの名前と、それらのログを検索します。 この例では `Type=ContainerLog cranky_stonebreaker` です。

**パフォーマンス情報の表示**

クエリの作成を始めたばかりの段階では、何が可能かを先に確認しておくと効率的です。 たとえば、すべてのパフォーマンス データを確認するには、次の検索クエリを入力して広範なクエリを試してみてください。

```
Type=Perf
```

![コンテナーのパフォーマンス](./media/log-analytics-containers/containers-perf01.png)

表示されているパフォーマンス データのスコープを特定のコンテナーに制限するには、その名前をクエリの右側に入力します。

```
Type=Perf <containerName>
```

そうすると、個々のコンテナーについて収集されたパフォーマンス メトリックの一覧が表示されます。

![コンテナーのパフォーマンス](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>検索クエリの例
クエリの作成の際には、多くの場合、1 ～ 2 個の例で始め、その後環境に合わせて変更するとうまくいきます。 まず、**[サンプル クエリ]** エリアで試すと、より高度なクエリを作成しやすくなります。

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![コンテナーのクエリ](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>ログ検索クエリの保存
クエリの保存は、Log Analytics の標準的な機能です。 クエリを保存しておけば、後で使えるように、便利なクエリを取っておくことができます。

作成したクエリが便利であることがわかったら、**[ログ検索]** ページの上部にある [お気に入り] をクリックして保存してください。 後で **[マイ ダッシュボード]** ページで簡単にアクセスできます。

## <a name="next-steps"></a>次のステップ
* [ログを検索](log-analytics-log-searches.md) して、詳細なコンテナー データ レコードを確認します。

