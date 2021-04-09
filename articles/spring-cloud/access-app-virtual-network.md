---
title: Azure Spring Cloud で仮想ネットワークのアプリにアクセスする
description: Azure Spring Cloud で仮想ネットワークのアプリにアクセスします。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 11/11/2020
ms.custom: devx-track-java
ms.openlocfilehash: 37c8b4bc186c217ecb27638f5f50297102345de7
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877665"
---
# <a name="access-your-application-in-a-private-network"></a>プライベート ネットワークのアプリにアクセスする

このドキュメントでは、プライベート ネットワーク内のアプリのエンドポイントにアクセスする方法について説明します。  アクセスするには、お使いのサブスクリプションに **Azure プライベート DNS ゾーン** を作成して、プライベート完全修飾ドメイン名 (FQDN) をその IP アドレスに変換または解決する必要があります。

仮想ネットワークで Azure Spring Cloud サービス インスタンスのアプリケーション向けに **[エンドポイントの割り当て]** がデプロイされている場合、そのエンドポイントはプライベート FQDN です。 このドメインは、プライベート ネットワーク内でのみアクセスできます。 アプリのエンドポイントは、アプリとサービスによって使用されます。 これには、「[アプリとデプロイを表示する](spring-cloud-howto-staging-environment.md#view-apps-and-deployments)」で説明されている **テスト エンドポイント** が含まれます。 「[Azure Spring Cloud アプリのログをリアルタイムでストリーム配信する](spring-cloud-howto-log-streaming.md)」で説明されている **ログ ストリーミング** も、プライベート ネットワーク内でのみ機能します。

## <a name="create-a-private-dns-zone"></a>プライベート DNS ゾーンの作成

次の手順に従って、プライベート ネットワークにアプリのプライベート DNS ゾーンを作成します。

1. Azure portal を開きます。 上部の検索ボックスで「**プライベート DNS ゾーン**」を検索し、その結果から **[プライベート DNS ゾーン]** を選択します。

2. **[プライベート DNS ゾーン]** ページで、 **[+ 追加]** を選択します。

3. **[プライベート DNS ゾーンの作成]** ページでフォームに入力します。 ゾーンの **[名前]** として「 **<span>private.azuremicroservices.io</span>** 」と入力します。

4. **[確認および作成]** を選択します。

5. **［作成］** を選択します

ゾーンの作成には数分かかることがあります。

## <a name="link-the-virtual-network"></a>仮想ネットワークのリンク

プライベート DNS ゾーンを仮想ネットワークにリンクさせるには、仮想ネットワーク リンクを作成する必要があります。

1. 上記で作成したプライベート DNS ゾーン リソース ( **<span>private.azuremicroservices.io</span>** ) を選択します 

2. 左側のウィンドウで、**[Virtual network links]\(仮想ネットワーク リンク\)** を選択します。

3. **[追加]** を選択します。

4. **[リンク名]** として「**azure-spring-cloud-dns-link**」と入力します。

5. **[仮想ネットワーク]** で、[Azure 仮想ネットワークに Azure Spring Cloud をデプロイする (VNet インジェクション)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md) 方法に関する記事の手順に従って作成した仮想ネットワークを選択します。

    ![仮想ネットワークリンクの追加](media/spring-cloud-access-app-vnet/add-virtual-network-link.png)

6. **[OK]** をクリックします。

## <a name="create-dns-record"></a>DNS レコードの作成

プライベート DNS ゾーンを使用して DNS を変換または解決するには、そのゾーンに種類 "A" のレコードを作成する必要があります。

1. [Azure 仮想ネットワークに Azure Spring Cloud をデプロイする (VNet インジェクション)](spring-cloud-tutorial-deploy-in-azure-virtual-network.md) 方法に関する記事の手順に従って作成した仮想ネットワーク リソースを選択します。

2. **[接続されているデバイス]** 検索ボックスに、「*kubernetes-internal*」と入力します。

3. フィルター処理された結果から、サービス インスタンスのサービス ランタイム **サブネット** に接続されている **デバイス** を見つけ、その **IP アドレス** をコピーします。 この例では、IP アドレスは *10.1.0.7* です。

    [ ![DNS レコードの作成](media/spring-cloud-access-app-vnet/create-dns-record.png) ](media/spring-cloud-access-app-vnet/create-dns-record.png)

または、次の az CLI コマンドを使用して IP をフェッチすることもできます。

```azurecli
SPRING_CLOUD_RG= # Resource group name of your Azure Spring Cloud service instance
SPRING_CLOUD= # Name of your Azure Spring Cloud service instance

SERVICE_RUNTIME_RG=`az spring-cloud show -g $SPRING_CLOUD_RG -n $SPRING_CLOUD --query \
"properties.networkProfile.serviceRuntimeNetworkResourceGroup" -o tsv`

IP_ADDRESS=`az network lb frontend-ip list --lb-name kubernetes-internal -g \
$SERVICE_RUNTIME_RG --query "[0].privateIpAddress" -o tsv`
```

4. 上記で作成したプライベート DNS ゾーン リソース ( **<span>private.azuremicroservices.io</span>** ) を選択します。

5. **[+ レコード セット]** を選択します。

6. **[レコード セットの追加]** で、この情報を入力または選択します。

    |設定     |値                                                                      |
    |------------|---------------------------------------------------------------------------|
    |名前        |「 *\** 」と入力します。                                                                 |
    |型        |**[A]** を選択します                                                               |
    |TTL         |「*1*」と入力します                                                                  |
    |TTL の単位    |**[時間]** を選択します                                                           |
    |IP アドレス  |手順 3 でコピーした IP アドレスを入力します。 このサンプルでは、「*10.1.0.7*」と入力します。    |

    **[OK]** をクリックします。

    ![プライベート DNS ゾーン レコードの追加](media/spring-cloud-access-app-vnet/private-dns-zone-add-record.png)

## <a name="assign-private-fqdn-for-your-application"></a>アプリにプライベート FQDN を割り当てる

[マイクロサービス アプリをビルドしてデプロイする](spring-cloud-tutorial-deploy-in-azure-virtual-network.md)方法に関する記事の手順を実行した後、アプリにプライベート FQDN を割り当てることができます。

1. 仮想ネットワークにデプロイされている Azure Spring Cloud サービス インスタンスを選択し、左側のメニューで **[アプリ]** タブを開きます。

2. アプリを選択して、 **[概要]** ページを表示します。

3. **[エンドポイントの割り当て]** を選択して、アプリにプライベート FQDN を割り当てます。 これには数分かかることがあります。

    ![プライベート エンドポイントの割り当て](media/spring-cloud-access-app-vnet/assign-private-endpoint.png)

4. これで、割り当てられたプライベート FQDN (ラベル付きの **URL**) が使用できるようになりました。 これは、プライベート ネットワーク内でのみアクセスできますが、インターネット上ではアクセスできません。

## <a name="access-application-private-fqdn"></a>アプリのプライベート FQDN にアクセスする

割り当ての後、プライベート ネットワークでアプリのプライベート FQDN にアクセスできます。 たとえば、同じ仮想ネットワークまたはピアリングされた仮想ネットワーク内にジャンプボックス マシンを作成し、そのジャンプボックス マシンでプライベート FQDN にアクセスできます。

![VNet でのプライベート エンドポイントへのアクセス](media/spring-cloud-access-app-vnet/access-private-endpoint.png)

## <a name="next-steps"></a>次のステップ

- [Application Gateway と Azure Firewall を使用してアプリをインターネットに公開する](spring-cloud-expose-apps-gateway-azure-firewall.md)

## <a name="see-also"></a>関連項目

- [VNET での Azure Spring Cloud のトラブルシューティング](spring-cloud-troubleshooting-vnet.md)
- [VNET での Azure Spring Cloud の実行に関するお客様の責任](spring-cloud-vnet-customer-responsibilities.md)