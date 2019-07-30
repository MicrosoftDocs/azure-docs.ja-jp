---
title: ハイブリッド接続 - Azure App Service | Microsoft Docs
description: ハイブリッド接続を作成し、それを使用して分散ネットワーク内のリソースにアクセスする方法
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
editor: ''
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 4b125649dee51680625ac5a92b31bdc9f6830529
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069622"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Services からのハイブリッド接続 #

ハイブリッド接続は、Azure のサービスであり、Azure App Service の機能でもあります。 サービスとして使用した場合、その用途と機能は、Azure App Service の用途と機能を上回ります。 ハイブリッド接続の詳細と Azure App Service では提供されない使用方法については、[Azure Relay ハイブリッド接続][HCService]に関するページを参照してください。

Azure App Service 内では、ハイブリッド接続を使用して、他のネットワークのアプリケーション リソースにアクセスできます。 アプリからアプリケーション エンドポイントにアクセスできます。 アプリケーションにアクセスするために別の機能を有効にすることはありません。 App Service で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。 つまり、TCP リッスン ポートにアクセスしている限り、任意のオペレーティング システムの任意のアプリケーションがハイブリッド接続エンドポイントになることができます。 ハイブリッド接続では、アプリケーション プロトコルやアクセス先は認識されません。 それは、ネットワーク アクセスを提供するだけです。  


## <a name="how-it-works"></a>動作のしくみ ##
ハイブリッド接続機能は、Service Bus Relay に対する 2 つの発信呼び出しで構成されます。 App Service でアプリが実行されているホストにライブラリから接続されます。 また、ハイブリッド接続マネージャー (HCM) から Service Bus Relay へも接続します。 HCM は、アクセスしようとしているネットワーク ホスト内にデプロイされるリレー サービスです。 

この 2 つの結合された接続を通して、HCM の他方の側の固定されたホストとポートの組み合わせに至るアプリの TCP トンネルが設定されます。 接続では、セキュリティには TLS 1.2 が、認証/承認には Shared Access Signature (SAS) キーが使用されます。    

![ハイブリッド接続のフローの概要図][1]

アプリが DNS 要求を行い、その要求が構成済みのハイブリッド接続エンドポイントと一致した場合、発信 TCP トラフィックがハイブリッド接続を介してリダイレクトされます。  

> [!NOTE]
> つまり、ハイブリッド接続では常に DNS 名を使用するようにします。 一部のクライアント ソフトウェアは、エンドポイントで IP アドレスが使用されている場合は DNS 参照を実行しません。
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service のハイブリッド接続のメリット ###

ハイブリッド接続機能には、次のようなさまざまなメリットがあります。

- アプリは、オンプレミスのシステムとサービスへセキュリティで保護されたアクセスを実行できます。
- インターネットにアクセス可能なエンドポイントは必要ありません。
- すばやく簡単に設定できます。 
- 各ハイブリッド接続が単一のホストとポートの組み合わせに照合されるため、セキュリティ面で優れています。
- 通常はファイアウォールに穴を開ける必要はありません。 接続はすべて、標準的な Web ポート経由の発信です。
- ネットワーク レベルの機能であるため、アプリで使用される言語とエンドポイントで使用されるテクノロジに依存しません。
- 単一のアプリから複数のネットワークにアクセスするために使用できます。 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>ハイブリッド接続で実行できないこと ###

ハイブリッド接続では次のことができません。

- ドライブのマウント
- UDP の使用
- FTP パッシブ モードや拡張パッシブ モードなどの動的ポートを使用する TCP ベースのサービスへのアクセス
- LDAP のサポート (UDP が必要な場合があるため)
- Active Directory のサポート (App Service worker にドメイン参加できないため)

## <a name="add-and-create-hybrid-connections-in-your-app"></a>アプリでハイブリッド接続を追加または作成する ##

ハイブリッド接続を作成するには、[Azure Portal][portal] に移動し、アプリを選択します。 **[ネットワーク]**  >  **[ハイブリッド接続エンドポイントの構成]** の順に選択します。 ここで、アプリ用に構成されているハイブリッド接続を確認できます。  

![ハイブリッド接続の一覧のスクリーンショット][2]

新しいハイブリッド接続を追加するには、 **[+] [ハイブリッド接続の追加]** を選択します。  既に作成したハイブリッド接続が一覧表示されます。 アプリに 1 つ以上のハイブリッド接続を追加するには、追加する接続をクリックし、 **[選択したハイブリッド接続の追加]** をクリックします。  

![ハイブリッド接続ポータルのスクリーンショット][3]

新しいハイブリッド接続を作成するには、 **[ハイブリッド接続の新規作成]** を選択します。 以下を指定します。 

- ハイブリッド接続の名前。
- エンドポイント ホストの名前。
- エンドポイント ポート。
- 使用する Service Bus 名前空間。

