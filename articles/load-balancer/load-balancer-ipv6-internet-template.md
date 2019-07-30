---
title: IPv6 でインターネットに接続するロード バランサーのデプロイ - Azure テンプレート
titlesuffix: Azure Load Balancer
description: Azure Load Balancer と負荷分散された VM に IPv6 サポートをデプロイする方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, デュアル スタック, パブリック IP, ネイティブ ipv6, モバイル, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 4286879dc53cc835532c7a8243eaf88813545265
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274996"
---
# <a name="deploy-an-internet-facing-load-balancer-solution-with-ipv6-using-a-template"></a>テンプレートを使用して IPv6 でインターネットに接続するロード バランサー ソリューションをデプロイする

> [!div class="op_single_selector"]
> * [PowerShell](load-balancer-ipv6-internet-ps.md)
> * [Azure CLI](load-balancer-ipv6-internet-cli.md)
> * [テンプレート](load-balancer-ipv6-internet-template.md)



Azure Load Balancer は、第 4 層 (TCP、UDP) のロード バランサーです。 ロード バランサーは、ロード バランサー セット内のクラウド サービスまたは仮想マシンの正常なサービス インスタンスに着信トラフィックを分散することによって高可用性を提供します。 さらに、Azure Load Balancer は、これらのサービスを複数のポート、複数の IP アドレス、またはその両方に提供できます。

## <a name="example-deployment-scenario"></a>デプロイ シナリオの例

次の図は、この記事で説明されているテンプレートの例を使用してデプロイされる負荷分散メソッドを示します。

![ロード バランサーのシナリオ](./media/load-balancer-ipv6-internet-template/lb-ipv6-scenario.png)

このシナリオでは、次の Azure リソースを作成します。

* IPv4 と IPv6 の両方のアドレスが割り当てられている各 VM の仮想ネットワーク インターフェイス
* IPv4 と IPv6 のパブリック IP アドレスでインターネットに接続するロード バランサー
* パブリック VIP をプライベート エンドポイントにマップする 2 つの負荷分散規則
* 2 つの VM が含まれる可用性セット
* 2 つの仮想マシン (VM)

## <a name="deploying-the-template-using-the-azure-portal"></a>Azure Portal を使用したテンプレートのデプロイ

この記事では、[Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/201-load-balancer-ipv6-create/) ギャラリーで公開されているテンプレートを参照します。 ギャラリーからテンプレートをダウンロードするか、ギャラリーから直接 Azure のデプロイメントを起動します。 この記事では、ローカル コンピューターにテンプレートをダウンロードしていることを前提としています。

1. Azure Portal を開き、Azure のサブスクリプション内で VM とネットワーク リソースを作成する権限を持つアカウントでサインインします。 また、既存のリソースを使用している場合を除き、アカウントにはリソース グループとストレージ アカウントを作成する権限が必要です。
2. メニューで [+ 新規] をクリックして検索ボックス「テンプレート」と入力します。 検索結果から [テンプレートのデプロイ] を選択します。

    ![lb-ipv6-portal-step2](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step2.png)

3. [すべて] ブレードで、[テンプレートのデプロイ] をクリックします。

    ![lb-ipv6-portal-step3](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step3.png)

4. [作成] をクリックします。

    ![lb-ipv6-portal-step4](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step4.png)

5. [テンプレートの編集] をクリックします。 既存のコンテンツを削除し、テンプレート ファイルのすべてのコンテンツ (最初の最後の { } を含む) をコピーして貼り付け、[保存] をクリックします。

    > [!NOTE]
    > Microsoft Internet Explorer を使用している場合は、貼り付ける際に Windows クリップボードへのアクセスを許可するように求めるダイアログ ボックスが表示されます。 [許可する] をクリックします。

    ![lb-ipv6-portal-step5](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step5.png)

