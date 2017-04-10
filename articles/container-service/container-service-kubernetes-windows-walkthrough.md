---
title: "Windows 用 Azure Kubernetes クラスター | Microsoft Docs"
description: "Azure Container Service で Windows コンテナー用 Kubernetes クラスターをデプロイして使ってみます"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: eb3af43b8a13eaaebfa9147848383ff889119d97
ms.lasthandoff: 04/03/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>Container Service で Kubernetes と Windows コンテナーを使用する


この記事では、Windows ノードを含む Azure Container Service で Kubernetes クラスターを作成し、Windows コンテナーを実行する方法について説明します。 

> [!NOTE]
> Azure Container Service における Kubernetes での Windows コンテナーのサポートはプレビュー段階です。 Azure Portal または Resource Manager テンプレートを使用して、Windows ノードを含む Kubernetes クラスターを作成します。 この機能は、現在 Azure CLI 2.0 ではサポートされていません。
>



次の図は、1 つの Linux マスター ノードと 2 つの Windows エージェント ノードを含む、Azure Container Service の Kubernetes クラスターのアーキテクチャを示しています。 

* Linux マスターは Kubernetes REST API を提供し、ポート 22 の SSH またはポート 443 の `kubectl` でアクセスできます。 
* Windows エージェント ノードは、Azure 可用性セットにグループ化され、コンテナーを実行します。 Windows ノードには、マスター ノードを介して RDP SSH トンネル経由でアクセスできます。 Azure Load Balancer ルールは、公開されているサービスに応じてクラスターに動的に追加されます。


