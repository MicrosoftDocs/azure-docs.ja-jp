---
title: 接続とネットワークの問題
description: この記事では、Microsoft Azure Cloud Services の接続とネットワークについてよくあるご質問を紹介します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: c7b83c615e4ac19e10b5c4f6cc1a102206b1a39a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98742422"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-classic-frequently-asked-questions-faqs"></a>Azure Cloud Services (クラシック) の接続とネットワークの問題:よく寄せられる質問 (FAQ)

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

この記事では、[Azure Cloud Services](https://azure.microsoft.com/services/cloud-services) の接続とネットワークの問題についてよくあるご質問を紹介します。 サイズについては、[Cloud Services VM サイズのページ](cloud-services-sizes-specs.md)を参照してください。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>マルチ VIP クラウド サービスの IP アドレスを予約できません。
まず、IP アドレスを予約しようとしている仮想マシン インスタンスがオンになっていることを確認してください。 次に、ステージング デプロイと運用環境デプロイの両方で予約済みの IP を使っていることを確認します。 *変更しない* でください。

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>NSG がある場合にリモート デスクトップを使用するにはどうすればよいですか。
ポート **3389** および **20000** でのトラフィックを許可するルールを NSG に追加します。 リモート デスクトップではポート **3389** を使用します。 クラウド サービス インスタンスは負荷分散されるため、接続先のインスタンスを直接制御することはできません。 *RemoteForwarder* および *RemoteAccess* エージェントは、リモート デスクトップ プロトコル (RDP) トラフィックを管理し、クライアントが RDP Cookie を送信して接続先の個々のインスタンスを指定できるようにします。 *RemoteForwarder* および *RemoteAccess* エージェントでは、ポート **20000** を開く必要があります。このポートは、NSG がある場合にブロックされる可能性があります。

## <a name="can-i-ping-a-cloud-service"></a>クラウド サービスに ping を実行することはできますか。

いいえ、通常の "ping"/ICMP プロトコルは使用できません。 ICMP プロトコルは、Azure Load Balancer を通過することを許可されません。

接続をテストするには、ポートの ping を実行することをお勧めします。 Ping.exe は ICMP を使用しますが、他のツール (PSPing、Nmap、telnet など) を使用して特定の TCP ポートへの接続をテストできます。

詳細については、「[Use port pings instead of ICMP to test Azure VM connectivity](/archive/blogs/mast/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity) (ICMP の代わりにポート ping を使用して Azure VM の接続をテストする)」を参照してください。

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>クラウド サービスへの悪意のある攻撃の可能性がある不明な IP アドレスからの数千件に上るヒットを受信するのを防止するにはどうすればよいですか。
Azure では、分散型サービス拒否 (DDoS) 攻撃からプラットフォーム サービスを保護するために複数層のネットワーク セキュリティを実装しています。 Azure の DDoS 防御システムは、Azure の継続的な監視プロセスの一部であり、侵入テストを通して継続的に強化されています。 この DDoS 防御システムは、外部からの攻撃だけではなく、他の Azure テナントからの攻撃にも耐えられるように設計されています。 詳細については、[Azure ネットワークのセキュリティ](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)に関するドキュメントを参照してください。

また、いくつかの特定の IP アドレスを選択的にブロックするスタートアップ タスクを作成することもできます。 詳細については、「[特定の IP アドレスをブロックする](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)」を参照してください。

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>自分のクラウド サービス インスタンスに RDP 接続しようとすると、"このユーザー アカウントの有効期限が切れています" というメッセージを受け取ります。
RDP の設定で構成されている有効期限の日付を無視すると、"このユーザー アカウントの有効期限が切れています" のエラー メッセージを受け取ることがあります。 ポータルから有効期限の日付を変更するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com) にサインインし、お使いのクラウド サービスに移動して、 **[リモート デスクトップ]** タブを選びます。

2. **[運用]** または **[ステージング]** のデプロイ スロットを選びます。

3. **[有効期限]** の日付を変更し、構成を保存します。

これで、お使いのマシンに RDP 接続できるようになるはずです。

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Azure Load Balancer がトラフィックを均等に分散しないのはなぜですか。
内部ロード バランサーのしくみについては、「[Azure Load Balancer new distribution mode (Azure Load Balancer の新しい分散モード)](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)」を参照してください。