6. [パラメーターの編集] をクリックします。 [パラメーター] ブレードで、[テンプレート パラメーター] セクションのガイダンスに従って値を指定し、[保存] をクリックして [パラメーター] ブレードを閉じます。 [カスタム デプロイ] ブレードで、サブスクリプションを選択するか、既存のリソース グループを選択または新しいリソース グループを作成します。 リソース グループを作成する場合は、リソース グループの場所を選択します。 次に、 **[法律条項]** をクリックし、法律条項で **[購入]** をクリックします。 Azure でリソースのデプロイを開始します。 すべてのリソースをデプロイするのに数分かかります。

    ![lb-ipv6-portal-step6](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step6.png)

    これらのパラメーターの詳細については、この記事で後述する「 [テンプレート パラメーターと変数](#template-parameters-and-variables) 」を参照してください。

7. テンプレートによって作成されたリソースを表示するには、[参照] をクリックし、一覧を下にスクロールして [リソース グループ] をクリックします。

    ![lb-ipv6-portal-step7](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step7.png)

8. [リソース グループ] ブレードで、手順 6 で指定したリソース グループの名前をクリックします。 デプロイされたすべてのリソースの一覧が表示されます。 すべて正常に行われると、[前回のデプロイ] に [成功] と表示されます。 表示されなかった場合は、必要なリソースを作成する権限がお使いのアカウントにあることを確認してください。

    ![lb-ipv6-portal-step8](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step8.png)

    > [!NOTE]
    > 手順 6 を実行した直後に [リソース グループ] を参照すると、リソースのデプロイ中 [前回のデプロイ] のステータスに [デプロイしています] と表示されます。

9. リソースの一覧で [myIPv6PublicIP] をクリックします。 IP アドレスの下に IPv6 があり、その DNS 名は手順 6 で dnsNameforIPv6LbIP パラメーターに指定した値になります。 このリソースは、インターネット クライアントにアクセス可能なパブリック IPv6 アドレスとホスト名です。

    ![lb-ipv6-portal-step9](./media/load-balancer-ipv6-internet-template/lb-ipv6-portal-step9.png)

## <a name="validate-connectivity"></a>接続の検証

テンプレートが正常にデプロイされると、次のタスクを実行して接続を検証できます。

1. Azure ポータルにサインインし、テンプレート デプロイメントで作成した各 VM に接続します。 Windows Server VM をデプロイした場合は、コマンド プロンプトから ipconfig /all を実行します。 VM に IPv4 と IPv6 の両方のアドレスがあることがわかります。 Linux VM をデプロイした場合は、お使いの Linux ディストリビューションで提供されている手順を使用して、動的な IPv6 のアドレスを受け取るように Linux OS を構成する必要があります。
2. インターネットに接続された IPv6 クライアントから、ロード バランサーのパブリック IPv6 アドレスへの接続を開始します。 ロード バランサーが 2 つの VM 間で負荷を分散していることを確認するには、各 VM に Microsoft インターネット インフォメーション サービス (IIS) などの Web サーバーをインストールします。 各サーバーの既定の Web ページには "Server0" や "Server1" などのテキストが含まれており、一意に識別できます。 次に、インターネットに接続された IPv6 クライアントでインターネット ブラウザーを開き、ロード バランサーの dnsNameforIPv6LbIP パラメーターに指定したホスト名を参照して、IPv6 の各 VM へのエンド ツー エンドの接続を確認します。 1 台のサーバーからの Web ページのみが表示された場合は、ブラウザー キャッシュをクリアする必要がある場合があります。 複数のプライベート ブラウズ セッションを開きます。 各サーバーからの応答が表示されます。
3. インターネットに接続された IPv4 クライアントから、ロード バランサーのパブリック IPv4 アドレスへの接続を開始します。 ロード バランサーが 2 つの VM の負荷を分散していることを確認するには、IIS を使用してテストできます。IIS については手順 2 に詳述されています。
4. 各 VM から、インターネットに接続されている IPv6 デバイスまたは IPv4 デバイスへの送信接続を開始します。 どちらの場合でも、宛先のデバイスで確認される送信元の IP がロード バランサーの IPv4 または IPv6 のパブリック アドレスです。

> [!NOTE]
> Azure ネットワークで IPv4 と IPv6 の両方の ICMP がブロックされます。 結果として、ping などの ICMP ツールは常に失敗します。 接続をテストするには、代わりに TCPing や PowerShell の Test-NetConnection コマンドレットなど、TCP を使用します。 図に表示される IP アドレスは、値の一例であることに注意してください。 IPv6 アドレスは動的に割り当てられるため、実際に受信するアドレスは別のものであり、地域によって異なる場合があります。 また、一般的にロード バランサーのパブリック IPv6 アドレスは、バックエンド プールのプライベート IPv6 アドレスとは別のプレフィックスで始まります。

## <a name="template-parameters-and-variables"></a>テンプレート パラメーターと変数

Azure Resource Manager のテンプレートには複数の変数とパラメーターが指定されており、必要に応じてカスタマイズできます。 変数は、ユーザーによる変更を制限する固定値に使用されます。 パラメーターは、テンプレートをデプロイするときにユーザーが指定する値に使用されます。 テンプレートの例は、この記事で説明するシナリオに合わせて構成されています。 お使いの環境に合わせてカスタマイズしてください。

この記事で使用されるテンプレートの例には、次の変数とパラメーターが指定されています。

| パラメーター/変数 | メモ |
| --- | --- |
| adminUsername |仮想マシンへのサインインに使用される管理者アカウントの名前を指定します。 |
| adminPassword |仮想マシンへのサインインに使用される管理者アカウントのパスワードを指定します。 |
| dnsNameforIPv4LbIP |ロード バランサーのパブリック名として割り当てる DNS ホスト名を指定します。 この名前は、ロード バランサーのパブリック IPv4 アドレスに解決されます。 名前は小文字で、^[a-z][a-z0-9-]{1,61}[a-z0-9]$ の正規表現と一致している必要があります。 |
| dnsNameforIPv6LbIP |ロード バランサーのパブリック名として割り当てる DNS ホスト名を指定します。 この名前は、ロード バランサーのパブリック IPv6 アドレスに解決されます。 名前は小文字で、^[a-z][a-z0-9-]{1,61}[a-z0-9]$ の正規表現と一致している必要があります。 IPv4 アドレスと同じ名前を指定できます。 クライアントがこの名前の DNS クエリを送信すると、名前が共有されている場合、Azure は A と AAAA の両方の記録を返します。 |
| vmNamePrefix |VM 名のプレフィックスを指定します。 テンプレートは VM が作成されるときに、名前の末尾に数字 (0、1 など) を追加します。 |
| nicNamePrefix |ネットワーク インターフェイス名のプレフィックスを指定します。 テンプレートはネットワーク インターフェイスが作成されるときに、名前の末尾に数字 (0、1 など) を追加します。 |
| storageAccountName |既存のストレージ アカウントの名前を入力するか、テンプレートによって作成される新しいストレージ アカウントの名前を指定します。 |
| availabilitySetName |VM で使用される可用性セットの名前を入力します。 |
| addressPrefix |Virtual Network のアドレスの範囲を定義するために使用されるアドレスのプレフィックス |
| subnetName |VNet に対して作成されるサブネットの名前 |
| subnetPrefix |サブネットのアドレスの範囲を定義するために使用されるアドレスのプレフィックス |
| vnetName |VM によって使用される VNet の名前を指定します。 |
| ipv4PrivateIPAddressType |プライベート IP アドレス (静的または動的) に使用される割り当て方法 |
| ipv6PrivateIPAddressType |プライベート IP アドレス (動的) に使用される割り当て方法。 IPv6 は、動的な割り当てのみをサポートします。 |
| numberOfInstances |テンプレートによってデプロイされる負荷分散されたインスタンスの数 |
| ipv4PublicIPAddressName |ロード バランサーのパブリック IPv4 アドレスとの通信に使用する DNS 名を指定します。 |
| ipv4PublicIPAddressType |パブリック IP アドレス (静的または動的) に使用される割り当て方法 |
| Ipv6PublicIPAddressName |ロード バランサーのパブリック IPv6 アドレスとの通信に使用する DNS 名を指定します。 |
| ipv6PublicIPAddressType |パブリック IP アドレス (動的) に使用される割り当て方法。 IPv6 は、動的な割り当てのみをサポートします。 |
| lbName |ロード バランサーの名前を指定します。 この名前はポータルに表示されるか、CLI または PowerShell コマンドを使用して参照するときに使用されます。 |

テンプレート内の残りの変数には、Azure がリソースを作成するときに割り当てられている派生値が含まれます。 これらの変数は変更しないでください。

## <a name="next-steps"></a>次の手順

テンプレート内のロード バランサーの JSON の構文とプロパティについては、「[Microsoft.Network/loadBalancers](/azure/templates/microsoft.network/loadbalancers)」を参照してください。
