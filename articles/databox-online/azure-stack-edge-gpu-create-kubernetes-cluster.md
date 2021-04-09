---
title: Azure Stack Edge Pro GPU デバイスで Kubernetes クラスターを作成および管理する | Microsoft Docs
description: Windows PowerShell インターフェイスを使用して Azure Stack Edge Pro GPU デバイスで Kubernetes クラスターを作成および管理する方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: ddbf3c99845f4c38f511260f4dec274903a9d586
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102631960"
---
# <a name="connect-to-and-manage-a-kubernetes-cluster-via-kubectl-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイスで kubectl を使用して Kubernetes クラスターに接続して管理する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro デバイスでは、コンピューティング ロールを構成するときに Kubernetes クラスターが作成されます。 Kubernetes クラスターが作成されると、*kubectl* などのネイティブ ツールを使用して、クライアント マシンからローカルでクラスターに接続し、管理できます。

この記事では、お使いの Azure Stack Edge Pro デバイス上の Kubernetes クラスターに接続し、*kubectl* を使用してそれを管理する方法について説明します。 


## <a name="prerequisites"></a>前提条件

開始する前に次の点を確認します。

1. Azure Stack Edge Pro デバイスにアクセスできること。

2. [Azure Stack Edge Pro のアクティブ化](azure-stack-edge-gpu-deploy-activate.md)に関するページで説明されているように、Azure Stack Edge Pro デバイスがアクティブ化されていること。

3. デバイスでコンピューティング ロールが有効になっていること。 Kubernetes クラスターは、[Azure Stack Edge Pro デバイスでのコンピューティングの構成](azure-stack-edge-gpu-deploy-configure-compute.md)に関するページの手順に従ってデバイスでコンピューティングを構成したときにもそのデバイスに作成されています。

4. デバイスにアクセスするために、PowerShell 5.0 以降を実行している Windows クライアント システムにアクセスできること。 [サポートされているオペレーティング システム](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)が搭載されている他のクライアントを使用することもできます。 

5. ローカル Web UI の **[デバイス]** ページで Kubernetes API エンドポイントを取得していること。 詳細については、[Kubernetes API エンドポイントの取得](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)に関するセクションの手順を参照してください。


## <a name="connect-to-powershell-interface"></a>PowerShell インターフェイスに接続する

Kubernetes クラスターが作成されたら、このクラスターにアクセスして名前空間とユーザーを作成し、ユーザーを名前空間に割り当てることができます。 そのためには、デバイスの PowerShell インターフェイスに接続する必要があります。 PowerShell を実行している Windows クライアントで次の手順に従います。

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]


## <a name="configure-cluster-access-via-kubernetes-rbac"></a>Kubernetes RBAC を使用してクラスター アクセスを構成する

Kubernetes クラスターが作成されたら、コマンドラインから *kubectl* を使用してそのクラスターにアクセスできます。 

このアプローチでは、名前空間とユーザーを作成します。 その後、ユーザーを名前空間に関連付けます。 また、*config* ファイルを取得する必要もあります。これを使用すると、Azure Stack Edge Pro デバイスの PowerShell インターフェイスに接続する必要なく、Kubernetes クライアントを使用して、作成した Kubernetes クラスターと直接通信できます。

1. 名前空間を作成する。 型:

    `New-HcsKubernetesNamespace -Namespace <string>` 

    > [!NOTE]
    > 名前空間とユーザー名の両方に、[DNS サブドメインの命名規則](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names)が適用されます。

    出力例を次に示します。

    `[10.100.10.10]: PS> New-HcsKubernetesNamespace -Namespace "myasetest1"`

