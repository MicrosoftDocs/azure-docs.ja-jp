---
title: Azure Stack Edge Pro GPU デバイス上の Arc 対応 Kubernetes で PHP `Guestbook` アプリをデプロイする | Microsoft Docs
description: Azure Stack Edge Pro デバイスの Arc 対応 Kubernetes クラスターで GitOps を使用して、Redis を使った PHP `Guestbook` ステートレス アプリケーションをデプロイする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 920f7912a1289fe92618d893b94943784e4a9a3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520729"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU 上の Arc 対応 Kubernetes クラスターで Redis を使用した PHP `Guestbook` ステートレス アプリケーションをデプロイする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Kubernetes と Azure Arc を使用してシンプルな多層 Web アプリケーションを構築してデプロイする方法について説明します。この例は、次のコンポーネントで構成されています。

- `guestbook`のエントリを格納する単一インスタンスの Redis マスター
- 読み取りに対応する複数の複製された Redis インスタンス
- 複数の Web フロントエンド インスタンス

デプロイは、Azure Stack Edge Pro デバイス上の Arc 対応 Kubernetes クラスターで GitOps を使用して行われます。 

この手順は、[Azure Stack Edge Pro デバイス上の Kubernetes ワークロード](azure-stack-edge-gpu-kubernetes-workload-management.md)に関する記事を確認し、[Azure Arc 対応 Kubernetes (プレビュー)](../azure-arc/kubernetes/overview.md) の概念を理解しているユーザーを対象としています。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "スレーブ" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

## <a name="prerequisites"></a>前提条件

ステートレス アプリケーションをデプロイする前に、お使いのデバイスと、そのデバイスへのアクセスに使用するクライアントで、次の前提条件が満たされていることを確認してください。

> [!NOTE]
> この記事には、Microsoft が使用しなくなった "スレーブ" という用語への言及が含まれています。 ソフトウェアからこの用語が削除された時点で、この記事から削除します。

### <a name="for-device"></a>デバイスでは

