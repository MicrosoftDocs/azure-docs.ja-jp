---
title: Hybrid Connections (ハイブリッド接続)
description: Azure App Service でハイブリッド接続を作成し、それを使用して分散ネットワーク内のリソースにアクセスする方法について説明します。
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 02/05/2020
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: 1b3fc4a254c1157f2c2336e6360ba7621f31364d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594233"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Services からのハイブリッド接続

ハイブリッド接続は、Azure のサービスであり、Azure App Service の機能でもあります。 サービスとして使用した場合、その用途と機能は、Azure App Service の用途と機能を上回ります。 ハイブリッド接続の詳細と Azure App Service では提供されない使用方法については、[Azure Relay ハイブリッド接続][HCService]に関するページを参照してください。

App Service 内では、任意のネットワークに含まれていて、443 ポートから Azure に発信呼び出しができるアプリケーション リソースにアクセスするため、ハイブリッド接続が使用できます。 ハイブリッド接続によって、アプリから TCP エンドポイントへのアクセスができるようになりますが、アプリにアクセスするための新しい方法が有効になるわけではありません。 App Service で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。 その結果、リソースが TCP エンドポイントである限り、それがどの OS 上にあろうとアプリからアクセスできるようになります。 ハイブリッド接続では、アプリケーション プロトコルやアクセス先は認識されません。 それは、ネットワーク アクセスを提供するだけです。  

## <a name="how-it-works"></a>しくみ ##
ハイブリッド接続を使用するには、対象のエンドポイントと Azure の両方に到達できる場所にリレー エージェントをデプロイする必要があります。 リレー エージェント (ハイブリッド接続マネージャー (HCM)) では、443 ポートを介して Azure Relay への呼び出しが行われます。 Web アプリのサイトからも、App Service インフラストラクチャにより、アプリケーションに代わって Azure Relay への接続が行われます。 このように組み合わされた接続により、アプリから対象のエンドポイントにアクセスできるようになります。 接続では、セキュリティには TLS 1.2 が、認証/承認には Shared Access Signature (SAS) キーが使用されます。    

![ハイブリッド接続のフローの概要図][1]

アプリが DNS 要求を行い、その要求が構成済みのハイブリッド接続エンドポイントと一致した場合、発信 TCP トラフィックがハイブリッド接続を介してリダイレクトされます。  

> [!NOTE]
> つまり、ハイブリッド接続では常に DNS 名を使用するようにします。 一部のクライアント ソフトウェアは、エンドポイントで IP アドレスが使用されている場合は DNS 参照を実行しません。 
>

### <a name="app-service-hybrid-connection-benefits"></a>App Service のハイブリッド接続のメリット ###

ハイブリッド接続機能には、次のようなさまざまなメリットがあります。

- アプリは、オンプレミスのシステムとサービスへセキュリティで保護されたアクセスを実行できます。
- インターネットにアクセス可能なエンドポイントは必要ありません。
- すばやく簡単に設定できます。 ゲートウェイは必要ありません。
- 各ハイブリッド接続が単一のホストとポートの組み合わせに照合されるため、セキュリティ面で優れています。
- 通常はファイアウォールに穴を開ける必要はありません。 接続はすべて、標準的な Web ポート経由の発信です。
- ネットワーク レベルの機能であるため、アプリで使用される言語とエンドポイントで使用されるテクノロジに依存しません。
- 単一のアプリから複数のネットワークにアクセスするために使用できます。 
- Windows アプリと Linux アプリ向けに GA でサポートされています。 Windows コンテナー アプリではサポートされていません。

### <a name="things-you-cannot-do-with-hybrid-connections"></a>ハイブリッド接続で実行できないこと ###

ハイブリッド接続では次のことができません。

- ドライブのマウント
- UDP の使用
- FTP パッシブ モードや拡張パッシブ モードなどの動的ポートを使用する TCP ベースのサービスへのアクセス
- LDAP のサポート (UDP が必要な場合があるため)
- Active Directory のサポート (App Service worker にドメイン参加できないため) 

## <a name="add-and-create-hybrid-connections-in-your-app"></a>アプリでハイブリッド接続を追加または作成する ##