2. ユーザーを作成し、構成ファイルを取得します。 型:

    `New-HcsKubernetesUser -UserName <string>`

    > [!NOTE]
    > ユーザー名として *aseuser* を使用することはできません。これは、Azure Stack Edge Pro の IoT 名前空間に関連付けられた既定のユーザー用に予約されているためです。

    構成ファイルの出力例を次に示します。
   
    ```powershell
    [10.100.10.10]: PS> New-HcsKubernetesUser -UserName "aseuser1"
        apiVersion: v1
        clusters:
        - cluster:
            certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01ERXlPVEUyTlRFeE4xb1hEVE13TURFeU5qRTJOVEV4TjFvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBTXNpCkdZUHB0U1VWcDhKZEdVcHE1MVBURWhsZm8wWkk3YXFBdUlrOHZWdUFCZHJQK1FBSzFxcEN1di93NjIwbUtpZ0QKak1aT3Q4QkREREppWHF6UDZRZm5Oc0U2VXBHMnh0YnYrcTZHV2R5K0t6WkxMbXlwWGY3VjlzZEJnejVKVDNvYQpIdzFja2NTUklHSlV3UWxTbklNaHJUS3JUNDZFUUp3d282TmlNUzZMZDZieVk3WkUrTGg3OS9aNEhLanhTRmhMClc5ZG8veThZR3FXUDZmZTFmMmVmSkhUeGtwR05HZE1UVjNuOFlCZ0pSRzdrNjh0N2MrZ1NhbUlVWVJpTUNSNFAKYlFxcFpscWYvV2REZEJHOFh6aDJ0M1l4SkVIMm00T0Z1cSsvUitMYm95aHdKbmNMdVJ5OEpNZWlwTEQ3UlN0QwpZTDNNR0EzN2JieTRyYm4zVzg4Q0F3RUFBYU1qTUNFd0RnWURWUjBQQVFIL0JBUURBZ0trTUE4R0ExVWRFd0VCCi93UUZNQU1CQWY4d0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNbzFwWlBtQzV1cmRPZUJhSWQ4eEQzRkxCMG8KTlErbXBXMWpDd0ZtY3h6dUtlWmRsNXc2N0tuS2JTcDR0TXo1cXg3bUtSc0UxcnBoWkh2VHlKUXg1ZFk2ZE1Kdgp5d2FQZjBpT05TNlU2cC9INE12U1dJaEtJZ1FuTnE1dDh4TjJCNnZpQW41RmZoRkx6WEQrUlZGSm42cnovWkZnCmV6MHpxTkNKYmcvelFucFROcmQ2cnFFRHpoSVFZOVdYVWQycFh3ZXRqUXJpMkpZamh4NmtEcTVoRkZTM0FLUnIKOWlQTVQxaWNkR1NUMFVvM1hIZ1k2ck45WGp3MHFrY2I0Sy83UlVVWlRvS3dKamROR3lNTnpad000L2puR0p5SwpQTE9ycU5Ddlkvb0lkVEM5eVZVY3VRbXVlR0VqT20xUnN1RDFHYVE0RTZwakppVWJpMVdrajJ1bFhOWT0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
            server: https://10.128.47.90:6443
            name: kubernetes
        contexts:
        - context:
            cluster: kubernetes
            user: aseuser1
            name: aseuser1@kubernetes
        current-context: aseuser1@kubernetes
        kind: Config
        preferences: {}
        users:
        - name: aseuser1
            user:
            client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwVENDQWJtZ0F3SUJBZ0lJWlFXcjY2cGFWSm93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBeE1qa3hOalV4TVRkYUZ3MHlNVEF4TWpneU1qVTJNVGRhTUJNeApFVEFQQmdOVkJBTVRDR0Z6WlhWelpYSXhNSUlCSWpBTkJna3Foa2lHOXcwQkFRRUZBQU9DQVE4QU1JSUJDZ0tDCkFRRUF4R3RDaXJ2cVhGYm5wVmtaYlVPRWQ3cXg2UzNVZ092MlhHRHNKM2VYWXN0bUxQVjMrQnlBcWwyN211L1AKaWdmaWt6MG9QSW1iYmtvcVJkamlYckxFZnk0N3dHcEhzdUhUOHNLY2tHTnJleFE2bXhaZ29xaU1nL2FuMUpMdwpiOFEvVnlQaWdVdUt6eVBseEhUZmlmSVM5MzR1VnZVZUc0dzlMRjAyZ2s2Nitpc0ZtanhsVmhseWRMNlc2UmZTCjl0OGpNMEFkdEpJL0xNbE13RHJJRVdFKzM4WDVNelJhQkJYNnlzNDFWSkZxekcwdW14dHdxN2pGOXp1UTE4ekIKalRZaDl3OWVKcDJwS2Fvak5tNE9SSDh4SzVSaUhocjJ2anFJWXkxRDd2WDh0b0U1K05HNmxHZjh5L1NvQnNRbQpmOG9vL1k3SEZmQXVGdlN6WUc1RUlQTFM4UUlEQVFBQm95Y3dKVEFPQmdOVkhROEJBZjhFQkFNQ0JhQXdFd1lEClZSMGxCQXd3Q2dZSUt3WUJCUVVIQXdJd0RRWUpLb1pJaHZjTkFRRUxCUUFEZ2dFQkFNR1BxY0YzS1BCbHZ0K24KN1NOcGE3anhWYkhZVGxyNTgwVWxzek93WEwwVnVPUUlCYmN2djIzZk9HNkhDZlQ0bWxBU0JRWVNZcmpLMjJTTwpTWld4cjNQUDlhVzNHajkxc0ttSnc1ZUF1WFhQbUJpK1RWQzBvY0ZLaEQvZ0o1aC93YnBaVndpVjVyRWE5Kzc2CnhNcFAzRld6dG5tT1hPaEl6UFNlR3B4YWpwQXd3ZXd4QU0yb0xGRFZFcy9XTFFMODJZM3NFcE93NVNaSVJJNXMKUHhMUTVnV1ZPM2x2SXcwZ3IrdkJlanZSOUZKaWVuTWFRdGdjSVgyRmpDaDBRMHVYRkdsTVNXWEljbjRLRTR0TApQSFFMalRSVUwyVnRXcW1YZ1RBM3RzN01DcGNRTFdPZFJUYkpSejZCbkc1aXVwcDdOSlFvYW9YcWpNVk5DVDZCCllYMEd0Skk9Ci0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            client-key-data: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb2dJQkFBS0NBUUVBeEd0Q2lydnFYRmJucFZrWmJVT0VkN3F4NlMzVWdPdjJYR0RzSjNlWFlzdG1MUFYzCitCeUFxbDI3bXUvUGlnZmlrejBvUEltYmJrb3FSZGppWHJMRWZ5NDd3R3BIc3VIVDhzS2NrR05yZXhRNm14WmcKb3FpTWcvYW4xSkx3YjhRL1Z5UGlnVXVLenlQbHhIVGZpZklTOTM0dVZ2VWVHNHc5TEYwMmdrNjYraXNGbWp4bApWaGx5ZEw2VzZSZlM5dDhqTTBBZHRKSS9MTWxNd0RySUVXRSszOFg1TXpSYUJCWDZ5czQxVkpGcXpHMHVteHR3CnE3akY5enVRMTh6QmpUWWg5dzllSnAycEthb2pObTRPUkg4eEs1UmlIaHIydmpxSVl5MUQ3dlg4dG9FNStORzYKbEdmOHkvU29Cc1FtZjhvby9ZN0hGZkF1RnZTellHNUVJUExTOFFJREFRQUJBb0lCQUVVSUVXM2kxMTQycU5raQo5RjNEWWZZV1pscTJZYjRoc0FjTmhWSGxwUTN5d0dsQ3FEUktDQ3BZSVF3MkJqSFR6WnpEM0xWU0E0K0NmMUxuCkE4QVdnaHJVcStsWE1QVzhpcG9DTGJaTlNzUUord0x3bld2dFl0MHFQaGZtd0p2M1UrK1RUQkwyOHNVVUw3ZVkKLzh0aWlhbno3ZU5mNklIMENyZmgxcnQ3WWhsemtRd1hBVHNScVJja0dMaTgrdGN5WnVzdGFhbENUSzBGRTdCaQpBUGE5a2w1SG56eCs4TTcvNWladHkwTUIxYWpWMnlGblBkUmlKSFVCb1AxVVV0QUthYjVZU0RvNllkZ2pIUTRHCjNWN1l1YWZobnVFMXA0VVIvUkloVVdjRlVVaTFBOFpZMFdnd1BDTmhnMWpQZU5vb2Y1UHpRbEY1OTRBREVwUUYKOFR2bG92RUNnWUVBOWZZbUxyY0tlQ0JiMTFoQVhoTi91Z1RTbU5xNnpFL1pPSWl6M0xwckdjRDhvWDdCVW9GcgplelkxbktSS2tkczE5OERnVjlQZUhuNzllQTRoMjM5RkIwNFFhMUJBdUVMRzRsdHJ3VlNxaFBENUR6YkcrSEhSCnJtYThVMEpUSmVVS0tJVjRUUGxlTzFtK2tjbkRJVXY1ckpwZDVXU3RvcUhXdk9RZkEvRUF0VlVDZ1lFQXpHOTcKTitCZVVvbFNiREttVUNGdTdPZGhYSXJYR3RnSEorZ2JOMDlnSHRURG5PY0IxZ1NzNkpZa1FPQU9qbWFxK05lRAp5SUF1NytheWlFRmpyT2tzTGhkSTREUXNkWFZveFFGVko1V1JwWlk3UTVRaFZpYUR2enR4NDlzSDlKSkplM2U0Cnl3NWdpNGkxKy90MnY2eWRKcWdNQ0xxOHlEdFRrcE9PSitkbkp5MENnWUJwZ3lpcURaZU9KTU9CUTdpSkl2QSsKQ21lVmJ1K0hTaEd6TU9HSHBPamc2V3IybEh1Mk94S3lqblM5TjdWTmtLNDhGQitwVFpnUm1RUi9CZ0Q4T2tLUQplYXFOZnFYazViQ1AxZ3dKcVpwazRVTFdoZmNoQ1NLY0lESlZ2VFFTSTRrU0RQK29kYWs0Nkt6WnVhWGRtTXdJCmdVZ2FhZkFhdmpaeVhhSDRmT0NDNlFLQmdHVXJCaDh3dVh5KzJEc1RGWnF4OE9McjNoS2Q0clUyRXRSODJIc1cKbk1xbEgraVZxU0x3VFdFTWJBUnUzTVU3cVlCYnBxdWlRNWdVNG1UcmR4Z3FpK0tEUTEwd2RJL3IrbDBEdTlCTApCRGlkajlaeGg4M0tZWWhSTXBzLzJULys1TDVsRU4zcnozczl2RkZtcisxS3pycENqeklDdDBtZmtrd0hHV0pGCjhaWkJBb0dBVXB3aUIrcWlHbkpxU1FtZHNSZFVabGFBaTRpbGhaa01RYTRHem95ZFQ3OTVHTm44ZThBRjd3WHMKTGpyYjdEV1FwakdCMnZpUlkySUZBVmIyKzZsdDlwOVJRMTZnSmxpNU5ZRXVvQWRoWXBsVWdBZGFHWHNGNHdabwo3SHFHTHBGdmUxVU5Gb0dQdkxpWUNrUFVYdGduQ3dNb0R2SEpKNzVYMXl6ckh6cmxUS1k9Ci0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==  
        
    [10.100.10.10]: PS>
    ```
    

