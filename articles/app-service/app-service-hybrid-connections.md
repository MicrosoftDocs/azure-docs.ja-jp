---
title: "Azure App Service のハイブリッド接続 | Microsoft Docs"
description: "ハイブリッド接続を作成し、それを使用して分散ネットワーク内のリソースにアクセスする方法"
services: app-service
documentationcenter: 
author: ccompy
manager: stefsch
editor: 
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2017
ms.author: ccompy
ms.openlocfilehash: fef9e7b280387934cb093f51b4c8aa134a3b87e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Services からのハイブリッド接続 #

## <a name="overview"></a>概要 ##

ハイブリッド接続は、Azure のサービスであり、Azure App Service の機能でもあります。  サービスとして使用した場合、その用途と能力は、Azure App Service での機能を上回ります。  ハイブリッド接続の詳細と Azure App Service では提供されない使用方法については、[Azure Relay ハイブリッド接続][HCService]に関するページを参照してください。

Azure App Service 内では、ハイブリッド接続を使用して、他のネットワークのアプリケーション リソースにアクセスできます。 それは、アプリからアプリケーション エンドポイントへのアクセスを提供します。  アプリケーションにアクセスするために別の機能を有効にすることはありません。  App Service で使用されるとき、各ハイブリッド接続は、単一の TCP ホストとポートの組み合わせに相互に関連付けられます。  つまり、TCP リッスン ポートをターゲットにしている限り、ハイブリッド接続エンドポイントは、任意のオペレーティング システムの任意のアプリケーションにすることができます。 ハイブリッド接続では、アプリケーション プロトコルやアクセス先は認識されず、問題になりません。  それは、ネットワーク アクセスを提供するだけです。  


## <a name="how-it-works"></a>動作のしくみ ##
ハイブリッド接続機能は、Service Bus Relay に対する 2 つの発信呼び出しで構成されます。  App Service でアプリが実行されているホストのライブラリからの接続と、Hybrid Connection Manager (HCM) から Service Bus Relay への接続があります。  HCM は、ネットワーク ホスト内にデプロイされるリレー サービスです。 

この 2 つの結合された接続を通して、HCM の他方の側の固定されたホストとポートの組み合わせに至るアプリの TCP トンネルが設定されます。  接続では、セキュリティのために TLS 1.2 が、認証/承認のために SAS キーが使用されます。    

![][1]

アプリが DNS 要求を行い、その要求が構成済みのハイブリッド接続エンドポイントと一致した場合、発信 TCP トラフィックがハイブリッド接続にリダイレクトされます。  

> [!NOTE]
> つまり、ハイブリッド接続では常に DNS 名を使用するようにします。  一部のクライアント ソフトウェアは、エンドポイントで IP アドレスが使用されている場合は DNS 参照を実行しません。
>
>

ハイブリッド接続の種類は 2 つあります。Azure Relay でサービスとして提供される新しいハイブリッド接続と、従来の BizTalk ハイブリッド接続です。  従来の BizTalk ハイブリッド接続は、ポータルではクラシック ハイブリッド接続と呼ばれます。  これらについては、このドキュメントの中で、後で詳しく説明します。

### <a name="app-service-hybrid-connection-benefits"></a>App Service のハイブリッド接続のメリット ###

ハイブリッド接続機能には、次のようなさまざまなメリットがあります。

- アプリがオンプレミスのシステムをサービスにセキュリティで保護されたアクセスを実行できます
- インターネットにアクセス可能なエンドポイントは必要ありません
- すばやく簡単にセットアップできます  
- 各ハイブリッド接続が単一のホストとポートの組み合わせと一致します。これはセキュリティ面で優れています
- 接続はすべて標準的な Web ポート経由で発信されるため、通常はファイアウォールを通過するための設定は必要ありません
- 機能はネットワーク レベルであり、これはアプリで使用される言語とエンドポイントで使用されるテクノロジに依存しないことも意味します
- 単一のアプリから複数のネットワークにアクセスするために使用できます 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>ハイブリッド接続で実行できないこと ###

ハイブリッド接続で実行できないことがいくつかあります。

- ドライブのマウント
- UDP の使用
- FTP パッシブ モードや拡張パッシブ モードなどの動的ポートを使用する TCP ベースのサービスへのアクセス
- LDAP のサポート (UDP が必要な場合があるため)
- Active Directory のサポート

## <a name="adding-and-creating-a-hybrid-connection-in-your-app"></a>アプリでのハイブリッド接続の追加と作成 ##

