---
title: Azure Monitor のコンテナー監視ソリューション | Microsoft Docs
description: Azure Monitor のコンテナー監視ソリューションを使用すると、Docker と Windows のコンテナー ホストを 1 か所で表示して管理できます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/06/2020
ms.openlocfilehash: 59f81103e72be6c0f1b629981fdb53c8467000a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037423"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Azure Monitor のコンテナー監視ソリューション

![コンテナーのシンボル](./media/containers/containers-symbol.png)

この記事では、Docker と Windows のコンテナー ホストを 1 か所で表示して管理できる、Azure Monitor のコンテナー監視ソリューションを設定して使用する方法について説明します。 Docker は、IT インフラストラクチャへのソフトウェアのデプロイを自動化するコンテナーを作成するために使用されるソフトウェア仮想化システムです。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

このソリューションは、どのコンテナーが実行中か、何のコンテナー イメージが実行中か、コンテナーがどこで実行中かを表示します。 コンテナーで使用されるコマンドを示す詳細な監査情報を確認できます。 また、Docker または Windows ホストをリモートで確認しなくても、一元化されたログを表示および検索して、コンテナーのトラブルシューティングを行うことができます。 ホストで余分なリソースを使用しているコンテナーや、ノイズが大きいコンテナーを特定できます。 また、コンテナーについて、CPU、メモリ、ストレージ、ネットワークの使用量とパフォーマンスに関する情報を一元的に確認できます。 Windows を実行しているコンピューターでは、Windows Server、Hyper-V、Docker の各コンテナーのログを一元化して比較できます。 このソリューションは、次のコンテナー オーケストレーターをサポートしています。

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

[Azure Service Fabric](../../service-fabric/service-fabric-overview.md) にデプロイされたコンテナーがある場合は、[Service Fabric ソリューション](../../service-fabric/service-fabric-diagnostics-oms-setup.md)とこのソリューションの両方を有効にして、クラスターイベントの監視を含めることをお勧めします。 Service Fabric ソリューションを有効にする前に、[Service Fabric ソリューションの使用](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md)に関する記事を確認して、提供される内容とその使用方法を理解してください。

Azure Kubernetes Service (AKS) でホストされている Kubernetes 環境にデプロイされているワークロードのパフォーマンスを監視する方法については、[Azure Kubernetes サービスの監視](../containers/container-insights-overview.md)に関するページを参照してください。 コンテナー監視ソリューションでは、そのプラットフォームの監視はサポートされていません。  

次のダイアグラムは、Azure Monitor を使用するさまざまなコンテナー ホストとエージェント間の関係を示しています。

![コンテナー ダイアグラム](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>システム要件とサポートされているプラットフォーム

始める前に、次の詳細を確認し、前提条件が満たされていることを確認してください。

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Docker Orchestrator と OS プラットフォームのコンテナー監視ソリューションのサポート

次の表は、Azure Monitor によるコンテナー インベントリ、パフォーマンス、およびログの Docker オーケストレーションとオペレーティング システムの監視サポートの概要を示しています。   

|Docker オーケストレーション | ACS | Linux | Windows | コンテナー<br>インベントリ | Image<br>インベントリ | Node<br>インベントリ | コンテナー<br>パフォーマンス | コンテナー<br>Event | Event<br>ログ | コンテナー<br>ログ |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| サービス<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
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
    - Kubernetes イベント、Kubernetes インベントリ、およびコンテナー プロセスは、バージョン 1.4.1～45 以降の Log Analytics エージェント for Linux のみでサポートされています。
- ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>サポートされている Windows オペレーティング システム

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional または Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Windows でサポートされている Docker のバージョン

- Docker 1.12 と 1.13
- Docker 17.03.0 以降

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成

次の情報を使用して、ソリューションをインストールおよび構成します。

1. コンテナー監視ソリューションを Log Analytics ワークスペースに追加します。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) から追加するか、[Solutions Gallery からの監視ソリューションの追加](../insights/solutions.md)に関するページで説明されている手順に従って追加してください。

