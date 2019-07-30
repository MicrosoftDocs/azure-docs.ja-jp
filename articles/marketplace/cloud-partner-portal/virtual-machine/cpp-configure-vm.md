---
title: Azure Marketplace 向けの Microsoft Azure でホストされる VM の構成
description: Azure でホストされている VM をサイズ指定、更新、および一般化する方法について説明します。
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 0637491a1d7799bcaef594123aab53f89690c86f
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654034"
---
# <a name="configure-the-azure-hosted-vm"></a>Azure でホストされる VM を構成する

この記事では、Azure でホストされている仮想マシン (VM) をサイズ指定、更新、および一般化する方法について説明します。  次の手順は、Azure Marketplace からデプロイする VM を準備するために必要です。


## <a name="sizing-the-vhds"></a>VHD のサイズ変更

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
オペレーティング システム (および必要に応じてその他のサービス) が事前に構成されているいずれかの VM を選択した場合、[仮想マシン SKU タブ](./cpp-skus-tab.md)に記載されている標準の Azure VM サイズが既に選択されています。事前構成された OS でソリューションを始めるのが、推奨されるアプローチです。  ただし、OS を手動でインストールする場合は、VM イメージ内のプライマリ VHD を次のようにサイズ変更する必要があります。

- Windows の場合、オペレーティング システム VHD は 127 ～ 128 GB の固定形式の VHD として作成する必要があります。 
- Linux の場合、この VHD は 30 ～ 50 GB の固定形式の VHD として作成する必要があります。

物理サイズが 127 ～ 128 GB 未満の場合、VHD が疎になります。 Windows と SQL Server の基本イメージがこれらの要件を既に満たしている場合は、取得された VHD の形式またはサイズを変更しないでください。 

データ ディスクは 1 TB にすることができます。 それらのサイズを決めるときに、お客様はデプロイメント時にイメージ内の VHD のサイズを変更できないことに注意してください。 データ ディスク VHD は固定形式の VHD として作成する必要があります。 また、疎にする必要もあります。 データ ディスクは最初に空にすることも、データを含むこともできます。


## <a name="install-the-most-current-updates"></a>最新の更新プログラムをインストールする

オペレーティング システム VM の基本イメージには、発行日までの最新の更新プログラムが含まれています。 作成したオペレーティング システム VHD を公開する前に、最新のすべてのセキュリティとメンテナンスの修正プログラムを使用して、OS およびすべてのインストール済みサービスを更新します。

Windows Server 2016 では、**更新プログラムの確認**コマンドを実行します。  それ以外の場合、以前のバージョンの Windows については、[Windows Update から更新プログラムを入手する方法](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update)を参照してください。  Windows Update では、最新の重大かつ重要なセキュリティ更新プログラムが自動的にインストールされます。

Linux ディストリビューションの場合、更新プログラムは通常、コマンド ライン ツールまたはグラフィカル ユーティリティによってダウンロードおよびインストールされます。  たとえば Ubuntu Linux では、OS を更新するための [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) コマンドおよび [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) ツールが提供されています。


## <a name="perform-additional-security-checks"></a>追加のセキュリティ チェックを実行する

Azure Marketplace のソリューション イメージのセキュリティを高いレベルに維持する必要があります。  次の記事では、この目的に役立つセキュリティ構成と手順のチェックリストを示します(「[Azure Marketplace イメージのセキュリティに関する推奨事項](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images)」)。  これらの推奨事項の一部は Linux ベースのイメージに固有ですが、ほとんどの事項はすべての VM イメージに当てはまります。 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>カスタム構成とスケジュールされたタスクを実行する

追加の構成が必要な場合、推奨されるアプローチは、デプロイ後に最終的な変更が VM に反映されるように、起動時に実行されるスケジュールされたタスクを使用することです。  また、次の推奨事項も考慮してください。
- 一度だけ実行されるタスクの場合、タスクが正常に実行された後でタスクがそれ自身を削除するようにすることをお勧めします。
- ドライブ C または D は常設の唯一のドライブであるため、構成はこれらの 2 つ以外のドライブに依存しないようにしてください。 ドライブ C はオペレーティング システム ディスクで、ドライブ D は一時的なローカル ディスクです。

拡張機能の詳細については、「[Linux 用の仮想マシンの拡張機能とその機能](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux)」を参照してください。


## <a name="generalize-the-image"></a>イメージを汎用化する

Azure Marketplace のすべてのイメージは汎用的な方法で再利用できる必要があります。 この再利用性を実現するために、オペレーティング システム VHD は*汎用化*する必要があります。これは、インスタンス固有の識別子とソフトウェア ドライバーを VM からすべて削除する操作です。

### <a name="windows"></a>Windows

Windows OS ディスクは [sysprep ツール](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)を使用して汎用化されます。 OS を後で更新または再構成する場合、sysprep を再実行する必要があります。 

> [!WARNING]
>  更新プログラムは自動的に実行されることがあるため、sysprep を一度実行したら、デプロイされるまで VM をオフにする必要があります。  このシャットダウンにより、VHD の OS またはインストール済みサービスに、インスタンス固有の変更を加える操作を回避できます。

sysprep の実行に関する詳細については、「[VHD を一般化する手順](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)」を参照してください。

### <a name="linux"></a>Linux

2 段階プロセスに従うことで、Linux VM を一般化して別の VM として再デプロイします。  詳細については、[仮想マシンまたは VHD のイメージの作成方法](../../../virtual-machines/linux/capture-image.md)に関するページを参照してください。 

#### <a name="remove-the-azure-linux-agent"></a>Azure Linux エージェントを削除する
1.  SSH クライアントを使って Linux VM に接続します。
2.  SSH のウィンドウで、次のコマンドを入力します。 <br/>
    `sudo waagent -deprovision+user`
3.  `y` を入力して続行します。 (前のコマンドに `-force` パラメーターを追加すると、この確認手順を省略できます。)
4.  コマンドが完了したら、`exit` を入力して SSH クライアントを閉じます。

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>イメージのキャプチャ
1.  Azure portal に移動し、リソース グループ (RG) を選択して VM の割り当てを解除します。
2.  これで VHD が汎用化されたため、この VHD を使用して新しい VM を作成することができます。


## <a name="create-one-or-more-copies"></a>1 つ以上のコピーを作成する

VM のコピーを作成すると、ソリューションのさまざまな構成を提供して、バックアップ、テスト、カスタマイズされたフェールオーバー、負荷分散などを行うことができるため便利です。 非管理クローンを行うために、プライマリ VHD を複製してダウンロードする方法については、次を参照してください。

- Linux VM:[Azure から Linux VHD をダウンロードする](../../../virtual-machines/linux/download-vhd.md)
- Windows VM:[Azure から Windows VHD をダウンロードする](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>次の手順

VM が構成されたら、[仮想ハード ディスクから仮想マシンをデプロイ](./cpp-deploy-vm-vhd.md)できます。
