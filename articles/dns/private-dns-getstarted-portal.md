---
title: クイックスタート - Azure portal を使用して Azure プライベート DNS ゾーンを作成する
description: このクイックスタートでは、Azure DNS でプライベート DNS ゾーンとレコードを作成してテストします。 Azure portal を使用して最初のプライベート DNS ゾーンとレコードを作成して管理するためのステップ バイ ステップ ガイドです。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: quickstart
ms.date: 10/20/2020
ms.author: rohink
ms.openlocfilehash: d298dfd5f3ad0beb56a511c124bab056ca25fd27
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92310050"
---
# <a name="quickstart-create-an-azure-private-dns-zone-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Azure プライベート DNS ゾーンを作成する

このクイックスタートでは、Azure portal を使用して最初のプライベート DNS ゾーンとレコードを作成する手順について説明します。

DNS ゾーンは、特定のドメインの DNS レコードをホストするために使用されます。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成する必要があります。 ドメインの DNS レコードはすべて、この DNS ゾーン内に作成されます。 仮想ネットワークにプライベート DNS ゾーンを発行するには、そのゾーン内のレコードを解決することが認められた仮想ネットワークの一覧を指定します。  これらを "*リンクされている*" 仮想ネットワーク と呼びます。 また、自動登録を有効にすると、仮想マシンの作成または削除、あるいはその IP アドレスの変更を行うたびに、Azure DNS でそのゾーン レコードも更新されます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

好みに応じて、[Azure PowerShell](private-dns-getstarted-powershell.md) または [Azure CLI](private-dns-getstarted-cli.md) を使用してこのクイックスタートの手順を実行することもできます。

## <a name="create-a-private-dns-zone"></a>プライベート DNS ゾーンの作成

次の例では、**MyAzureResourceGroup** というリソース グループに **private.contoso.com** という DNS ゾーンを作成します。

DNS ゾーンにはドメインの DNS エントリが含まれています。 Azure DNS でドメインのホストを開始するには、そのドメイン名用に DNS ゾーンを作成します。

![プライベート DNS ゾーンの検索](media/private-dns-portal/search-private-dns.png)

1. ポータル検索バーの検索テキスト ボックスに「**プライベート DNS ゾーン**」と入力し、**Enter** キーを押します。
1. **[プライベート DNS ゾーン]** を選択します。
2. **[Create private dns zone]\(プライベート DNS ゾーンの作成\)** を選択します。

1. **[Create Private DNS zone]\(プライベート DNS ゾーンの作成\)** ページで、以下の値を入力または選択します。

   - **[リソース グループ]** : **[新規作成]** を選択し、「*MyAzureResourceGroup*」と入力して、 **[OK]** を選択します。 Azure サブスクリプション内で一意となるリソース グループ名を使用してください。
   -  **Name**:この例では、「*private.contoso.com*」と入力します。
1. **[リソース グループの場所]** には **[米国中西部]** を選択します。

1. **[確認および作成]** を選択します。

1. **［作成］** を選択します

ゾーンの作成には数分かかることがあります。

## <a name="virtual-network-and-parameters"></a>仮想ネットワークとパラメーター

このセクションの手順では、各パラメーターを次のように置き換える必要があります。

| パラメーター                   | Value                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | MyAzureResourceGroup (既存のリソース グループを選択) |
| **\<virtual-network-name>** | MyAzureVNet          |
| **\<region-name>**          | 米国中西部      |
| **\<IPv4-address-space>**   | 10.2.0.0\16          |
| **\<subnet-name>**          | MyAzureSubnet        |
| **\<subnet-address-range>** | 10.2.0.0\24          |


