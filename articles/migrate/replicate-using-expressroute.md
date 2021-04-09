---
title: Azure Migrate Server Migration を使用して ExpressRoute 経由でデータをレプリケートする
description: Azure Migrate Server Migration を使用したレプリケーションのための Azure ExpressRoute の使用方法
author: DeSeelam
ms.author: deseelam
ms.manager: bsiva
ms.topic: how-to
ms.date: 02/22/2021
ms.openlocfilehash: 5dd27e4502ac70ef10f2623ed6dfb2f62de37f06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102448830"
---
# <a name="replicate-data-over-expressroute-with-azure-migrate-server-migration"></a>Azure Migrate: Server Migration を使用して ExpressRoute 経由でデータをレプリケートする

この記事では、サーバーを Azure に移行するときに、ExpressRoute 回線経由でデータをレプリケートするための [Azure Migrate: Server Migration](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-server-migration-tool) の構成方法について説明します。

## <a name="understand-azure-expressroute-circuits"></a>Azure ExpressRoute 回線について理解する
ExpressRoute (ER) 回線は、オンプレミスのインフラストラクチャを接続プロバイダー経由で Microsoft に接続します。 ExpressRoute 回線は、プライベート ピアリング、Microsoft ピアリング、またはその両方を使用するように構成できます。 [ExpressRoute 回線とピアリング](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare)に関する記事を参照して、ExpressRoute で使用できるさまざまなピアリング オプションの詳細を確認してください。

Azure Migrate の Server Migration ツールを使用すると、オンプレミスのサーバーと他のクラウドのサーバーを Azure の仮想マシンに簡単に移行できます。 このツールは、継続的なレプリケーション ストリームをセット アップして、移行対象サーバーから Azure サブスクリプションのマネージド ディスクにデータをレプリケートすることによって機能します。 サーバーを移行する準備ができたら、Azure 内のレプリケート済みデータを使用してサーバーを移行します。

オンプレミスのサーバーからレプリケートされたデータは、インターネット経由で (セキュリティで保護された暗号化接続を使用して)、または ExpressRoute 接続経由で Azure サブスクリプションに送信するように構成できます。 移行するサーバーの数が多い場合、レプリケーションに ExpressRoute を使用すれば、ExpressRoute 回線で使用可能なプロビジョニング済みの帯域幅を使用することによって、サーバーをより効率的に移行できます。

この記事では、次の内容について説明します。
> [!div class="checklist"]
>
> * ExpressRoute 回線を使用してプライベート ピアリングでデータをレプリケートする方法。
> * ExpressRoute 回線を使用して Microsoft ピアリングでデータをレプリケートする方法。

## <a name="replicate-data-using-an-expressroute-circuit-with-private-peering"></a>ExpressRoute 回線を使用してプライベート ピアリングでデータをレプリケートする

