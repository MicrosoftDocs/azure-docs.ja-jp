---
title: クイックスタート - Azure portal を使用してプライベート リンク サービスを作成する
titlesuffix: Azure Private Link
description: このクイックスタートでは、Azure portal を使用して Private Link サービスを作成する方法を説明します
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: quickstart
ms.date: 02/03/2020
ms.author: allensu
ms.openlocfilehash: 0d873401d377a03581a319769604f3d976f365be
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927240"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Private Link サービスを作成する

Azure Private Link サービスは、Private Link によって管理されるお客様独自のサービスを指します。 Azure Standard Load Balancer の背後で動作するサービスまたはリソースへのアクセス権を Private Link に付与することができます。 お客様のサービスのコンシューマーは自分の仮想ネットワークから非公開でそこにアクセスできます。 このクイックスタートでは、Azure portal を使用して Private Link サービスを作成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-an-internal-load-balancer"></a>内部ロード バランサーを作成します。

最初に、仮想ネットワークを作成します。 次に、Private Link サービスで使用する内部ロード バランサーを作成します。

## <a name="virtual-network-and-parameters"></a>仮想ネットワークとパラメーター

このセクションでは、仮想ネットワークを作成します。 また、ご自分の Private Link サービスにアクセスするロード バランサーをホストするサブネットを作成します。

このセクションの手順では、各パラメーターを次のように置き換える必要があります。

| パラメーター                   | Value                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 米国東部 2      |
| **\<IPv4-address-space>**   | 10.3.0.0/16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-a-standard-load-balancer"></a>Standard Load Balancer の作成

ポータルを使用して Standard 内部ロード バランサーを作成します。 指定する名前と IP アドレスは、ロード バランサーのフロント エンドとして自動的に構成されます。

1. ポータルの左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[Load Balancer]** の順に選択します。

1. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力または選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | **サブスクリプション**               | サブスクリプションを選択します。    |
    | **リソース グループ**         | ボックスから **[myResourceGroupLB]** を選択します。|
    | **Name**                   | 「**myLoadBalancer**」と入力します。                                   |
    | **リージョン**         | **[米国東部 2]** を選択します。                                        |
    | **Type**          | **[内部]** を選択します。                                        |
    | **SKU**           | **[Standard]** を選択します。                          |
    | **Virtual Network**           | **[myVNet]** を選択します。                          |
    | **IP アドレスの割り当て**              | **[静的]** を選択します。   |
    | **プライベート IP アドレス**|ご自分の仮想ネットワークとサブネットのアドレス空間内にあるアドレスを入力します。 例: 10.3.0.7。  |

1. 残りの設定は既定値をそのまま使用し、 **[確認と作成]** を選択します。

1. **[確認および作成]** タブで、 **[作成]** を選択します。

### <a name="create-standard-load-balancer-resources"></a>Standard ロード バランサーのリソースを作成する

このセクションでは、バックエンド アドレス プールと正常性プローブのロード バランサー設定を構成します。 また、ロード バランサー規則も指定します。

#### <a name="create-a-back-end-pool"></a>バックエンド プールの作成

バックエンド アドレス プールには、ロード バランサーに接続された仮想 NIC の IP アドレスが含まれています。 このプールを使用して、トラフィックを自分のリソースに分散させることができます。 **myBackendPool** という名前のバックエンド アドレス プールを作成し、トラフィックを負荷分散するリソースを含めます。

1. 左端のメニューから **[すべてのサービス]** を選択します。
1. **[すべてのリソース]** を選択し、リソースの一覧から **[myLoadBalancer]** を選択します。
1. **[設定]** で、 **[バックエンド プール]** 、 **[追加]** の順に選択します。
1. **[バックエンド プールの追加]** ページ上で、バックエンド プールの名前として「**myBackendPool**」と入力し、**[追加]** を選択します。

#### <a name="create-a-health-probe"></a>正常性プローブの作成

正常性プローブを使用して、ロード バランサーでリソースの状態を監視できるようにします。 正常性プローブは、正常性チェックへのリソース応答に基づいて、ロード バランサーのローテーションに含めるリソースを動的に追加したり削除したりします。

正常性プローブを作成してリソースの正常性を監視するには:

1. 左端のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[myLoadBalancer]** を選択します。

1. **[設定]** で、 **[正常性プローブ]** 、 **[追加]** の順に選択します。

1. **[正常性プローブの追加]** ページで、以下の値を入力または選択します。

   - **Name**:「**myHealthProbe**」と入力します。
   - **プロトコル**: **[TCP]** を選択します。
   - **ポート**:「**80**」と入力します。
   - **間隔**: 「**15**」と入力します。 この値はプローブの試行の間隔を示す秒数です。
   - **[異常のしきい値]** : 「**2**」と入力します。 この値は、プローブが連続して失敗した回数です (これを超えると仮想マシンは異常と見なされます)。

