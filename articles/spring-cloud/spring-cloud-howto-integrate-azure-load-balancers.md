---
title: チュートリアル - Azure Spring Cloud と Azure の負荷分散ソリューションを統合する
description: Azure Spring Cloud と Azure の負荷分散ソリューションを統合する方法
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 3a7ed148c4bc8d58b2dcbc344656137a403c62eb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87037544"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Azure Spring Cloud と Azure の負荷分散ソリューションを統合する

Azure Spring Cloud では、Azure 上のマイクロサービスがサポートされています。  ビジネスを拡大するには、複数のデータ センターで Azure Spring Cloud の複数のインスタンスの管理が必要になる場合があります。

Azure には、さまざまな負荷分散ソリューションが既に用意されています。 Azure Spring Cloud と Azure の負荷分散ソリューションを統合するには、次の 3 つのオプションがあります。

1.  Azure Spring Cloud と Azure Traffic Manager を統合する
2.  Azure Spring Cloud と Azure App Gateway を統合する
3.  Azure Spring Cloud と Azure Front Door を統合する

## <a name="prerequisites"></a>前提条件

* Azure Spring Cloud:[ Azure Spring Cloud サービスを作成する方法](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)
* Azure Traffic Manager:[Traffic Manager を作成する方法](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* Azure App Gateway:[アプリケーション ゲートウェイを作成する方法](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* Azure Front Door:[フロント ドアを作成する方法](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Azure Spring Cloud と Azure Traffic Manager を統合する

Azure Spring Cloud を Traffic Manager と統合するには、そのパブリック エンドポイントを Traffic Manager のエンドポイントとして追加し、Traffic Manager と Azure Spring Cloud の両方にカスタム ドメインを構成します。

### <a name="add-endpoint-in-traffic-manager"></a>Traffic Manager でエンドポイントを追加する
Traffic Manager でエンドポイントを追加します。
1.  **[タイプ]** に *[外部エンドポイント]* を指定します。
1.  各 Azure Spring Cloud パブリック エンドポイントの完全修飾ドメイン名 (FQDN) を入力します。
1. **[OK]** をクリックします。

    ![Traffic Manager 1](media/spring-cloud-load-balancers/traffic-manager-1.png) ![Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>カスタム ドメインを構成する
構成を終了するには:
1.  ドメイン プロバイダーの Web サイトにサインインし、カスタム ドメインから Traffic Manager の Azure の既定のドメイン名への CNAME レコード マッピングを作成します。
1.  [カスタム ドメインを Azure Spring Cloud に追加する方法](spring-cloud-tutorial-custom-domain.md)の手順に従います。
1. Traffic Manager への上記のカスタム ドメイン バインディングを Azure Spring Cloud 対応のアプリ サービスに追加し、そこに SSL 証明書をアップロードします。

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Azure Spring Cloud と Azure App Gateway を統合する

Azure Spring Cloud サービスと統合するには、次の構成を完了します。

### <a name="configure-backend-pool"></a>バックエンド プールを構成する
1. **[ターゲットの種類]** に *[IP アドレス* または *FQDN]* を指定します。
1. Azure Spring Cloud のパブリック エンドポイントを入力します。

    ![App Gateway 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>カスタム プローブを追加する
1. **[正常性プローブ]** 、 **[追加]** の順に選択して、カスタムの **[プローブ]** ダイアログを開きます。 
1. 重要なのは、 **[ホスト名をバックエンド HTTP 設定から選択します]** オプションに *[はい]* を選択することです。

    ![App Gateway 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>HTTP 設定を構成する
1.  **[HTTP 設定]** 、 **[追加]** の順に選択して、HTTP 設定を追加します。
1.  **[新しいホスト名でオーバーライドする]:** *[はい]* を選択します。
1.  **[ホスト名をオーバーライドする]** : **[バックエンド ターゲットからホスト名を選択する]** を選択します。
1.  **[カスタム プローブの使用]** : *[はい]* を選択し、上で作成したカスタム プローブを選択します。

    ![App Gateway 3](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Azure Spring Cloud と Azure Front Door を統合する

Azure Spring Cloud サービスと統合し、バックエンド プールを構成するには、以下を実行します。 
1. **バックエンド プールを追加します**。
1. ホストを追加してバックエンド エンドポイントを指定します。

    ![フロント ドア 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  **[バックエンド ホストの種類]** に *[カスタム ホスト]* を指定します。
1.  **[バックエンド ホスト名]** に、Azure Spring Cloud パブリック エンドポイントの FQDN を入力します。
1.  **[バックエンド ホスト ヘッダー]** は、既定値 (**バックエンド ホスト名**と同じ) をそのまま使用します。

    ![フロント ドア 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>次のステップ
* [Traffic Manager を作成する方法](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* [アプリケーション ゲートウェイを作成する方法](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* [フロント ドアを作成する方法](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)
