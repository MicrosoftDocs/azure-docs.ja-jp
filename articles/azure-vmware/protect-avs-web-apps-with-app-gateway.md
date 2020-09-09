---
title: Azure Application Gateway を使用して Azure VMware Solution 上の Web アプリを保護する
description: Azure VMware Solution で実行されている Web アプリを安全に公開するために Azure Application Gateway を構成します。
ms.topic: how-to
ms.date: 07/31/2020
ms.openlocfilehash: d4e193c58c5eccb29f603c3b4d56a09d26686975
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750604"
---
# <a name="use-azure-application-gateway-to-protect-your-web-apps-on-azure-vmware-solution"></a>Azure Application Gateway を使用して Azure VMware Solution 上の Web アプリを保護する

[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) は、Web アプリケーションに対するトラフィックを管理できるレイヤー 7 の Web トラフィック ロード バランサーです。 Cookie ベースのセッション アフィニティ、URL ベースのルーティング、Web アプリケーション ファイアウォール (WAF) などの多くの機能を備えています。 (すべての機能の一覧については、「[Azure Application Gateway の機能](../application-gateway/features.md)」を参照してください。)v1 と v2 の 2 つのバージョンで提供されています。 どちらも、Azure VMware Solution で実行されている Web アプリでテストされています。

この記事では、Azure VMware Solution で実行されている Web アプリを保護するために、Application Gateway を Web サーバー ファームの前で使用する一般的なシナリオについて、一連の構成と推奨事項を含めて説明します。 

## <a name="topology"></a>トポロジ
次の図に示すように、Application Gateway を使用して、Azure IaaS 仮想マシン、Azure 仮想マシン スケール セット、またはオンプレミスのサーバーを保護できます。 Azure VMware Solution の仮想マシンは、Application Gateway によってオンプレミスのサーバーとして扱われます。

![Application Gateway によって Azure VMware Solution の VM が保護されます。](media/protect-avs-web-apps-with-app-gw/app-gateway-protects.png)

> [!IMPORTANT]
> 現在、Azure Application Gateway は、Azure VMware Solution の仮想マシンで実行されている Web アプリを公開する、唯一のサポートされている方法です。

次の図は、Azure VMware Solution の Web アプリケーションを使用して Application Gateway を検証するために使用されるテスト シナリオを示しています。

![Web アプリを実行している Azure VMware Solution と Application Gateway の統合。](media/protect-avs-web-apps-with-app-gw/app-gateway-avs-scenario.png)

Application Gateway インスタンスは、専用サブネットのハブにデプロイされます。 これは Azure パブリック IP アドレスを保持します。仮想ネットワークに対して Standard の DDoS 保護をアクティブ化することをお勧めします。 Web サーバーは、NSX T0 および T1 ルーターの背後にある Azure VMware Solution プライベート クラウドでホストされています。 Azure VMware Solution は [ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) を使用して、ハブとオンプレミスのシステムとの通信を可能にします。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。
- Azure VMware Solution のプライベート クラウドがデプロイされ、実行されていること。

## <a name="deployment-and-configuration"></a>デプロイと構成

1. Azure portal で **Application Gateway** を検索し、 **[アプリケーション ゲートウェイの作成]** を選択します。

2. 次の図に示すように、基本的な詳細を入力し、 **[次へ: フロントエンド >]** を選択します。 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/create-app-gateway.png" alt-text="アプリケーション ゲートウェイの作成":::

3. フロントエンド IP アドレスの種類を選択します。 パブリックの場合は、既存のパブリック IP アドレスを選択するか、新しいものを作成します。 **[次へ: バックエンド >]** を選択します。

    > [!NOTE]
    > プライベート フロントエンドでは、Standard および Web Application Firewall (WAF) の SKU のみがサポートされています。

4. 次に、アプリケーションまたはサービスの一部であるインスタンスのセット (この場合は、Azure VMware Solution インフラストラクチャで実行されている仮想マシン) を表すバックエンド プールを追加します。 Azure VMware Solution のプライベート クラウドで実行されている Web サーバーの詳細を入力し、 **[追加]** を選択してから、 **[次へ: 構成 >]** を選択します。