![Azure 上の Kubernetes クラスターの図](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

すべての VM は同一のプライベート仮想ネットワーク内にあり、完全な相互アクセスが可能です。 すべての VM で、kubelet、Docker、およびプロキシが実行されます。

## <a name="prerequisites"></a>前提条件


* **SSH RSA 公開キー**: ポータルまたはいずれかの Azure クイックスタート テンプレートを通じてデプロイする場合は、Azure Container Service 仮想マシンに対する認証のための SSH RSA 公開キーを指定する必要があります。 Secure Shell (SSH) RSA キーを作成するには、[OS X と Linux](../virtual-machines/linux/mac-create-ssh-keys.md) または [Windows](../virtual-machines/linux/ssh-from-windows.md) のガイダンスを参照してください。 

* **サービス プリンシパル クライアント ID とシークレット**: 詳細とガイダンスについては、[Kubernetes クラスターのサービス プリンシパル](container-service-kubernetes-service-principal.md)に関するページをご覧ください。




## <a name="create-the-cluster"></a>クラスターを作成する

Azure Portal を使用して、Windows エージェント ノードを含む [Kubernetes クラスターを作成](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal)できます。 クラスターを作成するときの設定を次に示します。

* **[基本]** ブレードの **[Orchestrator]** で **[Kubernetes]** を選択します。 

  ![Kubernetes オーケストレーターを選択する](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* **[Master configuration (マスター構成)]** ブレードで、Linux マスター ノードに対するユーザーの資格情報とサービス プリンシパルの資格情報を入力します。 マスター数は 1、3、または 5 を選択します。

* **[エージェント構成]** ブレードの **[オペレーティング システム]** で、**[Windows (プレビュー)]** を選択します。 Windows エージェント ノードの管理者の資格情報を入力します。

  ![Windows エージェントを選択する](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

詳細については、[Azure Container Service クラスターのデプロイ](container-service-deployment.md)に関するページをご覧ください。

## <a name="connect-to-the-cluster"></a>クラスターへの接続

`kubectl` コマンド ライン ツールを使用して、ローカル コンピューターから Kubernetes クラスターのマスター ノードに接続します。 `kubectl` をインストールしてセットアップする手順については、[Azure Container Service クラスターへの接続](container-service-connect.md#connect-to-a-kubernetes-cluster)に関するトピックをご覧ください。 `kubectl` コマンドを使用すると、Kubernetes Web UI にアクセスし、Windows コンテナー ワークロードを作成および管理できます。

## <a name="create-your-first-kubernetes-service"></a>最初の Kubernetes サービスの作成

クラスターを作成し、`kubectl` に接続したら、基本的な Windows Web アプリを起動して、インターネットに公開してみます。 この例では、YAML ファイルを使用してコンテナー リソースを指定し、`kubctl apply` を使用して、それを作成します。

1. ノードの一覧を表示するには、「`kubectl get nodes`」と入力します。 ノードの詳細が必要な場合は、次を入力します。  

    ```
    kubectl get nodes -o yaml
    ```

2. `simpleweb.yaml` という名前のファイルを作成し、以下をコピーします。 このファイルにより、[Docker Hub](https://hub.docker.com/r/microsoft/windowsservercore/) から、Windows Server 2016 Server Core のベース OS イメージを使用して Web アプリが設定されます。  

```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add('http://*:80/') ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at http://*:80/') ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

      
> [!NOTE] 
> この構成には `type: LoadBalancer` が含まれます。 この設定により、Azure Load Balancer を介してサービスがインターネットに公開されます。 詳細については、「[Azure Container Service の Kubernetes クラスターのコンテナーで負荷を分散する](container-service-kubernetes-load-balancing.md)」を参照してください。
>

## <a name="start-the-application"></a>アプリケーションの起動

1. アプリケーションを起動するには、次のように入力します。  

    ```
    kubectl apply -f simpleweb.yaml
    ```  
  
  
2. サービスのデプロイを確認するには (約 30 秒かかります)、次のように入力します。  

    ```
    kubectl get pods
    ```

3. サービスの実行後は、サービスの内部および外部 IP アドレスを表示するには、次のように入力します。

    ```
    kubectl get svc
    ``` 
  
    ![Windows サービスの IP アドレス](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

    外部 IP アドレスの追加には数分をかかります。 ロード バランサーによって外部アドレスが構成される前は、`<pending>` のように表示されます。

4. 外部 IP アドレスが使用可能になると、Web ブラウザーでサービスに接続できます。

    ![ブラウザーでの Windows サーバー アプリ](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>Windows ノードへのアクセス
Windows ノードには、リモート デスクトップ接続を使用して、ローカル Windows コンピューターからアクセスできます。 マスター ノード経由で RDP SSH トンネルを使用することをお勧めします。 

Windows では、さまざまな方法で SSH トンネルを作成できます。 このセクションでは、PuTTY を使用してトンネルを作成する方法について説明します。

1. Windows システムに [PuTTY をダウンロード](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)します。

2. アプリケーションを実行します。

3. ホスト名を入力します。ホスト名は、クラスター管理者のユーザー名とクラスターの第 1 マスターのパブリック DNS 名で構成されます。 **[ホスト名]** は `adminuser@PublicDNSName` のようになります。 **[ポート]** に「22」と入力します。

  ![PuTTY configuration 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. **[SSH]、[認証]** の順に選択します。 認証用の秘密キー ファイル (.ppk 形式) のパスを追加します。 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) などのツールを使用すると、クラスターの作成に使用された SSH キーからこのファイルを生成できます。

  ![PuTTY configuration 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. **[SSH]、[トンネル]** の順に選択し、転送ポートを構成します。 ローカル Windows コンピューターはポート 3389 を既に使用しているため、次の設定を使用して、Windows ノード 0 と Windows ノード 1 に接続することをお勧めします  (追加の Windows ノードについても、このパターンを引き続き使用します)。

    **Windows ノード 0**

    * **発信元ポート:** 3390
    * **移動先:** 10.240.245.5:3389

    **Windows ノード 1**

    * **発信元ポート:** 3391
    * **移動先:** 10.240.245.6:3389

    ![Windows RDP トンネルの画像](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. 完了したら、**[セッション]、[保存]** の順にクリックして接続の構成を保存します。

7. PuTTY セッションに接続するには、**[開く]** をクリックします。 マスター ノードへの接続を完了します。

8. リモート デスクトップ接続を開始します。 最初の Windows ノードに接続するには、**コンピューター**の場合は、`localhost:3390` を指定して **[接続]** をクリックします  (次のノードに接続するには、`localhost:3390` を指定します。以降、同様に指定します)。接続を完了するには、デプロイ時に設定したローカル Windows 管理者のパスワードを指定します。


## <a name="next-steps"></a>次のステップ

Kubernetes について詳しく知りたい場合は、次のリンクを参照してください。

* [Kubernetes ブートキャンプ](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) - コンテナー化されたアプリケーションをデプロイ、スケール、更新、デバッグする方法について説明されています。
* [Kubernetes ユーザー ガイド](http://kubernetes.io/docs/user-guide/) - 既存の Kubernetes クラスターでのプログラム実行に関する情報が記載されています。
* [Kubernetes の例](https://github.com/kubernetes/kubernetes/tree/master/examples) - Kubernetes を使って実際のアプリケーションを実行する方法の例が記載されています。