> [!NOTE]
> プライベート ピアリング回線経由のレプリケーションは、現時点では [VMware 仮想マシンを Azure にエージェントレス移行する](./tutorial-migrate-vmware.md)場合にのみサポートされています。 他の[レプリケーション方法](./migrate-services-overview.md#azure-migrate-server-migration-tool)についてのプライベート エンドポイントのサポートは、間もなく利用できるようになります。

VMware 仮想マシンを Azure に移行するエージェントレス方式では、Azure Migrate アプライアンスは最初に、ご利用のサブスクリプションのストレージ アカウント (キャッシュ ストレージ アカウント) にレプリケーション データをアップロードします。 次に、Azure Migrate サービスによって、キャッシュ ストレージ アカウントのレプリケート済みデータが、ご利用のサブスクリプションのレプリカ マネージド ディスクに移動されます。 プライベート ピアリング回線をレプリケーションに使用するには、プライベート エンドポイントを作成し、キャッシュ ストレージ アカウントの使用にアタッチします。 プライベート エンドポイントでは、ご使用の仮想ネットワーク (VNet) の 1 つ以上のプライベート IP アドレスが使用され、ストレージ アカウントが実質的に VNet に取り込まれます。 プライベート エンドポイントを使用すると、Azure Migrate アプライアンスは ExpressRoute プライベート ピアリングを使用してキャッシュ ストレージ アカウントに接続し、プライベート IP アドレスでデータを直接転送できます。 <br/>  

![レプリケーション プロセス](./media/replicate-using-expressroute/replication-process.png)

> [!Important]
>
> - レプリケーション データに加えて、Azure Migrate アプライアンスではレプリケーションの調整などのコントロール プレーン アクティビティのための Azure Migrate サービスとの通信も行われます。 Azure Migrate アプライアンスと Azure Migrate サービスの間のコントロール プレーン通信は、Azure Migrate サービスのパブリック エンドポイントで引き続きインターネット経由で行われます。
> - ストレージ アカウントのプライベート エンドポイントは、Azure Migrate アプライアンスが配置されているネットワークからアクセスできる必要があります。
> - キャッシュ ストレージ アカウントの BLOB サービス エンドポイントについての Azure Migrate アプライアンスによる DNS クエリが、キャッシュ ストレージ アカウントにアタッチされたプライベート エンドポイントのプライベート IP アドレスに解決されるように DNS を構成する必要があります。
> - キャッシュ ストレージ アカウントは、パブリック エンドポイントでアクセス可能である必要があります。 (Azure Migrate サービスでは、キャッシュ ストレージ アカウントのパブリック エンドポイントを使用して、ストレージ アカウントのデータをレプリカ マネージド ディスクに移動します。) 


### <a name="1-pre-requisites"></a>1.前提条件

プライベート エンドポイントを作成する Azure ユーザーには、プライベート エンドポイントが作成されるリソース グループと仮想ネットワークに対する次のアクセス許可が必要です。

**ユース ケース** | **アクセス許可** 
--- | --- 
 プライベート エンドポイントを作成および管理する。 | Microsoft.Network/privateEndpoint/write/action<br/>Microsoft.Network/privateEndpoint/read/action  
|プライベート エンドポイントを VNet/サブネットにアタッチする。<br/>これはプライベート エンドポイントが作成される仮想ネットワークに対して必要です。| Microsoft.Network/virtualNetworks/subnet/join/action  Microsoft.Network/virtualNetworks/join/action
|プライベート エンドポイントをストレージ アカウントにリンクする。 <br/>| Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnectionApproval/action <br/> Microsoft.Microsoft.Storage/storageAccounts/privateEndpointConnections/read
|ネットワーク インターフェイスを作成してネットワーク セキュリティ グループに参加させる。 | Microsoft.Network/networkInterfaces/read <br/> Microsoft.Network/networkInterfaces/subnets/write <br/> Microsoft.Network/networkInterfaces/subnets/read<br/> Microsoft.Network/networkSecurityGroups/join/action (省略可能)
プライベート DNS ゾーンを作成および管理する。| プライベート DNS ゾーンの共同作成者ロール <br/> _Or_ <br/> Microsoft.Network/privateDnsZones/A/* <br/>  Microsoft.Network/privateDnsZones/write Microsoft.Network/privateDnsZones/read <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/write <br/> Microsoft.Network/privateEndpoints/privateDnsZoneGroups/read <br/> Microsoft.Network/privateDnsZones/virtualNetworkLinks/write <br/>  Microsoft.Network/privateDnsZones/virtualNetworkLinks/read <br/> Microsoft.Network/virtualNetworks/join/action 

### <a name="2-identify-the-cache-storage-account"></a>2.キャッシュ ストレージ アカウントを特定する 
 
Azure Migrate プロジェクトで (Azure portal のエクスペリエンスを使用して) 仮想マシンのレプリケーションを初めて構成するときに、キャッシュ ストレージ アカウントが自動的に作成されます。 ストレージ アカウントは、Azure Migrate プロジェクトを作成したのと同じサブスクリプションとリソース グループに作成されます。

ストレージ アカウントを作成して見つけるには、次のようにします。

1. Azure portal のエクスペリエンスを使用して、Azure Migrate プロジェクトの 1 つ以上の仮想マシンをレプリケートします。
2. Azure Migrate プロジェクトのリソース グループに移動します。
3. ストレージ アカウント名に **"lsa"** というプレフィックスを指定して、キャッシュ ストレージ アカウントを見つけます。

![リソース グループ ビュー](./media/replicate-using-expressroute/storage-account-name.png)

> [!Tip]
>  
> リソース グループ内に **"lsa"** というプレフィックスが付いたストレージ アカウントが複数ある場合は、[レプリケーション設定およびターゲット構成] メニューに移動して、プロジェクト内のいずれかのレプリケートされた VM を調べることによって、ストレージ アカウントを確認できます。 <br/> 
> ![レプリケーション設定の概要](./media/replicate-using-expressroute/storage-account.png)

### <a name="3-upgrade--cache-storage-account-to-general-purpose-v2"></a>3.キャッシュ ストレージ アカウントを General Purpose v2 にアップグレードする 

プライベート エンドポイントは、General Purpose v2 (GPv2) のストレージ アカウントでのみ作成できます。 キャッシュ ストレージ アカウントが GPv2 ストレージ アカウントでない場合は、次の手順に従って GPv2 にアップグレードします。

1. ストレージ アカウントに移動します。
2. **[構成]** を選択します。
3. **[アカウントの種類]** の **[アップグレード]** を選択します。
4. **[アップグレードの確認]** で、アカウントの名前を入力します。
5. ページの下部にある **[アップグレード]** を選択します。

![ストレージ アカウントのアップグレード](./media/replicate-using-expressroute/upgrade-storage-account.png)

### <a name="4-create-a-private-endpoint-for-the-storage-account"></a>4.ストレージ アカウントのプライベート エンドポイントを作成する

1. ストレージ アカウントに移動し、左側のメニューから **[ネットワーク]** を選択し、 **[プライベート エンドポイント接続]** タブを選択します。  
2. **[+ プライベート エンドポイント]** を選択します。

    a. **[プライベート エンドポイントの作成]** ウィンドウで、**サブスクリプション** と **リソース グループ** を選択します。 プライベート エンドポイントの名前を指定し、ストレージ アカウントのリージョンを選択します。  
    ![PE 構成ウィンドウ](./media/replicate-using-expressroute/storage-account-private-endpoint-creation.png)

    b. **[リソース]** タブで、ストレージ アカウントがある **サブスクリプション名** を指定します。 **[リソースの種類]** として **[Microsoft.Storage/storageAccounts]** を選択します。 **[リソース]** で、GPv2 タイプのレプリケーション ストレージ アカウントの名前を指定します。 **[ターゲット サブリソース]** として **[Blob]** を選択します。  
    ![storageaccountpesettings](./media/replicate-using-expressroute/storage-account-private-endpoint-settings.png)

    c. **[構成]** タブで、ストレージ アカウントのプライベート エンドポイントの **仮想ネットワーク** と **サブネット** を選択します。  

    > [!Note]
    > この仮想ネットワークは、ExpressRoute ゲートウェイ エンドポイントが含まれているか、ExpressRoute ゲートウェイを持つ仮想ネットワークに接続されている必要があります。 

    **[プライベート DNS の統合]** セクションで **[はい]** を選択して、プライベート DNS ゾーンと統合します。 **[はい]** を選択すると、選択した仮想ネットワークに DNS ゾーンが自動的にリンクされ、プライベート エンドポイント用に作成された新しい IP と完全修飾ドメイン名の DNS 解決に必要な DNS レコードが追加されます。 詳細については、[プライベート DNS ゾーン](https://docs.microsoft.com/azure/dns/private-dns-overview)をご覧ください。

    ![privatednszone](./media/replicate-using-expressroute/private-dns-zone.png)

    d. プライベート エンドポイントに **タグ** を追加することもできます。  

    e. 詳細の入力が完了したら、 **[確認および作成]** に進みます。 検証が完了したら、 **[作成]** を選択してプライベート エンドポイントを作成します。

    > [!Note]
    > プライベート エンドポイントを作成しているユーザーがストレージ アカウントの所有者でもある場合、プライベート エンドポイントは自動承認されます。 そうでない場合、所有者がプライベート エンドポイントの使用を承認する必要があります。 

#### <a name="create-private-dns-zones-and-add-dns-records-manually-optional"></a>プライベート DNS ゾーンを作成して DNS レコードを手動で追加する (省略可能)

プライベート エンドポイントを作成するときに、プライベート DNS ゾーンと統合するオプションを選択しなかった場合は、このセクションの手順に従ってプライベート DNS ゾーンを手動で作成します。 

> [!Note]
> プライベート DNS ゾーンと統合することに対して **[はい]** を選択した場合は、このセクションをスキップできます。 

1. プライベート DNS ゾーンを作成します。 

    ![createprivatedns](./media/replicate-using-expressroute/create-private-dns.png)

    a. **[プライベート DNS ゾーン]** ページで、 **[+追加]** ボタンを選択して新しいゾーンの作成を開始します。  
    b. **[プライベート DNS ゾーンの作成]** ページで、必要な詳細情報を入力します。 プライベート DNS ゾーンの名前を「_privatelink_.blob.core.windows.net」と入力します。  
    c. **[確認と作成]** タブに進み、DNS ゾーンを確認して作成します。   

2. 仮想ネットワークにプライベート DNS ゾーンをリンクします。  

    上で作成したプライベート DNS ゾーンは、プライベート エンドポイントがアタッチされている仮想ネットワークにリンクされている必要があります。

    a. 前の手順で作成したプライベート DNS ゾーンに移動し、ページの左側にある仮想ネットワーク リンクに移動します。 **[+追加]** ボタンを選択します。   
    b. 必須の詳細を入力します。 **[サブスクリプション]** と **[仮想ネットワーク]** の各フィールドには、プライベート エンドポイントがアタッチされている仮想ネットワークについて、対応する詳細を入力する必要があります。 その他のフィールドはそのままでかまいません。

3. 次の手順では、DNS ゾーンに DNS レコードを追加します。 ストレージ アカウントの完全修飾ドメイン名のエントリをプライベート DNS ゾーンに追加します。

    a. プライベート DNS ゾーンに移動し、ページの左側にある **[概要]** セクションに移動します。 **[+ レコード セット]** を選択してレコードの追加を開始します。  

    b. **[レコード セットの追加]** ページで、完全修飾ドメイン名およびプライベート IP のエントリを A タイプのレコードとして追加します。

> [!Important]
> ソース環境からストレージ アカウントのプライベート エンドポイントのプライベート IP アドレスを解決するために、追加の DNS 設定が必要になる場合があります。 必要な DNS 構成については、[こちら](https://docs.microsoft.com/azure/private-link/private-endpoint-dns#on-premises-workloads-using-a-dns-forwarder)の記事をご覧ください。

## <a name="replicate-data-using-an-expressroute-circuit-with-microsoft-peering"></a>ExpressRoute 回線を使用して Microsoft ピアリングでデータをレプリケートする

下の図に示すように、Microsoft ピアリングまたは既存のパブリック ピアリング ドメイン (新しい ExpressRoute 接続では非推奨) を使用して、ExpressRoute 回線経由でレプリケーション トラフィックをルーティングできます。
![replicationwithmicrosoftpeering](./media/replicate-using-expressroute/replication-with-microsoft-peering.png)

レプリケーション データが Microsoft ピアリング回線を通る場合でも、Azure Migrate サービスとのその他の通信 (コントロール プレーン) についてはオンプレミス サイトからのインターネット接続が必要になります。 レプリケーション プロセスを調整するためにレプリケーション アプライアンスおよび Hyper-V ホストがアクセスする必要がある URL がいくつかあり、これらには ExpressRoute を介して到達できません。 [VMware エージェントレス移行](https://docs.microsoft.com/azure/migrate/migrate-appliance#public-cloud-urls)または[エージェントベース移行](https://docs.microsoft.com/azure/migrate/migrate-replication-appliance)の移行シナリオに基づいて、URL 要件を確認できます。  

オンプレミス サイトでプロキシを使用し、レプリケーション トラフィックに対して ExpressRoute を使用する場合は、オンプレミス アプライアンス上で、関連する URL に対してプロキシ バイパスを構成する必要があります。 

### <a name="configure-proxy-bypass-rules-on-the-azure-migrate-appliance-for-vmware-agentless-migrations"></a>Azure Migrate アプライアンス上でプロキシ バイパス ルールを構成する (VMware エージェントレス移行の場合)

1. Azure Migrate アプライアンスにログインします (リモート デスクトップ)。   
2. メモ帳を使用して、C:/ProgramData/MicrosoftAzure/Config/appliance.jsonファイルを開きます。
3. このファイルで、"EnableProxyBypassList": "false" という行を "EnableProxyBypassList": "true" に変更します。 変更を保存し、アプライアンスを再起動します。
4. 再起動後、アプライアンス構成マネージャーを開くと、Web アプリの UI に プロキシ バイパス オプションが表示されます。 以下の URL をプロキシ バイパス リストに追加します。   
    - .*.vault.azure.net
    - .*.servicebus.windows.net
    - .*.discoverysrv.windowsazure.com
    - .*.migration.windowsazure.com
    - .*.hypervrecoverymanager.windowsazure.com
    - .*.blob.core.windows.net

### <a name="configure-proxy-bypass-rules-on-the-replication-appliance-for-agent-based-migrations"></a>レプリケーション アプライアンス上でプロキシ バイパス ルールを構成する (エージェントベース移行の場合)

構成サーバーとプロセス サーバー上でプロキシ バイパス リストを構成するには、下の手順に従います。

1. システム ユーザーのコンテキストにアクセスするための [PsExec ツールをダウンロード](https://docs.microsoft.com/sysinternals/downloads/psexec)します。
2. コマンド ラインで psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe" を実行して、システム ユーザーのコンテキストでインターネット エクスプローラーを開きます。
3. IE にプロキシ設定を追加します。
4. バイパスの一覧に、Azure ストレージの URL (*.blob.core.windows.net) を追加します。  

上記のバイパス ルールにより、レプリケーション トラフィックは ExpressRoute を経由し、管理通信はインターネットのプロキシを経由するようになります。  

さらに、次の BGP コミュニティのルート フィルター内でルートをアドバタイズして、Azure Migrate レプリケーション トラフィックがインターネットではなく ExpressRoute 回線を経由するようにする必要があります。  

- ソース Azure リージョンのリージョン BGP コミュニティ (Azure Migrate プロジェクト リージョン)
- ターゲット Azure リージョンのリージョン BGP コミュニティ (移行用リージョン)
- Azure Active Directory の BGP コミュニティ (12076:5060)

詳細については、[ルート フィルター](https://docs.microsoft.com/azure/expressroute/how-to-routefilter-portal)と、[ExpressRoute の BGP コミュニティ](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp)の一覧に関するページを参照してください。 

## <a name="next-steps"></a>次のステップ

- [ExpressRoute 回線](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings)について詳しく学習する。
- [ExpressRoute ルーティング ドメイン](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#peeringcompare)について詳しく学習する。
- [プライベート エンドポイント](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)について詳しく学習する。