1. **[構成]** タブで、 **[ルーティング規則の追加]** を選択します。

6. **[リスナー]** タブで、リスナーの詳細を入力します。 HTTPS を選択した場合、証明書は PFX ファイル、または Azure Key Vault の既存の証明書から指定する必要があります。 

7. **[バックエンド ターゲット]** タブを選択し、以前に作成したバックエンド プールを選択します。 **[HTTP 設定]** フィールドで、 **[新規追加]** を選択します。

8. HTTP 設定のパラメーターを構成します。 **[追加]** を選択します。

9. パスベースの規則を構成する場合は、 **[パスベースの規則を作成する複数のターゲットを追加します]** を選択します。 

10. パスベースの規則を追加し、 **[追加]** を選択します。 さらにパスベースの規則を追加するには、手順を繰り返します。 

11. パスベースの規則の追加を終えたら、再度 **[追加]** を選択し、 **[次へ: タグ >]** を選択します。 

12. 必要なタグがあれば追加します。 **[次へ: 確認と作成 >]** を選択します。

13. Application Gateway に対して検証が実行されます。成功した場合は、 **[作成]** を選択してデプロイします。

## <a name="configuration-examples"></a>構成の例

このセクションでは、次のユース ケースで、バックエンド プールとして Azure VMware Solution の仮想マシンを使用して、Application Gateway を構成する方法について説明します。 