ハイブリッド接続を作成するには、[Azure portal][portal] に移動し、アプリを選択します。 **[ネットワーク]**  >  **[ハイブリッド接続エンドポイントの構成]** の順に選択します。 ここで、アプリ用に構成されているハイブリッド接続を確認できます。  

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
| Basic | プランあたり 5 |
| Standard | プランあたり 25 |
| PremiumV2 | アプリあたり 200 |
| Isolated | アプリあたり 200 |

App Service プランの UI には、使用されているハイブリッド接続の数と、それを使用するアプリが表示されます。  

![App Service プラン プロパティのスクリーン ショット][6]

[ハイブリッド接続] を選択して詳細を表示します。 アプリ ビューに表示されるすべての情報を表示できます。 そのハイブリッド接続を使用している同じプラン内の他のアプリの数も確認できます。

App Service プランで使用できるハイブリッド接続エンドポイントの数には制限があります。 ただし、各ハイブリッド接続は、そのプラン内の無制限の数のアプリで使用できます。 たとえば、1 つの App Service プラン内の 5 つの異なるアプリによって使用されている 1 つのハイブリッド接続は、1 つのハイブリッド接続とみなされます。

### <a name="pricing"></a>価格 ###

App Service プランの SKU 要件が存在する以外に、ハイブリッド接続を使用するには追加コストがかかります。 ハイブリッド接続によって使用されるリスナーごとに料金が発生します。 リスナーはハイブリッド接続マネージャーです。 5 個のハイブリッド接続が 2 つのハイブリッド接続マネージャーによってサポートされている場合、リスナーは 10 になります。 詳細については、「[Service Bus の価格][sbpricing]」をご覧ください。

## <a name="hybrid-connection-manager"></a>Hybrid Connection Manager の使用 ##

ハイブリッド接続を機能させるためには、ハイブリッド接続エンドポイントをホストしているネットワークにリレー エージェントを作成する必要があります。 そのリレー エージェントは、Hybrid Connection Manager (HCM) と呼ばれます。 [Azure portal][portal] にあるアプリから HCM をダウンロードするには、 **[ネットワーク]**  >  **[ハイブリッド接続エンドポイントの構成]** を選択します。  

このツールは、Windows Server 2012 以降のバージョンの Windows で実行されます。 HCM はサービスとして実行し、送信のためにポート 443 で Azure Relay に接続します。  

HCM をインストールしたら、HybridConnectionManagerUi.exe を実行して、このツールの UI を使用できます。 このファイルは、Hybrid Connection Manager のインストール ディレクトリにあります。 Windows 10 では、検索ボックスに *ハイブリッド接続マネージャー UI* と入力して検索できます。  

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

サブスクリプションを持っていないユーザーが特定のハイブリッド接続に対する HCM インスタンスをホストするには、ハイブリッド接続用のゲートウェイ接続文字列を共有します。 [Azure portal][portal] の [ハイブリッド接続プロパティ] でゲートウェイ接続文字列を確認できます。 この文字列を使用するには、HCM で **[手動で入力]** ボタンをクリックし、ゲートウェイ接続文字列を貼り付けます。

![ハイブリッド接続の手動追加][11]

### <a name="upgrade"></a>アップグレード ###

ハイブリッド接続マネージャーは、問題の修正や機能強化のために定期的に更新されます。 アップグレードがリリースされると HCM UI にポップアップが表示されます。 アップグレードを適用すると、変更が適用され、HCM が再起動されます。 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>ハイブリッド接続をプログラミングによってアプリに追加する ##

ハイブリッド接続は、Azure CLI でサポートされています。 提供されているコマンドは、アプリおよび App Service プランの両方のレベルで動作します。  アプリ レベルのコマンドは次のとおりです。

```azurecli
az webapp hybrid-connection

Group
    az webapp hybrid-connection : Methods that list, add and remove hybrid-connections from webapps.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a hybrid-connection to a webapp.
    list   : List the hybrid-connections on a webapp.
    remove : Remove a hybrid-connection from a webapp.
```

