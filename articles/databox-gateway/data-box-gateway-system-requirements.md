---
title: Microsoft Azure Data Box Gateway のシステム要件| Microsoft Docs
description: Azure Data Box Gateway のソフトウェア要件とネットワーキング要件について説明します
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: e7c8653b39a3e0333ff6e98783a6e9a1437dba22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739213"
---
# <a name="azure-data-box-gateway-system-requirements"></a>Azure Data Box Gateway のシステム要件

この記事では、Microsoft Azure Data Box Gateway ソリューション、および Azure Data Box Gateway に接続するクライアントのシステム要件のうち、重要なものについて説明します。 この情報は、Data Box Gateway をデプロイする前によく確認し、デプロイ時やそれ以降の操作時にも、必要に応じて繰り返し参照することをお勧めします。 

Data Box Gateway 仮想デバイスのシステム要件は次のとおりです。

- **ホストのソフトウェア要件** - サポートされているプラットフォーム、ローカル構成 UI 用のブラウザー、SMB クライアント、およびデバイスに接続するホストのその他の要件について説明します。
- **デバイスのネットワーク要件** - 仮想デバイスの操作のためのネットワーク要件について説明します。


## <a name="specifications-for-the-virtual-device"></a>仮想デバイスの仕様

Data Box Gateway の基になっているホスト システムでは、仮想デバイスのプロビジョニング専用に次のリソースを使用できます。

| 仕様                                          | 説明              |
|---------------------------------------------------------|--------------------------|
| 仮想プロセッサ (コア)   | 最低 4 |
| メモリ  | 8 GB 以上。 16 GB 以上にすることを強くお勧めします。 |
| 可用性|単一ノード|
| ディスク| OS ディスク:250 GB <br> データ ディスク:最小 2 TB、シン プロビジョニング、SSD を使用する必要があります|
| ネットワーク インターフェイス|1 つ以上の仮想ネットワーク インターフェイス|


## <a name="supported-os-for-clients-connected-to-device"></a>デバイスに接続されるクライアントでサポートされている OS

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>デバイスにアクセスするクライアントでサポートされるプロトコル

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-virtualization-platforms-for-device"></a>デバイスでサポートされている仮想化プラットフォーム

| **オペレーティング システム/プラットフォーム**  |**[バージョン]**   |**ノート**  |
|---------|---------|---------|
|Hyper-V  |  2012 R2 <br> 2016 <br> 2019 |         |
|VMware ESXi     | 6.0 <br> 6.5 <br> 6.7       |VMware ツールはサポートされていません。         |


## <a name="supported-storage-accounts"></a>サポートされるストレージ アカウント

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]


## <a name="supported-storage-types"></a>サポートされているストレージの種類

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>ローカル Web UI 用にサポートされているブラウザー

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>ネットワーク ポートの要件

SMB、クラウド、または管理トラフィックを許可するためにファイアウォールで開く必要があるポートを次の表に示します。 この表では、"*イン*" ("*受信*") は、着信クライアント要求がデバイスにアクセスする方向を意味します。 "*アウト*" ("*送信*") は Data Box Gateway デバイスがデプロイを超えて外部に (たとえば、インターネットに) データを送信する方向を意味します。

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

## <a name="url-patterns-for-firewall-rules"></a>ファイアウォール ルールの URL パターン

多くの場合、ネットワーク管理者は、受信トラフィックと送信トラフィックをフィルターする URL パターンに基づいて、高度なファイアウォール ルールを構成できます。 Data Box Gateway デバイスと Data Box Gateway サービスは、Azure Service Bus、Azure Active Directory Access Control、ストレージ アカウント、Microsoft Update サーバーなど、他の Microsoft アプリケーションに依存しています。 その Microsoft アプリケーションと関連付けられた URL パターンを使用してファイアウォール ルールを構成できます。 Microsoft アプリケーションに関連付けられた URL パターンは変化する可能性がある点を理解することが重要です。 このため、ネットワーク管理者は必要に応じて Data Box Gateway のファイアウォール ルールを監視し更新する必要があります。

ほとんどの場合、送信トラフィックのファイアウォール ルールは Data Box Gateway 固定 IP アドレスに基づいて設定することが推奨されます。 ただし、次の情報を使用して、セキュリティで保護された環境を作成するのにために必要な高度なファイアウォール ルールを設定することもできます。

> [!NOTE]
> - デバイスの (送信元) IP は、常にすべてのクラウド対応ネットワーク インターフェイスに合わせて設定します。
> - 宛先 IP は、[Azure データセンターの IP 範囲](https://www.microsoft.com/download/confirmation.aspx?id=41653)に合わせて設定するようにします。

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-azure-government"></a>Azure Government の URL パターン

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

## <a name="internet-bandwidth"></a>インターネット帯域幅

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="next-step"></a>次の手順

* [Azure Data Box Gateway をデプロイする](data-box-gateway-deploy-prep.md)