- [複数サイトのホスティング](#hosting-multiple-sites)
- [URL によるルーティング](#routing-by-url)

### <a name="hosting-multiple-sites"></a>複数サイトのホスティング

Azure portal を使用して、アプリケーション ゲートウェイを作成するときに複数の Web サイトのホスティングを構成できます。 このチュートリアルでは、Azure VMware Solution のプライベート クラウドで実行されている仮想マシンを使用して、既存のアプリケーション ゲートウェイ上でバックエンド アドレス プールを定義します。 アプリケーション ゲートウェイは、「[ハブ アンド スポークのアーキテクチャで Azure VMware Solution を統合する](concepts-avs-hub-and-spoke-integration.md)」で説明されているように、ハブ仮想ネットワークの一部です。 このチュートリアルでは、複数のドメインを所有していることを前提とし、 www.contoso.com と www.fabrikam.com の例を使用します。

1. 仮想マシンを作成します。 Azure VMware Solution のプライベート クラウドで、仮想マシンの 2 つの異なるプールを作成します。1 つ目は Contoso を表し、2 つ目は Fabrikam を表します。 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs.png" alt-text="仮想マシンを作成します。":::

    このチュートリアルをわかりやすく説明するために、インターネット インフォメーション サービス (IIS) の役割がインストールされた Windows Server 2016 を使用しています。 仮想マシンがインストールされたら、次の PowerShell コマンドを実行して、各 VM で IIS を構成します。 

    ```powershell
    Install-WindowsFeature -Name Web-Server
    Add-Content -Path C:\inetpub\wwwroot\Default.htm -Value $($env:computername)
    ```

2. バックエンド プールを追加します。 既存のアプリケーション ゲートウェイ インスタンスで、左側のメニューから **[バックエンド プール]** を選択し、 **[追加]** を選択して、新しいプールの詳細を入力します。 右側のペインで **[追加]** を選択します。

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png" alt-text="バックエンド プールを追加します。" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-02.png":::

3. **[リスナー]** セクションで、各 Web サイトに新しいリスナーを作成します。 各リスナーの詳細を入力し、 **[追加]** を選択します。

4. 左側のナビゲーションで **[HTTP 設定]** を選択し、左側のペインで **[追加]** を選択します。 新しい HTTP 設定を作成するための詳細を入力して、 **[保存]** を選択します。

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png" alt-text="新しい HTTP 設定を作成するための詳細を入力して、[保存] を選択します。" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-03.png":::

5. 左側のメニューの **[規則]** セクションで規則を作成します。 各規則を対応するリスナーに関連付けます。 **[追加]** を選択します。

6. 対応するバックエンド プールと HTTP 設定を構成します。 **[追加]** を選択します。

7. 接続をテストします。 好みのブラウザーを開き、お使いの Azure VMware Solution 環境でホストされているさまざまな Web サイト (http://www.fabrikam.com など) に移動します。

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-multi-backend-pool-avs-07.png" alt-text="接続をテストします。":::

### <a name="routing-by-url"></a>URL によるルーティング

Azure Application Gateway を使用して、URL パスベースのルーティング規則を構成できます。 このチュートリアルでは、Azure VMware Solution のプライベート クラウドで実行されている仮想マシンを使用して、既存のアプリケーション ゲートウェイ上でバックエンド アドレス プールを定義します。 アプリケーション ゲートウェイは、[Azure VMware Solution の「Azure のネイティブ統合」のドキュメント](concepts-avs-hub-and-spoke-integration.md)で説明されているように、ハブ仮想ネットワークの一部です。 その後、Web トラフィックがプール内の適切なサーバーに確実に到着するようにルーティング規則を作成します。

1. 仮想マシンを作成します。 Azure VMware Solution のプライベート クラウドで、Web ファームを表す、仮想マシンのプールを作成します。 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs.png" alt-text="Azure VMware Solution で仮想マシンのプールを作成します。":::

    このチュートリアルをわかりやすく説明するために、IIS の役割がインストールされた Windows Server 2016 が使用されています。 仮想マシンがインストールされたら、次の PowerShell コマンドを実行して、各 VM でチュートリアル用に IIS を構成します。 

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

2. バックエンド プールを追加します。 既存のアプリケーション ゲートウェイ インスタンスに新しいバックエンド プールを 3 つ追加する必要があります。 左側のメニューで **[バックエンド プール]** を選択します。 **[追加]** を選択し、最初のプールである **contoso-web** の詳細を入力します。 ターゲットとして 1 つの VM を追加します。 **[追加]** を選択します。 **contoso-images** と **contoso-video** についてもこのプロセスを繰り返し、それぞれにターゲットとして一意の VM を 1 つ追加します。 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png" alt-text="新しいバックエンド プールを 3 つ追加します。" lightbox="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-02.png":::

3. **[リスナー]** セクションで、ポート 8080 を使用して、[基本] の種類の新しいリスナーを作成します。

4. 左側のナビゲーションで **[HTTP 設定]** を選択し、左側のペインで **[追加]** を選択します。 新しい HTTP 設定を作成するための詳細を入力して、 **[保存]** を選択します。

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-04.png" alt-text="HTP 設定の構成。":::

5. 左側のメニューの **[規則]** セクションで規則を作成します。 以前に作成したリスナーに各ルールを関連付けます。 次に、メインのバックエンド プールと HTTP 設定を構成します。 **[追加]** を選択します。

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-07.png" alt-text="左側のメニューの [規則] セクションで規則を作成します。":::

6. 構成をテストします。 Azure portal でアプリケーション ゲートウェイにアクセスし、 **[概要]** セクションで、パブリック IP アドレスをコピーします。 次に、新しいブラウザー ウィンドウを開き、URL `http://<app-gw-ip-address>:8080` を入力します。 

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-08.png" alt-text="Azure portal から構成をテストします。":::

    URL を `http://<app-gw-ip-address>:8080/images/test.htm` に変更します。

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-09.png" alt-text="URL を変更します。":::

    URL を `http://<app-gw-ip-address>:8080/video/test.htm` に再度変更します。

    :::image type="content" source="media/protect-avs-web-apps-with-app-gw/app-gateway-url-route-backend-pool-avs-10.png" alt-text="URL を再度変更します。":::

## <a name="next-steps"></a>次の手順

他の構成例については、「[Azure Application Gateway のドキュメント](https://docs.microsoft.com/azure/application-gateway/)」を参照してください。
