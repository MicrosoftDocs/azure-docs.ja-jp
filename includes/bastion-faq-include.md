---
title: インクルード ファイル
description: インクルード ファイル
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 05/04/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: efb32631c5ee1eedece6d2a06b94702b602ed418
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276118"
---
### <a name="which-regions-are-available"></a><a name="regions"></a>利用可能なリージョン

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="do-i-need-a-public-ip-on-my-virtual-machine"></a><a name="publicip"></a>自分の仮想マシンにはパブリック IP が必要ですか?

Azure Bastion を使用して VM に接続するときは、接続先となる Azure 仮想マシンのパブリック IP は必要ありません。 Bastion サービスは、ご使用の仮想ネットワーク内で、お客様の仮想マシンのプライベート IP 経由でお客様の仮想マシンへの RDP または SSH セッションや接続を開きます。

### <a name="is-ipv6-supported"></a>IPv6 はサポートされていますか?

現時点では、IPv6 はサポートされていません。 Azure Bastion は、IPv4 のみをサポートしています。

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>RDP または SSH クライアントは必要ですか?

ご使用の Azure portal 内でお客様の Azure 仮想マシンに RDP または SSH アクセスするために RDP または SSH クライアントは必要ありません。 [Azure portal](https://portal.azure.com) を使用して、ブラウザー内で仮想マシンに直接 RDP または SSH アクセスすることができます。

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Azure 仮想マシン内で実行するエージェントは必要ですか?

ご使用のブラウザーやお客様の Azure 仮想マシンにエージェントやソフトウェアをインストールする必要はありません。 Bastion サービスはエージェントレスのため、RDP や SSH 用の追加のソフトウェアを必要としません。

### <a name="how-many-concurrent-rdp-and-ssh-sessions-does-each-azure-bastion-support"></a><a name="limits"></a>各 Azure Bastion がサポートしている同時 RDP セッションと SSH セッションの数はいくつですか?

RDP と SSH はどちらも使用量ベースのプロトコルです。 セッションの使用量が多いと、要塞ホストでサポートされるセッションの合計数が少なくなります。 以下の数値は、通常の日常のワークフローを想定しています。

[!INCLUDE [limits](bastion-limits.md)]

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>RDP セッションではどのような機能がサポートされていますか?

現時点では、テキストのコピーと貼り付けのみがサポートされています。 ファイルのコピーなどの機能はサポートされていません。 [Azure Bastion フィードバック ページ](https://feedback.azure.com/forums/217313-networking?category_id=367303)で、新機能に関するフィードバックをぜひお寄せください。

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>どのブラウザーがサポートされていますか?

Windows では Microsoft Edge ブラウザーまたは Google Chrome をご使用ください。 Apple Mac では、Google Chrome ブラウザーをご使用ください。 Microsoft Edge Chromium も Windows と Mac の両方でサポートされます。

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>顧客データは Azure Bastion によってどこに格納されますか?

Azure Bastion によって、顧客データがデプロイされているリージョン外に移動または格納されることはありません。

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>仮想マシンにアクセスするには、なんらかのロールが必要ですか?

接続を作成するには、次のロールが必要です。

* 仮想マシンに対する閲覧者ロール
* 仮想マシンのプライベート IP を使用する NIC に対する閲覧者ロール
* Azure Bastion リソースに対する閲覧者ロール

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>料金はどのようになっていますか?

詳細については、 [価格に関するページ](https://aka.ms/BastionHostPricing)を参照してください。

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Azure Bastion には、Azure でホストされる VM を管理する目的で RDS CAL が必要ですか?
いいえ、管理の目的にのみ使用する場合、Azure Bastion による Windows Server VM へのアクセスに [RDS CAL](https://www.microsoft.com/en-us/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) は必要ありません。

### <a name="what-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Bastion リモート セッション中はどのようなキーボード レイアウトがサポートされますか?

Azure Bastion は現在、VM 内で en-us-qwerty のキーボード レイアウトをサポートしています。  キーボード レイアウトのその他のロケールに対するサポートは進行中の作業です。

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Azure Bastion サブネットでユーザー定義ルーティング (UDR) はサポートされますか?

いいえ。 Azure Bastion サブネットで UDR はサポートされません。
Azure Bastion と Azure Firewall/ネットワーク仮想アプライアンス (NVA) の両方が同じ仮想ネットワークに存在するシナリオでは、Azure Bastion と VM との間の通信はプライベートであるため、Azure Bastion サブネットから Azure Firewall にトラフィックを強制する必要がありません。 詳細については、[Azure Firewall と Bastion の内側の VM にアクセスする方法](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/)に関するページを参照してください。

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Bastion セッションの開始前に "セッションの有効期限が切れています" というエラー メッセージを受け取るのはなぜですか?

セッションは Azure portal からのみ開始される必要があります。 もう一度 Azure portal にサインインして、自分のセッションを開始してください。 別のブラウザー セッションまたはタブから直接 URL に移動する場合に、このエラーが発生します。 これは、自分のセッションがより安全で、セッションが Azure portal からのみアクセスできることを確保するのに役立ちます。

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>デプロイ エラーはどのように処理しますか?

エラー メッセージを確認し、必要に応じて [Azure portal 内でサポート リクエストを送信](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)してください。 [Azure サブスクリプションの制限、クォータ、制約](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)によって、デプロイ エラーが発生する可能性があります。 特に、サブスクリプションごとに許可されるパブリック IP アドレスの数に対する制限が発生する可能性があります。これは、Azure Bastion のデプロイが失敗する原因となります。