使用される分散アルゴリズムは、使用可能なサーバーにトラフィックをマップする 5 組 (ソース IP、ソース ポート、接続先 IP、接続先ポート、プロトコルの種類) のハッシュです。 これは、トランスポート セッション内でのみ持続性を提供します。 TCP または UDP の同じセッション内のパケットは、負荷分散されたエンドポイントの背後にある同じデータセンターの IP (DIP) インスタンスに送信されます。 クライアントが接続を閉じてからもう一度接続を開くか、同じソース IP から新しいセッションを開始すると、ソース ポートが変更され、トラフィックは別の DIP エンドポイントに送信されます。

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>自分のクラウド サービスの既定の URL への着信トラフィックをカスタム URL にリダイレクトするにはどうすればよいですか。

IIS の URL の書き換えモジュールを使って、クラウド サービスの既定の URL (例: \*.cloudapp.net) への着信トラフィックをカスタムの名前または URL にリダイレクトすることができます。 URL の書き換えモジュールは Web ロール上で既定で有効になっており、そのルールはアプリケーションの web.config に構成されているので、再起動や再イメージ化にかかわりなく、いつでも VM で使用可能です。詳細については、次を参照してください。

- [URL の書き換えモジュールの書き換えルールを作成する](/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [既定のリンクを削除する](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>自分のクラウド サービスの既定の URL への着信トラフィックをブロックまたは無効にするにはどうすればよいですか。

クラウド サービスの既定の URL または名前 (例: \*.cloudapp.net) への着信トラフィックを防ぐことができます。 次に示すように、クラウド サービス定義 (*.csdef) ファイル内のサイト バインド構成でホスト ヘッダーをカスタム DNS 名 (www\.MyCloudService.com など) に設定します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

このホスト ヘッダー バインドは csdef ファイルを介して強制されるため、サービスはカスタム名 "www.MyCloudService.com" を介してのみアクセスできます。 "*.cloudapp.net" ドメインへのすべての着信要求は常に失敗します。 サービス内でカスタム SLB プローブまたは内部ロード バランサーを使用している場合は、サービスの既定の URL や名前をブロックすると、プローブの動作を妨害する可能性があります。

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>クラウド サービスのパブリックに公開された IP アドレスが変更されないようにするにはどうすればよいですか。

対象のクラウド サービスのパブリックに公開された IP アドレス (別名、VIP) が変わらないようにして、いくつかの特定のクライアントでこれを習慣的に許可できるようにするには、予約済み IP を関連付けることをお勧めします。 それ以外の場合、Azure によって提供される仮想 IP は、デプロイを削除するとサブスクリプションから割り当て解除されます。 VIP スワップ操作を成功させるには、運用スロットとステージング スロットの両方について個々の予約済み IP が必要です。 これがないと、スワップ操作は失敗します。 IP アドレスを予約してクラウド サービスに関連付けるには、次の記事を参照してください。

- [既存のクラウド サービスの IP アドレスを予約する](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#reserve-the-ip-address-of-an-existing-cloud-service)
- [サービス構成ファイルを使用してクラウド サービスに予約済み IP を関連付ける](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

お使いのロールに複数のインスタンスがある場合、クラウド サービスに RIP を関連付けることでダウンタイムが発生することはありません。 また、お使いの Azure データセンターの IP 範囲を許可リストに登録することができます。 [Microsoft ダウンロード センター](https://www.microsoft.com/en-us/download/details.aspx?id=41653)で、Azure IP 範囲の一覧を入手できます。

このファイルには、Azure データセンターで使用される IP アドレス範囲 (計算、SQL、およびストレージの範囲を含む) が含まれています。 毎週投稿される最新のファイルには、現在デプロイされている範囲と今後変更される IP 範囲が反映されています。 このファイルに現れる新しい範囲は、少なくとも 1 週間はデータセンターで使用されません。 Azure で実行されているサービスを正しく識別するために、毎週新しい xml ファイルをダウンロードし、サイトで必要な変更を実行してください。 Azure ExpressRoute ユーザーは、このファイルを使用して、毎月第 1 週に Azure 領域の BGP アドバタイズが更新されていることに気付くかもしれません。

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>Azure Resource Manager 仮想ネットワークをクラウド サービスと共に使用するにはどうすればよいですか。

クラウド サービスを Azure Resource Manager 仮想ネットワークに配置することはできません。 Resource Manager 仮想ネットワークと従来のデプロイ仮想ネットワークは、ピアリングによって接続できます。 詳細については、「[仮想ネットワーク ピアリング](../virtual-network/virtual-network-peering-overview.md)」をご覧ください。


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Cloud Services で使用されるパブリック IP アドレスの一覧を取得する方法はありますか。

次の PS スクリプトを使用してお使いのサブスクリプションのクラウド サービスのパブリック IP の一覧を取得します。

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```