1. 1 ノードの Azure Stack Edge Pro デバイスに対するサインイン資格情報がある。
    1. デバイスがアクティブ化されている。 [デバイスのアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関する記事を参照してください。
    1. デバイスに、Azure portal を使用して構成されたコンピューティング ロールがあり、Kubernetes クラスターがある。 [コンピューティングの構成](azure-stack-edge-gpu-deploy-configure-compute.md)に関する記事を参照してください。

1. お使いのデバイス上の既存の Kubernetes クラスターで Azure Arc が有効になっていて、対応する Azure Arc リソースが Azure portal にある。 詳細な手順については、[Azure Stack Edge Pro デバイスでの Azure Arc の有効化](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)に関するページを参照してください。

### <a name="for-client-accessing-the-device"></a>デバイスにアクセスするクライアントの場合

1. Azure Stack Edge Pro デバイスへのアクセスに使用される Windows クライアント システムがある。
  
    - クライアントでは、Windows PowerShell 5.0 以降が実行されている。 Windows PowerShell の最新バージョンをダウンロードするには、「[Windows PowerShell のインストール](/powershell/scripting/install/installing-windows-powershell)」を参照してください。
    
    - [サポートされているオペレーティング システム](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)が搭載されている他のクライアントを使用することもできます。 この記事では、Windows クライアントを使用する場合の手順について説明します。 
    
1. [Azure Stack Edge Pro デバイス上の Kubernetes クラスターへのアクセス](azure-stack-edge-gpu-create-kubernetes-cluster.md)に関する記事で説明されている手順を完了している。 完了した内容:
    
    - クライアントに `kubectl` がインストールされている。 <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - `kubectl` クライアントのバージョンと、Azure Stack Edge Pro デバイスで実行されている Kubernetes マスターのバージョンの差が 1 未満であることを確認する。 
      - クライアントで実行されている kubectl のバージョンを確認するには、`kubectl version` を使用します。 完全なバージョン番号をメモしておきます。
      - お使いの Azure Stack Edge Pro デバイスのローカル UI で、 **[概要]** に移動し、Kubernetes ソフトウェアの番号をメモします。 
      - サポートされている Kubernetes バージョンで提供されているマッピングで、これら 2 つのバージョンの互換性を確認します。 <!--insert link-->

1. [Azure Arc デプロイの実行に使用できる GitOps 構成](https://github.com/kagoyal/dbehaikudemo)がある。 この例では、次の `yaml` ファイルを使用して Azure Stack Edge Pro デバイスにデプロイします。

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>構成のデプロイ

Azure portal で次の手順を実行して、GitOps 構成をデプロイするように Azure Arc リソースを構成します。 

1. Azure portal で、お使いのデバイス上の Kubernetes クラスターで Azure Arc を有効にしたときに作成した Azure Arc リソースに移動します。 

    ![Azure Arc リソースに移動する](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. **[構成]** に移動し、 **[+ 構成の追加]** を選択します。

    ![[構成の追加] が選択された Azure Arc 対応 Kubernetes クラスターを示すスクリーンショット。](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. **[構成の追加]** で、フィールドに適切な値を入力し、 **[適用]** を選択します。

    |パラメーター  |説明 |
    |---------|---------|
    |構成名     | 構成リソースの名前。        |
    |演算子のインスタンス名     |特定の構成を識別する演算子のインスタンス名。 名前は最大 253 文字の文字列で、使用できるのは小文字、英数字、ハイフン、およびピリオドのみです。         |
    |演算子の名前空間     | **demotestguestbook** に設定して、デプロイ `yaml` で指定されている名前空間と一致させます。 <br> このフィールドでは、演算子がインストールされる名前空間を定義します。 名前は最大 253 文字の文字列で、使用できるのは小文字、英数字、ハイフン、およびピリオドのみです。         |
    |リポジトリの URL     |<br>GitOps 構成が配置されている Git リポジトリのパス (`http://github.com/username/repo` または `git://github.com/username/repo` 形式)。         |
    |演算子のスコープ     | **[名前空間]** を選択します。 <br>このパラメーターは、演算子がインストールされるスコープを定義します。 デプロイ yaml ファイルで指定された名前空間に演算子をインストールするために [名前空間] を選択します。       |
    |演算子の種類     | 既定のままにします。 <br>このパラメーターは、演算子の種類を指定します (既定で flux として設定されます)。        |
    |演算子のパラメーター     | 空白のままにします。 <br>このパラメーターには、flux 演算子に渡すパラメーターが含まれます。        |
    |Helm     | このパラメーターを **[無効]** に設定します。 <br>グラフ ベースのデプロイを行う場合は、このオプションを有効にします。        |


    ![構成を追加する](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. 構成のデプロイが開始され、 **[Operator state]\(演算子の状態\)** が **[Pending]\(保留中\)** と表示されます。 

    ![更新時に保留中の状態にある Azure Arc 対応 Kubernetes クラスターを示すスクリーンショット。](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. デプロイには数分かかります。 デプロイが完了すると、 **[Operator state]\(演算子の状態\)** が **[インストール]** と表示されます。

    ![インストール済み状態の Azure Arc 対応 Kubernetes クラスターを示すスクリーンショット。](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>デプロイを検証する

GitOps 構成を使用したデプロイにより、Git リポジトリにあるデプロイ `yaml` ファイルで指定されているように `demotestguestbook` 名前空間が作成されます。

1. GitOps 構成を適用した後、[デバイスの PowerShell インターフェイスに接続](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)します。
1. 次のコマンドを実行して、デプロイに対応する `demotestguestbook` 名前空間で実行されているポッドを一覧表示します。

    `kubectl get pods -n <your-namespace>`
  
    出力例を次に示します。
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. この例では、フロントエンド サービスは type:LoadBalancer としてデプロイされています。 `guestbook`を表示するには、このサービスの IP アドレスを調べる必要があります。 次のコマンドを実行します。

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. `type:LoadBalancer` のフロントエンド サービスには、外部 IP アドレスがあります。 この IP は、デバイスでコンピューティング ネットワーク設定を構成するときに外部サービスに指定した IP アドレス範囲に含まれています。 この IP アドレスを使用して、`https://<external-IP-address>` という URL で`guestbook`を表示します。

    ![ゲストブックを表示する](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>展開の削除

デプロイを削除するには、Azure portal から構成を削除できます。 構成を削除すると、作成されたオブジェクトがデプロイとサービスを含めて削除されます。

1. Azure portal で、Azure Arc リソース > [構成] に移動します。 
1. 削除する構成を見つけます。 [...] を選択してコンテキスト メニューを呼び出し、 **[削除]** を選択します。
    ![構成を削除する](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

構成が削除されるまで数分かかる場合があります。
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>次のステップ

[Kubernetes ダッシュボードを使用して Azure Stack Edge Pro デバイス上のデプロイを監視する](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)方法について学習する