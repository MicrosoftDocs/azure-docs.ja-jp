---
title: Azure での Linux VM の時刻同期
description: Linux 仮想マシンの時刻同期。
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/20/2020
ms.author: cynthn
ms.openlocfilehash: 18c8570a8066985cab5263c4779787062dc32d75
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552645"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Azure での Linux VM の時刻同期

時刻同期は、セキュリティとイベントの関連付けで重要になります。 場合によっては、分散トランザクションの実装で使用されます。 複数のコンピューター システム間での時刻の精度は、同期によって実現されます。 同期は、再起動や、時刻をフェッチするコンピューターとタイム ソースとの間のネットワーク トラフィックなど、複数の要因に影響を受ける可能性があります。 

Azure は、Windows Server 2016 を実行しているインフラストラクチャによってサポートされます。 Windows Server 2016 では、時刻を修正し、ローカル クロックを調整して UTC と同期するために使用されるアルゴリズムが改善されています。  Windows Server 2016 の正確な時刻機能では、正確な時刻を確保するためにホストで VM を制御する VMICTimeSync サービスの動作が大幅に改善されています。 この改善には、VM の開始時や VM の復元時の初期時刻の精度の向上と、割り込み待ち時間の修正が含まれます。 

> [!NOTE]
> Windows Time サービスの概要については、こちらの[概要ビデオ](https://aka.ms/WS2016TimeVideo)をご覧ください。
>
> 詳細については、「[Windows Server 2016 の正確な時刻](/windows-server/networking/windows-time-service/accurate-time)」を参照してください。 

## <a name="overview"></a>概要

コンピューター クロックの精度は、そのコンピューター クロックと協定世界時 (UTC) 標準時間との誤差で測定されます。 UTC は、300 年に 1 秒しか誤差が生じない正確な原子時計の世界各国のサンプルによって定義されます。 しかし、UTC を直接読み取るには特殊なハードウェアが必要です。 代わりに、タイム サーバーが UTC と同期され、拡張性と堅牢性を提供するために他のコンピューターからアクセスされます。 すべてのコンピューターで時刻同期サービスが実行されており、サーバーの使用時刻が認識され、コンピューター クロックを修正する必要があるかどうかが定期的に確認され、必要に応じて調整されます。 

Azure ホストは内部の Microsoft タイム サーバーに同期されています。このサーバーでは、GPS アンテナを使用して、Microsoft が所有する Stratum 1 デバイスから時刻を取得します。 Azure の仮想マシンでは、そのホストに依存して正確な時刻 (*ホスト時刻*) を VM に渡すことも、VM でタイム サーバーから直接時刻を取得することも、あるいはこれらの両方を組み合わることもできます。 

スタンドアロン ハードウェアでは、Linux OS によって、起動時にホスト ハードウェア クロックのみが読み取られます。 その後、クロックは、Linux カーネルの割り込みタイマーを使用して維持されます。 この構成では、クロックは時間の経過と共に誤差が生じます。 Azure の新しい Linux ディストリビューションでは、VM で VMICTimeSync プロバイダーを使用できます。このプロバイダーは LIS (Linux Integration Services) に含まれており、ホストからより頻繁にクロック更新についてクエリを実行するためのものです。

仮想マシンとホストとのやりとりがクロックに影響する場合もあります。 [メモリ保持メンテナンス](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)中に、VM は最大で 30 秒間一時停止します。 たとえば、メンテナンスの開始前に、VM クロックには午前 10 時 00 分 00 秒と示され、この状態が 28 秒間続きます。 VM の再開後、VM のクロックはまだ午前 10 時 00 分 00 秒と示されており、28 秒遅れていることになります。 これを修正するため、VMICTimeSync サービスでは、ホストで何が発生しているのかを監視し、VM で生じた変更の補正を求めます。

時刻同期が動作しないと、VM のクロックでエラーが蓄積されます。 VM が 1 つのみであれば、ワークロードで高精度のタイムキーピングが必要でない限り、影響は大きくならない場合があります。 しかし、ほとんどの場合、相互接続された VM が複数あり、それらの VM ではトランザクションを追跡するために時間を使用し、その時間はデプロイ全体で一貫している必要があります。 VM 間の時間が異なると、次のような影響が見られる場合があります。

- 認証は失敗します。 Kerberos または証明書に依存するテクノロジのようなセキュリティ プロトコルでは、システム全体で時間が一貫していることが前提となります。
- ログ (またはその他のデータ) が時間と一致しない場合、システムで何が発生したかを把握するのは非常に困難です。 異なる時間に同じイベントが発生したように見え、関連付けが困難になります。
- クロックに誤差がある場合、請求額を正しく計算できません。



## <a name="configuration-options"></a>構成オプション

Azure でホストされている Linux VM 用の時刻同期を構成する方法は、通常、次の 3 つとなります。

- Azure Marketplace イメージ用の既定の構成では、NTP 時刻と VMICTimeSync ホスト時刻の両方が使用されます。 
- ホストのみ (VMICTimeSync を使用)。
- VMICTimeSync ホスト時刻を使用して、または使用せずに、別の外部のタイム サーバーを使用します。


### <a name="use-the-default"></a>既定値を使用する

既定では、Linux 用のほとんどの Azure Marketplace イメージが、次の 2 つのソースから同期するように構成されます。 

- NTP (プライマリとして)。NTP サーバーから時刻を取得します。 たとえば、Ubuntu 16.04 LTS の Marketplace イメージでは、**ntp.ubuntu.com** が使用されます。
- VMICTimeSync サービス (セカンダリとして)。VM にホスト時刻を知らせ、VM がメンテナンスのために一時停止した後、修正を行うために使用されます。 Azure ホストでは、Microsoft 所有の Stratum 1 デバイスを使用して、正確な時刻を保持します。

新しい Linux ディストリビューションでは、VMICTimeSync サービスで PTP (Precision Time Protocol) ハードウェア クロック ソースが提供されますが、以前のディストリビューションでは、このクロック ソースが提供されない可能性があり、ホストから時刻を取得するために NTP にフォールバックします。

NTP が正しく同期していることを確認するには、`ntpq -p` コマンドを実行します。

### <a name="host-only"></a>ホストのみ 

time.windows.com や ntp.ubuntu.com などの NTP サーバーはパブリックであるため、それらと時刻を同期するには、インターネット経由でトラフィックを送信する必要があります。 さまざまなパケットの遅延が、時刻同期の質に悪影響する可能性があります。ホストのみの同期に切り替えて NTP を削除すると、時刻同期の結果が改善される場合があります。

既定の構成を使用して時刻同期の問題が発生する場合は、ホストのみの時刻同期に切り替えるのが妥当です。 ホストのみの同期を試してみて、VM の時刻同期が改善されるかどうかを確認してください。 

### <a name="external-time-server"></a>外部のタイム サーバー

特定の時刻同期の要件がある場合は、外部のタイム サーバーを使用するオプションもあります。 外部のタイム サーバーでは特定の時刻を指定できます。これは、Microsoft 以外のデータセンターでホストされているコンピューターとの時間の統一性を確保する、あるいは特別な方法でうるう秒を処理する、テスト シナリオで役立つ場合があります。

外部のタイム サーバーと VMICTimeSync サービスを組み合わせることで、既定の構成と同じような結果が得られます。 外部のタイム サーバーと VMICTimeSync を組み合わせることは、VM がメンテナンスのために一時停止したときに原因となる可能性のある問題に対処するための最適なオプションです。 

## <a name="tools-and-resources"></a>ツールとリソース

時刻同期構成を確認するための基本的なコマンドがいくつかあります。 Linux ディストリビューションのドキュメントには、そのディストリビューション用の時刻同期を構成する最適な方法の詳細が示されます。

### <a name="integration-services"></a>統合サービス

統合サービス (hv_utils) が読み込まれているかどうかを確認します。

```bash
lsmod | grep hv_utils
```
次のように表示されます。

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

Hyper-V 統合サービスのデーモンが実行されているかどうかを確認します。

```bash
ps -ef | grep hv
```

次のように表示されます。

```
root        229      2  0 17:52 ?        00:00:00 [hv_vmbus_con]
root        391      2  0 17:52 ?        00:00:00 [hv_balloon]
```


### <a name="check-for-ptp-clock-source"></a>PTP クロック ソースを確認する

新しいバージョンの Linux では、PTP (Precision Time Protocol) クロック ソースを VMICTimeSync プロバイダーの一部として利用できます。 以前のバージョンの Red Hat Enterprise Linux や CentOS 7.x では、[Linux Integration Services](https://github.com/LIS/lis-next) をダウンロードし、それを使用して更新されたドライバーをインポートできます。 PTP クロック ソースを使用できる場合、Linux デバイスの形式は /dev/ptp *x* になります。 

どの PTP クロック ソースを使用できるかを確認します。

```bash
ls /sys/class/ptp
```

この例では、返される値は *ptp0* であるため、これを使用してクロック名を確認します。 デバイスを確認するには、クロック名を調べます。

```bash
cat /sys/class/ptp/ptp0/clock_name
```

この場合は、`hyperv` が返されるはずです。

### <a name="chrony"></a>chrony

Ubuntu 19.10 以降のバージョン、Red Hat Enterprise Linux、および CentOS 8.x では、PTP ソース クロックを使用するように [chrony](https://chrony.tuxfamily.org/) が構成されています。 以前の Linux リリースでは、chrony ではなく、PTP ソースがサポートされない ntpd (Network Time Protocol Daemon) が使用されています。 これらのリリースで PTP を有効にするには、chrony.conf で次のコードを使用して chrony を手動でインストールして構成する必要があります。

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0
```

Ubuntu および NTP の詳細については、[時刻同期](https://ubuntu.com/server/docs/network-ntp)に関するページを参照してください。

Red Hat および NTP の詳細については、[NTP の構成](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP)に関するページを参照してください。 

chrony の詳細については、[chrony の使用](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony)に関するページを参照してください。

chrony および VMICTimeSync ソースの両方が同時に有効になっている場合は、一方を **優先** としてマークし、もう一方のソースをバックアップとして設定することができます。 NTP サービスでは、長時間の経過後を除き、大きなスキューが発生した場合にクロックが更新されないため、VMICTimeSync によって、NTP ベースのツールを単独で使用する場合よりはるかに早く一時停止した VM イベントからクロックが回復されます。

既定では、chronyd は、時間の誤差を修正するために、システム クロックを加速または減速します。 誤差が大きすぎる場合、chrony は誤差の修正に失敗します。 これを解決するために、 **/etc/chrony.conf** の `makestep` パラメーターを変更して、誤差が、指定されたしきい値を超えた場合に時刻同期を強制的に適用することができます。

 ```bash
makestep 1.0 -1
```

ここでは、chrony は、誤差が 1 秒よりも大きい場合に時間の更新を強制します。 変更を適用するには、chronyd サービスを再起動します。

```bash
systemctl restart chronyd
```

### <a name="systemd"></a>systemd 

19.10 より前の SUSE と Ubuntu では、時刻同期は [systemd](https://www.freedesktop.org/wiki/Software/systemd/) を使用して構成されます。 Ubuntu の詳細については、[時刻同期](https://help.ubuntu.com/lts/serverguide/NTP.html)に関するページを参照してください。 SUSE の詳細については、[SUSE Linux Enterprise Server 12 SP3 に関するリリース ノート](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)の第 4.5.8 条を参照してください。

## <a name="next-steps"></a>次のステップ

詳細については、「[Windows Server 2016 の正確な時刻](/windows-server/networking/windows-time-service/accurate-time)」を参照してください。


