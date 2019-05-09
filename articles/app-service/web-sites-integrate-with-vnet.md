---
title: アプリと Azure 仮想ネットワークを統合する - Azure App Service
description: Azure App Service のアプリを新規または既存の Azure 仮想ネットワークに接続する方法を説明します。
services: app-service
documentationcenter: ''
author: ccompy
manager: stefsch
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/12/2018
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 768179f8569eac14166bcbb0a888e1cdbe41d497
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369702"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>アプリを Azure 仮想ネットワークに統合する
このドキュメントでは、Azure App Service の仮想ネットワーク統合機能について説明し、この機能を [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) でアプリと共にセットアップする方法を示します。 [Azure Virtual Network][VNETOverview] (VNet) を使用すると、多くの Azure リソースをインターネット以外のルーティング可能なネットワークに配置できます。 これらのネットワークはその後、VPN テクノロジを使用してオンプレミスのネットワークに接続できます。 

Azure App Service には、2 つの形式があります。 

1. Isolated を除くすべての価格プランをサポートするマルチテナント システム
2. VNet にデプロイされる App Service Environment (ASE)。 

このドキュメントでは、マルチ テナント App Service での使用に向けた VNet 統合機能について説明します。  アプリが [App Service Environment][ASEintro] 内にある場合は、既に VNet 内にアプリが存在しているので、同じ VNet 内のリソースに到達するために VNet 統合機能を使用する必要はありません。

VNet 統合により、Web アプリから仮想ネットワーク内のリソースにアクセスできるようになりますが、仮想ネットワークからその Web アプリへのプライベート アクセスは付与されません。 プライベート サイト アクセスとは、Azure 仮想ネットワークなどプライベート ネットワークのみからアプリにアクセスできるようにすることです。 プライベート サイトには、内部ロード バランサー (ILB) を使用して ASE が構成されている場合のみアクセスできます。 ILB ASE の使用方法について詳しくは、最初にこちらの[ILB ASE の作成と使用][ILBASE]に関する記事を参照してください。 

VNet 統合は、多くの場合、アプリから、VNet で実行されているデータベースや Web サービスにアクセスできるようにするために使用されます。 VNet 統合を使用すれば、VM 上のアプリケーション用にパブリック エンドポイントを公開せずに済み、インターネット以外のルーティング可能なプライベート アドレスを使用できます。 

以下は、VNet 統合機能の特徴です。

* Standard、Premium、PremiumV2 いずれかの価格プランが必要 
* Classic VNet または Resource Manager VNet に対応 
* TCP と UDP をサポート
* Web アプリ、モバイル アプリ、API アプリ、関数アプリと連携可能
* アプリが一度に接続できるのは 1 つの VNet のみ
* App Service プランでは、最大 5 つの VNet と統合可能 
* App Service プランでは、複数のアプリで同じ VNet を使用可能
* ポイント対サイト VPN を使用して構成されている Virtual Network ゲートウェイが必要
* ゲートウェイでの SLA のおかげで 99.9% の SLA をサポート

以下に、VNet 統合でサポートされていないことの例を示します。

* ドライブのマウント
* AD 統合 
* NetBIOS
* プライベート サイトへのアクセス
* ExpressRoute を越えてのリソースへのアクセス 
* サービス エンドポイントを越えてのリソースへのアクセス 

### <a name="getting-started"></a>使用の開始
ここでは、Web アプリを仮想ネットワークに接続する前に考慮する必要がある点について説明します。

* ターゲットの仮想ネットワークでは、アプリに接続するために、ルート ベースのゲートウェイによるポイント対サイト VPN が有効になっている必要があります。 
* VNet は、App Service プラン (ASP) と同じサブスクリプションにする必要があります。
* VNet と統合されるアプリケーションでは、その VNet に対して指定されている DNS を使用します。

## <a name="enabling-vnet-integration"></a>VNet 統合の有効化

VNet 統合機能には、プレビュー段階の新しいバージョンがあります。 これは、ポイント対サイト VPN に依存しておらず、ExpressRoute またはサービス エンドポイントを越えてのリソースへのアクセスもサポートしています。 新しいプレビュー機能に関する詳細については、このドキュメントの最後を参照してください。 

