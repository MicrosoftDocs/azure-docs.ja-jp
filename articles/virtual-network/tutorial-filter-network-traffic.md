---
title: ネットワーク トラフィックをフィルター処理する - チュートリアル - Azure portal
titlesuffix: Azure Virtual Network
description: このチュートリアルでは、ネットワーク セキュリティ グループと Azure portal を使用して、サブネットに対するネットワーク トラフィックをフィルター処理する方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: kumud
ms.openlocfilehash: 2d0519abdf25a6fc8373f9d1a3a7232a9783d316
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984902"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>チュートリアル:Azure portal を使用してネットワーク セキュリティ グループでネットワーク トラフィックをフィルター処理する

ネットワーク セキュリティ グループを使用して、仮想ネットワーク サブネットとの間で送受信されるネットワーク トラフィックをフィルター処理できます。 ネットワーク セキュリティ グループには、IP アドレス、ポート、およびプロトコルでネットワーク トラフィックをフィルター処理するセキュリティ規則が含まれています。 セキュリティ規則は、サブネットに展開されたリソースに適用されます。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ネットワーク セキュリティ グループと規則を作成する
> * 仮想ネットワークを作成し、ネットワーク セキュリティ グループをサブネットに関連付ける
> * 仮想マシン (VM) をサブネットに展開する
> * トラフィック フィルターをテストする

普段使用している [Azure CLI](tutorial-filter-network-traffic-cli.md) や [PowerShell](tutorial-filter-network-traffic-powershell.md) を使用してこのチュートリアルの手順を実行することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選択してから、 **[仮想ネットワーク]** を選択します。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[作成]** を選択します。

    | Setting                 | 値                                              |
    | ---                     | ---                                                |
    | 名前                    | myVirtualNetwork                                   |
    | アドレス空間           | 10.0.0.0/16                                        |
    | Subscription            | サブスクリプションを選択します。                          |
    | Resource group          | **[新規作成]** を選択し、「*myResourceGroup*と入力します。 |
    | Location                | **[米国東部]** を選択します。                                |
    | サブネット名            | mySubnet                                           |
    | サブネット アドレス範囲  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>アプリケーション セキュリティ グループを作成する

アプリケーション セキュリティ グループを使用すると、Web サーバーなど、同様の機能を持つサーバーをグループ化できます。

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[Marketplace を検索]** ボックスに、「*アプリケーション セキュリティ グループ*」と入力します。 検索結果に**アプリケーション セキュリティ グループ**が表示されたら、それを選択し、 **[すべて]** の下の**アプリケーション セキュリティ グループ** をもう一度選択します。次に、 **[作成]** を選択します。
3. 以下の情報を入力するか選んだ後、 **[作成]** を選びます。

    | Setting        | 値                                                         |
    | ---            | ---                                                           |
    | 名前           | myAsgWebServers                                               |
    | Subscription   | サブスクリプションを選択します。                                     |
    | Resource group | **[既存のものを使用]** 、 **[myResourceGroup]** の順に選択します。 |
    | Location       | East US                                                       |

4. 手順 3 を繰り返し、次の値を指定します。

    | Setting        | 値                                                         |
    | ---            | ---                                                           |
    | 名前           | myAsgMgmtServers                                              |
    | Subscription   | サブスクリプションを選択します。                                     |
    | Resource group | **[既存のものを使用]** 、 **[myResourceGroup]** の順に選択します。 |
    | Location       | East US                                                       |

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[ネットワーク]** を選び、 **[ネットワーク セキュリティ グループ]** を選びます。
3. 以下の情報を入力するか選んだ後、 **[作成]** を選びます。

    |Setting|値|
    |---|---|
    |名前|myNsg|
    |Subscription| サブスクリプションを選択します。|
    |Resource group | **[既存のものを使用]** 、 *[myResourceGroup]* の順に選択します。|
    |Location|East US|

## <a name="associate-network-security-group-to-subnet"></a>ネットワーク セキュリティ グループをサブネットに関連付ける

