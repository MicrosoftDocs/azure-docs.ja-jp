---
title: Azure でネットワーク仮想アプライアンスの問題をトラブルシューティングする | Microsoft Docs
description: Azure でのネットワーク仮想アプライアンス (NVA) に関する問題のトラブルシューティングを行い、NVA 構成のため基本的な Azure Platform の要件を検証します。
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: fe4c17b74cd786d03bd19257dea190a21ecaa9f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99095643"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Azure でのネットワーク仮想アプライアンスの問題

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Microsoft Azure でサードパーティのネットワーク仮想アプライアンス (NVA) を使用しているときに、VM または VPN の接続の問題が発生する可能性があります。 この記事では、NVA 構成に対する基本の Azure Platform の要件を確認するのに役立つ基本的な手順を示します。

サードパーティの NVA および Azure プラットフォームとの統合向けのテクニカル サポートは、NVA のベンダーによって提供されます。

> [!NOTE]
> NVA に関する接続性やルーティングの問題がある場合は、直接 [NVA のベンダーにお問い合わせ](https://mskb.pkisolutions.com/kb/2984655)いただく必要があります。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>NVA のベンダーと共にトラブルシューティングするためのチェックリスト

- NVA の VM ソフトウェア用のソフトウェア更新プログラム
- サービス アカウントのセットアップと機能
- NVA に直接通信する仮想ネットワークのサブネット上のユーザー定義のルート (UDR)
- NVA から直接通信する仮想ネットワークのサブネット上の UDR
- NVA 内のテーブルとルールのルーティング (例: NIC1 から NIC2)
- ネットワーク トラフィックの送受信を確認するための NVA NIC のトレース
- Standard SKU と Public IP を使用するときは、NSG が作成されていて、NVA へのトラフィックのルーティングを許可する明示的なルールが存在する必要があります。

## <a name="basic-troubleshooting-steps"></a>基本的なトラブルシューティングの手順

- 基本構成の確認
- NVA のパフォーマンスの確認
- 高度なネットワークのトラブルシューティング

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Azure 上の NVA に対する最小構成要件の確認

各 NVA には Azure 上で正常に機能するための基本構成要件があります。 次のセクションでは、これらの基本構成を確認するための手順を示します。 詳細については、[NVA のベンダーにお問い合わせください](https://mskb.pkisolutions.com/kb/2984655)。

**NVA 上で IP 転送が有効かどうかを確認する**

Azure Portal の使用

1. [Azure portal](https://portal.azure.com) で NVA リソースを検索し、ネットワークを選択して、ネットワーク インターフェイスを選択します。
2. [ネットワーク インターフェイス] ページで、[IP 構成] を選択します。
3. IP 転送が有効になっていることを確認します。

PowerShell の使用

1. PowerShell を開き、ご自分の Azure アカウントにサインインします。
2. 次のコマンドを実行します (かっこで囲まれた値をご自分の情報に置き換えます)。

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. **EnableIPForwarding** プロパティを確認します。
4. IP 転送が有効になっていない場合は、次のコマンドを実行して有効にします。

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Standard SKU と Pubilc IP の使用時に NVA を確認する** Standard SKU と Public IP の使用時には、NSG が作成済みで、明示的なルールでトラフィックの NVA へのルーティングが許可されている必要があります。

**トラフィックを NVA にルーティングできるかどうかを確認する**

1. [Azure portal](https://portal.azure.com) で **Network Watcher** を開いて、 **[次ホップ]** を選択します。
2. トラフィックを NVA にリダイレクトするように構成された VM と、次ホップを表示する宛先 IP アドレスを指定します。 
3. NVA が **[次ホップ]** として一覧表示されていない場合は、Azure ルート テーブルを確認して更新します。

**トラフィックで NVA に到達できるかどうかを確認する**

1. [Azure portal](https://portal.azure.com) で **Network Watcher** を開いて、 **[IP フローの確認]** を選択します。 
2. VM および NVA の IP アドレスを指定して、トラフィックが任意のネットワーク セキュリティ グループ (NSG) によってブロックされるかどうかを確認します。
3. トラフィックをブロックする NSG ルールがある場合は、**有効なセキュリティ** 規則で NSG を検索して、トラフィックで通過できるように更新します。 次に、もう一度 **[IP フローの確認]** を実行して **[接続のトラブルシューティング]** を使用し、VM からご利用の内部または外部の IP アドレスへの TCP 通信をテストします。

**NVA および VM が予想されるトラフィックをリッスンしているかどうかを確認する**

1. RDP または SSH を使用して NVA に接続して、次のコマンドを実行します。

    Windows の場合:

    ```console
   netstat -an
    ```

    Linux の場合:

    ```console
   netstat -an | grep -i listen
    ```
2. 結果に一覧されている NVA ソフトウェアによって使用される TCP ポートが表示されない場合は、それらのポートに到達するトラフィックをリッスンして応答するように、NVA と VM 上にアプリケーションを構成する必要があります。 [必要に応じて、NVA のベンダーにお問い合わせください](https://mskb.pkisolutions.com/kb/2984655)。

## <a name="check-nva-performance"></a>NVA のパフォーマンスの確認

### <a name="validate-vm-cpu"></a>VM CPU を検証する

CPU 使用率が 100 パーセントに近い場合は、影響するネットワーク パケットが低下する問題が発生する可能性があります。 ご利用の VM では、Azure portal で特定の期間の平均 CPU が報告されます。 CPU のスパイク時に、高い CPU の原因となっているゲスト VM 上のプロセスを調査して、可能であれば、そのプロセスを軽減させます。 また、より大きい SKU サイズに VM のサイズを変更するか、仮想マシン スケール セットの場合は、インスタンス数を増やしたり、CPU 使用率に自動スケールを設定したりする必要がある場合もあります。 これらの問題のいずれかについては、必要に応じて、[NVA のベンダーにお問い合わせください](https://mskb.pkisolutions.com/kb/2984655)。

### <a name="validate-vm-network-statistics"></a>VM ネットワークの統計を検証する

VM ネットワークでスパイクを使用したり、高い使用率の期間を示したりする場合は、スループット機能をさらに高めるために、VM の SKU サイズを増加させる必要もある可能性があります。 高速ネットワークを有効にすることで、VM を再デプロイすることもできます。 NVA で高速ネットワーク機能がサポートされているかどうかを確認するには、必要に応じて、[NVA のベンダーにお問い合わせください](https://mskb.pkisolutions.com/kb/2984655)。

## <a name="advanced-network-administrator-troubleshooting"></a>高度なネットワーク管理者のトラブルシューティング

### <a name="capture-network-trace"></a>ネットワーク トレースのキャプチャ
**[PsPing](/sysinternals/downloads/psping)** または **Nmap** を実行しながら、ソース VM、NVA、宛先の VM 上で同時ネットワーク追跡をキャプチャしてから、追跡を停止します。

1. 同時ネットワーク追跡をキャプチャするには、次のコマンドを実行します。

   **Windows の場合**

   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

   **Linux の場合**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. ソース VM から宛先 VM に対して **PsPing** または **Nmap** を使用します (例: `PsPing 10.0.0.4:80` または `Nmap -p 80 10.0.0.4`)。
3. [ネットワーク モニター](https://download.cnet.com/s/network-monitor)または tcpdump を使用して、宛先 VM からネットワーク トレースを開きます。 `IPv4.address==10.0.0.4 (Windows netmon)` または `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux) など、**PsPing** または **Nmap** を実行したソース VM の IP に表示フィルターを適用します。

### <a name="analyze-traces"></a>トレースの分析

バックエンド VM トレースにパケットが着信していない場合、NSG または UDR により妨害されているか、NVA ルーティング テーブルが正しくない可能性があります。

パケットを受信しているのに応答がない場合は、VM アプリケーションまたはファイアウォールに関する問題に対処する必要があります。 これらの問題のいずれかについては、[必要に応じて、NVA のベンダーにお問い合わせください](https://mskb.pkisolutions.com/kb/2984655)。