2. Log Analytics エージェントを使って Docker をインストールし､使用します｡ ご使用のオペレーティング システムと Docker Orchestrator に基づいて、次のメソッドを使用してエージェントを構成できます。
   - スタンドアロン ホストの場合
     - サポートされている Linux オペレーティング システムでは、Docker をインストールして実行し、[Log Analytics エージェント for Linux](../vm/quick-collect-linux-computer.md) をインストールして構成します。  
     - CoreOS で、Log Analytics エージェント for Linux を実行することはできません。 代わりに、コンテナー化されたバージョンの Log Analytics エージェント Linux を実行できます。 Azure Government Cloud のコンテナーで作業をしている場合は、CoreOS を含む Linux コンテナー ホストまたは CoreOS を含む Azure Government Linux コンテナー ホストに関するセクションを参照してください。
     - Windows Server 2016 および Windows 10 では、Docker エンジンとクライアントをインストールした後、エージェントを接続して情報を収集し、Azure Monitor に送信します。 Windows 環境をご利用の場合は、「[Windows コンテナー ホストをインストールして構成する](#install-and-configure-windows-container-hosts)」を確認します。
   - Docker の複数ホストのオーケストレーションの場合
     - Red Hat OpenShift 環境がある場合は、「Log Analytics エージェント for Red Hat OpenShift を構成する」を参照してください。
     - Kubernetes クラスターで Azure Container Service を使用している場合:
       - ｢[Log Analytics Linux エージェント for Kubernetes を構成する](#configure-a-log-analytics-linux-agent-for-kubernetes)｣をお読みください｡
       - ｢[Log Analytics Windows エージェント for Kubernetes を構成する](#configure-a-log-analytics-windows-agent-for-kubernetes)｣をお読みください。
       - Helm を使用して Linux Kubernetes に Log Analytics エージェントをデプロイする方法に関するセクションを参照してください。
     - Azure Container Service DC/OS クラスターがある場合、詳細については、[Azure Monitor を使用した Azure Container Service DC/OS クラスターの監視](/previous-versions/azure/container-service/dcos-swarm/container-service-monitoring-oms)に関するページを参照してください。
     - Docker Swarm モード環境がある場合、詳細については、「Log Analytics エージェント for Docker Swarm を構成する」を参照してください。
     - Service Fabric クラスターがある場合、詳細については、[Azure Monitor を使用したコンテナーの監視](../../service-fabric/service-fabric-diagnostics-oms-containers.md)に関するページを参照してください。

Windows を実行しているコンピューターに Docker エンジンをインストールして構成する方法の詳細については、「[Windows 上の Docker エンジン](/virtualization/windowscontainers/manage-docker/configure-docker-daemon)」をご覧ください。

> [!IMPORTANT]
> Docker は、コンテナー ホストに [Log Analytics エージェント for Linux](../vm/quick-collect-linux-computer.md) をインストールする **前** に起動しておく必要があります。 Docker をインストールした後で Log Analytics エージェント for Linux をインストールしている場合は、エージェントをインストールし直す必要があります。 Docker の詳細については、[Docker の Web サイト](https://www.docker.com)を参照してください。

### <a name="install-and-configure-linux-container-hosts"></a>Linux コンテナー ホストをインストールして構成する

Docker をインストールした後で、コンテナー ホストの次の設定を使用して、Docker で使用するためにエージェントを構成します。 まず、Log Analytics のワークスペース ID とキーが必要です。これらは Azure Portal 上で見つけることができます。 ワークスペースで **[クイック スタート]**  >  **[コンピューター]** をクリックして、**ワークスペース ID** と **主キー** を表示します。  両方をコピーしてお使いのエディターに貼り付けます。

**CoreOS を除くすべての Linux コンテナー ホスト**

- Log Analytics エージェント for Linux のインストール方法に関する詳細と手順は、[Log Analytics エージェントの概要](../agents/log-analytics-agent.md)に関する記事をご覧ください。

**CoreOS を含むすべての Linux コンテナー ホスト**

監視するコンテナーを起動します。 次の例に変更を加えて使用してください。

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**CoreOS を含むすべての Azure Government Linux コンテナー ホスト**

監視するコンテナーを起動します。 次の例に変更を加えて使用してください。

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**インストール済みの Linux エージェントからコンテナー内のエージェントの使用への切り替え**

直接インストールしたエージェントを過去に使用したことがあり、代わりにコンテナーで実行されているエージェントを使用したい場合は、まず Log Analytics エージェント for Linux を削除する必要があります。 Log Analytics エージェント for Linux を正しくアンインストール方法については、「[Log Analytics エージェント for Linux のアンインストール](../vm/quick-collect-linux-computer.md)」をご覧ください。  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Log Analytics エージェント for Docker Swarm を構成する

Docker Swarm で、Log Analytics エージェントをグローバル サービスとして実行できます。 次の情報を参考に､Log Analytics エージェント サービスを作成します。 Log Analytics ワークスペースの ID と主キーを指定する必要があります。

- マスター ノードで、次を実行します。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Docker Swarm 用のシークレットを保護する

Docker Swarm の場合、ワークスペース ID と主キーのシークレットが作成されたら、次の情報を使用してシークレット情報を作成します。

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

3. 次のコマンドを実行して、コンテナー化された Log Analytics エージェントにシークレットをマウントします。

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Log Analytics エージェント for Red Hat OpenShift を構成する

Red Hat OpenShift に Log Analytics エージェントを追加してコンテナーの監視データの収集を開始するには、次の 3 通りの方法があります｡

* OpenShift の各ノードに直接 [Log Analytics エージェント for Linux をインストールする](../vm/quick-collect-linux-computer.md)  
* Azure 内に存在する OpenShift の各ノードで [Log Analytics VM 拡張機能を有効にする](../vm/quick-collect-azurevm.md)  
* Log Analytics エージェントを OpenShift デーモン セットとしてインストールする  

このセクションでは、Log Analytics エージェントを OpenShift デーモン セットとしてインストールするために必要な手順を説明します。  

1. OpenShift のマスター ノードにサインオンし、yaml ファイル [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) を GitHub からマスター ノードにコピーして、値を Log Analytics のワークスペース ID と 主キーに変更します。
2. 次のコマンドを実行して Azure Monitor のプロジェクトを作成し、ユーザー アカウントを設定します。

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. デーモン セットをデプロイするには、次の手順を実行します。

    `oc create -f ocp-omsagent.yaml`

4. 構成と動作が正しいことを確認するには、次を入力します。

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

Log Analytics エージェント デーモン セットの yaml ファイルを使用している場合､シークレットを利用して Log Analytics のワークスペース ID と主キーのセキュリティを保護するには、次の手順を実行します。

1. OpenShift のマスター ノードにサインオンして、yaml ファイル [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) とシークレットを生成するスクリプト [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) を GitHub からコピーします。  このスクリプトにより Log Analytics のワークスペース ID と主キーのシークレット yaml ファイルが生成され、秘密情報がセキュリティで保護されます。  
2. 次のコマンドを実行して Azure Monitor のプロジェクトを作成し、ユーザー アカウントを設定します。 シークレットを生成するスクリプトは Log Analytics のワークスペース ID `<WSID>` と主キー `<KEY>` を要求し、完了すると ocp-secret.yaml ファイルが作成されます。  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. 次のコマンドを実行して、シークレット ファイルをデプロイします。

    `oc create -f ocp-secret.yaml`

4. 次を実行して、デプロイを確認します。

    `oc describe secret omsagent-secret`  

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

5. 次を実行して、Log Analytics エージェントのデーモン セットの yaml ファイルをデプロイします。

    `oc create -f ocp-ds-omsagent.yaml`  

6. 次を実行して、デプロイを確認します。

    `oc describe ds oms`

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Log Analytics Linux エージェント for Kubernetes を構成する

Kubernetes に対しては、スクリプトを使用して、ワークスペース ID と主キーのシークレット yaml ファイルを生成して Log Analytics エージェント for Linux をインストールします。 [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) ページには、シークレット情報があるときと､シークレット情報がないときに利用できる両方のファイルがあります。

- 既定の Log Analytics エージェント for Linux DaemonSet には、シークレット情報 (omsagent.yaml) はありません。
- Log Analytics エージェント for Linux DaemonSet の yaml ファイルは、シークレット情報 (omsagent-ds-secrets.yaml) とシークレット生成スクリプトを使用してシークレット yaml (omsagentsecret.yaml) ファイルを生成します。

omsagent DaemonSet は、シークレットを使用して作成するか使用せずに作成するかを選択できます。

**シークレットを使用しない既定の OMSagent DaemonSet yaml ファイル**

- 既定の Log Analytics エージェントの DaemonSet yaml ファイルでは、`<WSID>` と `<KEY>` を自分の WSID と KEY に置き換えます。 ファイルをマスター ノードにコピーし、次を実行します。

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**シークレットを使用する既定の OMSagent DaemonSet yaml ファイル**

1. シークレット情報を使用して Log Analytics エージェントのDaemonSet を使用するには、まずシークレットを作成します。
    1. スクリプトとシークレット テンプレート ファイルをコピーし、それらが同じディレクトリにあることを確認します。
        - シークレット生成スクリプト: secret-gen.sh
        - シークレット テンプレート: secret-template.yaml
    2. 次の例のように、スクリプトを実行します。 このスクリプトでは、Log Analytics のワークスペース ID と主キーの入力を求められます。それらを入力すると、シークレット yaml ファイルが作成され、実行できるようになります。   

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

    5. ```sudo kubectl create -f omsagent-ds-secrets.yaml``` を実行して、omsagent daemon-set を作成します。

2. 次のように、Log Analytics エージェントの DaemonSet がすでに起動していることを確認します。

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Kubernetes の場合は、スクリプトを使用して、Log Analytics エージェント for Linux 用のワークスペース ID と 主キー用のシークレット yaml ファイルを生成します。 [omsagent yaml ファイル](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml)で次の例の情報を使用して、シークレット情報を保護します。

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Log Analytics Windows エージェント for Kubernetes を構成する

Windows Kubernetes の場合は､スクリプトを使用して、ワークスペース ID と主キー用のシークレット yaml ファイルを生成して Log Analytics エージェントをインストールします。 [Log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) ページには、シークレット情報があるときに利用できるファイルがあります。  Log Analytics エージェントは､マスター ノードとエージェント ノードごとにインストールする必要があります。  

1. マスター ノードでシークレット情報を使用して Log Analytics エージェント DaemonSet を使用するには、まずサインインして、シークレットを作成します。
    1. スクリプトとシークレット テンプレート ファイルをコピーし、それらが同じディレクトリにあることを確認します。
        - シークレット生成スクリプト: secret-gen.sh
        - シークレット テンプレート: secret-template.yaml

    2. 次の例のように、スクリプトを実行します。 このスクリプトでは、Log Analytics のワークスペース ID と主キーの入力を求められます。それらを入力すると、シークレット yaml ファイルが作成され、実行できるようになります。

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. ```kubectl create -f omsagentsecret.yaml``` を実行して、omsagent daemon-set を作成します。
    4. 確認するには、次のコマンドを実行します。

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        出力は、次のようになるはずです。

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
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

    5. ```kubectl create -f ws-omsagent-de-secrets.yaml``` を実行して、omsagent daemon-set を作成します。

2. 次のように、Log Analytics エージェントの DaemonSet がすでに起動していることを確認します。

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Windows を実行している worker ノードにエージェントをインストールするには、セクション「[Windows コンテナー ホストをインストールして構成する](#install-and-configure-windows-container-hosts)」の手順に従います。

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Helm を使用して Linux Kubernetes に Log Analytics エージェントをデプロイする

Helm を使用して Linux Kubernetes 環境内に Log Analytics エージェントをデプロイするには、次の手順を実行します。

1. ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms``` を実行して、omsagent daemon-set を作成します。
2. 結果は次のようになります。

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```

3. ```helm status "omsagent"``` を実行して omsagent の状態を確認できます。出力は次のようになります。

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   
    詳細については、[コンテナー ソリューション Helm チャート](https://aka.ms/omscontainerhelm)に関するページを参照してください。

### <a name="install-and-configure-windows-container-hosts"></a>Windows コンテナー ホストをインストールして構成する

セクション内の情報を使用して、Windows コンテナー ホストをインストールして構成します。

#### <a name="preparation-before-installing-windows-agents"></a>Windows エージェントをインストールする前の準備

Windows を実行しているコンピューターにエージェントをインストールする前に、Docker サービスを構成する必要があります。 構成により、Windows エージェントまたは Azure Monitor 仮想マシン拡張機能は、エージェントが Docker デーモンにリモートでアクセスできるように Docker TCP ソケットを使用できるようになり、監視用のデータをキャプチャすることが可能になります。

##### <a name="to-configure-the-docker-service"></a>Docker サービスを構成するには  

次の PowerShell コマンドを実行して、Windows Server の TCP パイプと名前付きパイプを有効化します。

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Windows コンテナーで使用する Docker デーモン構成の詳細については、「[Windows 上の Docker エンジン](/virtualization/windowscontainers/manage-docker/configure-docker-daemon)」をご覧ください。

#### <a name="install-windows-agents"></a>Windows エージェントのインストール

Windows および Hyper-V コンテナーの監視を有効にするには、コンテナー ホストである Windows コンピューターに Microsoft Monitoring Agent (MMA) をインストールします。 Windows を実行しているオンプレミス環境のコンピューターの場合は、[Windows コンピューターの Azure Monitor への接続](../agents/agent-windows.md)に関するページを参照してください。 Azure で実行されている仮想マシンの場合は、[仮想マシン拡張機能](../vm/quick-collect-azurevm.md)を使用して Azure Monitor に接続します。

Service Fabric で実行されている Windows コンテナーを監視できます。 ただし、現在 Service Fabric でサポートされているのは、[Azure で実行される仮想マシン](../vm/quick-collect-azurevm.md)と[オンプレミス環境で Windows を実行するコンピューター](../agents/agent-windows.md)のみです。

Windows でコンテナー監視ソリューションが正しく設定されていることを確認できます。 管理パックが正常にダウンロードされているかどうかを確認するには、*ContainerManagement.xxx* を探します。 ファイルは C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs フォルダーにあります。

## <a name="solution-components"></a>ソリューションのコンポーネント

Azure ポータルから *[ソリューション ギャラリー]* に移動し、 **[コンテナー監視ソリューション]** を追加します。 Windows エージェントを使用している場合、このソリューションを追加するときに、各コンピューターにエージェントと共に次の管理パックがインストールされます。 この管理パックは構成や保守が不要です。

- *ContainerManagement.xxx* は、C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs にインストールされます。

## <a name="container-data-collection-details"></a>コンテナーのデータ収集の詳細

コンテナー監視ソリューションでは、有効化されたエージェントを使用して、コンテナー ホストとコンテナーからさまざまなパフォーマンス メトリックとログ データを収集します。

データは、次のエージェントの種類によって 3 分ごとに収集されます。

- [Log Analytics エージェント for Linux](../vm/quick-collect-linux-computer.md)
- [Windows エージェント](../agents/agent-windows.md)
- [Log Analytics VM 拡張機能](../vm/quick-collect-azurevm.md)

### <a name="container-records"></a>コンテナー レコード

次の表に、コンテナー監視ソリューションによって収集されるレコードと、ログ検索結果に表示されるデータ型の例を示します。

| データ型 | ログ検索のデータ型 | フィールド |
| --- | --- | --- |
| ホストとコンテナーのパフォーマンス | `Perf` | Computer、ObjectName、CounterName (%Processor Time、Disk Reads MB、Disk Writes MB、Memory Usage MB、Network Receive Bytes、Network Send Bytes、Processor Usage sec、Network)、CounterValue、TimeGenerated、CounterPath、SourceSystem |
| コンテナー インベントリ | `ContainerInventory` | TimeGenerated、Computer、container name、ContainerHostname、Image、ImageTag、ContainerState、ExitCode、EnvironmentVar、Command、CreatedTime、StartedTime、FinishedTime、SourceSystem、ContainerID、ImageID |
| コンテナー イメージ インベントリ | `ContainerImageInventory` | TimeGenerated、Computer、Image、ImageTag、ImageSize、VirtualSize、Running、Paused、Stopped、Failed、SourceSystem、ImageID、TotalContainer |
| コンテナー ログ | `ContainerLog` | TimeGenerated、Computer、image ID、container name、LogEntrySource、LogEntry、SourceSystem、ContainerID |
| コンテナー サービス ログ | `ContainerServiceLog`  | TimeGenerated、Computer、TimeOfCommand、Image、Command、SourceSystem、ContainerID |
| コンテナー ノード インベントリ | `ContainerNodeInventory_CL`| TimeGenerated、Computer、ClassName_s、DockerVersion_s、OperatingSystem_s、Volume_s、Network_s、NodeRole_s、OrchestratorType_s、InstanceID_g、SourceSystem|
| Kubernetes インベントリ | `KubePodInventory_CL` | TimeGenerated、Computer、PodLabel_deployment_s、PodLabel_deploymentconfig_s、PodLabel_docker_registry_s、Name_s、Namespace_s、PodStatus_s、PodIp_s、PodUid_g、PodCreationTimeStamp_t、SourceSystem |
| コンテナー プロセス | `ContainerProcess_CL` | TimeGenerated、Computer、Pod_s、Namespace_s、ClassName_s、InstanceID_s、Uid_s、PID_s、PPID_s、C_s、STIME_s、Tty_s、TIME_s、Cmd_s、Id_s、Name_s、SourceSystem |
| Kubernetes イベント | `KubeEvents_CL` | TimeGenerated、Computer、Name_s、ObjectKind_s、Namespace_s、Reason_s、Type_s、SourceComponent_s、SourceSystem、Message |

*PodLabel* データ型に付加されるラベルは、独自のカスタム ラベルです。 表に示されている、付加された PodLabel ラベルは一例です。 そのため、`PodLabel_deployment_s`、`PodLabel_deploymentconfig_s`、`PodLabel_docker_registry_s` はご利用の環境のデータ セットによって異なり、一般的には `PodLabel_yourlabel_s` に似ています。

## <a name="monitor-containers"></a>コンテナーの監視
Azure portal でソリューションを有効化すると、コンテナー ホストと、ホストで実行されているコンテナーに関する概要情報が **[コンテナー]** タイルに表示されます。

![[コンテナー] タイル](./media/containers/containers-title.png)

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

![収集されたデータを表示するためのダッシュボードを示すスクリーンショット。 ](./media/containers/containers-dash01.png)

![コンテナー ダッシュボード](./media/containers/containers-dash02.png)

**[コンテナーの状態]** エリアで、上部のエリアをクリックすると次のように表示されます。

![コンテナーの状態](./media/containers/containers-status.png)

Log Analytics が開き、コンテナーの状態に関する情報が表示されます。

![コンテナーの Log Analytics](./media/containers/containers-log-search.png)

ここで検索クエリを編集して、関心のある情報のみが見つかるように変更できます。 ログ クエリの詳細については、[Azure Monitor でのログ クエリ](../logs/log-query-overview.md)に関するページを参照してください。

## <a name="troubleshoot-by-finding-a-failed-container"></a>失敗したコンテナーを特定してトラブルシューティングを行う

ゼロ以外の終了コードで終了したコンテナーは、Log Analytics によって **[失敗]** とマークされます。 **[失敗したコンテナー]** エリアで、環境におけるエラーと失敗の概要を確認できます。

### <a name="to-find-failed-containers"></a>失敗したコンテナーを特定するには

1. **[コンテナーの状態]** エリアをクリックします。  
   ![コンテナーの状態](./media/containers/containers-status.png)
2. Log Analytics が開き、次のようにコンテナーの状態が表示されます。  
   ![コンテナーの状態](./media/containers/containers-log-search.png)
3. Failed 行を展開し、[+] をクリックして、クエリにその条件を追加します。 その後、クエリで Summarize 行をコメント アウトします。
   ![コメント アウトする必要がある行を示すスクリーンショット。](./media/containers/containers-state-failed-select.png)  
1. クエリを実行し、結果内の行を展開して、イメージ ID を表示します。  
   ![イメージ ID を表示する方法を示すスクリーンショット。](./media/containers/containers-state-failed.png)  
1. ログ クエリに以下を入力します。 `ContainerImageInventory | where ImageID == <ImageID>` により、停止したイメージと失敗したイメージのサイズと数など、イメージに関する詳細が表示されます。  
   ![失敗したコンテナー](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>コンテナー データのログをクエリする

特定のエラーのトラブルシューティングを実行する際には、環境のどこでそのエラーが発生しているのかを確認すると役立つ場合があります。 次のログの種類は、目的の情報を返すクエリを作成するうえで役立ちます。

- **ContainerImageInventory** – この種類は、イメージ別に整理された情報を見つける場合や、イメージの ID やサイズなどのイメージ情報を確認する場合に使用します。
- **ContainerInventory** – この種類は、コンテナーの場所、コンテナーの名前、実行中のイメージに関する情報が必要な場合に使用します。
- **ContainerLog** – この種類は、特定のエラー ログの情報やエントリを見つける場合に使用します。
- **ContainerNodeInventory_CL** – コンテナーが置かれているホスト/ノードに関する情報が必要な場合に使用します。 これにより、Docker のバージョン、オーケストレーションの種類、ストレージ、ネットワーク情報を取得できます。
- **ContainerProcess_CL** – コンテナー内で実行中のプロセスをすぐに確認したいときに使用します。
- **ContainerServiceLog** – この種類は、開始、停止、削除、プルのコマンドなど、Docker デーモンの監査証跡情報を見つける場合に使用します。
- **KubeEvents_CL** – Kubernetes イベントを確認するときに使用します。
- **KubePodInventory_CL** – クラスター階層に関する情報を把握したいときに使用します。

### <a name="to-query-logs-for-container-data"></a>コンテナー データのログをクエリするには

* 最近失敗したことがわかっているイメージを選択し、そのエラー ログを見つけます。 まず、**ContainerInventory** 検索で、そのイメージを実行しているコンテナー名を特定します。 たとえば、`ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"` を検索します。  
    ![Ubuntu コンテナーの検索](./media/containers/search-ubuntu.png)

  結果内の任意の行を展開すると、そのコンテナーの詳細が表示されます。

## <a name="example-log-queries"></a>ログ クエリの例

クエリの作成の際には、多くの場合、1 ～ 2 個の例で始め、その後環境に合わせて変更するとうまくいきます。 出発点として、ソリューション ページの右端にある **[サンプル クエリ]** 領域で実験を行うと、より高度なクエリを作成するのに役立ちます。

![コンテナーのクエリ](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>ログ クエリの保存

クエリの保存は、Azure Monitor の標準的な機能です。 クエリを保存しておけば、後で使えるように、便利なクエリを取っておくことができます。

作成したクエリが便利であることがわかったら、 **[ログ検索]** ページの上部にある [お気に入り] をクリックして保存してください。 後で **[マイ ダッシュボード]** ページで簡単にアクセスできます。

## <a name="next-steps"></a>次のステップ

[ログをクエリ](../logs/log-query-overview.md)して、詳細なコンテナー データ レコードを確認します。