1. ポータルの上部にある *[リソース、サービス、ドキュメントの検索]* ボックスで、「*myNsg*」と入力します。 検索結果に **myNsg** が表示されたら、それを選択します。
2. 次の図に示すように、 **[設定]** で **[サブネット]** を選択し、 **[+ 関連付け]** を選択します。

    ![NSG をサブネットに関連付ける](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. **[サブネットの関連付け]** で **[仮想ネットワーク]** を選択し、 **[myVirtualNetwork]** を選択します。 **[サブネット]** を選択し、 **[mySubnet]** を選択して **[OK]** を選択します。

## <a name="create-security-rules"></a>セキュリティ規則を作成する

1. 次の図に示すように、 **[設定]** で **[受信セキュリティ規則]** を選択し、 **[+ 追加]** を選択します。

    ![受信セキュリティ規則を追加する](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. **myAsgWebServers** アプリケーション セキュリティ グループに、ポート 80 と 443 を許可するセキュリティ規則を作成します。 **[受信セキュリティ規則]** で、値を入力するか次の値を選択し、残りの既定値はそのまま受け入れて、 **[追加]** を選択します。

    | Setting                 | 値                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Destination             | **[アプリケーションのセキュリティ グループ]** を選択し、**アプリケーション セキュリティ グループ**として **[myAsgWebServers]** を選択します。  |
    | 宛先ポート範囲 | 「80,443」と入力                                                                                                    |
    | Protocol                | [TCP] を選択                                                                                                      |
    | 名前                    | Allow-Web-All                                                                                                   |

3. 手順 2 を繰り返して、次の値を指定します。

    | Setting                 | 値                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Destination             | **[アプリケーションのセキュリティ グループ]** を選択し、**アプリケーション セキュリティ グループ**として **[myAsgMgmtServers]** を選択します。 |
    | 宛先ポート範囲 | 「3389」と入力                                                                                                      |
    | Protocol                | [TCP] を選択                                                                                                      |
    | Priority                | 「110」と入力                                                                                                       |
    | 名前                    | Allow-RDP-All                                                                                                   |

    このチュートリアルでは、*myAsgMgmtServers* アプリケーション セキュリティ グループに割り当てられている VM 用に、RDP (ポート 3389) がインターネットに公開されています。 運用環境では、ポート 3389 をインターネットに公開せずに、VPN またはプライベート ネットワーク接続を使用して、管理する Azure リソースに接続することをお勧めします。

手順 1 から 3 を完了したら、作成した規則を確認します。 一覧は、次の図のように表示されます。

![セキュリティ規則](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>仮想マシンを作成する

仮想ネットワーク内に 2 つの VM を作成します。

### <a name="create-the-first-vm"></a>最初の VM を作成する

1. Azure Portal の左上隅にある **[+ リソースの作成]** を選択します。
2. **[コンピューティング]** 、 **[Windows Server 2016 Datacenter]** の順に選択します。
3. 次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用します。

    |Setting|値|
    |---|---|
    |Subscription| サブスクリプションを選択します。|
    |Resource group| **[既存のものを使用]** を選択し、 **[myResourceGroup]** を選択します。|
    |名前|myVmWeb|
    |Location| **[米国東部]** を選択します。|
    |ユーザー名| 任意のユーザー名を入力します。|
    |パスワード| 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|

   

4. VM のサイズを選択して、 **[選択]** を選択します。
5. **[ネットワーク]** で次の値を選択します。それ以外は既定値のままにしてください。

    |Setting|値|
    |---|---|
    |仮想ネットワーク |**[myVirtualNetwork]** を選択します。|
    |NIC ネットワーク セキュリティ グループ |**[Advanced] \(詳細設定)** を選択します。|
    |パブリック受信ポート|**[なし]** を選択します。 |

6. 左下隅の **[確認および作成]** を選択し、 **[作成]** を選択して VM のデプロイを開始します。

### <a name="create-the-second-vm"></a>2 つ目の VM を作成する

手順 1 から 6 をもう一度実行します。ただし、手順 3 では、VM の名前を *myVmMgmt* にします。 VM のデプロイには数分かかります。 VM がデプロイされるまで、次の手順に進まないでください。

## <a name="associate-network-interfaces-to-an-asg"></a>ネットワーク インターフェイスを ASG に関連付ける

ポータルで VM が作成されると、各 VM 用のネットワーク インターフェイスが作成され、そのネットワーク インターフェイスを VM に接続します。 各 VM 用のネットワーク インターフェイスを、前に作成したアプリケーション セキュリティ グループの 1 つに追加します。

1. ポータルの上部にある *[リソース、サービス、ドキュメントの検索]* ボックスで、「*myVmWeb*」と入力します。 検索結果に **[myVmWeb]** VM が表示されたら、それを選択します。
2. **[設定]** で、 **[ネットワーク]** を選択します。  次の図に示すように、 **[Configure the application security groups]\(アプリケーション セキュリティ グループの構成\)** を選択し、 **[Application security groups]\(アプリケーション セキュリティ グループ\)** で **[myAsgWebServers]** を選択して、 **[OK]** を選択します。

    ![ASG を関連付ける](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. 手順 1 および 2 をもう一度実行し、**myVmMgmt** VM を検索して、**myAsgMgmtServers** ASG を選択します。

## <a name="test-traffic-filters"></a>トラフィック フィルターをテストする

1. *myVmMgmt* VM に接続します。 ポータル上部の [検索] ボックスに「*myVmMgmt*」と入力します。 検索結果に **[myVmMgmt]** が表示されたら、それを選択します。 **[接続]** を選択します。
2. **[RDP ファイルのダウンロード]** を選択します。
3. ダウンロードした RDP ファイルを開き、 **[接続]** を選択します。 VM の作成時に指定したユーザー名とパスワードを入力します。 場合によっては、 **[その他]** 、 **[別のアカウントを使用する]** を選択して、VM の作成時に入力した資格情報を指定する必要があります。
4. **[OK]** を選択します。
5. サインイン処理中に証明書の警告が表示される場合があります。 警告を受け取ったら、 **[はい]** または **[続行]** を選択して接続処理を続行します。

    インターネットから、*myVmMgmt* VM に接続されているネットワーク インターフェイスが含まれている *myAsgMgmtServers* アプリケーション セキュリティ グループに対する送信でポート 3389 が許可されているため、この接続は成功します。

6. PowerShell セッションで次のコマンドを入力し、*myVmMgmt* VM から *myVmWeb* VM に接続します。

    ``` 
    mstsc /v:myVmWeb
    ```

    myVmMgmt VM から myVmWeb VM に接続できるのは、同じ仮想ネットワーク内にある VM が既定で任意のポートを使用して互いに通信できるためです。 ただし、*myAsgWebServers* のセキュリティ規則は、インターネットからのポート 3389 を介した受信を許可せず、すべてのリソースに対するインターネットからの受信トラフィックは既定で拒否されるため、インターネットから *myVmWeb* VM へのリモート デスクトップ接続を作成することはできません。

7. *myVmWeb* VM に Microsoft IIS をインストールするには、*myVmWeb* VM の PowerShell セッションから次のコマンドを入力します。

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. IIS のインストールが完了したら、*myVmWeb* VM から切断します。*myVmMgmt* VM のリモート デスクトップ接続は保持されます。
9. *myVmMgmt* VM から切断します。
10. Azure portal の上部にある *[リソース、サービス、ドキュメントの検索]* ボックスに、お使いのコンピューターから「*myVmWeb*」と入力します。 検索結果に **[myVmWeb]** が表示されたら、それを選択します。 実際の VM の**パブリック IP アドレス**をメモします。 次の図に示すアドレスは 137.135.84.74 になっていますが、実際のアドレスは異なります。

    ![パブリック IP アドレス](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. インターネットから *myVmWeb* Web サーバーにアクセスできることを確認するには、コンピューターでインターネット ブラウザーを開き、`http://<public-ip-address-from-previous-step>` にアクセスします。 IIS のウェルカム画面が表示されます。*myVmWeb* VM に接続されているネットワーク インターフェイスを含む *myAsgWebServers* アプリケーション セキュリティ グループへのインターネットからの受信トラフィックが、ポート 80 で許可されているためです。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそれに含まれるすべてのリソースが不要になったら、それらを削除します。

1. ポータル上部の **[検索]** ボックスに「*myResourceGroup*」と入力します。 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ネットワーク セキュリティ グループを作成し、それを仮想ネットワーク サブネットに関連付けました。 ネットワーク セキュリティ グループについて詳しくは、[ネットワーク セキュリティ グループの概要](security-overview.md)と[ネットワーク セキュリティ グループの管理](manage-network-security-group.md)に関する記事を参照してください。

Azure の既定では、サブネット間でトラフィックがルーティングされます。 代わりに、たとえばファイアウォールとして機能する VM を介してサブネット間でトラフィックをルーティングすることもできます。 ルート テーブルを作成する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ルート テーブルの作成](./tutorial-create-route-table-portal.md)
