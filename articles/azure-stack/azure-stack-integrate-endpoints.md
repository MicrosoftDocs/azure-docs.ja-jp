---
title: "Azure Stack とデータセンターの統合 - エンドポイントの公開"
description: "データセンターで Azure Stack のエンドポイントを公開する方法を学習します"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 9/25/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: 02d73a3d843ee7cd3cdfbf6b137908e03d7306a7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stack-datacenter-integration---publish-endpoints"></a>Azure Stack とデータセンターの統合 - エンドポイントの公開

*適用対象: Azure Stack 統合システム*

Azure Stack は、さまざまなエンドポイント (VIP - 仮想 IP アドレス) をエンドポイントのインフラストラクチャ ロールに応じて設定します。 この VIP はパブリック IP アドレス プールから割り当てられます。 各 VIP は、ソフトウェア定義のネットワーク レイヤーで、アクセス制御リスト (ACL) で保護されます。 ACL は、ソリューションをさらに強化するために、さまざまな物理スイッチ (TOR や BMC) でも使われます。 デプロイ時に指定した外部 DNS ゾーン内のエンドポイントごとに DNS エントリが作成されます。


次のアーキテクチャ図は、さまざまなネットワーク レイヤーと ACL を示しています。

![アーキテクチャ図](media/azure-stack-integrate-endpoints/Integrate-Endpoints-01.png)

## <a name="ports-and-protocols-inbound"></a>ポートとプロトコル (受信)

外部ネットワークに Azure Stack のエンドポイントを公開するために必要なインフラストラクチャの VIP は、次の表に掲載されています。 この一覧では、各エンドポイント、必要なポート、そしてプロトコルを示しています。 特定のリソースプロバイダーの展開に関するドキュメントでは、SQL のリソースプロバイダーなどのような、追加のリソースプロバイダーに必要なエンドポイントについて説明します。

社内インフラストラクチャの VIP は Azure Stack の発行には不要なため、記載されていません。

> [!NOTE]
> ユーザーの VIP は動的で、Azure Stack オペレーターによって管理されず、ユーザー自身が定義します。


|エンドポイント (VIP)|DNS ホスト A レコード|プロトコル|ポート|
|---------|---------|---------|---------|
|AD FS|`Adfs.[Region].[External FQDN]`|HTTPS|443|
|ポータル (管理者)|`Adminportal.[Region].[External FQDN]`|HTTPS|443|
|Azure Resource Manager (管理者)|`Adminmanagement.[Region].[External FQDN]`|HTTPS|443<br>30024|
|ポータル (ユーザー)|`Portal. [Region].[External FQDN]`|HTTPS|443<br>12495<br>12649<br>13001<br>13010<br>13011<br>13020<br>13021<br>30015<br>13003|
|Azure Resource Manager (ユーザー)|`Management.[Region].[External FQDN]`|HTTPS|443<br>30024|
|Graph|`Graph.[Region].[External FQDN]`|HTTPS|443|
|証明書の失効リスト|`Crl.[Region].[External FQDN]`|HTTP|80|
|DNS|`*.[Region].[External FQDN]`|TCP と UDP|53|
|Key Vault (ユーザー)|`*.vault.[Region].[External FQDN]`|TCP<br>TCP|443<br>12490|
|Key Vault (管理者)|`*.adminvault.[Region].[External FQDN]`|TCP<br>TCP|443<br>12492|
|ストレージ キュー|`*.queue.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|ストレージ テーブル|`*.table.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|
|ストレージ BLOB|`*.blob.[Region].[External FQDN]`|HTTP<br>HTTPS|80<br>443|

## <a name="ports-and-urls-outbound"></a>ポートと URL (送信)

Azure Stack は、透過的なプロキシ サーバーのみをサポートします。 透過プロキシから従来のプロキシ サーバーへのアップリンクが存在するデプロイ環境では、次のポートと URL に外部への通信を許可する必要があります。


|目的|URL|プロトコル|ポート|
|---------|---------|---------|---------|
|ID|`login.windows.net`<br>`login.microsoftonline.com`<br>`graph.windows.net`|HTTP<br>HTTPS|80<br>443|
|Marketplace シンジケーション|`https://management.azure.com`<br>`https://*.blob.core.windows.net`<br>`https://*.azureedge.net`<br>`https://*.microsoftazurestack.com`|HTTPS|443|
|パッチと更新プログラム|`https://*.azureedge.net`|HTTPS|443|
|登録|`https://management.azure.com`|HTTPS|443|
|使用法|`https://*.microsoftazurestack.com`<br>`https://*.trafficmanager.com`|HTTPS|443|