App Service プランのコマンドでは、特定のハイブリッド接続で使用するキーを設定することができます。 各ハイブリッド接続では、プライマリとセカンダリの 2 つのキーが設定されます。 次のコマンドにより、プライマリ キーとセカンダリ キーのどちらを使用するかを選択できます。 これにより、キーを定期的に再生成する際にキーを切り替えることができます。 

```azurecli
az appservice hybrid-connection --help

Group
    az appservice hybrid-connection : A method that sets the key a hybrid-connection uses.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    set-key : Set the key that all apps in an appservice plan use to connect to the hybrid-
                connections in that appservice plan.
```

## <a name="secure-your-hybrid-connections"></a>ハイブリッド接続をセキュリティで保護する ##

基になる Azure Service Bus Relay に対する十分なアクセス許可を持つユーザーは、既存のハイブリッド接続を他の App Service Web Apps に追加できます。 これは、他のユーザーが同じハイブリッド接続を再利用できないようにする必要がある場合 (たとえば、ターゲット リソースが、未承認のアクセスを防ぐための追加のセキュリティ対策が取られていないサービスである場合)、Azure Service Bus Relay へのアクセスをロックダウンする必要があることを意味します。

Relay への `Reader` アクセス権限を持つユーザーは誰でも、Azure portal でハイブリッド接続を自分の Web アプリに追加しようとするときにそれを "_表示_" することはできますが、リレー接続の確立に使用される接続文字列を取得するためのアクセス許可がないため、"_追加_" することはできません。 ハイブリッド接続を正常に追加するには、`listKeys` アクセス許可 (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`) が必要です。 Relay に対するこのアクセス許可を含む `Contributor` ロールまたはその他のロールにより、ユーザーはハイブリッド接続を使用し、独自の Web Apps にそれを追加することが許可されます。

## <a name="troubleshooting"></a>トラブルシューティング ##

"接続済み" 状態の場合は、少なくとも 1 つの HCM がそのハイブリッド接続で構成され、Azure にアクセスできます。 ハイブリッド接続の状態が **[接続済み]** にならない場合、Azure にアクセスできる HCM でハイブリッド接続が構成されていません。 HCM に **[未接続]** と表示されている場合は、次の点を確認する必要があります。

* ホストに、ポート 443 での Azure への発信アクセスはありますか? PowerShell コマンド *Test-NetConnection Destination -P Port* を使用して、HCM ホストからテストすることができます。 
* HCM が正しくない状態である可能性はありますか? ‘Azure ハイブリッド接続マネージャー サービス' ローカル サービスを再起動してみてください。

状態に **[接続済み]** と表示されているにもかかわらず、アプリがエンドポイントに接続できない場合は:

* ハイブリッド接続で DNS 名を使用していることを確認してください。 IP アドレスを使用している場合、必要なクライアント DNS 検索が行われないことがあります。 Web アプリで実行されているクライアントによって DNS 検索が実行されない場合、ハイブリッド接続は機能しません
* ハイブリッド接続で使用されている DNS 名が HCM ホストから解決できることを確認します。 *nslookup EndpointDNSname* を使用して解決を確認します。ここで、EndpointDNSname は、ハイブリッド接続定義で使用されているものと完全に一致します。
* PowerShell コマンド *Test-NetConnection EndpointDNSname -P Port* 使用して、HCM ホストからエンドポイントへのアクセスをテストします。HCM ホストからエンドポイントにアクセスできない場合は、宛先ホストのホストベースのファイアウォールを含む 2 つのホスト間のファイアウォールを確認します。

App Service では、Advanced Tools (Kudu) コンソールから **tcpping** コマンドライン ツールを呼び出すことができます。 このツールは、TCP エンドポイントにアクセスできるかどうかを通知できますが、ハイブリッド接続エンドポイントにアクセスできるかどうは通知しません。 コンソールで、ハイブリッド接続エンドポイントに対してツールを使用している場合は、host:port の組み合わせが使用されていることのみを確認できます。  

お使いのエンドポイント用のコマンドライン クライアントがある場合は、アプリ コンソールから接続をテストできます。 たとえば、curl を使用して、Web サーバーのエンドポイントへのアクセスをテストできます。


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
[HCService]: /azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: /azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
