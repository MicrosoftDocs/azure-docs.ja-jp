---
title: チュートリアル:NAT ゲートウェイの作成 - Azure portal
titlesuffix: Azure Virtual Network NAT
description: このクイックスタートでは、Azure portal を使用して NAT ゲートウェイを作成する方法について説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: 5010dc08b695a0376ace5dde935c63caf0c39633
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2020
ms.locfileid: "84711410"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal"></a>チュートリアル:Azure portal を使用した NAT ゲートウェイの作成

このチュートリアルでは、Azure Virtual Network NAT サービスを使用する方法について説明します。 Azure 内の仮想マシンに送信接続を提供する NAT ゲートウェイを作成しましょう。 

これらの手順は、必要に応じて、ポータルではなく [Azure CLI](quickstart-create-nat-gateway-cli.md) や [Azure PowerShell](quickstart-create-nat-gateway-powershell.md) を使用するか、[ARM テンプレート](quickstart-create-nat-gateway-powershell.md)をデプロイすることで実行できます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="virtual-network-and-parameters"></a>仮想ネットワークとパラメーター

VM をデプロイして NAT ゲートウェイを使用する前に、リソース グループおよび仮想ネットワークを作成しておく必要があります。

このセクションの手順では、各パラメーターを次のように置き換える必要があります。

| パラメーター                   | 値                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 米国東部 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>NAT ゲートウェイを使用する VM の作成

今度は、NAT サービスを使用する VM を作成します。 この VM には、ユーザーが VM にアクセスできるようにするための、インスタンスレベルのパブリック IP として使用するパブリック IP があります。 NAT サービスはフロー方向を認識し、サブネットの既定のインターネットの宛先を置き換えます。 VM のパブリック IP アドレスは、送信接続には使用されません。

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]\(計算\)**  >  **[Ubuntu Server 18.04 LTS]** を選択するか、Marketplace 検索で **Ubuntu Server 18.04 LTS** を検索します。

2. **[仮想マシンの作成]** の **[Basic]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : **[myResourceGroupNAT]** を選択します。
   - **[インスタンスの詳細]**  >  **[仮想マシン名]** : 「**myVM**」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** > **[米国東部 2]** を選択します。
   - **[管理者アカウント]**  >  **[認証の種類]** : **[パスワード]** を選択します。
   - **[管理者アカウント]** > **[ユーザー名]** 、 **[パスワード]** 、および **[パスワードの確認]** に情報を入力します。
   - **[受信ポートの規則]**  >  **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択します。
   - **[受信ポートの規則]**  >  **[受信ポートの選択]** : **[SSH (22)]** を選択します
   - **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。

3. **[ネットワーク]** タブで、以下が選択されていることを確認します。
   - **[仮想ネットワーク]** : **myVNet**
   - **[サブネット]** : **mySubnet**
   - **[パブリック IP]** > **[新規作成]** を選択します｡  **[パブリック IP アドレスの作成]** ウィンドウで、 **[名前]** フィールドに「**myPublicIPVM**」と入力し、 **[SKU]** で **[Standard]** を選択します。  **[OK]** をクリックします。
   - **NIC ネットワーク セキュリティ グループ**: **[Basic]** を選択します。
   - **[パブリック受信ポート]** : **[選択したポートを許可する]** を選択します。
   - **受信ポートを選択**: **[SSH]** が選択されていることを確認します。

4. **[管理]** タブの **[監視]** で、 **[起動の診断]** を **[オフ]** に設定します。

5. **[Review + create]\(レビュー + 作成\)** を選択します。 

6. 設定を確認し、 **[作成]** をクリックします。

## <a name="create-the-nat-gateway"></a>NAT ゲートウェイの作成

1 つまたは複数のパブリック IP アドレス リソース、パブリック IP プレフィックス、またはその両方を使用できます。 パブリック IP リソース、パブリック IP プレフィックス、および NAT ゲートウェイ リソースを追加します。

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
    | 名前 | 「**myPublicIP**」と入力します。 |
    | サブスクリプション | サブスクリプションを選択します。|
    | Resource group | **[myResourceGroupNAT]** を選択します。 |
    | 場所 | **[米国東部 2]** を選択します。|

3. 残りの部分は既定値のままにし、 **[作成]** を選択します。