### <a name="set-up-a-gateway-in-your-vnet"></a>VNet 内にゲートウェイを設定する ###

ポイント対サイトのアドレスを使用して構成されているゲートウェイが既にある場合は、VNet 統合とアプリとの構成を省略できます。  
ゲートウェイを作成するには:

1. VNet 内に[ゲートウェイ サブネットを作成][creategatewaysubnet]します。  

1. [VPN ゲートウェイを作成][creategateway]します。 VPN の種類はルート ベースを選択します。

1. [ポイントからサイトへアドレスを設定][setp2saddresses]します。 ゲートウェイが Basic SKU 内にない場合は、ポイント対サイトの構成で IKEV2 を無効にする必要があり、SSTP を選択する必要があります。 アドレス空間は、次のアドレス ブロックのいずれかである必要があります。

* 10.0.0.0/8 - これは、10.0.0.0 から 10.255.255.255 までの IP アドレス範囲を意味します
* 172.16.0.0/12 - これは、172.16.0.0 から 172.31.255.255 までの IP アドレス範囲を意味します 
* 192.168.0.0/16 - これは、192.168.0.0 から 192.168.255.255 までの IP アドレス範囲を意味します

App Service の VNet 統合で使用するゲートウェイを作成しているだけの場合は、証明書をアップロードする必要はありません。 ゲートウェイの作成には 30 分かかることがあります。 ゲートウェイがプロビジョニングされるまで、アプリを VNet に統合することはできません。 

### <a name="configure-vnet-integration-with-your-app"></a>アプリで VNet 統合を構成する ###

アプリで VNet 統合を有効にするには: 

1. Azure portal で目的のアプリに移動し、アプリの設定を開いて [ネットワーク] > [VNet 統合] と選択します。 VNet 統合を構成する前に、お使いの ASP を Standard SKU 以上にスケールします。 
 ![[VNet 統合] UI][1]

1. **[Add VNet]\(VNet の追加)** を選択します。 
 ![VNet 統合を追加する][2]

1. 目的の VNet を選択します。 
  ![目的の VNet を選択する][8]
  
この最後の手順の後にアプリが再起動されます。  

## <a name="how-the-system-works"></a>システムの動作
VNet 統合機能は、ポイント対サイト VPN テクノロジ上に構築されます。 Azure App Service のアプリは、マルチテナント システムでホストされ、アプリを VNet 内に直接プロビジョニングすることはできなくなっています。 ポイント対サイト テクノロジによって、ネットワーク アクセスを、アプリをホストしている仮想マシンのみに制限しています。 アプリは、ハイブリッド接続または VNet 統合を通してのみ、インターネットにトラフィックを送信するように制限されています。 

![VNet 統合の動作][3]

## <a name="managing-the-vnet-integrations"></a>VNet 統合の管理
VNet への接続と、VNet との接続の切断は、アプリ レベルの機能です。 複数のアプリをまたいで VNet 統合に影響する可能性がある操作は、App Service プラン レベルで行われます。 アプリの UIDから、VNet に関する詳細を取得できます。 同じ情報は、ASP レベルでも表示されます。 

 ![VNet の詳細][4]

[ネットワーク機能の状態] ページで、アプリが VNet に接続されているかどうかを確認できます。 何らかの理由で VNet ゲートウェイがダウンしている場合、状態は、接続されていないと表示されます。 

アプリケーション レベルの VNet 統合の UI では、ASP から得られる詳細情報と同じ情報を確認できるようになりました。 それらの項目を次に示します。

