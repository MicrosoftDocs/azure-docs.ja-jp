---
title: kubectl を使用して Azure Stack Edge Pro GPU デバイスに Kubernetes ステートレス アプリケーションをデプロイする | Microsoft Docs
description: Microsoft Azure Stack Edge Pro デバイスで kubectl を使用して、Kubernetes ステートレス アプリケーションのデプロイを作成および管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: fd55ce702ae3c9485fc2dcc37fc90915a8990ce7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102637059"
---
# <a name="deploy-a-kubernetes-stateless-application-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスで kubectl を使用して Kubernetes ステートレス アプリケーションをデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、既存の Kubernetes クラスターで kubectl コマンドを使用して、ステートレス アプリケーションをデプロイする方法について説明します。 また、この記事では、ステートレス アプリケーションでポッドを作成および設定するプロセスについて説明します。

## <a name="prerequisites"></a>前提条件

Kubernetes クラスターを作成し、`kubectl` コマンドライン ツールを使用する前に、確実に次のようになっている必要があります。

- 1 ノードの Azure Stack Edge Pro デバイスに対するサインイン資格情報がある。

- Azure Stack Edge Pro デバイスにアクセスするために、Windows クライアント システムに Windows PowerShell 5.0 以降がインストールされている。 オペレーティング システムがサポートされている他のクライアントを使用することもできます。 この記事では、Windows クライアントを使用する場合の手順について説明します。 最新バージョンの Windows PowerShell をダウンロードするには、[Windows への PowerShell のインストール](/powershell/scripting/install/installing-windows-powershell)に関するページに移動します。

- Azure Stack Edge Pro デバイスでコンピューティングが有効になっている。 コンピューティングを有効にするには、デバイスのローカル UI で **[コンピューティング]** ページに移動します。 その後、コンピューティングに対して有効にするネットワーク インターフェイスを選択します。 **[有効化]** を選択します。 コンピューティングを有効にすると、そのネットワーク インターフェイスでデバイスの仮想スイッチが作成されます。 詳細については、[Azure Stack Edge Pro でのコンピューティング ネットワークの有効化](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)に関するページを参照してください。

- Azure Stack Edge Pro デバイスで、バージョン v1.9 以降の Kubernetes クラスター サーバーが実行されている。 詳細については、[Microsoft Azure Stack Edge Pro デバイスでの Kubernetes クラスターの作成と管理](azure-stack-edge-gpu-create-kubernetes-cluster.md)に関するページを参照してください。

- `kubectl` をインストールしている。

## <a name="deploy-a-stateless-application"></a>ステートレス アプリケーションをデプロイする

作業を開始する前に、次のようになっている必要があります。

1. Kubernetes クラスターが作成されている。
2. 名前空間が設定されている。
3. ユーザーと名前空間が関連付けられている。
4. ユーザー構成が `C:\Users\<username>\.kube` に保存されている。
5. `kubectl` がインストールされている。

これで、Azure Stack Edge Pro デバイスでのステートレス アプリケーションのデプロイの実行と管理を開始できるようになりました。 `kubectl` の使用を開始する前に、正しいバージョンの `kubectl` があることを確認する必要があります。

### <a name="verify-you-have-the-correct-version-of-kubectl-and-set-up-configuration"></a>正しいバージョンの kubectl があり、構成が設定されていることを確認する

`kubectl` のバージョンを確認するには、次のようにします。

1. `kubectl` のバージョンが 1.9 以上であることを確認します。

   ```powershell
   kubectl version
   ```
    
   出力の例を次に示します。
    
   ```powershell
   PS C:\WINDOWS\system32> C:\windows\system32\kubectl.exe version
   Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
   Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
   ```

   この場合、kubectl のクライアント バージョンは v1.15.2 であり、互換性があるため、先に進みます。

