---
title: CloudSimple による Azure VMware ソリューション - ExpressRoute を使用したオンプレミス接続
description: CloudSimple リージョン ネットワークから ExpressRoute を使用してオンプレミス接続を要求する方法について説明します。
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0dd5ede110255b6e53bbc397e683e66b3beffc65
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910550"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>ExpressRoute を使用してオンプレミスから CloudSimple に接続する

外部の場所 (オンプレミスなど) から Azure への Azure ExpressRoute 接続が既にある場合は、CloudSimple 環境に接続できます。 これは、2 つの ExpressRoute 回線が相互に接続できる Azure の機能を介して行うことができます。 この方法によって、2 つの環境間に、セキュリティで保護された、非公開、高帯域幅、低待機時間の接続が確立します。

[![オンプレミスの ExpressRoute 接続 - Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>開始する前に

オンプレミスから Global Reach 接続を確立するには、 **/29** ネットワーク アドレス ブロックが必要です。  /29 アドレス空間は、ExpressRoute 回線間のトランジット ネットワークに使用されます。  トランジット ネットワークは、Azure 仮想ネットワーク、オンプレミス ネットワーク、または CloudSimple プライベート クラウド ネットワークのいずれとも重複しないようにする必要があります。

## <a name="prerequisites"></a>前提条件

* Azure ExpressRoute 回線は、回線と CloudSimple プライベート クラウド ネットワーク間の接続を確立する前に必要です。
* ExpressRoute 回線上で承認キーを作成する特権を持つユーザーが必要です。

## <a name="scenarios"></a>シナリオ

オンプレミス ネットワークをプライベート クラウド ネットワークに接続すると、次のシナリオのようなさまざまな方法でプライベート クラウドを使用できるようになります。

* サイト間 VPN 接続を作成せずに、プライベート クラウド ネットワークにアクセスする。
* プライベート クラウド上で ID ソースとしてオンプレミスの Active Directory を使用する。
* オンプレミス上で実行されている仮想マシンをプライベート クラウドに移行する。
* ディザスター リカバリー ソリューションの一部としてプライベート クラウドを使用する。
* プライベート クラウド ワークロード VM 上でオンプレミス リソースを使用する。

## <a name="connecting-expressroute-circuits"></a>ExpressRoute 回線の接続

ExpressRoute 接続を確立するには、ExpressRoute 回線上で承認を作成し、認証情報を CloudSimple に提供する必要があります。


### <a name="create-expressroute-authorization"></a>ExpressRoute 承認の作成

1. Azure ポータルにサインインします。

2. 上部の検索バーで **ExpressRoute 回線** を検索して、 **[サービス]** の **[ExpressRoute 回線]** をクリックします。
    [![ExpressRoute 回線](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. CloudSimple ネットワークに接続する ExpressRoute 回線を選択します。

4. [ExpressRoute] ページで **[承認]** をクリックし、承認の名前を入力して、 **[保存]** をクリックします。
    [![ExpressRoute 回線の承認](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. [コピー] アイコンをクリックして、リソース ID と承認キーをコピーします。 ID とキーをテキスト ファイルに貼り付けます。
    [![ExpressRoute 回線の承認のコピー](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **[リソース ID]** は、UI からコピーする必要があります。また、サポートするように指定するときは、```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` という形式にする必要があります。

6. 作成する接続の <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">サポート</a> に関するチケットを申請します。
    * [問題の種類]\:**テクニカル**
    * サブスクリプション:**CloudSimple サービスがデプロイされるサブスクリプション**
    * サービス:**VMware Solution by CloudSimple**
    * 問題の種類:**サービス リクエスト**
    * 問題のサブタイプ:**オンプレミスへの ExpressRoute 接続を作成する**
    * コピーして [詳細] ウィンドウに保存したリソース ID と承認キーを指定します。
    * トランジット ネットワーク用の /29 ネットワーク アドレス空間を指定します。
    * ExpressRoute 経由で既定のルートを送信していますか?
    * プライベート クラウドのトラフィックは、ExpressRoute 経由で送信される既定のルートを使用する必要がありますか?

    > [!IMPORTANT]
    > 既定のルートを送信することで、オンプレミスのインターネット接続を使用して、プライベート クラウドからすべてのインターネット トラフィックを送信することができます。  プライベート クラウドに構成されている既定のルートを無効にし、オンプレミス接続の既定のルートを使用するには、サポート チケットに詳細を指定してください。

## <a name="next-steps"></a>次の手順

* [Azure ネットワーク接続の詳細を確認します](cloudsimple-azure-network-connection.md)  