* VNet 名 - 仮想ネットワーク UI へのリンク
* 場所 - VNet の場所が表示されます。 別の場所にある VNet と統合すると、アプリの待機時間の問題が発生する可能性があります。 
* 証明書の状態 - 証明書が App Service プランと VNet の間で同期しているかどうかが表示されます。
* ゲートウェイの状態 - 何らかの理由でゲートウェイがダウンすると、アプリは VNet 内のリソースにアクセスできなくなります。 
* VNet アドレス空間 - VNet の IP アドレス空間が表示されます。 
* ポイント対サイト アドレス空間 - VNet のポイント対サイト IP アドレス空間が表示されます。 VNet への呼び出しを行うと、アプリの送信元アドレスは、これらのアドレスの 1 つになります。 
* サイト対サイト アドレス空間 - サイト間 VPN を使用して、VNet をオンプレミスのリソースまたは他の VNet に接続できます。 その VPN 接続を使って定義されている IP 範囲は、ここに表示されます。
* DNS サーバー - ユーザーの VNet で構成されている DNS サーバーが表示されます。
* VNet にルーティングされる IP アドレス - VNet にトラフィックを誘導するためにルーティングされるアドレス ブロックが表示されます 

VNet 統合のアプリ ビューで実行できる操作は、現在接続されている VNet からアプリを切断する操作のみです。 VNet からアプリを切断するには、**[切断]** を選択します。 VNet から切断すると、アプリは再起動されます。 切断によって VNet に変更は生じません。 VNet とその構成 (ゲートウェイなど) は、元の状態のまま維持されます。 後に VNet を削除する必要が生じた場合は、その VNet に含まれるゲートウェイなどのリソースを最初に削除する必要があります。 

ASP VNet 統合 UI にアクセスするには、ASP UI を開き、**[ネットワーク]** を選択します。  [VNet 統合] で、**[Click here to configure]\(ここをクリックして構成)** を選択し、ネットワーク機能の状態 UI を開きます。

![ASP VNet 統合の情報][5]

ASP VNet 統合 UI には、ASP でアプリが使用しているすべての VNet が表示されます。 App Service プラン内の任意の数のアプリに、最大 5 つの VNet を接続できます。 各アプリで構成できる統合は 1 つだけです。 各 VNet の詳細を表示するには、目的の VNet をクリックします。 ここでは 2 つの操作を実行できます。

* **ネットワークを同期する**。 同期ネットワークの操作によって、証明書とネットワーク情報が同期するようにします。VNet の DNS を変更する場合は、**ネットワークの同期**操作を実行する必要があります。 この操作によって、この VNet を使用しているすべてのアプリが再起動されます。
* **ルートを追加する** ルートを追加すると、送信トラフィックが VNet に誘導されます。

**ルーティング** VNet に定義されたルートは、アプリから VNet にトラフィックを転送するために使用されます。 VNet に追加の送信トラフィックを送信する必要がある場合は、ここでそれらのアドレス ブロックを追加できます。   

**証明書** VNet 統合が有効になっているときには、接続のセキュリティを確保するために、証明書の交換が必要です。 証明書と共に、DNS の構成、ルート、ネットワークについて記述するその他の同様の情報があります。
証明書またはネットワーク情報が変更された場合は、[ネットワークの同期] をクリックする必要があります。 [ネットワークの同期] をクリックすると、アプリと VNet の間の接続が短時間途切れます。 アプリが再起動されていない間は、接続の途切れによって、サイトが正常に機能しなくなる可能性があります。 

## <a name="accessing-on-premises-resources"></a>オンプレミスのリソースへのアクセス
アプリは、サイト間接続がある Vnet と統合することで、オンプレミスのリソースにアクセスできます。 オンプレミスのリソースにアクセスするには、ポイント間アドレス ブロックを指定し、オンプレミスの VPN ゲートウェイのルートを更新する必要があります。 サイト間 VPN が初めてセットアップされるときには、その構成に使用するスクリプトによって、ルートが正しくセットアップされるはずです。 サイト間 VPN を作成した後にポイント対サイト アドレスを追加する場合は、ルートを手動で更新する必要があります。 その詳しい手順はゲートウェイごとに異なるため、ここでは取り上げません。 また、サイト間 VPN 接続による BGP の構成は行えません。

> [!NOTE]
> VNet 統合機能では、アプリは、ExpressRoute ゲートウェイを使う VNet とは統合されません。 ExpressRoute ゲートウェイが[共存モード][VPNERCoex]で構成されている場合であっても、VNet 統合は機能しません。 ExpressRoute 接続を通してリソースにアクセスする必要がある場合は、VNet で実行する [App Service 環境][ASE]を使うことができます。 
> 
> 

