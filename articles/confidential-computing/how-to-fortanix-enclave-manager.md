---
title: Fortanix Enclave Manager でアプリケーションを実行する方法
description: Fortanix Enclave Manager を使用してコンテナー イメージを変換する方法について説明します
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: how-to
ms.date: 8/12/2020
ms.author: JenCook
ms.openlocfilehash: b5b0f9acc45dba81bb7653c844bb8c78a8bd29ba
ms.sourcegitcommit: ac7ae29773faaa6b1f7836868565517cd48561b2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88826295"
---
# <a name="how-to-run-an-application-with-fortanix-enclave-manager"></a>方法:Fortanix Enclave Manager でアプリケーションを実行する 

[Fortanix](https://www.fortanix.com/) の [Fortanix Enclave Manager](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.enclave_manager?tab=Overview) および [Fortanix Node Agent](https://azuremarketplace.microsoft.com/marketplace/apps/fortanix.rte_node_agent) を使用して Azure Confidential Computing でアプリケーションの実行を開始します。


Fortanix は、Azure インフラストラクチャ上に構築された製品とサービスを提供するサードパーティのソフトウェア ベンダーです。 Azure 上のコンフィデンシャル コンピューティング サービスを提供するサードパーティ プロバイダーは他にもあります。

> [!Note] 
 > このドキュメントで言及されている製品は、Microsoft の管理下にありません。 Microsoft はこの情報を便宜上お勧めするものとして提供しており、これらの Microsoft 製以外の製品への言及は、Microsoft による保証を意味しません。



このチュートリアルでは、アプリケーション イメージを、コンフィデンシャル コンピューティングで保護されたイメージに変換する方法について説明します。 この環境では、Azure の DCsv2 シリーズの Intel SGX 対応の仮想マシンを搭載している [Fortanix](https://www.fortanix.com/) ソフトウェアを使用します。 このソリューションでは、本人確認やデータ アクセス制御などの重要なセキュリティ ポリシーを調整します。

 Fortanix 固有のサポートについては、[Fortanix Slack コミュニティ](https://fortanix.com/community/)に参加し、チャネル #enclavemanager を使用してください。


## <a name="prerequisites"></a>前提条件

1. Fortanix Enclave Manager のアカウントを持っていない場合は、開始する前に[サインアップ](https://em.fortanix.com/auth/sign-up)します。
1. 変換されたアプリケーション イメージをプッシュするためのプライベート [Docker](https://docs.docker.com/) レジストリ。
1. Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

> [!NOTE]
> 無料試用版アカウントでは、このチュートリアルで使用されている仮想マシンを利用できません。 従量課金制サブスクリプションにアップグレードしてください。

## <a name="add-an-application-to-fortanix-enclave-manager"></a>Fortanix Enclave Manager にアプリケーションを追加する
1. [Fortanix EM](https://fortanix.com) にサインインします
1. **[Accounts]** ページに移動し、 **[ADD ACCOUNT]** を選択して新しいアカウントを作成します。 
    
![アカウントを作成する](media/how-to-fortanix-enclave-manager/create-account.png)

1. アカウントが作成されたら、 **[SELECT]** をクリックして、新しく作成したアカウントを選択します。 これで、コンピューティング ノードの登録とアプリケーションの作成を開始できます。 
1. **[+ APPLICATION]** ボタンを選択してアプリケーションを追加します。 この例では、Flask Server Enclave OS アプリケーションを追加します。 

1. Enclave OS アプリケーションの **[ADD]** ボタンを選択します。 

    ![[アプリケーションの追加]](media/how-to-fortanix-enclave-manager/add-enclave-application.png)

    > [!NOTE]
    > このチュートリアルでは、Enclave OS アプリケーションの追加のみについて説明します。 EDP Rust アプリケーションを Fortanix Enclave Manager に取り込む方法の詳細については、[こちら](https://support.fortanix.com/hc/en-us/articles/360044746932-Bringing-EDP-Rust-Apps-to-Enclave-Manager)を参照してください。 

6. このチュートリアルでは、サンプルアプリケーションに Fortanix の docker レジストリを使用します。 次の情報を入力します。 プライベート docker レジストリを使用して、出力イメージを保持します。 
 
    - **アプリケーション名**:Python アプリケーション サーバー
    - **説明**:Python Flask Server
    - **入力イメージ名**: fortanix/python-flask
    - **出力イメージ名**: fortanx-private/python-flask-sgx
    - **ISVPRODID**:1
    - **ISVSVM**:1
    - **メモリ サイズ**:1 GB
    - **スレッド数**:128

    *省略可能*:アプリケーションを実行します。
    - **Docker Hub**: [https://hub.docker.com/u/fortanix](https://hub.docker.com/u/fortanix)
    - **アプリ**: fortanix/python-flask

        次のコマンドを実行します。
         ```bash
            sudo docker run fortanix/python-flask
         ```

1. 証明書を追加します。 以下の詳細を使用して情報を入力し、 **[NEXT]** を選択します。
    - **[Domain]** : myapp.domain.dom
    - **[種類]** :Enclave Manager によって発行された証明書 
    - **[Key path]** : /appkey.pem
    - **[Key type]** :RSA
    - **[Certificate path]** : /appcert.pem
    - **[RSA Key Size]** :2048 ビット
    

## <a name="create-an-image"></a>イメージの作成
Fortanix EM イメージは、アプリケーションのソフトウェア リリースまたはバージョンです。 各イメージは、1 つのエンクレーブ ハッシュ (MRENCLAVE) に関連付けられています。 
1. **[Add Image]** ページで、 **[Output image name]** に**レジストリ資格**情報を入力します。 これらの資格情報は、イメージがプッシュされるプライベート docker レジストリにアクセスするために使用されます。 

    ![イメージを作成する](media/how-to-fortanix-enclave-manager/create-image.png)
1. イメージ タグを指定し、 **[Create]** を選択します。

    ![タグを追加する](media/how-to-fortanix-enclave-manager/add-tag.png)

## <a name="domain-and-image-allow-listing"></a>ドメインとイメージの許可リスト 
ドメインが許可リストに追加されたアプリケーションは、Fortanix Enclave Manager から TLS 証明書を取得します。 同様に、変換されたイメージからアプリケーションを実行すると、Fortanix Enclave Manager に接続しようとします。 その後、アプリケーションは TLS 証明書を要求します。 

左側の **[Tasks]** タブに切り替え、保留中の要求を承認して、ドメインとイメージを許可します。 

## <a name="enroll-compute-node-agent-in-azure"></a>Azure にコンピューティング ノード エージェントを登録する

### <a name="generate-and-copy-join-token"></a>参加トークンを生成およびコピーする
Fortanix Enclave Manager で、トークンを作成します。 このトークンにより、Azure のコンピューティング ノードが自身を認証できるようになります。 このトークンを Azure 仮想マシンに渡す必要があります。
1. 管理コンソールで、 **[+ ENROLL NODE]** ボタンを選択します。 
1. **[GENERATE TOKEN]** を選択して、参加トークンを生成します。 トークンをコピーします。

### <a name="enroll-nodes-into-fortanix-node-agent-in-azure-marketplace"></a>Azure Marketplace で Fortanix Node Agent にノードを登録する

Fortanix Node Agent を作成すると、仮想マシン、ネットワーク インターフェイス、仮想ネットワーク、ネットワーク セキュリティ グループ、およびパブリック IP アドレスが Azure リソース グループにデプロイされます。 Azure サブスクリプションは、仮想マシンに対して時間単位で課金されます。 Fortanix Node Agent を作成する前に、DCsv2 シリーズについての Azure [仮想マシンの価格に関するページ](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)を参照してください。 使用しない場合は Azure リソースを削除します。 

1. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) に移動し、Azure 資格情報を使用してサインインします。
1. 検索バーに「**Fortanix Confidential Computing Node Agent**」と入力します。 **Fortanix Confidential Computing Node Agent** という検索ボックスに表示されるアプリを選択して、オファリングのホームページに移動します。 
     ![search marketplace](media/how-to-fortanix-enclave-manager/search-fortanix-marketplace.png)
1. **[今すぐ入手]** を選択し、必要に応じて情報を入力して、 **[続行]** を選択します。 Azure portal にリダイレクトされます。 
1. **[作成]** を選択して、Fortanix Confidential Computing Node Agent のデプロイ ページを開きます。
1. このページでは、仮想マシンをデプロイするための情報を入力します。 具体的には、この VM は、Fortanix Node Agent ソフトウェアがインストールされた、Azure からの DCsv2 シリーズの Intel SGX 対応の仮想マシンです。 Node Agent により、変換されたイメージは Azure の Intel SGX ノードで安全に実行できます。  仮想マシンおよび関連付けられているリソースをデプロイする**サブスクリプション**と**リソース グループ**を選択します。 
 
    > [!NOTE]
    > DCsv2 シリーズの仮想マシンを Azure にデプロイする場合、制約があります。 追加のコアのクォータを要求することが必要な場合もあります。 詳細については、[Azure VM でのコンフィデンシャル コンピューティング ソリューション](https://docs.microsoft.com/azure/confidential-computing/virtual-machine-solution)に関するページを参照してください。 

1. 使用可能なリージョンを選択します。
1. 仮想マシンの名前を **[ノード名]** フィールドに入力します。 
1. 仮想マシンへの認証に使用するユーザー名とパスワード (または SSH キー) を入力します。
1. 既定の [OS ディスク サイズ] を 200 のままにし、[VM サイズ] を選択します (このチュートリアルでは Standard_DC4s_v2 で十分です)
1. 前の手順で生成したトークンを **[参加トークン]** フィールドに貼り付けます。

     ![deploy resource](media/how-to-fortanix-enclave-manager/deploy-fortanix-node-agent.png)

1. **[確認および作成]** を選択します。 検証に合格したことを確認し、 **[作成]** を選択します。 すべてのリソースをデプロイすると、コンピューティング ノードが Enclave Manager に登録されるようになります。 

## <a name="run-the-application-image-on-the-compute-node"></a>コンピューティング ノードでアプリケーション イメージを実行する
次のコマンドを実行して、アプリケーションを実行します。 ノード IP、ポート、および変換されたイメージ名は、特定のアプリケーションの入力として変更してください。 
 
このチュートリアルでは、次のコマンドを実行します。     

```bash
    sudo docker run `
        --device /dev/isgx:/dev/isgx `
        --device /dev/gsgx:/dev/gsgx `
        -v /var/run/aesmd/aesm.socket:/var/run/aesmd/aesm.socket `
        -e NODE_AGENT_BASE_URL=http://52.152.206.164:9092/v1/ fortanix-private/python-flask-sgx
```

パラメーターの説明 
- *52.152.206.164* は Node Agent のホスト IP です
- *9092* は Node Agent がリッスンするポートです
- *fortanix-private/python-flask-sgx* は変換されたアプリであり、Fortanix Enclave Manage Web ポータルの [Images] タブで、 **[Images]** テーブルの **[Image Name]** 列から確認できます。 
    
## <a name="verify-and-monitor-the-running-application"></a>実行中のアプリケーションを確認して監視する
1. [Fortanix Enclave Manager](https://em.fortanix.com/console) に戻ります
1. ノードを登録した**アカウント**内で作業していることを確認します
1. 左側のナビゲーション ペインの上部アイコンを選択して、 **[管理コンソール]** に移動します 
1. **[アプリケーション]** タブを選択します
1. コンピューティング ノードが関連付けられている実行中のアプリケーションがあることを確認します


## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、仮想マシン、および関連リソースを削除できます。 リソース グループを削除すると、変換されたイメージに関連付けられているノードの登録が解除されます。 

仮想マシンのリソース グループを選択し、 **[削除]** をクリックします。 リソース グループの名前を確認してリソースの削除を終了します。

作成した Fortanix Enclave Manager アカウントを削除するには、Enclave Manager の [[Accounts Page]](https://em.fortanix.com/accounts) します。 削除するアカウントにマウス ポインターを移動します。 右上隅の垂直方向の黒いドットを選択し、 **[Delete Account]** を選択します。

  ![delete](media/how-to-fortanix-enclave-manager/delete-account.png)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Fortanix ツールを使用して、コンフィデンシャル コンピューティング仮想マシン上で実行されるようにアプリケーション イメージを変換しました。 Azure におけるコンフィデンシャル コンピューティング仮想マシンの詳細については、「[仮想マシン上のソリューション](virtual-machine-solutions.md)」を参照してください。 

Azure のコンフィデンシャル コンピューティング サービスの詳細については、[「Azure Confidential Computing の概要」](overview.md)を参照してください。

 Azure 上で ([Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) や [Scone](https://sconedocs.github.io)などの) 他のサードパーティ オファリングを使用して同様のタスクを実行する方法について学習します。  