2. Kubernetes クラスターで実行されているポッドの一覧を取得します。 ポッドは、Kubernetes クラスターで実行されるアプリケーション コンテナー (つまり、プロセス) です。

   ```powershell
   kubectl get pods -n <namespace-string>
   ```
    
   コマンドの使用例を次に示します。
    
   ```powershell
   PS C:\WINDOWS\system32> kubectl get pods -n "test1"
   No resources found.
   PS C:\WINDOWS\system32>
   ```
    
   クラスターで実行されているアプリケーションが存在しないため、出力にはリソース (ポッド) が見つからないことが示されるはずです。

   このコマンドを実行すると、"C:\Users\\&lt;ユーザー名&gt;\\.kube\" のディレクトリ構造に構成ファイルが設定されます。 Kubernetes クラスターでステートレス アプリケーションを作成および管理するために、kubectl コマンドライン ツールでこれらのファイルが使用されます。  

3. "C:\Users\\&lt;ユーザー名&gt;\\.kube\" のディレクトリ構造を手動で確認し、*kubectl* によって次のサブフォルダーが設定されていることを確かめます。

   ```powershell
   PS C:\Users\username> ls .kube
    
    
      Directory: C:\Users\user\.kube
    
   Mode                LastWriteTime         Length Name
   ----                -------------         ------ ----
   d-----         2/18/2020 11:05 AM                cache
   d-----         2/18/2020 11:04 AM                http-cache
   -a----         2/18/2020 10:41 AM           5377 config
   ```

> [!NOTE]
> すべての kubectl コマンドの一覧を表示するには、「`kubectl --help`」と入力します。

### <a name="create-a-stateless-application-using-a-deployment"></a>デプロイを使用してステートレス アプリケーションを作成する

kubectl コマンドラインのバージョンが正しいことと、必要な構成ファイルがあることを確認したので、ステートレス アプリケーションのデプロイを作成できます。

ポッドは、Kubernetes アプリケーションの基本的な実行単位であり、作成またはデプロイする Kubernetes オブジェクト モデルでの最も小さくてシンプルな単位です。 また、ポッドにより、ストレージ リソース、一意のネットワーク IP、およびコンテナーの実行方法を制御するオプションがカプセル化されます。

作成するステートレス アプリケーションの種類は、nginx Web サーバーのデプロイです。

ステートレス アプリケーションのデプロイを作成および管理するために使用するすべての kubectl コマンドで、構成に関連付けられている名前空間を指定する必要があります。 この名前空間は、`New-HcsKubernetesNamespace` を使用して、[Microsoft Azure Stack Edge Pro デバイスでの Kubernetes クラスターの作成と管理](azure-stack-edge-gpu-create-kubernetes-cluster.md)に関するチュートリアルで Azure Stack Edge Pro デバイス上のクラスターに接続しているときに作成したものです。

kubectl コマンドで名前空間を指定するには、`kubectl <command> -n <namespace-string>` を使用します。

これらの手順に従って、nginx デプロイを作成します。

1. Kubernetes デプロイ オブジェクトを作成することによって、ステートレス アプリケーションを適用します。

   ```powershell
   kubectl apply -f <yaml-file> -n <namespace-string>
   ```

   この例では、アプリケーションの YAML ファイルへのパスは外部ソースです。

   コマンドの使用法と出力の例を以下に示します。

   ```powershell
   PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment.yaml -n "test1"
    
   deployment.apps/nginx-deployment created
   ```

   次のマークダウンをローカル コンピューターに保存し、 *-f* パラメーターでパスとファイル名を置き換えることもできます。 たとえば、"C:\Kubernetes\deployment.yaml" のようになります。 アプリケーションのデプロイの構成を以下に示します。

   ```markdown
   apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
   kind: Deployment
   metadata:
     name: nginx-deployment
   spec:
     selector:
       matchLabels:
         app: nginx
     replicas: 2 # tells deployment to run 2 pods matching the template
     template:
       metadata:
         labels:
           app: nginx
       spec:
         containers:
         - name: nginx
           image: nginx:1.7.9
           ports:
           - containerPort: 80
   ```

   このコマンドにより、アプリケーションを実行する 2 つのポッドがあるデフォルト nginx-deployment が作成されます。