## <a name="peering"></a>ピアリング
VNet 統合を使用して、接続先の VNet にピアリングされている Vnet 内のリソースにアクセスできます。 アプリで動作するようにピアリングを構成するには:

1. アプリの接続先の VNet でピアリング接続を追加します。 ピアリング接続の追加時には、**[仮想ネットワークアクセスを許可する]** を有効にして、**[転送されたトラフィックを許可する]** チェック ボックスと **[ゲートウェイ転送を許可する]** チェック ボックスをオンにします。
1. 接続先の Vnet にピアリングされている VNet でピアリング接続を追加します。 対象の VNet でピアリング接続を追加するときには、**[仮想ネットワークアクセスを許可する]** を有効にして、**[転送されたトラフィックを許可する]** チェック ボックスと **[Allow remote gateways]\(リモート ゲートウェイを許可する)** チェック ボックスをオンにします。
1. ポータルで [App Service プラン] > [ネットワーク] > VNet 統合 UI と移動します。  アプリの接続先 VNet を選択します。 ルーティングのセクションで、アプリの接続先 VNet とピアリングされている VNet のアドレス範囲を追加します。  


## <a name="pricing-details"></a>価格の詳細
VNet 統合機能の使用に関連する料金は 3 種類あります。

* ASP の価格レベルの要件
* データ転送コスト
* VPN Gateway のコスト

アプリは、Standard、Premium、または PremiumV2 の App Service プランに属している必要があります。 これらのコストの詳細については、「[App Service の価格][ASPricing]」を参照してください。 

VNet が同じデータ センター内にある場合でも、データのエグレスには料金がかかります。 それらの料金については、[データ転送の価格の詳細][DataPricing]に関するページを参照してください。 

ポイント対サイト VPN には必須の VNet ゲートウェイに対して費用がかかります。 詳細については、[VPN Gateway の価格][VNETPricing]に関するページを参照してください。

## <a name="troubleshooting"></a>トラブルシューティング
この機能は簡単にセットアップできるものの、問題が発生しないわけではありません。 目的のエンドポイントへのアクセスに関して問題が発生した場合は、アプリのコンソールからの接続テストに、いくつかのユーティリティを利用できます。 利用できるコンソールが 2 つあります。 1 つは Kudu コンソールで、もう 1 つは Azure portal 内のコンソールです。 アプリから Kudu コンソールにアクセスするには、[ツール]、[Kudu] の順に移動します。 これは [サイト名].scm.azurewebsites.net に移動するのと同じです。 コンソールが開いたら、その [デバッグ] タブに移動します。Azure ポータルにホストされたコンソールにアクセスするには、アプリで [ツール]、[コンソール] の順に移動します。 

#### <a name="tools"></a>ツール
**ping**、**nslookup**、**tracert** の各ツールは、セキュリティの制約により、コンソールから使用することはできません。 それを補うために、2 つの独立したツールが追加されています。 DNS 機能のテスト用に、nameresolver.exe という名前のツールを追加しました。 の構文は次のとおりです。

    nameresolver.exe hostname [optional: DNS Server]

**nameresolver** を使用すると、アプリが依存しているホスト名を確認できます。 この方法で、DNS の構成に誤りがあるかどうかや、DNS サーバーへのアクセス権がないかどうかをテストできます。

次のツールでは、ホストとポートを組み合わせたものへの TCP 接続をテストすることができます。 このツールは **tcpping** という名前で、構文は次のとおりです。

    tcpping.exe hostname [optional: port]

**tcpping** ユーティリティを使用すると、特定のホストとポートにアクセスできるかどうかがわかります。 成功として示されるのは、ホストとポートの組み合わせでリッスンしているアプリケーションがあり、アプリから指定のホストとポートへのネットワーク アクセスがある場合のみです。

#### <a name="debugging-access-to-vnet-hosted-resources"></a>VNet にホストされたリソースへのアクセスのデバッグ
アプリから特定のホストとポートへのアクセスは、さまざまな要因によって妨げられる可能性があります。 ほとんどの場合、次の 3 つのうちのいずれかです。