1. **[OK]** を選択します。

#### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則では、リソースに対するトラフィックの分散方法を定義します。 規則で定義される内容:

- 受信トラフィックのフロントエンド IP 構成。
- トラフィックを受信するバックエンド IP プール。
- 必須のソースおよび宛先のポート。

**myLoadBalancerRule** という名前のロード バランサー規則は、**LoadBalancerFrontEnd** フロントエンドのポート 80 をリッスンしています。 この規則では、同じポート 80 でネットワーク トラフィックをバックエンド アドレス プール **myBackendPool** に送信します。

ロード バランサー規則を作成するには:

1. 左端のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[myLoadBalancer]** を選択します。

1. **[設定]** で、**[負荷分散規則]**、**[追加]** の順に選択します。

1. **[負荷分散規則の追加]** ページで、次の値を入力または選択します (まだ指定されていない場合)。

   - **Name**:「**myLoadBalancerRule**」と入力します。
   - **[フロントエンド IP アドレス]** : 「**LoadBalancerFrontEnd**」と入力します。
   - **プロトコル**: **[TCP]** を選択します。
   - **ポート**:「**80**」と入力します。
   - **[バックエンド ポート]** :「**80**」と入力します。
   - **[バックエンド プール]** : **[myBackendPool]** を選択します。
   - **[正常性プローブ]** : **[myHealthProbe]** を選択します。 

1. **[OK]** を選択します。

## <a name="create-a-private-link-service"></a>Private Link サービスを作成する

このセクションでは、Standard ロード バランサーの背後に Private Link サービスを作成します。

1. Azure portal のページの左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[プライベート リンク センター (プレビュー)]** を選択します。 Private Link の検索にはポータルの検索ボックスを使用することもできます。

1. **[プライベート リンク センター - 概要]**  >  **[独自のサービスを公開して、他のユーザーが接続できるようにする]** で、 **[開始]** を選択します。

1. **[Create a private link service - Basics]\(Private Link サービスの作成 - 基本\)** で、この情報を入力または選択します。

    | 設定           | 値                                                                        |
    |-------------------|------------------------------------------------------------------------------|
    | プロジェクトの詳細:  |                                                                              |
    | **サブスクリプション**      | サブスクリプションを選択します。                                                     |
    | **リソース グループ**    | **[myResourceGroupLB]** を選択します。                                                    |
    | インスタンスの詳細: |                                                                              |
    | **Name**              | 「**myPrivateLinkService**」と入力します。 |
    | **リージョン**            | **[米国東部 2]** を選択します。                                                        |

1. **Next:次へ: 送信設定** を選択します。

1. **[Create a private link service - Outbound settings]\(Private Link サービスの作成 - 送信設定\)** で、この情報を入力または選択します。

    | 設定                           | 値                                                                           |
    |-----------------------------------|---------------------------------------------------------------------------------|
    | **Load Balancer**                     | **[myLoadBalancer]** を選択します。                                                           |
    | **ロード バランサー フロントエンド IP アドレス** | **myLoadBalancer** のフロントエンド IP アドレスを選択します。                                |
    | **ソース NAT 仮想ネットワーク**        | **[myVNet]** を選択します。                                                                   |
    | **ソース NAT サブネット**                 | **[myBackendSubnet]** を選択します。                                                          |
    | **TCP プロキシ v2 を有効にする**               | アプリケーションで TCP プロキシ v2 ヘッダーが必要かどうかに応じて、**[はい]** または **[いいえ]** を選択します。 |
    | **プライベート IP アドレスの設定**       | 各 NAT IP の割り当て方法と IP アドレスを構成します。                  |

1. **Next:次へ: アクセス セキュリティ** を選択します。

1. **[Create a private link service - Access security]\(Private Link サービスの作成 - アクセス セキュリティ\)** で **[表示]** を選択し、**[ロールベースのアクセス制御のみ]** を選択します。
  
1. **[Next: Tags]\(次へ: タグ\)**  >  **[確認と作成]** の順に選択するか、ページ上部にある **[確認と作成]** タブを選択します。

1. 情報を確認し、**[作成]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Private Link サービスの使用が完了したら、リソース グループを削除して、このクイックスタートで使用したリソースをクリーンアップします。

1. ポータルの上部にある検索ボックスに「**myResourceGroupLB**」と入力し、検索結果から **[myResourceGroupLB]** を選択します。
1. **[リソース グループの削除]** を選択します。
1. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名の入力\)** に「**myResourceGroup**」と入力します。
1. **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure 内部ロード バランサーと Private Link サービスを作成しました。 [Azure portal を使用してプライベート エンドポイントを作成する](https://docs.microsoft.com/azure/private-link/create-private-endpoint-portal)方法についてもご覧ください。
