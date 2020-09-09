---
title: チュートリアル:NAT ゲートウェイの作成とテスト - Azure portal
titlesuffix: Azure Virtual Network NAT
description: このチュートリアルでは、Azure portal を使用して NAT ゲートウェイを作成し、NAT サービスをテストする方法について説明します
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: d798725892a9586c17cd7023863fe5cf7df05cb6
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417839"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>チュートリアル:Azure portal を使用した NAT ゲートウェイの作成と NAT サービスのテスト

このチュートリアルでは、Azure 内の仮想マシンに送信接続を提供する NAT ゲートウェイを作成します。 NAT ゲートウェイをテストするために、ソースと宛先の仮想マシンをデプロイします。 ソースから宛先の仮想マシンへのパブリック IP アドレスへの送信接続を行って、NAT ゲートウェイをテストします。  このチュートリアルでは、わかりやすくするために、同じリソース グループ内の 2 つの異なる仮想ネットワークにソースと宛先をデプロイしています。

必要に応じて、ポータルではなく [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) または [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) を使用してこのような手順を実行できます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="prepare-the-source-for-outbound-traffic"></a>送信トラフィックのソースの準備

次の手順では、完全なテスト環境の構成とテスト自体の実行について説明します。 まず、ソースの作業から始めます。後のステップで作成する NAT ゲートウェイ リソースが使用されます。

## <a name="virtual-network-and-parameters"></a>仮想ネットワークとパラメーター

VM をデプロイして NAT ゲートウェイを使用する前に、リソース グループおよび仮想ネットワークを作成しておく必要があります。

このセクションの手順では、各パラメーターを次のように置き換える必要があります。

| パラメーター                   | 値                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | 米国東部 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>ソース仮想マシンの作成

今度は、NAT サービスを使用する VM を作成します。 この VM には、ユーザーが VM にアクセスできるようにするための、インスタンスレベルのパブリック IP として使用するパブリック IP があります。 NAT サービスはフロー方向を認識し、サブネットの既定のインターネットの宛先を置き換えます。 VM のパブリック IP アドレスは、送信接続には使用されません。

NAT ゲートウェイをテストするために、外部からこの VM にアクセスするためのインスタンスレベルのパブリック IP として、パブリック IP アドレス リソースを割り当てます。 このアドレスは、テスト アクセスのみを目的としたものです。  NAT サービスが他の送信オプションよりも優先される方法を示します。

演習として、この VM をパブリック IP なしで作成し、ジャンプボックスとして使用する別の VM をパブリック IP なしで作成することもできます。

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]\(計算\)**  >  **[Ubuntu Server 18.04 LTS]** を選択するか、Marketplace 検索で **Ubuntu Server 18.04 LTS** を検索します。

2. **[仮想マシンの作成]** の **[基本]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : **[myResourceGroupNAT]** を選択します。
   - **[インスタンスの詳細]**  >  **[仮想マシン名]** : 「**myVMsource**」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** > **[米国東部 2]** を選択します。
   - **[管理者アカウント]**  >  **[Authentication enter]\(認証を入力\)** : **[パスワード]** を選択します。
   - **[管理者アカウント]** > **[ユーザー名]** 、 **[パスワード]** 、および **[パスワードの確認]** に情報を入力します。
   - **[受信ポートの規則]**  >  **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択します。
   - **[受信ポートの規則]**  >  **[受信ポートの選択]** : **[SSH (22)]** を選択します
   - **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。

3. **[ネットワーク]** タブで、以下が選択されていることを確認します。
   - **[Virtual network]\(仮想ネットワーク\)** : **[myVnetsource]**
   - **[Subnet]\(サブネット\)** : **[mySubnetsource]**
   - **[パブリック IP]** > **[新規作成]** を選択します｡  **[パブリック IP アドレスの作成]** ウィンドウで、 **[名前]** フィールドに「**myPublicIPsourceVM**」と入力します。 **[SKU]** で **[Standard]** を選択します。 残りの部分は既定値のままにし、 **[OK]** をクリックします。
   - **NIC ネットワーク セキュリティ グループ**: **[Basic]** を選択します。
   - **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択します。
   - **受信ポートを選択**: **[SSH]** が選択されていることを確認します。