* **ファイアウォールがルートを塞いでいる。** ルートを塞いでいるファイアウォールがあると、TCP のタイムアウトに達します。 この場合の TCP タイムアウトは 21 秒です。 **tcpping** ツールを使用して接続をテストします。 TCP タイムアウトの原因は、ファイアウォール以外にさまざまなことが考えられますが、まずここから始めます。 
* **DNS にアクセスできない。** DNS タイムアウトは、DNS サーバーごとに 3 秒です。 2 つの DNS サーバーがある場合、タイムアウトは 6 秒です。 nameresolver を使用して、DNS が機能しているかどうかを確認します。 nslookup は使用できないことに注意してください。これは、nslookup が VNet の構成に使用されている DNS を使用しないためです。
* **ポイント対サイト アドレス範囲が無効。** ポイント対サイト IP 範囲は、RFC 1918 プライベート IP 範囲 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255) に含まれる必要があります。 範囲で使用される IP がこれら以外の場合は、機能しません。 

以上の内容で問題を解決できない場合は、最初に次のような単純な点を確認してください。 

* ゲートウェイはポータルに稼働中と表示されているか。
* 証明書は同期していると表示されているか。
* 影響を受ける ASP で "ネットワークの同期" を行わずにネットワーク構成が変更されたか。 

ゲートウェイがダウンしている場合は、再起動してください。 証明書が同期されていない場合は、VNet 統合の ASP ビューに移動し、[ネットワークの同期] をクリックします。 VNet の構成に、ASP と同期していない変更が加えられたと思われる場合は、[ネットワークの同期] をクリックします。  [ネットワークの同期] 操作により、その VNet と統合されている、ASP 内のすべてのアプリが再起動されます。 

これらすべてに問題がない場合は、もう少し詳しく調べる必要があります。

* VNet 統合を使用して同じ VNet 内のリソースにアクセスするアプリが他にないか。 
* アプリのコンソールに移動し、tcpping を使用して VNet 内の他のリソースに到達できるか。 

上記のどちらかに到達できる場合は、その VNet 統合は正常で、問題は別の場所で発生しています。 このような状況になると、ホスト:ポートに到達できない理由を簡単に確認する手段がないため、究明は難しくなります。 以下に原因の例を示します。

* ホスト上で稼働しているファイアウォールが、ポイント対サイト IP の範囲からアプリケーション ポートへのアクセスを妨げている。 サブネットの境界を越えるには、多くの場合パブリック アクセスが必要になります。
* ターゲット ホストがダウンしている
* アプリケーションがダウンしている
* IP またはホスト名が誤っている
* アプリケーションが予期しないポートでリッスンしている。 エンドポイント ホストで "netstat-aon" を使用することで、プロセス ID と、リッスンしているポートを一致させることができます。 
* ネットワーク セキュリティ グループが、ポイント対サイト IP の範囲からアプリケーション ホストとポートへのアクセスをブロックするように構成されている

ポイント対サイト IP の範囲にある IP のうち、どれをアプリが使用するのかはわからないため、IP 範囲全体からのアクセスを許可する必要があります。 

追加のデバッグ手順は次のとおりです。

* VNet 内の VM に接続し、そこからリソースのホスト:ポートへのアクセスを試します。 TCP アクセスのテストには、PowerShell コマンド **test-netconnection** を使用します。 の構文は次のとおりです。

      test-netconnection hostname [optional: -Port]

* VM 上でアプリケーションを起動し、アプリのコンソールから、そのホストとポートにアクセスできるかどうかをテストします

#### <a name="on-premises-resources"></a>オンプレミスのリソース ####

アプリがオンプレミスのリソースにアクセスできない場合は、VNet からリソースにアクセスできるかどうかを確認します。 TCP アクセスを確認するには、PowerShell コマンド **test-netconnection** を使用します。 VM がオンプレミスのリソースにアクセスできない場合は、サイト間 VPN 接続が機能していることを確認してください。 機能している場合は、前に説明したのと同じ内容と、オンプレミス ゲートウェイの構成および状態を確認します。 