![[ハイブリッド接続の新規作成] ダイアログ ボックスのスクリーン ショット][4]

すべてのハイブリッド接続は Service Bus 名前空間に関連付けられており、各 Service Bus 名前空間は Azure リージョンに存在します。 ネットワーク待機時間の誘発を避けるためには、できるだけアプリと同じリージョンの Service Bus 名前空間を使用することが重要です。

ハイブリッド接続をアプリから削除するには、ハイブリッド接続を右クリックし、 **[切断]** を選択します。  

ハイブリッド接続をアプリに追加すると、ハイブリッド接続を選択するだけで詳細情報を表示できます。 

![ハイブリッド接続の詳細のスクリーンショット][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Azure Relay ポータルでのハイブリッド接続の作成 ###

アプリ内からポータルを操作するだけでなく、Azure Relay ポータルからハイブリッド接続を作成することもできます。 ハイブリッド接続を App Service で使用するには、以下が必要です。

* クライアント承認を要求する。
* メタデータ項目に、host:port の組み合わせを値として含むエンドポイントの名前を付ける。

## <a name="hybrid-connections-and-app-service-plans"></a>ハイブリッド接続と App Service プラン ##

App Service ハイブリッド接続は、Basic、Standard、Premium、および Isolated の価格 SKU でのみ利用できます。 料金プランに関連付けられている制限があります。  

| 料金プラン | プランで使用できるハイブリッド接続の数 |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolated | 200 |

App Service プランの UI には、使用されているハイブリッド接続の数と、それを使用するアプリが表示されます。  

![App Service プラン プロパティのスクリーン ショット][6]

[ハイブリッド接続] を選択して詳細を表示します。 アプリ ビューに表示されるすべての情報を表示できます。 そのハイブリッド接続を使用している同じプラン内の他のアプリの数も確認できます。

App Service プランで使用できるハイブリッド接続エンドポイントの数には制限があります。 ただし、各ハイブリッド接続は、そのプラン内の無制限の数のアプリで使用できます。 たとえば、1 つの App Service プラン内の 5 つの異なるアプリによって使用されている 1 つのハイブリッド接続は、1 つのハイブリッド接続とみなされます。

### <a name="pricing"></a>価格 ###

App Service プランの SKU 要件が存在する以外に、ハイブリッド接続を使用するには追加コストがかかります。 ハイブリッド接続によって使用されるリスナーごとに料金が発生します。 リスナーはハイブリッド接続マネージャーです。 5 個のハイブリッド接続が 2 つのハイブリッド接続マネージャーによってサポートされている場合、リスナーは 10 になります。 詳細については、「[Service Bus の価格][sbpricing]」をご覧ください。

## <a name="hybrid-connection-manager"></a>Hybrid Connection Manager の使用 ##

ハイブリッド接続を機能させるためには、ハイブリッド接続エンドポイントをホストしているネットワークにリレー エージェントを作成する必要があります。 そのリレー エージェントは、Hybrid Connection Manager (HCM) と呼ばれます。 [Azure ポータル]にあるアプリから HCM をダウンロードするには、 [portal] **[ネットワーク]**  >  **[ハイブリッド接続エンドポイントの構成]** を選択します。  

このツールは、Windows Server 2012 以降のバージョンの Windows で実行されます。 HCM はサービスとして実行し、送信のためにポート 443 で Azure Relay に接続します。  

HCM をインストールしたら、HybridConnectionManagerUi.exe を実行して、このツールの UI を使用できます。 このファイルは、Hybrid Connection Manager のインストール ディレクトリにあります。 Windows 10 では、検索ボックスに*ハイブリッド接続マネージャー UI*と入力して検索できます。  

![ハイブリッド接続マネージャーのスクリーンショット][7]

HCM UI を起動すると、HCM のこのインスタンスに構成されているすべてのハイブリッド接続の一覧が最初に表示されます。 変更を加える場合は、まず Azure で認証します。 

1 つまたは複数のハイブリッド接続を HCM に追加するには:

1. HCM UI を起動します。
2. **[Configure another hybrid connection] \(別のハイブリッド接続の構成\)** を選択します。
![[Configure New Hybrid Connection] \(新しいハイブリッド接続の構成\) のスクリーン ショット][8]

1. お使いのサブスクリプションで使用できるハイブリッド接続を取得する Azure アカウントでサインインします。 これ以降、HCM では Azure アカウントの使用は続行されません。 
1. サブスクリプションを選択します。
1. HCM をリレーするハイブリッド接続を選択します。
![ハイブリッド接続のスクリーンショット][9]

1. **[保存]** を選択します。

追加したハイブリッド接続が表示されます。 構成済みのハイブリッド接続を選択すると詳細を表示することもできます。

![ハイブリッド接続の詳細のスクリーンショット][10]

構成済みのハイブリッド接続を HCM でサポートするには、以下が必要です。

- ポート 443 経由の Azure への TCP アクセス
- ハイブリッド接続エンドポイントに対する TCP アクセス
- エンドポイント ホストおよび Service Bus 名前空間で DNS 参照を実行する機能。

> [!NOTE]
> Azure Relay は、Web ソケットを使用して接続します。 この機能は、Windows Server 2012 以降でのみご利用いただけます。 このため、ハイブリッド接続マネージャーは、Windows Server 2012 より前のバージョンではサポートされていません。
>

### <a name="redundancy"></a>冗長性 ###

HCM はそれぞれ、複数のハイブリッド接続をサポートできます。 また、特定のハイブリッド接続については、複数の HCM がサポートすることができます。 既定の動作は、特定のエンドポイントに対して構成された HCM でのトラフィックの転送です。 ネットワークからのハイブリッド接続の高可用性が必要な場合は、複数の HCM を別個のコンピューターで実行します。 リレー サービスがトラフィックを HCM に分散するために使用する負荷分散アルゴリズムでは、割り当てがランダムに行われます。 

### <a name="manually-add-a-hybrid-connection"></a>ハイブリッド接続の手動追加 ###

サブスクリプションを持っていないユーザーが特定のハイブリッド接続に対する HCM インスタンスをホストするには、ハイブリッド接続用のゲートウェイ接続文字列を共有します。 [Azure Portal][portal] の [ハイブリッド接続プロパティ] でゲートウェイ接続文字列を確認できます。 この文字列を使用するには、HCM で **[手動で入力]** ボタンをクリックし、ゲートウェイ接続文字列を貼り付けます。

![ハイブリッド接続の手動追加][11]

### <a name="upgrade"></a>アップグレード ###

ハイブリッド接続マネージャーは、問題の修正や機能強化のために定期的に更新されます。 アップグレードがリリースされると HCM UI にポップアップが表示されます。 アップグレードを適用すると、変更が適用され、HCM が再起動されます。 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>ハイブリッド接続をプログラミングによってアプリに追加する ##

次に示す API を直接使用して、アプリに接続するハイブリッド接続を管理できます。 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

ハイブリッド接続に関連付けられた JSON オブジェクトは次のようになります。

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

この情報を使用する方法の 1 つとして、[ARMClient][armclient] GitHub プロジェクトから取得できる armclient があります。 既存のハイブリッド接続をアプリに接続する例を次に示します。 上記のスキーマに対して次の JSON ファイルを作成します。

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

この API を使用するには、キーの送信とリレー リソース ID が必要です。 ファイル名 hctest.json として情報を保存した場合は、次のコマンドを発行してハイブリッド接続をアプリに接続します。 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="troubleshooting"></a>トラブルシューティング ##

"接続済み" 状態の場合は、少なくとも 1 つの HCM がそのハイブリッド接続で構成され、Azure にアクセスできます。 ハイブリッド接続の状態が **[接続済み]** にならない場合、Azure にアクセスできる HCM でハイブリッド接続が構成されていません。

クライアントがエンドポイントに接続できない主な理由は、エンドポイントが DNS 名の代わりに IP アドレスを使用して指定されたためです。 アプリが目的のエンドポイントに到達できないときに IP アドレスを使用していた場合は、HCM を実行中のホストで有効な DNS 名の使用に切り替えます。 また、HCM が実行されているホストで DNS 名が解決されていることを確認してください。 HCM が実行されているホストからハイブリッド接続エンドポイントに接続していることを確認します。  

App Service では、Advanced Tools (Kudu) コンソールから **tcpping** ツールを呼び出すことができます。 このツールは、TCP エンドポイントにアクセスできるかどうかを通知できますが、ハイブリッド接続エンドポイントにアクセスできるかどうは通知しません。 コンソールで、ハイブリッド接続エンドポイントに対してツールを使用している場合は、host:port の組み合わせが使用されていることのみを確認できます。  

お使いのエンドポイント用のコマンドライン クライアントがある場合は、アプリ コンソールから接続をテストできます。 たとえば、curl を使用して、Web サーバーのエンドポイントへのアクセスをテストできます。

## <a name="biztalk-hybrid-connections"></a>BizTalk ハイブリッド接続 ##

以前、この機能は BizTalk ハイブリッド接続と呼ばれていました。 これは 2018 年 5 月 31 日で終了となり、運用が停止されました。 BizTalk ハイブリッド接続は、すべてのアプリから削除され、ポータルや API からアクセスできなくなっています。 このような古い接続がまだハイブリッド接続マネージャーに構成されている場合、[中止] というステータスが表示され、一番下には有効期間が終了したという説明が表示されます。

![HCM での BizTalk ハイブリッド接続][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