4. **[管理]** タブの **[監視]** で、 **[起動の診断]** を **[オフ]** に設定します。

5. **[Review + create]\(レビュー + 作成\)** を選択します。

6. 設定を確認し、 **[作成]** をクリックします。

## <a name="create-the-nat-gateway"></a>NAT ゲートウェイの作成

NAT ゲートウェイでは、1 つまたは複数のパブリック IP アドレス リソース、パブリック IP プレフィックス、またはその両方を使用できます。 パブリック IP リソース、パブリック IP プレフィックス、および NAT ゲートウェイ リソースを追加します。

このセクションでは、NAT ゲートウェイ リソースを使用して、次の NAT サービスのコンポーネントを作成して構成する方法について説明します。
  - NAT ゲートウェイ リソースによって変換される送信フローに使用する、パブリック IP プールとパブリック IP プレフィックス。
  - アイドル タイムアウトを既定値の 4 分から 10 分に変更します。

### <a name="create-a-public-ip-address"></a>パブリック IP アドレスの作成

1. ポータルの左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[パブリック IP アドレス]** を選択するか、Marketplace 検索で**パブリック IP アドレス**を検索します。 

2. **[パブリック IP アドレスの作成]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | IP バージョン | **[IPv4]** を選択します。
    | SKU | **[Standard]** を選択します。
    | 名前 | 「**myPublicIPsource**」と入力します。 |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[myResourceGroupNAT]** を選択します。 |
    | 場所 | **[米国東部 2]** を選択します。|

3. 残りの部分は既定値のままにし、 **[作成]** を選択します。

### <a name="create-a-public-ip-prefix"></a>パブリック IP プレフィックスの作成

1. ポータルの左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[パブリック IP プレフィックス]** を選択するか、Marketplace 検索で**パブリック IP プレフィックス**を検索します。

2. **[パブリック IP プレフィックスの作成]** の **[基本]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : **[myResourceGroupNAT]** > を選択します
   - **[インスタンスの詳細]**  >  **[名前]** : 「**myPublicIPprefixsource**」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** : **[米国東部 2]** を選択します。
   - **[インスタンスの詳細]**  >  **[プレフィックス サイズ]** : **[/31 (2 addresses)]\(/31 (2 アドレス)\)** を選択します

3. 残りの部分は既定値のままにし、 **[確認および作成]** を選択します。

4. 設定を確認し、 **[作成]** を選択します。


### <a name="create-a-nat-gateway-resource"></a>NAT ゲートウェイ リソースの作成

1. ポータルの左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[NAT ゲートウェイ]** を選択するか、Marketplace 検索で **NAT ゲートウェイ**を検索します。

2. **[ネットワーク アドレス変換 (NAT) ゲートウェイを作成します]** の **[基本]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : **[myResourceGroupNAT]** を選択します。
   - **[インスタンスの詳細]**  >  **[NAT ゲートウェイ名]** : **[myNATgateway]** を入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** : **[米国東部 2]** を選択します。
   - **[インスタンスの詳細]**  >  **[アイドル タイムアウト (分)]** : 「**10**」と入力します。
   - **[パブリック IP]** タブを選択するか、 **[次へ: パブリック IP]** を選択します。

3. **[パブリック IP]** タブに次の値を入力するか選択します。
   - **パブリック IP アドレス**: **[myPublicIPsource]** を選択します。
   - **パブリック IP プレフィックス**: **[myPublicIPprefixsource]** を選択します。
   - **[サブネット]** タブを選択するか、 **[次へ: サブネット]** を選択します。