ハイブリッド接続は、アプリ ポータルまたはハイブリッド接続サービス ポータルから作成できます。  アプリで使用するハイブリッド接続は、アプリ ポータルを使用して作成することを強くお勧めします。  ハイブリッド接続を作成するには、[Azure ポータル][portal]に移動し、アプリの UI に移動します。  **[ネットワーク]、[ハイブリッド接続エンドポイントの構成]** の順に選択します。  ここで、アプリで構成されているハイブリッド接続を確認できます。  

![][2]

新しいハイブリッド接続を追加するには、[ハイブリッド接続の追加] をクリックします。  表示される UI に、既に作成したハイブリッド接続が一覧表示されます。  アプリに 1 つ以上のハイブリッド接続を追加するには、追加する接続先をクリックし、**[選択したハイブリッド接続の追加]** をクリックします。  

![][3]

新しいハイブリッド接続を作成する場合は、**[新しいハイブリッド接続の作成]** をクリックします。  ここで、次の項目を指定します。 

- エンドポイント
- エンドポイント ホストの名前
- エンドポイント ポート
- 使用する Service Bus 名前空間

![][4]

すべてのハイブリッド接続は Service Bus 名前空間に関連付けられており、各 Service Bus 名前空間は Azure リージョンに存在します。  ネットワークで誘発される待機時間を防ぐために、できるだけアプリと同じリージョンの Service Bus 名前空間を使用することが重要です。

ハイブリッド接続をアプリから削除する場合は、ハイブリッド接続を右クリックし、**[切断]** を選択します。  

ハイブリッド接続が Web アプリに追加されたら、ハイブリッド接続をクリックするだけで詳細情報を表示できます。  

![][5]

## <a name="hybrid-connections-and-app-service-plans"></a>ハイブリッド接続と App Service プラン ##

現在作成できるハイブリッド接続は、新しいハイブリッド接続だけです。  それらは、Basic、Standard、Premium および Isolated の価格 SKU でのみ利用できます。  料金プランに関連付けられている制限があります。  

| 料金プラン | プランで使用できるハイブリッド接続の数 |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolated | 200 |

App Service プランの制限があるため、App Service プランごとの UI もあり、各 UI にはいくつのハイブリッド接続がどのアプリによって使用されているかが表示されます。  

![][6]

アプリ ビューと同じように、ハイブリッド接続をクリックして詳細を表示できます。  表示されるプロパティで、アプリ ビューと同じ情報をすべて確認できますが、表示中のハイブリッド接続を使用している同じ App Service プラン内の他のアプリの数も確認できます。

![][7]

App Service プランで使用できるハイブリッド接続のエンドポイントの数には制限がありますが、各ハイブリッド接続は、同じ App Service プラン内の複数のアプリで使用することができます。  つまり、App Service プランで 1 つのハイブリッド接続を 5 つの別々のアプリで使用している場合、使用されているハイブリッド接続の数は 1 のままです。

Basic、Standard、Premium、または Isolated SKU で使用できる数のハイブリッド接続を超えるハイブリッド接続には追加コストがかかります。  ハイブリッド接続の価格については、「[Service Bus の価格][sbpricing]」を参照してください。

## <a name="hybrid-connection-manager"></a>Hybrid Connection Manager の使用 ##

ハイブリッド接続を機能させるためには、ハイブリッド接続エンドポイントをホストしているネットワークにリレー エージェントを作成する必要があります。  そのリレー エージェントは、Hybrid Connection Manager (HCM) と呼ばれます。  このツールは、[Azure ポータル][portal]で **[ネットワーク]、[ハイブリッド接続エンドポイントの構成]** の順に選択して表示される UI からダウンロードしてアプリから使用できます。  

このツールは、Windows Server 2008 R2 以降のバージョンの Windows で実行されます。  HCM は、インストール後はサービスとして実行されます。  このサービスは、構成されたエンドポイントに基づいて Azure Service Bus リレーに接続します。  HCM からの接続は、ポート 80 と 443 に対して発信されます。    

HCM には、それを構成するための UI があります。  HCM をインストールした後、Hybrid Connection Manager のインストール ディレクトリ内に配置される HybridConnectionManagerUi.exe を実行してその UI を起動できます。  Windows 10 では、検索ボックスに「*Hybrid Connection Manager UI*」と入力することで簡単に起動できます。  

HCM UI が起動すると、最初に表示されるのは、HCM のこのインスタンスで構成されているハイブリッド接続の一覧です。  変更する場合は、Azure で認証する必要があります。 

1 つまたは複数のハイブリッド接続を HCM に追加するには:

