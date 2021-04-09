---
title: Azure Application Gateway を使用して Azure VMware Solution 上の Web アプリを保護する
description: Azure VMware Solution で実行されている Web アプリを安全に公開するために Azure Application Gateway を構成します。
ms.topic: how-to
ms.date: 02/10/2021
ms.openlocfilehash: f92027ee46fdaae275939acaea10e144b6bde101
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601918"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Azure Application Gateway を使用して Azure VMware Solution 上の Web アプリを保護する

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) は、Web アプリケーションに対するトラフィックを管理できるレイヤー 7 の Web トラフィック ロード バランサーです。 これは、Azure VMware Solution v1.0 と v2.0 の両方で提供されています。 どちらのバージョンも、Azure VMware Solution で実行されている Web アプリでテストされています。

機能は次のとおりです。 
- Cookie ベースのセッション アフィニティ
- URL ベースのルーティング
- Web アプリケーション ファイアウォール (WAF)

すべての機能の一覧については、「[Azure Application Gateway の機能](../application-gateway/features.md)」を参照してください。 

この記事では、Web サーバー ファームの前で Application Gateway を使用して、Azure VMware Solution で実行されている Web アプリを保護する方法について説明します。 

## <a name="topology"></a>トポロジ
次の図に、Application Gateway を使用して、Azure IaaS 仮想マシン (VM)、Azure 仮想マシン スケール セット、またはオンプレミスのサーバーを保護する方法を示します。 Application Gateway では、Azure VMware Solution VM がオンプレミス サーバーとして扱われます。 

![Application Gateway で Azure IaaS 仮想マシン (VM)、Azure 仮想マシン スケール セット、またはオンプレミス サーバーがどのように保護されるかを示す図。](media/protect-azure-vmware-solution-with-application-gateway/app-gateway-protects.png)

> [!IMPORTANT]
> 現在、Azure Application Gateway は、Azure VMware Solution の VM で実行されている Web アプリを公開する、サポートされている唯一の方法です。

次の図は、Azure VMware Solution の Web アプリケーションを使用して Application Gateway を検証するために使用されるテスト シナリオを示しています。

:::image type="content" source="media/hub-spoke/azure-vmware-solution-second-level-traffic-segmentation.png" alt-text="Azure VMware Solution の Web アプリケーションを使用して Application Gateway を検証するために使用されるテスト シナリオを示す図。" border="false":::

Application Gateway インスタンスは、専用サブネットのハブにデプロイされます。 これは Azure パブリック IP アドレスを保持します。 仮想ネットワーク用に [Azure DDoS Protection Standard](../ddos-protection/ddos-protection-overview.md) をアクティブ化することをお勧めします。 Web サーバーは、NSX T0 および T1 ゲートウェイの背後にある Azure VMware Solution プライベート クラウドでホストされています。 Azure VMware Solution では、[ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) を使用して、ハブとオンプレミスのシステムとの通信が可能になります。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。
- Azure VMware Solution のプライベート クラウドがデプロイされ、実行されていること。

## <a name="deployment-and-configuration"></a>デプロイと構成

1. Azure portal で **Application Gateway** を検索し、 **[アプリケーション ゲートウェイの作成]** を選択します。

2. 次の図に示すように、基本的な詳細を入力し、 **[次へ: フロントエンド >]** を選択します。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/create-app-gateway.png" alt-text="Azure portal の [アプリケーション ゲートウェイの作成] ページを示すスクリーンショット。":::

3. フロントエンド IP アドレスの種類を選択します。 パブリックの場合は、既存のパブリック IP アドレスを選択するか、新しいものを作成します。 **Next:バックエンド >** を選択します。

    > [!NOTE]
    > プライベート フロントエンドでは、Standard および Web Application Firewall (WAF) の SKU のみがサポートされています。

4. Azure VMware Solution インフラストラクチャで実行される VM のバックエンド プールを追加します。 Azure VMware Solution のプライベート クラウドで実行されている Web サーバーの詳細を入力し、 **[追加]** を選択します。  次に、 **[次のステップ: 構成 >]** を選択します。

5. **[構成]** タブで、 **[ルーティング規則の追加]** を選択します。