3. 構成ファイルはプレーンテキストで表示されます。 このファイルをコピーし、*config* ファイルとして保存します。 

    > [!IMPORTANT]
    > 構成ファイルは *.txt* ファイルとして保存せず、ファイル拡張子なしで保存してください。

4. この構成ファイルは、ローカル コンピューター上のユーザー プロファイルの `.kube` フォルダーに存在する必要があります。 このファイルをユーザー プロファイルのそのフォルダーにコピーします。

    ![クライアント上の構成ファイルの場所](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)

5. 作成したユーザーに名前空間を関連付けます。 型:

    `Grant-HcsKubernetesNamespaceAccess -Namespace <string> -UserName <string>`

    出力例を次に示します。

    `[10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace "myasetest1" -UserName "aseuser1"`

    構成ファイルを作成したら、クラスターに物理的にアクセスする必要はありません。 クライアントが Azure Stack Edge Pro デバイスの IP に対して ping を実行できる場合は、*kubectl* コマンドを使用してクラスターに命令できます。

6. クライアントで新しい PowerShell セッションを開始します。 デバイスのインターフェイスに接続する必要はありません。 これで、次のコマンドを使用してクライアントに `kubectl` をインストールできるようになりました。

    ```powershell
    PS C:\windows\system32> curl https://storage.googleapis.com/kubernetes-release/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    
    PS C:\windows\system32>
    ```
    たとえば、Kubernetes マスター ノードで v1.15.2 が実行されていた場合は、クライアントに v1.15.2 をインストールします。

    > [!IMPORTANT]
    > マスターとのマイナー バージョンの差が 1 未満のクライアントをダウンロードしてください。 ただし、クライアントのバージョンは、マスターよりも最大 1 マイナー バージョン上位であってもかまいません。 たとえば、v1.3 のマスターは v1.1、v1.2、v1.3 のノードで動作し、v1.2、v1.3、v1.4 のクライアントで動作します。 Kubernetes クライアント バージョンの詳細については、「[Kubernetes バージョンとバージョン スキュー サポート ポリシー](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)」を参照してください。 Azure Stack Edge Pro の Kubernetes サーバー バージョンの詳細については、Kubernetes サーバー バージョンの取得に関するページを参照してください。<!-- insert link-->
    > Docker for Windows やその他のツールを実行している場合は、`kubectl` がシステムにプレインストールされていることもあります。 この kubernetes クラスターを使用するには、このセクションに示すように特定のバージョンの `kubectl` をダウンロードすることが重要です。 

    インストールには数分かかります。