VNet でホストされている VM はオンプレミス システムにアクセスでき、アプリはアクセスできない場合、以下の理由のいずれかが原因と考えられます。

* オンプレミスのゲートウェイで、ルートがポイント対サイト IP 範囲で構成されていない。
* ネットワーク セキュリティ グループが、ポイント対サイト IP 範囲へのアクセスをブロックしている。
* オンプレミスのファイアウォールが、ポイント対サイト IP 範囲からのトラフィックをブロックしている。


## <a name="powershell-automation"></a>PowerShell オートメーション

App Service は、PowerShell を使用して Azure 仮想ネットワークと統合できます。 すぐに使用できるスクリプトについては、「[Connect an app in Azure App Service to an Azure Virtual Network (Azure App Service のアプリを Azure 仮想ネットワークに接続する)](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3)」をご覧ください。

## <a name="hybrid-connections-and-app-service-environments"></a>ハイブリッド接続と App Service Environment
VNet にホストされているリソースへのアクセスを実現する、3 つの機能があります。 次に例を示します。

* VNet 統合
* ハイブリッド接続と
* App Service Environment

ハイブリッド接続を利用する場合は、ハイブリッド接続マネージャー (HCM) と呼ばれるリレー エージェントをネットワークにインストールする必要があります。 HCM は、Azure のほか、アプリケーションにも接続できなければなりません。 ハイブリッド接続は、VPN 接続には必要な、インターネット アクセスが可能な受信エンドポイントを、リモート ネットワーク用には必要としません。 HCM は、Windows 上でのみ動作し、最大 5 つのインスタンスを実行して高可用性を実現できます。 ただし、ハイブリッド接続でサポートされるのは TCP だけです。各エンドポイントは、特定のホスト:ポートの組み合わせと一致している必要があります。 

App Service Environment 機能を使用すると、VNet で Azure App Service のシングル テナント インスタンスを実行できます。 アプリが App Service Environment 内にある場合、アプリは、追加手順なしで VNet 内のリソースにアクセスできます。 App Service Environment を使用すると、アプリより強力な worker で実行され、100 個の ASP インスタンスまでスケールアップできます。 App Service Environment は、ExpressRoute やサービス エンドポイントを含むすべてのネットワーク機能と共に機能します。  

共通する利用例はあるものの、これらの機能はいずれも互いに置き換え可能ではありません。 どの機能を使用するかは、ご自分のニーズと結び付いています。 例: 

* 開発者が、机の下にあるワークステーション上のデータベースにアクセスできるサイトを Azure で実行したい場合は、ハイブリッド接続を使用するのが最も簡単な方法です。 
* 大規模な組織がパブリック クラウドに多数の Web プロパティを配置し、独自のネットワークで管理する必要がある場合は、App Service Environment を使用できます。 
* VNet 内のリソースにアクセスする必要があるアプリが複数ある場合は、VNet 統合を使用します。 

VNet が既にオンプレミスのネットワークに接続されているときには、VNet 統合または App Service Environment を使用すると、オンプレミスのリソースを簡単に利用できます。 VNet がオンプレミスのネットワークに接続されていない場合は、VNet との間でサイト間 VPN の設定を行うと、HCM のインストールと比べてずっと多くのオーバーヘッドが生じます。 

機能だけではなく、価格にも違いがあります。 App Service Environment の機能は、Premium サービスによって提供されますが、その内容には、考えられるほとんどのネットワーク構成と、その他の優れた機能が含まれます。 VNet 統合は Standard または Premium ASP で利用可能であり、マルチテナントの App Service から VNet 内のリソースを安全に利用する場合に最適です。 ハイブリッド接続を利用するには、現時点では BizTalk アカウントが必要です。これには複数の価格レベルが用意されており、無料で開始した後に、必要量に基づいて段階的に上位の価格レベルに移行できます。 ただし、多数のネットワークが存在する環境で運用する場合には、ハイブリッド接続ほど適した機能はありません。ハイブリッド接続では、100 をはるかに超える独立したネットワークのリソースにアクセスすることができます。 

