---
title: "Microsoft Azure SUSE Linux VM での SAP NetWeaver のテスト | Microsoft Docs"
description: "Microsoft Azure SUSE Linux VM での SAP NetWeaver のテスト"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2017
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: 046f58eba906980f23ce1177794b28930e3314a1
ms.openlocfilehash: e7255c4123849f8f3fb29767308b433b5c0e511b


---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Microsoft Azure SUSE Linux VM での SAP NetWeaver の実行
この記事では、Microsoft Azure SUSE Linux 仮想マシン (VM) で SAP NetWeaver を実行する際のさまざまな考慮事項について説明します。 2016 年 5 月 19 日 の時点で、SAP NetWeaver は Azure 上の SUSE Linux VM で正式にサポートされています。 Linux のバージョンや SAP カーネルのバージョンなど、すべての詳細については、SAP Note 1928533「SAP Applications on Azure: Supported Products and Azure VM types (Azure 上の SAP アプリケーション: サポートされる製品と Azure VM の種類)」を参照してください。
Linux VM における SAP に関するその他のドキュメントについては、「[Linux 仮想マシン (VM) における SAP の使用](virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

以下の情報は、失敗の可能性を回避するために役立ちます。

## <a name="suse-images-on-azure-for-running-sap"></a>Azure で SAP を実行するための SUSE イメージ
Azure で SAP NetWeaver を実行するには、SUSE Linux Enterprise Server SLES 12 (SPx) のみを使用してください。SAP Note 1928533 も参照してください。 Azure Marketplace には、特殊な SUSE イメージ ("SLES 11 SP3 for SAP CAL") がありますが、これは一般的な使用を目的としたものではありません。 このイメージは [SAP Cloud Appliance Library](https://cal.sap.com/) ソリューション用に予約されているため、使用しないでください。  

Azure でのすべての新しいテストとインストールには、Azure Resource Manager を使用する必要があります。 Azure PowerShell または Azure コマンドライン インターフェイス (CLI) を使用して SUSE SLES イメージおよびバージョンを検索するには、次のコマンドを使用します。 この出力は、新しい SUSE Linux VM をデプロイするために JSON テンプレートで OS イメージを定義することなどに使用できます。
以下の PowerShell コマンドは、バージョン 1.0.1 以降の Azure PowerShell で有効です。

SAP インストールで標準 SLES イメージを使用することは可能ですが、現在 Azure イメージ ギャラリーで入手できる新しい SLES for SAP イメージの使用をお勧めします。 これらのイメージの詳細については、対応する [Azure Marketplace に関するページ]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP )または [SUSE の SLES for SAP に関するよく寄せられる質問のページ]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ )をご覧ください。


* SUSE を含む既存の発行元を検索:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* SUSE からの既存の提供を検索:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* SUSE SLES の提供を検索:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* SLES SKU の特定のバージョンを検索:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>SUSE VM への WALinuxAgent のインストール
WALinuxAgent というエージェントは、Azure Marketplace の SLES イメージの一部です。 手動インストール (オンプレミスから SLES OS 仮想ハード ディスク (VHD) をアップロードする場合など) に関する情報については、以下のリンク先を参照してください。

