---
title: Azure Stack Edge Pro GPU デバイスで Edge コンテナー レジストリを有効にする
description: Azure Stack Edge Pro GPU デバイスで Edge コンテナー レジストリを有効にする方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: f19625f1d558071ccb29487efe56146756c7692c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437538"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>ご自分の Azure Stack Edge Pro GPU デバイスで Edge コンテナー レジストリを有効にする

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Edge コンテナー レジストリを有効にし、Azure Stack Edge Pro デバイスの Kubernetes クラスター内から使用する方法について説明します。 この記事で使用されている例では、ソース レジストリ (この場合は Microsoft Container Registry) から、Azure Stack Edge デバイス上のレジストリ (Edge コンテナー レジストリ) にイメージをプッシュする方法について詳しく説明します。

### <a name="about-edge-container-registry"></a>Edge コンテナー レジストリについて

コンテナー化されたコンピューティング アプリケーションは、コンテナー イメージ上で実行され、これらのイメージはレジストリに格納されます。 レジストリには、Docker Hub のようなパブリックなもの、プライベートなもの、および Azure Container Registry のようなクラウド プロバイダーが管理するものなどがあります。 詳細については、「[レジストリ、リポジトリ、イメージについて](../container-registry/container-registry-concepts.md)」を参照してください。

Edge コンテナー レジストリによって、Azure Stack Edge Pro デバイス上の Edge にリポジトリが提供されます。 このレジストリを使用して、プライベート コンテナー イメージを格納および管理することができます。

マルチノード環境では、コンテナー イメージをダウンロードし、Edge コンテナー レジストリに一度プッシュすることができます。 すべての Edge アプリケーションで、後続のデプロイに Edge コンテナー レジストリを使用できます。


## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. Azure Stack Edge Pro デバイスにアクセスできること。

