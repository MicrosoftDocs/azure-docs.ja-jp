---
title: "Microsoft Azure Cloud Services の接続とネットワークの問題についてよくあるご質問 | Microsoft Docs"
description: "この記事では、Microsoft Azure Cloud Services の接続とネットワークについてよくあるご質問を紹介します。"
services: cloud-services
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/10/2017
ms.author: genli
ms.translationtype: Human Translation
ms.sourcegitcommit: db18dd24a1d10a836d07c3ab1925a8e59371051f
ms.openlocfilehash: 3fc0d34fdb617ebb1af9c9f33e018d5fe6ec9a7d
ms.contentlocale: ja-jp
ms.lasthandoff: 06/15/2017

---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>Azure Cloud Services の接続とネットワークの問題についてよくあるご質問 (FAQ)

この記事では、[Microsoft Azure Cloud Services](https://azure.microsoft.com/services/cloud-services) の接続とネットワークの問題についてよくあるご質問を紹介します。 サイズについては、 [Cloud Services VM サイズのページ](cloud-services-sizes-specs.md) を参照してください。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>マルチ VIP クラウド サービスの IP アドレスを予約できません
まず、IP アドレスを予約しようとしている仮想マシン インスタンスが有効であることを確認してください。 次に、ステージング デプロイと運用環境デプロイの両方で予約済みの IP を使っていることを確認します。 **変更しない** でください。

## <a name="how-do-i-remote-desktop-when-i-have-an-nsg"></a>NSG がある場合にリモート デスクトップを使用するには
ポート **3389** および **20000** でのトラフィックを許可するルールを NSG に追加します。  リモート デスクトップはポート **3389** を使用します。  クラウド サービス インスタンスは負荷分散されるため、接続先のインスタンスを直接制御することはできません。  *RemoteForwarder* および *RemoteAccess* エージェントは、RDP トラフィックを管理し、クライアントが RDP クッキーを送信して接続先の個々のインスタンスを指定できるようにします。  *RemoteForwarder* および *RemoteAccess* エージェントではポート **20000** を開く必要があります。NSG がある場合、このポートはブロックされている可能性があります。

## <a name="can-i-ping-a-cloud-service"></a>クラウド サービスに ping を実行することはできますか。

いいえ、通常の "ping"/ICMP プロトコルは使用できません。 ICMP プロトコルは、Azure Load Balancer を通過することを許可されません。

接続をテストするには、ポートの ping を実行することをお勧めします。 Ping.exe は ICMP を使用していますが、PSPing、Nmap、telnet など、他のツールを使用すると、特定の TCP ポートへの接続をテストできます。

詳細については、「[Use port pings instead of ICMP to test Azure VM connectivity](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/) (ICMP の代わりにポート ping を使用して Azure VM の接続をテストする)」を参照してください。

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-indicate-some-sort-of-malicious-attack-to-the-cloud-service"></a>クラウド サービスへの悪意のある何らかの攻撃を示す、不明な IP アドレスからのヒットが数千に上りますが、どのように防止すればよいですか。
Azure では、分散型サービス拒否 (DDoS) 攻撃からプラットフォーム サービスを保護するために複数層のネットワーク セキュリティを実装しています。 Azure の DDoS 防御システムは、Azure の継続的な監視プロセスの一部であり、侵入テストを通して継続的に強化されています。 この DDoS 防御システムは、外部からの攻撃だけではなく、他の Azure テナントからの攻撃にも耐えられるように設計されています。 詳細については、「[Microsoft Azure Network Security](http://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf) (Microsoft Azure のネットワーク セキュリティ)」を参照してください。

また、いくつか特定の IP アドレスを選択的にブロックするスタートアップ タスクを作成することもできます。 詳細については、「[特定の IP アドレスをブロックする](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)」を参照してください。

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>自分のクラウド サービス インスタンスに RDP 接続しようとすると、「ユーザー アカウントの有効期限が切れました」というメッセージを受け取ります。
RDP の設定で構成されている有効期限の日付を無視すると、「このユーザー アカウントは有効期限が切れました」のエラー メッセージを受け取ることがあります。 ポータルから有効期限の日付を変更するには、次の手順を実行します。
1. Azure の管理コンソール (https://manage.windowsazure.com) にログインし、お使いのクラウド サービスに移動して、**[構成]** タブを選択します。
2. **[リモート]** を選択します。
3. [有効期限] の日付を変更し、構成を保存します。

これで、お使いのマシンに RDP 接続できるようになるはずです。

## <a name="why-is-loadbalancer-not-balancing-traffic-equally"></a>LoadBalancer がトラフィックを均等に分散しないのはなぜですか。
ロード バランサー内部のしくみについては、「[Azure Load Balancer new distribution mode](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/) (Azure ロード バランサーの新しい分散モード)」を参照してください。

使用される分散アルゴリズムは、使用可能なサーバーにトラフィックをマップする 5 組 (ソース IP、ソース ポート、接続先 IP、接続先ポート、プロトコルの種類) のハッシュです。 これは、トランスポート セッション内でのみ持続性を提供します。 TCP または UDP の同じセッション内のパケットは、負荷分散されたエンドポイントの背後にある同じデータ センターの IP (DIP) インスタンスに送信されます。 クライアントがもう一度接続を開くか、同じソース IP から新しいセッションを開始すると、ソース ポートが変更され、トラフィックは別の DIP エンドポイントに送信されます。