* [OpenSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP の "拡張された監視機能"
SAP の "拡張された監視機能" は、Azure で SAP を実行するうえで必須の前提条件です。 詳細については、SAP Note 2191498「SAP on Linux with Azure: Enhanced Monitoring (Azure を使用した Linux 上の SAP: 拡張された監視機能)」を確認してください。

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Azure Linux VM への Azure データ ディスクの接続
デバイス ID を使用して Azure データ ディスクを Azure Linux VM にマウントしないでください。 代わりに、汎用一意識別子 (UUID) を使用します。 たとえば、グラフィカル ツールを使用して Azure データ ディスクをマウントする場合には注意してください。 /etc/fstab 内のエントリを再確認してください。

デバイス ID の問題点は、変更される可能性があり、Azure VM が起動プロセスで停止する場合があることです。 問題を軽減するために、/etc/fstab に nofail パラメーターを追加できます。 ただし、nofail の使用には注意してください。起動時に外付け Azure データ ディスクがマウントされなかった場合、アプリケーションは従来どおりマウント ポイントを使用し、ルート ファイル システムに書き込む可能性があります。

UUID によるマウントに関する唯一の例外として、次のセクションで説明するように、トラブルシューティングの目的で OS ディスクを接続する場合があります。

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>アクセスできなくなった SUSE VM のトラブルシューティング
起動プロセス中に Azure 上の SUSE VM が停止する状況が発生することがあります (ディスクのマウントに関連するエラーなど)。 この問題は、Azure ポータルの Azure Virtual Machines v2 の起動診断機能を使用して検証できます。 詳細については、 [起動診断](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)に関するページをご覧ください。

問題を解決するための&1; つの方法として、破損している VM から Azure 上の別の SUSE VM に OS ディスクを接続します。 その後、次のセクションで説明するように、/etc/fstab の編集やネットワーク udev 規則の削除など、適切な変更を加えます。

ここで、1 つの重要な考慮事項があります。 同じ Azure Marketplace イメージ (SLES 11 SP4 など) から複数の SUSE VM をデプロイすると、OS ディスクは常に同じ UUID でマウントされます。 したがって、その UUID を使用して、同じ Azure Marketplace イメージを使用してデプロイされた別の VM から OS ディスクを接続すると、同じ UUID が&2; つ存在することになります。 これによって、問題が発生します。つまり、トラブルシューティング用の VM は、実際には、元のディスクではなく、接続された破損している OS ディスクから起動します。

この回避方法は次の&2; とおりあります。

* トラブルシューティング用の VM に別の Azure Marketplace イメージを使用する (SLES 12 の代わりに SLES 11 SPx など)。
* UUID を使用して別の VM から破損した OS ディスクを接続せずに、他のものを使用する。

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>オンプレミスから Azure への SUSE VM のアップロード
SUSE VM をオンプレミスから Azure にアップロードする手順については、「[Azure 用の SLES または openSUSE 仮想マシンの準備](virtual-machines-linux-suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

最後にプロビジョニング解除手順を使用せずに VM をアップロードする場合 (たとえば、既存の SAP インストールやホスト名を保持する場合) は、次の項目を確認してください。

* デバイス ID ではなく UUID を使用して OS ディスクがマウントされていることを確認します。 OS ディスクの場合、/etc/fstab で UUID に変更するだけでは十分ではありません。 YaST を経由したり /boot/grub/menu.lst を編集したりすることによるブート ローダーの調整も忘れずに行ってください。
* SUSE OS ディスクに VHDX 形式を使用し、Azure にアップロードするために VHD に変換する場合、ネットワーク デバイスが eth0 から eth1 に変更される可能性が非常に高くなります。 後で Azure で起動する際に問題を回避するには、「 [Fixing eth0 in cloned SLES 11 VMware (複製された SLES 11 VMware で eth0 を修復する)](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/)」に説明されているように、eth0 に戻してください。

この記事に記載されているものに加え、以下も削除することをお勧めします。

   /lib/udev/rules.d/75-persistent-net-generator.rules

複数の NIC がある場合を除き、Azure Linux エージェント (waagent) をインストールすると、潜在的な問題を回避するために役立ちます。

## <a name="deploying-a-suse-vm-on-azure"></a>Azure への SUSE VM のデプロイ
新しい Azure Resource Manager モデルで JSON テンプレート ファイルを使用して、新しい SUSE VM を作成する必要があります。 JSON テンプレート ファイルが作成されると、PowerShell の代わりに次の CLI コマンドを使用して VM をデプロイできます。

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
JSON テンプレート ファイルの詳細については、「[Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」および「[Azure クイック スタート テンプレート](https://azure.microsoft.com/documentation/templates/)」を参照してください。

CLI と Azure Resource Manager の詳細については、「[Azure Resource Manager での、Mac、Linux、および Windows 用 Azure CLI の使用](../xplat-cli-azure-resource-manager.md)」を参照してください。

## <a name="sap-license-and-hardware-key"></a>SAP のライセンスとハードウェア キー
正式な SAP-Azure 証明書のために、SAP ライセンスに使用される SAP ハードウェア キーを計算するための新しいメカニズムが導入されました。 SAP カーネルはこれを利用するように適合させる必要がありました。 Linux 向けの SAP カーネルの以前のバージョンには、このコード変更が含まれていませんでした。 そのため、特定の状況 (Azure VM のサイズ変更など) では、SAP ハードウェア キーが変更され、SAP のライセンスが無効になりました。 これは、最新の SAP Linux のカーネルで解決されています。 詳細については、SAP Note 1928533 を確認してください。

## <a name="suse-sapconf-package--tuned-adm"></a>SUSE の sapconf パッケージ / tuned-adm
SUSE には、一連の SAP 固有の設定を管理する、"sapconf" と呼ばれるパッケージが用意されています。 このパッケージの実行内容、インストール方法、使用方法の詳細については、「[Using sapconf to prepare a SUSE Linux Enterprise Server to run SAP systems (SAP システムを実行するために sapconf を使用して SUSE Linux Enterprise Server を準備する)](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/)」および「[What is sapconf or how to prepare a SUSE Linux Enterprise Server for running SAP systems? (sapconf の説明と SAP システムの実行用に SUSE Linux Enterprise Server を準備する方法)](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems)」を参照してください。

また、sapconf に代わる新しいツール tuned-adm もあります。 このツールの詳細については、次の&2; つのリンク先をご覧ください。

[tuned-adm プロファイルの sap-hana に関する SLES ドキュメント](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

[Tuning Systems for SAP Workloads with tuned-adm (tuned-adm を使用した SAP ワークロードのチューニング システム)](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) (第 6.2 章)

## <a name="nfs-share-in-distributed-sap-installations"></a>分散 SAP インストールでの NFS 共有
たとえばデータベースと SAP アプリケーション サーバーを別々の VM にインストールする分散インストールの場合、ネットワーク ファイル システム (NFS) を利用して /sapmnt ディレクトリを共有できます。 /sapmnt の NFS 共有を作成した後にインストール手順で問題が発生した場合は、共有に "no_root_squash" が設定されているかどうかを確認します。

## <a name="logical-volumes"></a>論理ボリューム
従来、複数の Azure データ ディスクで大容量の論理ボリュームが必要になった場合は (SAP データベース用など)、lvm の一部が Azure で検証されていなかったため、mdadm を使用することが推奨されていました。 mdadm を使用して Azure で Linux RAID を設定する方法については、「[Linux でのソフトウェア RAID の構成](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。 一方、2016 年 5 月初めの時点で、lvm も Azure で完全にサポートされ、mdadm の代わりに使用できます。 Azure における lvm に関するその他の情報については、「[Azure で Linux VM の LVM を構成する](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="azure-suse-repository"></a>Azure SUSE リポジトリ
標準の Azure SUSE リポジトリへのアクセスに問題がある場合は、単純なコマンドを使用してリセットすることができます。 この問題は、プライベート OS イメージを Azure リージョンに作成した後、そのイメージを別のリージョン (このプライベート OS イメージに基づいて新しい VM をデプロイするリージョン) にコピーした場合に発生する可能性があります。 VM 内で次のコマンドを実行します。

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome デスクトップ
Gnome デスクトップを使用して、1 つの VM 内に SAP GUI、ブラウザー、SAP 管理コンソールなどの完全な SAP デモ システムをインストールする場合、Azure SLES イメージにインストールするためのヒントを次に示します。

   SLES 11 の場合:

   ```
   zypper in -t pattern gnome
   ```

   SLES 12 の場合:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>クラウド内の Linux での SAP による Oracle のサポート
仮想化環境では、Linux 上の Oracle にはサポートの制約があります。 これは Azure 固有のトピックではありませんが、理解しておくことが重要です。 Azure のようなパブリック クラウドでは、SAP は SUSE または Red Hat 上の Oracle をサポートしません。 このトピックについては、直接 Oracle にお問い合わせください。




<!--HONumber=Feb17_HO2-->


