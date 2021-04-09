---
title: クイック スタート - プライベート クラウドに Azure VMware VM を作成する - Azure VMware Solution by CloudSimple
description: CloudSimple プライベート クラウドに VMware 仮想マシンを作成する方法について説明します。 CloudSimple ポータルには Azure portal からアクセスします。
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c741f01ccc3e34ec991aa77fc850fd9f60c4d8aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899288"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>プライベート クラウドに VMware 仮想マシンを作成する

プライベート クラウドに仮想マシンを作成するには、最初に Azure portal から CloudSimple ポータルにアクセスします。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="access-the-cloudsimple-portal"></a>CloudSimple ポータルにアクセスする

1. **[すべてのサービス]** を選択します。
2. **CloudSimple Services** を検索します。
3. プライベート クラウドを作成する CloudSimple サービスを選択します。
4. **[概要]** ページから、**[Go to the CloudSimple portal]\(CloudSimple ポータルに移動する\)** をクリックして CloudSimple ポータルの新しいブラウザー タブを開きます。  メッセージが表示されたら、Azure サインインの資格情報でサインインします。  

    ![CloudSimple ポータルを起動する](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>vCenter の Web UI を起動する

vCenter を起動して、仮想マシンとポリシーを設定できるようになりました。

vCenter にアクセスするには、CloudSimple ポータルから始めます。 ホーム ページの **[Common Tasks]\(一般タスク\)** で、 **[Launch vSphere Client]\(vSphere クライアントを起動\)** をクリックします。  プライベート クラウドを選択し、プライベート クラウドで **[Launch vSphere Client]\(vSphere クライアントを起動\)** をクリックします。

   ![vSphere Client を起動する](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>ISO または vSphere テンプレートをアップロードする

  > [!WARNING]
  > ISO のアップロードには、vSphere HTML5 クライアントを使用します。  Flash クライアントを使用するとエラーになる場合があります。

1. VM を作成し、それをローカル システムで使用できるようにするための、vCenter にアップロードする ISO または vSphere テンプレートを取得します。
2. vCenter で、**[Disk]\(ディスク\)** アイコンをクリックして、**[vsanDatastore]** を選択します。 **[Files]\(ファイル\)** をクリックし、**[New Folder]\(新しいフォルダー\)** をクリックします。
    ![vCenter ISO](media/vciso00.png)

3. 'ISOs and Templates' という名前のフォルダーを作成します。

4. ISOs and Templates 内の ISOs フォルダーに移動し、**[ファイルのアップロード]** をクリックします。 画面上の指示に従って、ISO をアップロードします。

## <a name="create-a-virtual-machine-in-vcenter"></a>vCenter で仮想マシンを作成する

1. vCenter で、 **[Hosts and Clusters]\(ホストおよびクラスター\)** アイコンをクリックします。

2. **[Workload]\(ワークロード\)** を右クリックし、**[New Virtual Machine]\(新しい仮想マシン\)** を選択します。
    ![[新しい仮想マシン] メニュー オプションが強調表示されているスクリーンショット。](media/vcvm01.png)

3. **[Create new virtual machine]\(新しい仮想マシンの作成\)** を選択し、 **[Next]\(次へ\)** をクリックします。
    ![[新しい仮想マシンの作成] オプションが強調表示されているスクリーンショット。](media/vcvm02.png)

4. マシンに名前を付け、**[Workload VM's] (ワークロード VM)** の場所を選択して **[次へ]** をクリックします。
    ![[ワークロード VM] オプションが強調表示されているスクリーンショット。](media/vcvm03.png)

5. **[Workload]\(ワークロード\)** コンピューティング リソースを選択し、**[Next]\(次へ\)** をクリックします。
    ![[ワークロード] コンピューティング リソースが強調表示されているスクリーンショット。](media/vcvm04.png)

6. **[vsanDatastore]** を選択して、**[Next]\(次へ\)** をクリックします。
    ![[vsanDatastore] オプションが強調表示されているスクリーンショット。](media/vcvm05.png)

7. 既定の ESXi 6.5 互換性を選択したままにして、 **[Next]\(次へ\)** をクリックします。
    ![選択された ESXi 6.5 互換性オプションを示しているスクリーンショット。](media/vcvm06.png)

8. 作成している VM の ISO のゲスト OS を選択し、 **[次へ]** をクリックします。
    ![VM の ISO の ゲスト OS を選択する方法を示しているスクリーンショット。](media/vcvm07.png)

9. ハード ディスクとネットワークのオプションを選択します。 [New CD/DVD Drive]\(新しい CD または DVD ドライブ\) で、**[Datastore ISO file]\(データストアの ISO ファイル\)** を選択します。  パブリック IP アドレスからこの VM へのトラフィックを許可する場合は、ネットワークを **[vm-1]** として選択します。
    ![Datastore ISO ファイルを選択する場所が強調表示されているスクリーンショット。](media/vcvm08.png)

10. 選択ウィンドウが開きます。 ISOs and Templates フォルダーに以前アップロードしたファイルを選択し、 **[OK]** をクリックします。
    ![新しい VM](media/vcvm10.png)

11. 設定を確認し、**[OK]** をクリックして VM を作成します。
    ![設定の確認場所を示しているスクリーンショット。](media/vcvm11.png)

これで VM が Workload コンピューティング リソースに追加され、使用する準備ができました。 
![ワークロード コンピューティング リソースに追加されている VM を示しているスクリーンショット。](media/vcvm12.png)

これで、基本的なセットアップが完了しました。 オンプレミスの VM インフラストラクチャを使用する場合と同様の方法で、プライベート クラウドの使用を開始できます。

以降のセクションには、プライベート クラウド ワークロードのための DNS および DHCP サーバーのセットアップや、既定のネットワーク構成の変更に関するオプション情報が含まれています。

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>vCenter にユーザーと ID ソースを追加する (省略可能)

CloudSimple は、既定の vCenter ユーザー アカウントにユーザー名 `cloudowner@cloudsimple.local` を割り当てます。 開始するために、追加のアカウントを設定する必要はありません。  CloudSimple は通常、管理者に、通常の操作を実行するために必要な特権を割り当てます。  オンプレミスの Active Directory または Azure AD を、プライベート クラウド上の[追加の ID ソース](set-vcenter-identity.md)としてセットアップします。

## <a name="create-a-dns-and-dhcp-server-optional"></a>DNS および DHCP サーバーを作成する (省略可能)

プライベート クラウド環境で実行されるアプリケーションとワークロードには、ルックアップと IP アドレスの割り当てのための名前解決と DHCP サービスが必要です。 これらのサービスを提供する、適切な DHCP と DNS インフラストラクチャが必要です。 vCenter で仮想マシンを構成して、プライベート クラウド環境でこれらのサービスを提供することができます。

前提条件

* VLAN が構成された分散ポート グループ

* オンプレミスまたはインターネット ベースの DNS サーバーへのルート設定

* 仮想マシンを作成するための仮想マシン テンプレートまたは ISO

次のリンクは、Linux と Windows での DHCP と DNS サーバーの設定に関するガイダンスを提供します。

#### <a name="linux-based-dns-server-setup"></a>Linux ベースの DNS サーバーの設定

Linux では、DNS サーバーを設定するためのさまざまなパッケージが提供されています。  オープン ソースの BIND DNS サーバーを設定するための手順へのリンクを次に示します。

[セットアップの例](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>Windows ベースのセットアップ

これらの Microsoft のトピックでは、Windows サーバーを DNS サーバーおよび DHCP サーバーとして設定する方法について説明しています。

[DNS サーバーとしての Windows Server](/windows-server/networking/dns/dns-top)

[DHCP サーバーとしての Windows Server](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>ネットワーク構成をカスタマイズする (省略可能)

CloudSimple ポータルの [ネットワーク] ページを使用すると、VM のファイアウォール テーブルやパブリック IP アドレスの構成を指定できます。

### <a name="allocate-public-ips"></a>パブリック IP を割り当てる

1. CloudSimple ポータルで、**[Network]\(ネットワーク\) > [Public IP]\(パブリック IP\)** に移動します。
2. **[Allocate Public IP]\(パブリック IP を割り当てる\)** をクリックします。
3. IP アドレス エントリを識別する名前を入力します。
4. 既定の場所はそのままにします。
5. 必要な場合は、スライダーを使用してアイドル タイムアウトを変更します。
6. パブリック IP アドレスを割り当てる対象となるローカル IP アドレスを入力します。
7. 必要な場合は、関連付けられた DNS 名を入力します。
8. **[Done]** をクリックします。

    ![パブリック IP](media/quick-create-pc-public-ip.png)

パブリック IP アドレスの割り当てのタスクが開始します。 タスクの状態を **[Activity]\(アクティビティ\) > [Tasks]\(タスク\)** ページで確認できます。 割り当てが完了したら、新しいエントリがパブリック IP ページに表示されます。

この IP アドレスをマップする必要がある VM を、上で指定されたローカル アドレスを使用して構成する必要があります。 IP アドレスを構成する手順は、VM オペレーティング システムに固有です。 正しい手順については、VM オペレーティング システムのドキュメントを参照してください。

#### <a name="example"></a>例

たとえば、Ubuntu 16.04 の場合の詳細を次に示します。

ファイル /etc/network/interfaces 内の inet アドレス ファミリ構成に静的メソッドを追加します。 アドレス、ネットマスク、およびゲートウェイの値を変更します。 この例では、eth0 インターフェイス、内部 IP アドレス 192.168.24.10、ゲートウェイ アドレス 192.168.24.1、およびネットマスク 255.255.255.0 を使用します。 使用している環境については、使用可能なサブネット情報がウェルカム メールで提供されます。

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
インターフェイスを手動で無効にします。

```
sudo ifdown eth0
```
もう一度インターフェイスを手動で有効にします。

```
sudo ifup eth0
```

既定では、インターネットからのすべての受信トラフィックは **拒否** されます。 その他の任意のポートを開く場合は、[ファイアウォール テーブル](firewall.md)を作成します。

内部 IP アドレスを静的 IP アドレスとして構成した後、VM 内からインターネットに到達できることを確認します。

```
ping 8.8.8.8
```
また、パブリック IP アドレスを使用して、インターネットから VM に到達できることも確認します。

VM 上のどの iptable 規則もポート 80 の受信をブロックしていないことを確認します。
        
```
netstat -an | grep 80
```

ポート 80 でリッスンする http サーバーを起動します。
       
```
python2.7 -m SimpleHTTPServer 80
```

または

```
python3 -m http.server 80
```
デスクトップ上でブラウザーを起動し、パブリック IP アドレスのポート 80 を指定して VM 上のファイルを参照します。

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>パブリック IP のための既定の CloudSimple ファイアウォール規則

* VPN トラフィック:VPN とすべてのワークロード ネットワークおよび管理ネットワークの間のすべてのトラフィックが許可されます。
* プライベート クラウド内部トラフィック:(上記の) ワークロード ネットワークおよび管理ネットワークの間のすべての East-West トラフィックが許可されます。
* インターネット トラフィック:
  * ワークロード ネットワークおよび管理ネットワークに対するインターネットからのすべての受信トラフィックは拒否されます。
  * ワークロード ネットワークまたは管理ネットワークからインターネットへのすべての発信トラフィックは許可されます。

ファイアウォール規則の機能を使用して、トラフィックをセキュリティ保護する方法を変更することもできます。 詳細については、[ファイアウォールのテーブルと規則のセットアップ](firewall.md)に関するページを参照してください。

## <a name="install-solutions-optional"></a>ソリューションをインストールする (省略可能)

CloudSimple プライベート クラウドにソリューションをインストールして、プライベート クラウド vCenter 環境を最大限に活用することができます。 仮想マシンを保護するために、バックアップ、ディザスター リカバリー、レプリケーション、およびその他の機能をセットアップすることができます。 例として、VMware Site Recovery Manager (VMware SRM) や Veeam Backup & Replication などがあります。

ソリューションをインストールするには、一定期間の追加の特権を要求する必要があります。 [権限をエスカレートする](escalate-private-cloud-privileges.md)ことに関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure での VMware VM の使用](quickstart-create-vmware-virtual-machine.md)
* [Azure ExpressRoute を使用してオンプレミスのネットワークに接続する](on-premises-connection.md)
* [CloudSimple ネットワーク上の VPN ゲートウェイを設定する](vpn-gateway.md)
