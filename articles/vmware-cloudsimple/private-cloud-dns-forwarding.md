---
title: Azure VMware ソリューション - プライベート クラウドからオンプレミスへの DNS 転送
description: CloudSimple プライベート クラウドの DNS サーバーからオンプレミスのリソースの参照を転送できるようにする方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3571455db6ecc600bf0948087b40c281d72512ce
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091251"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>CloudSimple プライベート クラウドの DNS サーバーからオンプレミスのリソースの DNS 参照を DNS サーバーに転送できるようにする

プライベート クラウドの DNS サーバーを使うと、オンプレミスのリソースの DNS 参照を DNS サーバーに転送できます。  参照を有効にすると、Private Cloud vSphere コンポーネントを使って、オンプレミス環境で実行されているサービスを参照し、完全修飾ドメイン名 (FQDN) を使ってそれらと通信できるようになります。

## <a name="scenarios"></a>シナリオ 

オンプレミスの DNS サーバーの DNS 参照を転送すると、次のシナリオでプライベート クラウドを使用できます。

* オンプレミスの VMware ソリューションのディザスター リカバリー設定としてプライベート クラウドを使用する
* プライベート クラウドの vSphere の ID ソースとしてオンプレミスの Active Directory を使用する
* オンプレミスからプライベート クラウドへの仮想マシンの移行に HCX を使用する

## <a name="before-you-begin"></a>開始する前に

DNS 転送を機能させるには、プライベート クラウド ネットワークからオンプレミス ネットワークへのネットワーク接続が存在している必要があります。  ネットワーク接続を設定するには、以下を使用します。

* [ExpressRoute を使用してオンプレミスから CloudSimple に接続する](on-premises-connection.md)
* [サイト間 VPN ゲートウェイのセットアップ](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

DNS 転送を機能させるには、この接続でファイアウォール ポートを開く必要があります。  使用されるポートは、TCP ポート 53 または UDP ポート 53 です。

> [!NOTE]
> サイト間 VPN を使用している場合は、オンプレミスの DNS サーバー サブネットをオンプレミスのプレフィックスの一部として追加する必要があります。

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>プライベート クラウドからオンプレミスへの DNS 転送を要求する

プライベート クラウドからオンプレミスへの DNS 転送を有効にするには、[サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を送信し、次の情報を提供します。

* 問題の種類:**テクニカル**
* サブスクリプション:**CloudSimple サービスがデプロイされるサブスクリプション**
* サービス:**VMware Solution by CloudSimple**
* 問題の種類:**アドバイザリまたは操作方法...**
* 問題のサブタイプ:**NW に関するヘルプが必要です**
* 詳細ウィンドウで、オンプレミス ドメインのドメイン名を指定します。
* 詳細ウィンドウで、プライベート クラウドから参照を転送するオンプレミスの DNS サーバーの一覧を指定します。

## <a name="next-steps"></a>次のステップ

* [オンプレミスのファイアウォールの構成についての詳細情報](on-premises-firewall-configuration.md)
* [オンプレミスの DNS サーバーの構成](on-premises-dns-setup.md)
