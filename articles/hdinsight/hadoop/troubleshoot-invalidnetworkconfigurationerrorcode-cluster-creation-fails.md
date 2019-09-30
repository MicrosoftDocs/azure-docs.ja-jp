---
title: Azure HDInsight でのクラスターの作成からの InvalidNetworkConfigurationErrorCode
description: Azure HDInsight で InvalidNetworkConfigurationErrorCode を使用したクラスターの作成が失敗するさまざまな理由
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: a6b207086325018deb63383a0775af8dfe195ac4
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091714"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Azure HDInsight で InvalidNetworkConfigurationErrorCode を使用したクラスターの作成が失敗する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

"Virtual Network configuration is not compatible with HDInsight Requirement" (仮想ネットワーク構成が HDInsight の要件と互換性がありません) という説明のエラー コード `InvalidNetworkConfigurationErrorCode` が表示される場合は、通常、クラスターの[仮想ネットワーク構成](../hdinsight-plan-virtual-network-deployment.md)に問題があることを示しています。 エラーの残りの説明に基づき、以下のセクションに従って問題を解決します。

## <a name="hostname-resolution-failed"></a>"ホスト名の解決に失敗しました"

### <a name="issue"></a>問題

エラーの説明に "ホスト名の解決に失敗しました" が含まれています。

### <a name="cause"></a>原因

このエラーは、カスタムの DNS 構成に問題があることを示しています。 仮想ネットワーク内の DNS サーバーでは、Azure の再帰リゾルバーに DNS クエリを転送してその仮想ネットワーク内のホスト名を解決することができます (詳細については、[仮想ネットワークの名前解決](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)に関するページを参照してください)。 Azure の再帰的競合回避モジュールへのアクセスは、仮想 IP 168.63.129.16 を通じて提供されます。 この IP には、Azure VM からのみアクセスできます。 そのため、オンプレミス DNS サーバーを使用している場合や、DNS サーバーが Azure VM であり、それがクラスターの vNet に属していない場合は機能しません。

### <a name="resolution"></a>解決策

1. クラスターの一部である VM に SSH で接続し、コマンド `hostname -f` を実行します。 これにより、ホストの完全修飾ドメイン名が返されます (以下の手順では `<host_fqdn>` と呼ばれます)。

1. 次に、コマンド `nslookup <host_fqdn>` を実行します (たとえば `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`)。 このコマンドによって名前が IP アドレスに解決される場合は、DNS サーバーが正常に動作していることを意味します。 この場合は、HDInsight でサポート ケースを送信してください。Microsoft がお客様の問題を調査します。 サポート ケースには、実行したトラブルシューティングの手順が含めてください。 こうすることで、より迅速に問題を解決できます。

1. 上記のコマンドで IP アドレスが返されない場合は、`nslookup <host_fqdn> 168.63.129.16` を実行します(たとえば `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`)。 このコマンドで IP を解決できる場合は、DNS サーバーからクエリが Azure の DNS に転送されていないか、クラスターと同じ vNet に属する VM ではないことを意味します。

1. クラスターの vNet でカスタム DNS サーバーとして機能できる Azure VM がない場合は、まずそれを追加する必要があります。 vNet に VM を作成します。これは、DNS フォワーダーとして構成されます。

1. vNet に VM をデプロイしたら、この VM 上で DNS 転送規則を構成します。 すべての iDNS 名前解決要求を 168.63.129.16 に転送し、その他の要求を DNS サーバーに転送します。 このカスタム DNS サーバーの設定例については、[こちら](../hdinsight-plan-virtual-network-deployment.md)を参照してください。

1. この VM の IP アドレスを仮想ネットワーク DNS 構成の最初の DNS エントリとして追加します。

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Failed to connect to Azure Storage Account" (Azure Storage アカウントに接続できませんでした)

### <a name="issue"></a>問題

エラーの説明に "Failed to connect to Azure Storage Account" (Azure Storage アカウントに接続できませんでした) または "Failed to connect to Azure SQL" (Azure SQL に接続できませんでした) が含まれています

### <a name="cause"></a>原因

Azure Storage と SQL には固定の IP アドレスがないため、これらのサービスにアクセスできるようにするには、すべての IP への発信接続を許可する必要があります。 正確な解決手順は、ネットワーク セキュリティ グループ (NSG) またはユーザー定義規則 (UDR) を設定しているかどうかによって異なります。 これらの構成の詳細については、[HDInsight でのネットワーク セキュリティ グループとユーザー定義ルートを使用したネットワーク トラフィックの制御](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip)に関するページを参照してください。

### <a name="resolution"></a>解決策

* クラスターに[ネットワーク セキュリティ グループ (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) を使用している場合。

    Azure portal に移動し、クラスターがデプロイされているサブネットに関連付けられている NSG を特定します。 **[送信セキュリティ規則]** セクションで、制限なしでインターネットへの発信アクセスを許可します (ここでは、**優先順位**の数値が小さいほど優先順位が高くなることに注意してください)。 また、**subnets** セクションで、この NSG がクラスター サブネットに適用されているかどうかを確認します。

* クラスターで[ユーザー定義ルート (UDR)](../../virtual-network/virtual-networks-udr-overview.md) が使用されている場合。

    Azure portal に移動し、クラスターがデプロイされているサブネットに関連付けられているルート テーブルを特定します。 サブネットのルート テーブルが見つかったら、その中の **routes** セクションを調べます。

    定義されているルートがある場合は、クラスターがデプロイされたリージョンの IP アドレスのルートが存在し、各ルートの **NextHopType** が **Internet** であることを確認します。 前述の記事に記載されている必要な IP アドレスごとに、ルートが定義されている必要があります。

---

### <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

* [Azure コミュニティのサポート](https://azure.microsoft.com/support/community/)を通じて Azure エキスパートから回答を得る。

* [@AzureSupport](https://twitter.com/azuresupport) (Azure コミュニティを適切なリソース (回答、サポート、専門家) につなぐことで、カスタマー エクスペリエンスを向上させる Microsoft Azure の公式アカウント) に問い合わせる。

* さらにヘルプが必要な場合は、[Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) からサポート リクエストを送信できます。 メニュー バーから **[サポート]** を選択するか、 **[ヘルプとサポート]** ハブを開いてください。 詳細については、「[Azure サポート要求を作成する方法](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)」をご覧ください。 サブスクリプション管理と課金サポートへのアクセスは、Microsoft Azure サブスクリプションに含まれていますが、テクニカル サポートはいずれかの [Azure のサポート プラン](https://azure.microsoft.com/support/plans/)を通して提供されます。