2. 作成した Kubernetes の nginx-deployment の説明を取得します。

   ```powershell
   kubectl describe deployment nginx-deployment -n <namespace-string>
   ```

   コマンドの使用法を出力と共に次に示します。
    
   ```powershell
   PS C:\Users\user> kubectl describe deployment nginx-deployment -n "test1"
    
   Name:                   nginx-deployment
   Namespace:              test1
   CreationTimestamp:      Tue, 18 Feb 2020 13:35:29 -0800
   Labels:                 <none>
   Annotations:            deployment.kubernetes.io/revision: 1
                           kubectl.kubernetes.io/last-applied-configuration:
                             {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"nginx-deployment","namespace":"test1"},"spec":{"repl...
   Selector:               app=nginx
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   StrategyType:           RollingUpdate
   MinReadySeconds:        0
   RollingUpdateStrategy:  25% max unavailable, 25% max surge
   Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.7.9
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
   Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
   OldReplicaSets:  <none>
   NewReplicaSet:   nginx-deployment-5754944d6c (2/2 replicas created)
   Events:
     Type    Reason             Age    From                   Message
     ----    ------             ----   ----                   -------
     Normal  ScalingReplicaSet  2m22s  deployment-controller  Scaled up replica set nginx-deployment-5754944d6c to 2
   ```

   "*レプリカ*" の設定では、次のようになっているのがわかります。
    
   ```powershell
   Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
   ```

   "*レプリカ*" の設定は、デプロイの仕様で 2 つのポッドが求められたこと、それらのポッドが作成および更新されたこと、およびそれらが使用できる状態になっていることを示しています。

   > [!NOTE]
   > デバイス ノードで障害が発生した場合や、デバイスのアップグレードが中断された場合など、何らかの理由で削除または終了されたポッドがレプリカ セットによって置き換えられます。 このため、アプリケーションに必要なポッドが 1 つだけの場合でも、レプリカ セットを使用することをお勧めします。

3. デプロイ内のポッドを一覧表示するには、次のようにします。

   ```powershell
   kubectl get pods -l app=nginx -n <namespace-string>
   ```
    
   コマンドの使用法を出力と共に次に示します。
    
   ```powershell
   PS C:\Users\user> kubectl get pods -l app=nginx -n "test1"
    
   NAME                                READY   STATUS    RESTARTS   AGE
   nginx-deployment-5754944d6c-7wqjd   1/1     Running   0          3m13s
   nginx-deployment-5754944d6c-nfj2h   1/1     Running   0          3m13s
   ```

   この出力で、kubectl を使用して参照できる一意の名前を持つ 2 つのポッドがあることを確認できます。

4. デプロイ内の個々のポッドに関する情報を表示するには、次のようにします。

   ```powershell
   kubectl describe pod <podname-string> -n <namespace-string>
   ```

  コマンドの使用法を出力と共に次に示します。

   ```powershell
   PS C:\Users\user> kubectl describe pod "nginx-deployment-5754944d6c-7wqjd" -n "test1"

   Name:           nginx-deployment-5754944d6c-7wqjd
   Namespace:      test1
   Priority:       0
   Node:           k8s-1d9qhq2cl-n1/10.128.46.184
   Start Time:     Tue, 18 Feb 2020 13:35:29 -0800
   Labels:         app=nginx
                   pod-template-hash=5754944d6c
   Annotations:    <none>
   Status:         Running
   IP:             172.17.246.200
   Controlled By:  ReplicaSet/nginx-deployment-5754944d6c
    Containers:
      nginx:
        Container ID:   docker://280b0f76bfdc14cde481dc4f2b8180cf5fbfc90a084042f679d499f863c66979
        Image:          nginx:1.7.9
        Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
        Port:           80/TCP
        Host Port:      0/TCP
        State:          Running
          Started:      Tue, 18 Feb 2020 13:35:35 -0800
        Ready:          True
        Restart Count:  0
        Environment:    <none>
        Mounts:
          /var/run/secrets/kubernetes.io/serviceaccount from default-token-8gksw (ro)
    Conditions:
      Type              Status
      Initialized       True
      Ready             True
      ContainersReady   True
      PodScheduled      True
    Volumes:
      default-token-8gksw:
        Type:        Secret (a volume populated by a Secret)
        SecretName:  default-token-8gksw
        Optional:    false
    QoS Class:       BestEffort
    Node-Selectors:  <none>
    Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                     node.kubernetes.io/unreachable:NoExecute for 300s
    Events:
      Type    Reason     Age    From                       Message
      ----    ------     ----   ----                       -------
      Normal  Scheduled  4m58s  default-scheduler          Successfully assigned test1/nginx-deployment-5754944d6c-7wqjd to k8s-1d9qhq2cl-n1
      Normal  Pulling    4m57s  kubelet, k8s-1d9qhq2cl-n1  Pulling image "nginx:1.7.9"
      Normal  Pulled     4m52s  kubelet, k8s-1d9qhq2cl-n1  Successfully pulled image "nginx:1.7.9"
      Normal  Created    4m52s  kubelet, k8s-1d9qhq2cl-n1  Created container nginx
      Normal  Started    4m52s  kubelet, k8s-1d9qhq2cl-n1  Started container nginx
   ```