### <a name="create-a-public-ip-prefix"></a>パブリック IP プレフィックスの作成

1. ポータルの左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[パブリック IP プレフィックス]** を選択するか、Marketplace 検索で**パブリック IP プレフィックス**を検索します。 

2. **[パブリック IP プレフィックスの作成]** の **[基本]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : **[myResourceGroupNAT]** > を選択します
   - **[インスタンスの詳細]**  >  **[名前]** :「**myPublicIPprefix**」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** : **[米国東部 2]** を選択します。
   - **[インスタンスの詳細]**  >  **[プレフィックス サイズ]** : **[/31 (2 addresses)]\(/31 (2 アドレス)\)** を選択します

3. 残りの部分は既定値のままにし、 **[確認および作成]** を選択します。

4. 設定を確認し、 **[作成]** を選択します。
   

### <a name="create-a-nat-gateway-resource"></a>NAT ゲートウェイ リソースの作成

1. ポータルの左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[NAT ゲートウェイ]** を選択するか、Marketplace 検索で **NAT ゲートウェイ**を検索します。

2. **[ネットワーク アドレス変換 (NAT) ゲートウェイを作成します]** の **[基本]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : **[myResourceGroupNAT]** を選択します。
   - **[インスタンスの詳細]**  >  **[NAT ゲートウェイ名]** :「**myNATgateway**」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** : **[米国東部 2]** を選択します。
   - **[インスタンスの詳細]**  >  **[アイドル タイムアウト (分)]** :「**10**」と入力します。
   - **[パブリック IP]** タブを選択するか、 **[次へ: パブリック IP]** を選択します。

3. **[パブリック IP]** タブに次の値を入力するか選択します。
   - **パブリック IP アドレス**: **[myPublicIP]** を選択します。
   - **[パブリック IP プレフィックス]** : **[myPublicIPprefix]** を選択します。
   - **[サブネット]** タブを選択するか、 **[次へ: サブネット]** を選択します。

4. **[サブネット]** タブに次の値を入力するか選択します。
   - **[仮想ネットワーク]** : **[myResourceGroupNAT]**  >  **[myVnet]** を選択します。
   - **[サブネット名]** : **[mySubnet]** の横のボックスを選択します。

5. **[Review + create]\(レビュー + 作成\)** を選択します。

6. 設定を確認し、 **[作成]** を選択します。

## <a name="discover-the-ip-address-of-the-vm"></a>VM の IP アドレスの検出

1. ポータルの左側にある **[リソース グループ]** を選択します。
2. **[myResourceGroupNAT]** を選択します。
3. **[myVM]** を選択します。
4. **[概要]** で、 **[パブリック IP アドレス]** の値をコピーし、VM へのアクセスに使用できるようにメモ帳に貼り付けます。

>[!IMPORTANT]
>パブリック IP アドレスをコピーし、VM へのアクセスに使用できるようにメモ帳に貼り付けます。

## <a name="sign-in-to-vm"></a>VM へのサインイン

ご使用のブラウザーで [Azure Cloud Shell](https://shell.azure.com) を開きます。 前の手順で取得した IP アドレスを使用して、仮想マシンに SSH 接続します。

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

これで、NAT サービスを使用する準備が整いました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、NAT ゲートウェイ、およびすべての関連リソースを削除します。 NAT ゲートウェイを含むリソース グループ **[myResourceGroupNAT]** を選択し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、NAT ゲートウェイとそれを使用する VM を作成しました。 

Azure Monitor でメトリックを見て、NAT サービスの稼動状態を確認しましょう。 利用可能な SNAT ポートのリソース枯渇などの問題を診断します。  SNAT ポートのリソース枯渇は、追加のパブリック IP アドレス リソースかパブリック IP プレフィックス リソース、またはその両方を追加することで解決できます。


- [Azure Virtual Network NAT](./nat-overview.md) について学習する。
- [NAT ゲートウェイ リソース](./nat-gateway-resource.md)について学習する。
- [Azure CLI を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-cli.md)をデプロイするためのクイックスタート。
- [Azure PowerShell を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-powershell.md)をデプロイするためのクイックスタート。
- [Azure portal を使用して NAT ゲートウェイ リソース](./quickstart-create-nat-gateway-portal.md)をデプロイするためのクイックスタート。
> [!div class="nextstepaction"]