## <a name="new-vnet-integration"></a>新しい VNet 統合 ##

ポイント対サイト VPN テクノロジに依存しない新しいバージョンの VNet 統合機能があります。 以前からある機能とは異なり、新しいプレビュー機能は、ExpressRoute やサービス エンドポイントと共に機能します。 

新しい機能は、新しい Azure App Service スケール ユニットからのみ使用できます。 PremiumV2 にスケールできる場合は、新しい App Service スケール ユニットになっています。 ポータルの VNet 統合 UI には、お使いのアプリで新しい VNet 統合機能を使用できるかどうかが示されます。 

新しいバージョンはプレビュー段階にあり、以下の特徴があります。

* 新しい VNet 統合機能を使用するためにゲートウェイは必要ありません。
* ExpressRoute で接続された VNet と統合することのほかに、追加構成なしで、ExpressRoute 接続を介してリソースにアクセスできます。
* アプリと VNet は同じリージョンに存在する必要があります。
* 新しい機能では、Resource Manager の VNet 内に未使用のサブネットが必要です。
* Standard、Premium、PremiumV2 のいずれかの App Service プランを使用する必要があります
* 新機能では、プレビュー期間中、運用環境のワークロードはサポートされません
* アプリは、Premium v2 にスケール アップできる Azure App Service デプロイ内に配置されている必要があります。
* 新しい VNet 統合機能は、App Service Environment 内のアプリに対しては機能しません。
* 統合アプリで VNet を削除することはできません。  
* 新しい VNet 統合では、ルート テーブルとグローバル ピアリングはまだ使用できません。  
* App Service プランのインスタンスごとに 1 つのアドレスが使用されます。 サブネットのサイズは割り当て後に変更できないため、最大スケール サイズに余裕を持って対応できるサブネットを使用します。 20 インスタンスまでスケーリングする App Service プランに対応できるので、32 アドレスの /27 が推奨サイズです。
* 新しい VNet 統合機能を使用して、サービス エンドポイントのセキュリティで保護されたリソースを使用できます。 これを行うには、VNet 統合に使用されるサブネットでサービス エンドポイントを有効にします。

新機能を使用するには:

1. ポータルで [ネットワーク] の UI に移動します。 アプリが新機能を使用できる場合は、新しいプレビュー機能を使用するための機能が表示されます。  

   ![プレビューの新しい VNet 統合を選択する][6]

1. **[Add VNet (preview)]\(VNet の追加 (プレビュー))** を選択します。  

1. 統合の対象にする Resource Manager VNet を選択し、新しいサブネットを作成するか、空の既存のサブネットを選択します。 統合は 1 分未満で完了します。 統合中にアプリは再起動されます。  統合が完了すると、統合対象の VNet に関する詳細と、この機能はプレビュー段階にあることを通らせる上部のバナーが表示されます。

   ![VNet とサブネットを選択する][7]

VNet が構成されているアプリで DNS サーバーを使用できるようにするには、名前が WEBSITE_DNS_SERVER で値がサーバーの IP アドレスであるアプリケーション設定を、そのアプリに対して作成します。  セカンダリ DNS サーバーがある場合は、名前が WEBSITE_DNS_ALT_SERVER で、値がそのサーバーの IP アドレスである別のアプリケーション設定を作成します。 

VNet からアプリを切断するには、**[切断]** を選択します。 これにより、Web アプリが再起動されます。 

新しい VNet 統合機能では、サービス エンドポイントを使用できます。  アプリでサービス エンドポイントを使用するには、新しい VNet 統合を使用し、選択した VNet に接続してから、統合に使用したサブネット上のサービス エンドポイントを構成します。 

<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-howitworks.png
[4]: ./media/web-sites-integrate-with-vnet/vnetint-details.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-aspdetails.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-newvnet.png
[7]: ./media/web-sites-integrate-with-vnet/vnetint-newvnetdetails.png
[8]: ./media/web-sites-integrate-with-vnet/vnetint-selectvnet.png

<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ASEintro]: environment/intro.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#gatewaysubnet
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