1. [Azure Stack Edge Pro のアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関するページで説明されているように、Azure Stack Edge Pro デバイスがアクティブ化されていること。

1. デバイスでコンピューティング ロールが有効になっていること。 Kubernetes クラスターは、[Azure Stack Edge Pro デバイスでのコンピューティングの構成](azure-stack-edge-gpu-deploy-configure-compute.md)に関するページの手順に従ってデバイスでコンピューティングを構成したときにもそのデバイスに作成されています。

1. ローカル Web UI の **[デバイス]** ページで Kubernetes API エンドポイントを取得していること。 詳細については、[Kubernetes API エンドポイントの取得](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)に関するセクションの手順を参照してください。

1. [サポートされているオペレーティング システム](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)でクライアント システムにアクセスしていること。 Windows クライアントを使用している場合、デバイスにアクセスするには、システムで PowerShell 5.0 以降が実行されている必要があります。

    1. 独自のコンテナー イメージをプルおよびプッシュする場合は、システムに Docker クライアントがインストールされていることを確認してください。 Windows クライアントを使用している場合は、[Windows に Docker Desktop をインストール](https://docs.docker.com/docker-for-windows/install/)します。  


## <a name="enable-container-registry-as-add-on"></a>コンテナー レジストリをアドオンとして有効にする

最初のステップは、Edge コンテナー レジストリをアドオンとして有効にすることです。

1. [デバイスの PowerShell インターフェイスに接続します](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)。 

1. コンテナー レジストリをアドオンとして有効にするには、次のように入力します。 

    `Set-HcsKubernetesContainerRegistry`
    
    この操作が完了するまでに数分かかる場合があります。

    このコマンドの出力例を次に示します。  
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. コンテナー レジストリの詳細を取得するには、次のように入力します。

    `Get-HcsKubernetesContainerRegistryInfo`

    このコマンドのサンプルを次に示します。  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ``` 

1. `Get-HcsKubernetesContainerRegistryInfo` の出力のユーザー名とパスワードをメモしておきます。 これらの資格情報は、イメージのプッシュ中に Edge コンテナー レジストリにサインインするために使用されます。         


## <a name="manage-container-registry-images"></a>コンテナー レジストリ イメージの管理

Azure Stack Edge デバイスの外部からコンテナー レジストリにアクセスすることができます。 また、レジストリ内のイメージをプッシュまたはプルすることもできます。

Edge コンテナー レジストリにアクセスするには、次の手順に従います。

1. Edge コンテナー レジストリのエンドポイントの詳細を取得します。
    1. デバイスのローカル UI で、 **[デバイス]** にアクセスします。
    1. **Edge コンテナー レジストリ エンドポイント** を見つけます。
        ![デバイス ページの Edge コンテナー レジストリ エンドポイント](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Edge コンテナー レジストリ エンドポイントに接続するには、このエンドポイントをコピーし、対応する DNS エントリをクライアントの `C:\Windows\System32\Drivers\etc\hosts` ファイルに作成します。 

        <IP address of the Kubernetes main node>    <Edge container registry endpoint> 
        
        ![Edge コンテナー レジストリ エンドポイントの DNS エントリの追加](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Edge コンテナー レジストリ証明書をローカル UI からダウンロードします。 
    1. デバイスのローカル UI で、 **[証明書]** にアクセスします。
    1. **Edge コンテナー レジストリ証明書** のエントリを見つけます。 このエントリの右側にある **[ダウンロード]** を選択して、デバイスへのアクセスに使用するクライアント システムの Edge コンテナー レジストリ証明書をダウンロードします。 

        ![Edge コンテナー レジストリ エンドポイント証明書のダウンロード](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. ダウンロードした証明書をクライアントにインストールします。 Windows クライアントを使用している場合は、次の手順を実行します。 
    1. 証明書を選択し、 **[証明書のインポート ウィザード]** で、保存場所として **[ローカル コンピューター]** を選択します。 

        ![証明書のインストール 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. 証明書を信頼されたルート ストアのローカル コンピューターにインストールします。 

        ![証明書のインストール 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. 証明書がインストールされたら、システム上の Docker クライアントを再起動します。

1. Edge コンテナー レジストリにサインインします。 型:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    **[デバイス]** ページからの Edge コンテナー レジストリ エンドポイントと、`Get-HcsKubernetesContainerRegistryInfo` の出力から取得したユーザー名とパスワードを指定します。 

1. docker push または pull コマンドを使用して、コンテナー レジストリからコンテナー イメージをプッシュまたはプルします。
 
    1. Microsoft Container Registry イメージからイメージをプルします。 型:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. レジストリへの完全修飾パスを使用して、プルしたイメージのエイリアスを作成します。

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. レジストリにイメージをプッシュします。
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. レジストリにプッシュしたイメージを実行します。
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        pull コマンドと push コマンドの出力例を次に示します。

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. ブラウザーで `http://localhost:8080` に移動して、実行中のコンテナーを表示します。 この例では、実行中の nginx Web サーバーが表示されます。

    ![実行中のコンテナーを参照する](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    コンテナーを停止して削除するには、`Control+C` を押します。

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Kubernetes ポッド経由で Edge コンテナー レジストリ イメージを使用する

Edge コンテナー レジストリにプッシュしたイメージを Kubernetes ポッド内からデプロイできるようになりました。

1. イメージをデプロイするには、*kubectl* を使用してクラスター アクセスを構成する必要があります。 名前空間とユーザーを作成し、名前空間へのユーザー アクセスを許可して、"*構成*" ファイルを取得します。 Kubernetes ポッドに接続できることを確認します。 
    
    「[Azure Stack Edge Pro GPU デバイスで kubectl を使用して Kubernetes クラスターに接続して管理する](azure-stack-edge-gpu-create-kubernetes-cluster.md)」のすべての手順を実行してください。 

    ここでは、ユーザーが Kubernetes クラスターにアクセスできる、デバイス上の名前空間の出力例を示します。

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. イメージ プル シークレットは、デバイス上のすべての Kubernetes 名前空間で既に設定されています。 シークレットは、`get secrets` コマンドを使用して取得できます。 出力例を次に示します。

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Kubectl を使用して、名前空間にポッドをデプロイします。 次の `yaml` を使用します。 

    image: `<image-name>` をコンテナー レジストリにプッシュされたイメージに置き換えます。 name: `ase-ecr-credentials` の imagePullSecrets を使用して名前空間のシークレットを参照します。
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. apply コマンドを使用して、作成した名前空間にデプロイを適用します。 コンテナーが動作していることを確認します。 出力例を次に示します。
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>コンテナー レジストリ イメージの削除

Edge コンテナー レジストリ ストレージは、デバイス上の使用可能なストレージによって制限される、Azure Stack Edge Pro デバイス内のローカル共有でホストされます。 Docker HTTP v2 API (https://docs.docker.com/registry/spec/api/) ) を使用して、コンテナー レジストリから未使用の docker イメージを削除する必要があります。  

1 つ以上のコンテナー イメージを削除するには、次の手順を実行します。

1. イメージ名を、削除するイメージに設定します。

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. コンテナー レジストリのユーザー名とパスワードを PS 資格情報として設定します

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. イメージに関連付けられているタグを一覧表示する

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. 削除するタグに関連付けられているダイジェストを一覧表示します。 この場合は、上記のコマンドの出力の $tags を使用します。 複数のタグがある場合は、そのいずれかを選択し、次のコマンドで使用します。

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. image:tag のダイジェストを使用してイメージを削除する

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

未使用のイメージを削除すると、その参照されていないイメージに関連付けられている領域が、夜間に実行されるプロセスによって自動的に再利用されます。 

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro にステートレス アプリケーションをデプロイする](azure-stack-edge-j-series-deploy-stateless-application-kubernetes.md)。