1. HCM UI を起動します。
1. [Configure another hybrid connection (別のハイブリッド接続の構成)] をクリックします。![][8]

1. Azure アカウントでサインインします。
1. サブスクリプションを選択します。
1. この HCM をリレーするハイブリッド接続をクリックします。![][9]

1. [保存] をクリックします。

この時点で、追加したハイブリッド接続が表示されます。  構成済みのハイブリッド接続をクリックしてその接続の詳細を表示することもできます。

![][10]

構成済みのハイブリッド接続を HCM でサポートできるようにするには、以下が必要です。

- Azure に対するポート 80 と 443 経由の TCP アクセス
- ハイブリッド接続エンドポイントに対する TCP アクセス
- エンドポイント ホストと Azure Service Bus 名前空間で DNS 参照を実行する能力

HCM は、新しいハイブリッド接続と従来の BizTalk ハイブリッド接続の両方をサポートします。

### <a name="redundancy"></a>冗長性 ###

各 HCM は、複数のハイブリッド接続をサポートできます。  さらに、特定のハイブリッド接続は、複数の HCM によってサポートできます。  既定の動作は、特定のエンドポイントに対して構成された HCM でのトラフィックのラウンド ロビンです。  ネットワークからのハイブリッド接続の高可用性を求める場合は、単純に複数の HCM を別個のコンピューターにインスタンス化します。 

### <a name="manually-adding-a-hybrid-connection"></a>ハイブリッド接続の手動追加 ###

サブスクリプションを持っていないユーザーで特定のハイブリッド接続に対する HCM インスタンスをホストする場合は、ハイブリッド接続用のゲートウェイ接続文字列を共有できます。  これは、[Azure ポータル][portal]で、ハイブリッド接続のプロパティで確認できます。 この文字列を使用するには、HCM で **[手動で構成する]** ボタンをクリックし、ゲートウェイ接続文字列を貼り付けます。


## <a name="troubleshooting"></a>トラブルシューティング ##

実行中のアプリでハイブリッド接続を設定し、そのハイブリッド接続が構成されている HCM が少なくとも 1 つある場合、ポータルに表示される状態は **[接続中]** になります。  **[接続中]** と表示されない場合は、アプリが停止しているか、HCM がポート 80 または 443 で Azure に接続できないことを意味します。  

クライアントがエンドポイントに接続できない主な理由は、エンドポイントが DNS 名の代わりに IP アドレスを使用して指定されたためです。  アプリが目的のエンドポイントに到達できないときに IP アドレスを使用していた場合は、HCM を実行中のホストで有効な DNS 名の使用に切り替えます。  他にチェックするのは、HCM を実行中のホストで DNS 名が正しく解決されることと、HCM を実行中のホストからハイブリッド接続エンドポイントへの接続が存在していることです。  

App Service には、tcpping と呼ばれるコンソールから呼び出すことのできるツールがあります。  このツールは、TCP エンドポイントにアクセスできるかどうかを通知できますが、ハイブリッド接続エンドポイントにアクセスできるかどうは通知しません。  コンソールでハイブリッド接続エンドポイントに対して使用し、ping が成功した場合でも、それは、ホストとポートの組み合わせを使用するハイブリッド接続がアプリに構成されていることを通知しているだけです。  

## <a name="biztalk-hybrid-connections"></a>BizTalk ハイブリッド接続 ##

従来の BizTalk ハイブリッド接続機能では、新しい BizTalk ハイブリッド接続は作成できなくなりました。  既存の BizTalk ハイブリッド接続は、引き続きアプリで使用できますが、新しいサービスに移行する必要があります。  新しいサービスには、BizTalk バージョンに比べて次のメリットがあります。

- BizTalk アカウントを追加する必要はありません
- TLS は、BizTalk ハイブリッド接続の 1.0 ではなく 1.2 になります
- Azure に到達するための通信は、ポート 80 と 443 で DNS 名 を使用して行われ、IP アドレスとその他のポートの追加使用はありません  

BizTalk ハイブリッド接続をアプリに追加するには、[Azure ポータル][portal]でアプリに移動し、**[ネットワーク]、[ハイブリッド接続エンドポイントの構成]** の順にクリックします。  [クラシック ハイブリッド接続] テーブルで、**[クラシック ハイブリッド接続の追加]** をクリックします。  BizTalk ハイブリッド接続の一覧が表示されます。  


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

<!--Links-->
[HCService]: http://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: http://portal.azure.com/
[oldhc]: http://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: http://azure.microsoft.com/pricing/details/service-bus/