### <a name="rescale-the-application-deployment-by-increasing-the-replica-count"></a>レプリカ数を増やしてアプリケーションのデプロイを再スケーリングする

各ポッドは、特定のアプリケーションの 1 つのインスタンスを実行することを意図しています。 複数のインスタンスを実行するようにアプリケーションを水平方向にスケーリングする場合は、ポッドの数をインスタンスごとに 1 つずつ増やすことができます。 Kubernetes では、これをレプリケーションと呼びます。
新しい YAML ファイルを適用することによって、アプリケーションのデプロイでポッドの数を増やすことができます。 YAML ファイルを使用して、レプリカの設定を 4 に変更します。これにより、デプロイのポッドの数が増え、4 ポッドになります。 ポッドの数を 2 から 4 に増やすには、次のようにします。

```powershell
PS C:\WINDOWS\system32> kubectl apply -f https://k8s.io/examples/application/deployment-scale.yaml -n "test1"
```

次のマークダウンをローカル コンピューターに保存し、`kubectl apply` の *-f* パラメーターのパスとファイル名に置き換えることもできます。 たとえば、"C:\Kubernetes\deployment-scale.yaml" のようになります。 アプリケーションのデプロイ スケールの構成は次のとおりです。

```markdown
apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 4 # Update the replicas from 2 to 4
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.8
        ports:
        - containerPort: 80
```

デプロイに 4 つのポッドがあることを確認するには、次のようにします。

```powershell
kubectl get pods -l app=nginx
```

デプロイを再スケーリングして、ポッドを 2 つから 4 つにする場合の出力例を次に示します。

```powershell
PS C:\WINDOWS\system32> kubectl get pods -l app=nginx

NAME                               READY     STATUS    RESTARTS   AGE
nginx-deployment-148880595-4zdqq   1/1       Running   0          25s
nginx-deployment-148880595-6zgi1   1/1       Running   0          25s
nginx-deployment-148880595-fxcez   1/1       Running   0          2m
nginx-deployment-148880595-rwovn   1/1       Running   0          2m
```

出力からわかるように、現在、デプロイ内には、アプリケーションを実行できる 4 つのポッドがあります。

### <a name="delete-a-deployment"></a>デプロイを削除する

すべてのポッドを含む、デプロイを削除するには、デプロイ *nginx-deployment* の名前と名前空間名を指定して、`kubectl delete deployment` を実行する必要があります。 デプロイを削除するには、次のようにします。

   ```powershell
   kubectl delete deployment nginx-deployment -n <namespace-string>
   ```

コマンドの使用例を出力と共に次に示します。

```powershell
PS C:\Users\user> kubectl delete deployment nginx-deployment -n "test1"
deployment.extensions "nginx-deployment" deleted
```

## <a name="next-steps"></a>次のステップ

[Kubernetes の概要](azure-stack-edge-gpu-kubernetes-overview.md)