## <a name="firewall-publishing"></a>ファイアウォールの発行

既存のファイアウォールを介してAzure Stack サービスを発行するとき、前のセクションに記載されているポートをインバウンド通信に適用します。

ファイアウォール デバイスを使って、Azure Stack を保護することをお勧めします。 ただし、これは厳密な要件ではありません。 ファイアウォールは、分散型サービス拒否 (DDOS) 攻撃やコンテンツ検査などに効果がありますが、BLOB、テーブル、キューなどの Azure Storage サービスのスループットのボトルネックにもなります。

ID モデル (Azure ADまたはAD FS) によって、AD FS のエンドポイントを公開する必要がある場合と、ない場合とがあります。 切断されたデプロイ モードを使用する場合は、AD FS のエンドポイントを発行する必要があります。 (詳細については、データ センターの統合の ID のトピックをご覧ください。)

Azure Resource Manager (管理者)、管理者ポータル、Key Vault (管理者) のエンドポイントは、外部発行が必須というわけではありません。 これはシナリオによって異なります。 たとえば、サービス プロバイダーは、攻撃対象領域を制限して、インターネットからではなくネットワークの内部から Azure Stack の管理のみをすることができます。

企業組織では、外部のネットワークは既存の企業ネットワークを指定できます。 このようなシナリオでは、企業ネットワークから Azure Stack を操作するには、そのエンドポイントを公開する必要があります。

## <a name="edge-firewall-scenario"></a>エッジ ファイアウォール シナリオ

エッジ デプロイでは、Azure Stack は 外側にファイアウォールがあってもなくても、エッジ ルーター (ISPが提供) の内側に直接デプロイされます。

![Azure Stack のエッジ デプロイのアーキテクチャ図](media/azure-stack-integrate-endpoints/Integrate-Endpoints-02.png)

通常、エッジ デプロイではパブリックにルーティング可能な IP アドレスは、デプロイ時にパブリック VIP プールに指定されます。 このシナリオでは、Azure などのパブリック クラウドでのエクスペリエンスのような、完全に自己管理型のクラウドを体験できます。

### <a name="using-nat"></a>NAT の使用

オーバーヘッドのため推奨はされませんが、エンドポイント公開用にネットワーク アドレス変換 (NAT) を使用できます。 ユーザーが完全に管理するエンドポイント公開のためには、ユーザーが使う可能性のあるすべてのポートを含むユーザー VIP ごとに NAT 規則が必要です。

別の考慮事項は、Azure のハイブリッド クラウド シナリオで NAT を使うエンドポイントへの VPN トンネルの設定を、Azure がサポートしていないことです。

## <a name="enterpriseintranetperimeter-network-firewall-scenario"></a>企業、イントラネット、境界ネットワークのファイアウォール シナリオ

企業、イントラネット、境界のデプロイでは、2 つ目のファイアウォールの先に Azure Stack がデプロイされ、通常、境界ネットワーク (DMZ とも呼ばれます) の一部となります。

![Azure Stack ファイアウォール シナリオ](media/azure-stack-integrate-endpoints/Integrate-Endpoints-03.png)

パブリックにルーティング可能な IP アドレスが Azure Stack のパブリック VIP プールに指定されている場合、これらのアドレスは論理的には境界ネットワークに属し、プライマリ ファイアウォールで公開規則が必要となります。

### <a name="using-nat"></a>NAT の使用

パブリックにルーティング不能な IP アドレスが Azure Stack のパブリック VIP プールに指定されている場合、Azure Stack のエンドポイントを公開するためにセカンダリ ファイアウォールで NAT が使われます。 このシナリオでは、エッジの先のプライマリ ファイアウォールとセカンダリ ファイアウォールで公開規則を構成する必要があります。 NAT を使用する場合は、次の点を考慮します。

- ソフトウェア定義ネットワーク (SDN) スタックでは、ユーザーが独自のエンドポイントと独自の公開規則を管理するため、ファイアウォール ルールを管理する場合に NAT はオーバーヘッドを増加させます。 ユーザーは Azure Stack オペレーターと連絡をとって、自分の VIP を公開させ、ポート リストを更新する必要があります。
- NAT の使用によりユーザー エクスペリエンスは制限されますが、オペレーターは公開要求を完全に管理できます。
- Azure のハイブリッド クラウド シナリオでは、NAT を使うエンドポイントへの VPN トンネルの設定を Azure がサポートしていないことを考慮します。


## <a name="next-steps"></a>次のステップ

[Azure Stack とデータセンターの統合 - DNS](azure-stack-integrate-dns.md)