7. インストールされたバージョンがダウンロードしたものであることを確認します。 システムで `kubectl.exe` がインストールされた場所への絶対パスを指定する必要があります。
    
    ```powershell
    PS C:\Users\myuser> C:\windows\system32\kubectl.exe version
    Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.2", GitCommit:"f6278300bebbb750328ac16ee6dd3aa7d3549568", GitTreeState:"clean", BuildDate:"2019-08-05T09:23:26Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"windows/amd64"}
    Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.1", GitCommit:"4485c6f18cee9a5d3c3b4e523bd27972b1b53892", GitTreeState:"clean", BuildDate:"2019-07-18T09:09:21Z", GoVersion:"go1.12.5", Compiler:"gc", Platform:"linux/amd64"}
    PS C:\Users\myuser>
    ``` 
    
    Kubernetes クラスターの管理に使用する `kubectl` コマンドの詳細については、「[kubectl の概要](https://kubernetes.io/docs/reference/kubectl/overview/)」を参照してください。

8. システム上の hosts ファイルに DNS エントリを追加します。 

    1. メモ帳を管理者として実行し、`C:\windows\system32\drivers\etc\hosts` にある `hosts` ファイルを開きます。 
    2. 前の手順でローカル UI の **[デバイス]** ページから保存した情報を使用して、hosts ファイルにエントリを作成します。 

        たとえば、このエンドポイント `https://compute.asedevice.microsoftdatabox.com/[10.100.10.10]` をコピーして、デバイスの IP アドレスと DNS ドメインを含む次のエントリを作成します。 

        `10.100.10.10 compute.asedevice.microsoftdatabox.com`

9. Kubernetes ポッドに接続できることを確認するには、次のように入力します。
    
    ```powershell
    PS C:\Users\myuser> kubectl get pods -n "myasetest1"
    No resources found.
    PS C:\Users\myuser>
    ```
これで、アプリケーションを名前空間にデプロイし、それらのアプリケーションとそのログを表示できます。

> [!IMPORTANT]   
> 管理者アクセス権を必要とするコマンドなど、実行できないコマンドは多数あります。 名前空間で許可されている操作のみを実行できます。


## <a name="remove-kubernetes-cluster"></a>Kubernetes クラスターを削除する

Kubernetes クラスターを削除するには、IoT Edge 構成を削除する必要があります。

詳細な手順については、[IoT Edge 構成の管理](azure-stack-edge-gpu-manage-compute.md#manage-iot-edge-configuration)に関するページを参照してください。
   

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro にステートレス アプリケーションをデプロイする](azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md)。