6. **[リスナー]** タブで、リスナーの詳細を入力します。 HTTPS を選択した場合、証明書は、PFX ファイル、または Azure Key Vault の既存の証明書から指定する必要があります。 

7. **[バックエンド ターゲット]** タブを選択し、以前に作成したバックエンド プールを選択します。 **[HTTP 設定]** フィールドで、 **[新規追加]** を選択します。

8. HTTP 設定のパラメーターを構成します。 **[追加]** を選択します。

9. パスベースの規則を構成する場合は、 **[パスベースの規則を作成する複数のターゲットを追加します]** を選択します。 

10. パスベースの規則を追加し、 **[追加]** を選択します。 さらにパスベースの規則を追加するには、手順を繰り返します。 

11. パスベースの規則の追加を終えたら、再度 **[追加]** を選択し、 **[次へ: タグ >]** を選択します。 

12. タグを追加し、 **[次へ: 確認と作成 >]** を選択します。

13. Application Gateway に対して検証が実行されます。成功した場合は、 **[作成]** を選択してデプロイします。

## <a name="configuration-examples"></a>構成の例

次のユース ケースで、バックエンド プールとして Azure VMware Solution の VM を使用して、Application Gateway を構成します。 

- [複数サイトのホスティング](#hosting-multiple-sites)
- [URL によるルーティング](#routing-by-url)

### <a name="hosting-multiple-sites"></a>複数サイトのホスティング

この手順では、Azure VMware Solution のプライベート クラウドで実行されている VM を使用して、既存のアプリケーション ゲートウェイ上でバックエンド アドレス プールを定義する方法を示します。 

>[!NOTE]
>この手順では、複数のドメインを持っていることを前提とし、 www.contoso.com と www.fabrikam.com の例を使用します。


1. プライベート クラウドで、VM の 2 つの異なるプールを作成します。 1 つは Contoso、2 つ目は Fabrikam を表します。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool.png" alt-text="VSphere クライアントでの Web サーバーの詳細の概要を示すスクリーンショット。":::

    インターネット インフォメーション サービス (IIS) の役割がインストールされた Windows Server 2016 を使用しています。 VM がインストールされたら、次の PowerShell コマンドを実行して、各 VM で IIS を構成します。 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. 既存のアプリケーション ゲートウェイ インスタンスで、左側のメニューから **[バックエンド プール]** を選択し、 **[追加]** を選択して、新しいプールの詳細を入力します。 右側のペインで **[追加]** を選択します。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png" alt-text="バックエンド プールを追加するための [バックエンド プール] ページのスクリーンショット。" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-02.png":::

3. **[リスナー]** セクションで、各 Web サイトに新しいリスナーを作成します。 各リスナーの詳細を入力し、 **[追加]** を選択します。

4. 左側で **[HTTP 設定]** を選択し、左側のペインで **[追加]** を選択します。 新しい HTTP 設定を作成するための詳細を入力して、 **[保存]** を選択します。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png" alt-text="新しい HTTP 設定を作成するための [HTTP 設定] ページのスクリーンショット。" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-03.png":::

5. 左側のメニューの **[規則]** セクションで規則を作成します。 各規則を対応するリスナーに関連付けます。 **[追加]** を選択します。

6. 対応するバックエンド プールと HTTP 設定を構成します。 **[追加]** を選択します。

7. 接続をテストします。 好みのブラウザーを開き、お使いの Azure VMware Solution 環境でホストされているさまざまな Web サイト (http://www.fabrikam.com など) に移動します。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-multi-backend-pool-07.png" alt-text="接続のテストが成功したことを示すブラウザー ページのスクリーンショット。":::

### <a name="routing-by-url"></a>URL によるルーティング

次の手順では、Azure VMware Solution プライベート クラウドで実行されている VM を使用してバックエンド アドレス プールを定義します。 プライベート クラウドは、既存のアプリケーション ゲートウェイ上にあります。 その後、Web トラフィックがプール内の適切なサーバーに確実に到着するようにルーティング規則を作成します。

1. プライベート クラウドで、Web ファームを表す仮想マシン プールを作成します。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool.png" alt-text="別の VM の概要を示す VSphere クライアントのページのスクリーンショット。":::

    このチュートリアルをわかりやすく説明するために、IIS の役割がインストールされた Windows Server 2016 が使用されています。 VM がインストールされたら、次の PowerShell コマンドを実行して、VM チュートリアルごとに IIS を構成します。 

    1 つ目の仮想マシンの contoso-web-01 は、メインの Web サイトをホストします。

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

    2 つ目の仮想マシンの contoso-web-02 は、画像サイトをホストします。
 
    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "images" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\images\test.htm -Value $($env:computername)
    ```

    3 つ目の仮想マシンの contoso-web-03 は、ビデオ サイトをホストします。

    ```powershell
    Install-WindowsFeature -Name Web-Server
    New-Item -Path "C:\inetpub\wwwroot\" -Name "video" -ItemType "directory"
    Add-Content -Path C:\inetpub\wwwroot\video\test.htm -Value $($env:computername)
    ```

2. 既存のアプリケーション ゲートウェイ インスタンスに新しいバックエンド プールを 3 つ追加します。 

   1. 左側のメニューで **[バックエンド プール]** を選択します。 
   1. **[追加]** を選択し、最初のプールである **contoso-web** の詳細を入力します。 
   1. ターゲットとして 1 つの VM を追加します。 
   1. **[追加]** を選択します。 
   1. **contoso-images** と **contoso-video** についてもこのプロセスを繰り返し、ターゲットとして一意の VM を 1 つ追加します。 

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png" alt-text="3 つの新しいバックエンド プールの追加を示す [バックエンド プール] ページのスクリーンショット。" lightbox="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-02.png":::

3. **[リスナー]** セクションで、ポート 8080 を使用して、[基本] の種類の新しいリスナーを作成します。

4. 左側のナビゲーションで **[HTTP 設定]** を選択し、左側のペインで **[追加]** を選択します。 新しい HTTP 設定を作成するための詳細を入力して、 **[保存]** を選択します。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-04.png" alt-text="HTTP 設定の構成を示す [HTTP 設定の追加] ページのスクリーンショット。":::

5. 左側のメニューの **[規則]** セクションで規則を作成します。 以前に作成したリスナーに各ルールを関連付けます。 次に、メインのバックエンド プールと HTTP 設定を構成します。 **[追加]** を選択します。

    :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-07.png" alt-text="バックエンド ターゲットにルーティング規則を構成するための [ルーティング規則の追加] ページのスクリーンショット。":::

6. 構成をテストします。 Azure portal でアプリケーション ゲートウェイにアクセスし、 **[概要]** セクションで、パブリック IP アドレスをコピーします。 

   1. 新しいブラウザー ウィンドウを開き、URL `http://<app-gw-ip-address>:8080` を入力します。 

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-08.png" alt-text="構成のテストが成功したことを示すブラウザー ページのスクリーンショット。":::

   1. URL を `http://<app-gw-ip-address>:8080/images/test.htm` に変更します。

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-09.png" alt-text="新しい URL で成功した別のテストのスクリーンショット。":::

   1. URL を `http://<app-gw-ip-address>:8080/video/test.htm` に再度変更します。

      :::image type="content" source="media/protect-azure-vmware-solution-with-application-gateway/app-gateway-url-route-backend-pool-10.png" alt-text="最終的な URL で成功したテストのスクリーンショット。":::

## <a name="next-steps"></a>次の手順

Application Gateway を使用した Azure VMware ソリューションで実行されている Web アプリの保護について理解したので、次のことを確認する必要がある可能性があります。

- [さまざまなシナリオ向けの Azure Application Gateway の構成](../application-gateway/configuration-overview.md)。
- [Azure VMware Solution ワークロードを分散するための Traffic Manager のデプロイ](deploy-traffic-manager-balance-workloads.md)。
- [Azure NetApp Files と Azure VMware Solution ベースのワークロードの統合](netapp-files-with-azure-vmware-solution.md)。
- [仮想ネットワーク内の Azure リソースの保護](../ddos-protection/ddos-protection-overview.md)。