[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="link-the-virtual-network"></a>仮想ネットワークのリンク

プライベート DNS ゾーンを仮想ネットワークにリンクするには、仮想ネットワーク リンクを作成します。

![仮想ネットワークリンクの追加](media/private-dns-portal/dns-add-virtual-network-link.png)

1. **MyAzureResourceGroup** リソース グループを開き、**private.contoso.com** プライベート ゾーンを選択します。
2. 左側のウィンドウで、**[Virtual network links]\(仮想ネットワーク リンク\)** を選択します。
3. **[追加]** を選択します。
4. **[リンク名]** に「**myLink**」と入力します。
5. **[仮想ネットワーク]** で **[myAzureVNet]** を選択します。
6. **[Enable auto registration]\(自動登録を有効にする\)** チェック ボックスをオンにします。
7. **[OK]** を選択します。

## <a name="create-the-test-virtual-machines"></a>テスト用仮想マシンの作成

次に、プライベート DNS ゾーンをテストできるように 2 つの仮想マシンを作成します。

1. ポータル ページの左上で **[リソースの作成]** を選択し、**[Windows Server 2016 Datacenter]** を選択します。
1. リソース グループに **[MyAzureResourceGroup]** を選択します。
1. 仮想マシンの名前には「**myVM01**」と入力します。
1. **[リージョン]** に **[米国中西部]** を選択します。
1. 管理者ユーザーの名前を入力します。
2. パスワードの入力と確認入力を行います。
5. **[パブリック受信ポート]** で **[選択したポートを許可する]** を選択し、**[受信ポートを選択]** で **[RDP (3389)]** を選択します。
10. そのページの他の値は既定のままとし、 **[Next: Disks >]\(次へ: ディスク >\)** をクリックします。
11. **[ディスク]** ページは既定値のままとし、 **[Next: Networking >]\(次へ: ネットワーク >\)** をクリックします。
1. 仮想ネットワークに **[myAzureVNet]** が選択されていることを確認します。
1. そのページの他の値は既定のままとし、 **[Next: Management >]\(次へ: 管理 >\)** をクリックします。
2. **[ブート診断]** に **[オフ]** を選択します。その他は既定値のままとし、**[Review + create]\(確認と作成\)** を選択します。
1. 設定を確認し、 **[作成]** をクリックします。

これらの手順を繰り返して、**myVM02** という名前の別の仮想マシンを作成します。

両方の仮想マシンが完成するまでに数分かかります。

## <a name="create-an-additional-dns-record"></a>追加の DNS レコードの作成

 次の例では、リソース グループ **MyAzureResourceGroup** の DNS ゾーン **private.contoso.com** に、相対名が **db** のレコードを作成します。 レコード セットの完全修飾名は、**db.private.contoso.com** になります。 レコードの種類は "A" で、IP アドレスは **myVM01** です。

1. **MyAzureResourceGroup** リソース グループを開き、**private.contoso.com** プライベート ゾーンを選択します。
2. **[+ レコード セット]** を選択します。
3. **[名前]** に「**db**」と入力します。
4. 表示されている **myVM01** の IP アドレスを **[IP アドレス]** に入力します。 仮想マシンが起動されたときに、これが自動的に登録されることが必要です。
5. **[OK]** を選択します。

## <a name="test-the-private-zone"></a>プライベート ゾーンのテスト

これで、**private.contoso.com** プライベート ゾーンでの名前解決をテストできます。

### <a name="configure-vms-to-allow-inbound-icmp"></a>受信 ICMP を許可するように VM を構成する

名前解決は ping コマンドを使用してテストできます。 そこで、両方の仮想マシン上のファイアウォールを、受信 ICMP パケットを許可するように構成します。

1. myVM01 に接続し、管理者特権で Windows PowerShell ウィンドウを開きます。
2. 次のコマンドを実行します。

   ```powershell
   New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
   ```

MyVM02 についても同じ手順を繰り返します。

### <a name="ping-the-vms-by-name"></a>VM を名前で ping する

1. myVM02 の Windows PowerShell コマンド プロンプトから、自動的に登録されたホスト名を使用して myVM01 を ping します。
   ```
   ping myVM01.private.contoso.com
   ```
   次のような出力が表示されるはずです。
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. 次に、前に作成した **db** 名を ping します。
   ```
   ping db.private.contoso.com
   ```
   次のような出力が表示されるはずです。
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したリソースが不要になったときに削除するには、**MyAzureResourceGroup** リソース グループを削除します。


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure DNS Private Zones のシナリオ](private-dns-scenarios.md)