4. **[サブネット]** タブに次の値を入力するか選択します。
   - **[仮想ネットワーク]** : **[myResourceGroupNAT]**  >  **[myVnetsource]** を選択します。
   - **[サブネット名]** : **[mySubnetsource]** の横のボックスを選択します。

5. **[Review + create]\(レビュー + 作成\)** を選択します。

6. 設定を確認し、 **[作成]** を選択します。

インターネットを宛先とするすべての送信トラフィックが、この NAT サービスを使用するようになります。  UDR を構成する必要はありません。


## <a name="prepare-destination-for-outbound-traffic"></a>送信トラフィックの宛先の準備

今度は、NAT サービスをテストできるよう、NAT サービスによって変換された送信トラフィックの宛先を作成しましょう。


## <a name="virtual-network-and-parameters-for-destination"></a>宛先の仮想ネットワークとパラメーター

宛先の VM をデプロイする前に、宛先の仮想マシンを配置できる仮想ネットワークを作成する必要があります。 次の手順はソース VM の手順と同じですが、宛先エンドポイントを公開するための若干の変更が追加されています。

このセクションの手順では、各パラメーターを次のように置き換える必要があります。

| パラメーター                   | 値                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | 米国東部 2      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>接続先の仮想マシンの作成

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]\(計算\)**  >  **[Ubuntu Server 18.04 LTS]** を選択するか、Marketplace 検索で **Ubuntu Server 18.04 LTS** を検索します。

2. **[仮想マシンの作成]** の **[基本]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : **[myResourceGroupNAT]** を選択します。
   - **[インスタンスの詳細]**  >  **[仮想マシン名]** : 「**myVMdestination**」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** > **[米国東部 2]** を選択します。
   - **[管理者アカウント]**  >  **[Authentication enter]\(認証を入力\)** : **[パスワード]** を選択します。
   - **[管理者アカウント]** > **[ユーザー名]** 、 **[パスワード]** 、および **[パスワードの確認]** に情報を入力します。
   - **[受信ポートの規則]**  >  **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択します。
   - **[受信ポートの規則]**  >  **[受信ポートの選択]** : **[SSH (22)]** および **[HTTP (80)]** を選択します。
   - **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。

3. **[ネットワーク]** タブで、以下が選択されていることを確認します。
   - **[仮想ネットワーク]** : **[myVnetdestination]**
   - **[サブネット]** : **[mySubnetdestination]**
   - **[パブリック IP]** > **[新規作成]** を選択します｡  **[パブリック IP アドレスの作成]** ウィンドウで、 **[名前]** フィールドに「**myPublicIPdestinationVM**」と入力します。 **[SKU]** で **[Standard]** を選択します。 残りの部分は既定値のままにし、 **[OK]** をクリックします。
   - **NIC ネットワーク セキュリティ グループ**: **[Basic]** を選択します。
   - **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択します。
   - **受信ポートを選択**: **[SSH]** および **[HTTP]** が選択されていることを確認します。

4. **[管理]** タブの **[監視]** で、 **[起動の診断]** を **[オフ]** に設定します。

5. **[Review + create]\(レビュー + 作成\)** を選択します。

6. 設定を確認し、 **[作成]** を選択します。

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>宛先 VM における Web サーバーの準備とペイロードのテスト

まず、宛先 VM の IP アドレスを検出する必要があります。 

1. ポータルの左側にある **[リソース グループ]** を選択します。
2. **[myResourceGroupNAT]** を選択します。
3. **[myVMdestination]** を選択します。
4. **[概要]** で、 **[パブリック IP アドレス]** の値をコピーし、VM へのアクセスに使用できるようにメモ帳に貼り付けます。

>[!IMPORTANT]
>パブリック IP アドレスをコピーしたら、後続の手順で使用できるようにメモ帳に貼り付けます。 これが宛先の仮想マシンであることがわかるようにしておいてください。

### <a name="sign-in-to-destination-vm"></a>宛先 VM へのサインイン

ご使用のブラウザーで [Azure Cloud Shell](https://shell.azure.com) を開きます。 前の手順で取得した IP アドレスを使用して、仮想マシンに SSH 接続します。

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

ログインしたら、次のコマンドをコピーして貼り付けます。  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

これらのコマンドによって仮想マシンが更新され、nginx がインストールされて、100 キロバイトのファイルが作成されます。 このファイルは、NAT サービスを使用してソース VM から取得されます。

宛先 VM との SSH セッションを閉じます。

## <a name="prepare-test-on-source-vm"></a>ソース VM におけるテストの準備

まず、ソース VM の IP アドレスを検出する必要があります。

1. ポータルの左側にある **[リソース グループ]** を選択します。
2. **[myResourceGroupNAT]** を選択します。
3. **[myVMsource]** を選択します。
4. **[概要]** で、 **[パブリック IP アドレス]** の値をコピーし、VM へのアクセスに使用できるようにメモ帳に貼り付けます。

>[!IMPORTANT]
>パブリック IP アドレスをコピーしたら、後続の手順で使用できるようにメモ帳に貼り付けます。 これがソースの仮想マシンであることがわかるようにしておいてください。

### <a name="log-into-source-vm"></a>ソース VM へのログイン

ご使用のブラウザーで [Azure Cloud Shell](https://shell.azure.com) の新しいタブを開きます。  前の手順で取得した IP アドレスを使用して、仮想マシンに SSH 接続します。 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

NAT サービスのテスト準備を行うために、次のコマンドをコピーして貼り付けます。

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

このコマンドによって仮想マシンが更新されます。go がインストールされ、GitHub から [hey](https://github.com/rakyll/hey) がインストールされて、ご利用のシェル環境が更新されます。

これで、NAT サービスをテストする準備が整いました。

## <a name="validate-nat-service"></a>NAT サービスの検証

ソース VM にログインしている間、**curl** と **hey** を使用して、宛先 IP アドレスへの要求を生成できます。

curl を使用して 100 キロバイトのファイルを取得します。  下の例の **\<ip-address-destination>** は、先ほどコピーした宛先 IP アドレスに置き換えてください。  **--output** パラメーターは、取得したファイルが破棄されることを示します。

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

**hey** を使用して、一連の要求を生成することもできます。 この場合も、 **\<ip-address-destination>** は、先ほどコピーした宛先 IP アドレスに置き換えてください。

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

このコマンドは、100 の要求を同時に 10 生成し、タイムアウトは 30 秒で、TCP 接続を再使用しません。  それぞれの要求で 100 キロバイトが取得されます。  実行の最後に、NAT サービスの稼動状態についていくつかの統計情報が **hey** からレポートされます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、NAT ゲートウェイ、およびすべての関連リソースを削除します。 NAT ゲートウェイを含むリソース グループ **[myResourceGroupNAT]** を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、NAT ゲートウェイを作成し、ソース VM と宛先 VM を作成した後、NAT ゲートウェイをテストしました。

Azure Monitor でメトリックを見て、NAT サービスの稼動状態を確認しましょう。 利用可能な SNAT ポートのリソース枯渇などの問題を診断します。  SNAT ポートのリソース枯渇は、新しいパブリック IP アドレス リソースかパブリック IP プレフィックス リソース、またはその両方を追加することで簡単に解決できます。

- [Virtual Network NAT](./nat-overview.md) について学習する。
- [NAT ゲートウェイ リソース](./nat-gateway-resource.md)について学習する。
- [Azure CLI を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-cli.md)をデプロイするためのクイックスタート。
- [Azure PowerShell を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-powershell.md)をデプロイするためのクイックスタート。
- [Azure portal を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-portal.md)をデプロイするためのクイックスタート。

> [!div class="nextstepaction"]

