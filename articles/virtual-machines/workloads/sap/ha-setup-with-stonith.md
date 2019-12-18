---
title: STONITH を使用した SAP HANA on Azure (L インスタンス) の高可用性のセットアップ | Microsoft Docs
description: STONITH を使って SUSE の SAP HANA on Azure (L インスタンス) の高可用性を確立します
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/21/2017
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0f23fe2aa17934b967e7aecf41687cc555b9552c
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "71212535"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith"></a>STONITH を使用した SUSE での高可用性のセットアップ
このドキュメントでは、STONITH デバイスを使って SUSE オペレーティング システムに高可用性をセットアップする詳しい手順について説明します。

**免責事項:** *このガイドは、正常に動作する Microsoft HANA L インスタンス環境でのセットアップのテストによって得られたものです。HANA L インスタンスの Microsoft サービス管理チームはオペレーティング システムをサポートしていないので、オペレーティング システム レイヤーでの詳細なトラブルシューティングや不明点については、SUSE に問い合わせる必要があります。Microsoft のサービス管理チームは、STONITH デバイスのセットアップを行い、STONITH デバイスに関する問題のトラブルシューティングについて全面的にサポートします。*
## <a name="overview"></a>概要
SUSE のクラスタリングを使って高可用性をセットアップするには、次の前提条件が満たされている必要があります。
### <a name="pre-requisites"></a>前提条件
- HANA L インスタンスがプロビジョニングされている
- オペレーティング システムが登録されている
- HANA L インスタンスが SMT サーバーに接続されていて、修正プログラム/パッケージを取得できる
- オペレーティング システムに最新の修正プログラムがインストールされている
- NTP (タイム サーバー) がセットアップされている
- 最新バージョンの SUSE のドキュメントで HA のセットアップに関する情報を読み、理解している

### <a name="setup-details"></a>セットアップの詳細
このガイドでは、次のセットアップを使用します。
- オペレーティング システム:SLES 12 SP1 for SAP
- HANA L インスタンス:2xS192 (4 ソケット、2 TB)
- HANA バージョン:HANA 2.0 SP1
- サーバー名: sapprdhdb95 (node1) および sapprdhdb96 (node2)
- STONITH デバイス: iSCSI ベースの STONITH デバイス
- HANA L インスタンス ノードの 1 つに NTP のセットアップ

HSR で HANA L インスタンスをセットアップするときは、Microsoft サービス管理チームに STONITH のセットアップを依頼できます。 HANA L インスタンスがプロビジョニングされた既存のお客様が、既存のブレード用に STONITH デバイスをセットアップする必要がある場合は、サービス要求フォーム (SRF) で次の情報を Microsoft サービス管理チームに提供する必要があります。 SRF フォームは、テクニカル アカウント マネージャーまたは HANA L インスタンスのオンボーディングに関する Microsoft の担当者に要求できます。 新しいお客様は、プロビジョニング時に STONITH デバイスを要求できます。 入力はプロビジョニング要求フォームでできます。

- サーバー名とサーバー IP アドレス (例: myhanaserver1、10.35.0.1)
- 場所 (例: 米国東部)
- 顧客名 (例: Microsoft)
- SID - HANA システム識別子 (例: H11)

STONITH デバイスの構成が済むと、Microsoft のサービス管理チームから iSCSI ストレージの SBD デバイス名と IP アドレスが提供されるので、それを使って STONITH のセットアップを構成できます。 

STONITH を使ってエンド ツー エンドの HA をセットアップするには、次の手順に従う必要があります。

1.  SBD デバイスを識別する
2.  SBD デバイスを初期化する
3.  クラスターを構成する
4.  Softdog ウォッチドッグを設定する
5.  クラスターにノードを参加させる
6.  クラスターを検証する
7.  クラスターのリソースを構成する
8.  フェールオーバー プロセスをテストする

## <a name="1---identify-the-sbd-device"></a>1. SBD デバイスを識別する
このセクションでは、Microsoft のサービス管理チームが STONITH を構成した後で、セットアップ対象の SBD デバイスを特定する方法について説明します。 **このセクションは、既存のお客様のみが対象となります**。 新しいお客様の場合は、Microsoft のサービス管理チームが SBD のデバイス名を提供するので、お客様はこのセクションをスキップできます。

1.1 */etc/iscsi/initiatorname.isci* を次のように変更します 
``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft のサービス管理チームがこの文字列を提供します。 **両方**のノードでファイルを修正します。ただし、ノード番号はノードごとに異なります。

![initiatorname.png](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 */etc/iscsi/iscsid.conf* を次のように変更します。*node.session.timeo.replacement_timeout=5* および *node.startup = automatic* を設定します。 **両方**のノードでファイルを修正します。

1.3 discovery コマンドを実行します。4 つのセッションが表示されます。 両方のノードで実行します。

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iSCSIadmDiscovery.png](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 コマンドを実行して iSCSI デバイスにログインします。4 つのセッションが表示されます。 **両方**のノードで実行します。

```
iscsiadm -m node -l
```
![iSCSIadmLogin.png](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 再スキャン スクリプト *rescan-scsi-bus.sh* を実行します。自動的に作成された新しいディスクが表示されます。  両方のノードで実行します。 ゼロより大きい LUN 番号が表示されます (例:1、2 など)

```
rescan-scsi-bus.sh
```
![rescanscsibus.png](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 デバイス名を確認するには、*fdisk –l* コマンドを実行します。 両方のノードで実行します。 サイズが **178 MiB** のデバイスを選択します。

```
  fdisk –l
```

![fdisk-l.png](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. SBD デバイスを初期化する

2.1 **両方**のノードで SBD デバイスを初期化します

```
sbd -d <SBD Device Name> create
```
![sbdcreate.png](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 デバイスに書き込まれた内容を確認します。 **両方**のノードで行います

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. クラスターを構成する
このセクションでは、SUSE HA クラスターをセットアップする手順について説明します。
### <a name="31-package-installation"></a>3.1 パッケージのインストール
3.1.1   ha_sles および SAPHanaSR-doc パターンがインストールされていることを確認します。 インストールされていない場合はインストールします。 **両方**のノードにインストールしてください。
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![zypperpatternha_sles.png](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![zypperpatternSAPHANASR-doc.png](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 クラスターのセットアップ
3.2.1   *ha-cluster-init* コマンドまたは yast2 ウィザードを使って、クラスターをセットアップできます。 この例では、yast2 ウィザードを使用します。 このステップは、**プライマリ ノードのみ**で実行します。

[yast2] > [High Availability]\(高可用性) > [Cluster]\(クラスター\) の順に選びます ![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
![yast-hawk-install.png](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

halk2 パッケージは既にインストールされているので、 **[Cancel]\(キャンセル\)** をクリックします。

![yast-hawk-continue.png](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

**[Continue]\(続行\)** をクリックします

予想される値 = デプロイされているノードの数 (この場合は 2) ![yast-Cluster-Security.png](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png) **[Next]\(次へ\)** をクリックします
![yast-cluster-configure-csync2.png](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png) ノード名を追加し、[Add suggested files]\(示されたファイルを追加する\) をクリックします

[Turn csync2 ON]\(csync2 をオンにする\) をクリックします

[Generate Pre-Shared-Keys]\(事前共有キーの生成\) をクリックします。次のポップアップが表示されます

![yast-key-file.png](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

**[OK]**

Csync2 で IP アドレスと事前共有キーを使って認証が実行されます。 キー ファイルは csync2 -k /etc/csync2/key_hagroup で生成されます。 作成された key_hagroup ファイルを、クラスターのすべてのメンバーに手動でコピーする必要があります。 **node1 から node2 にファイルを必ずコピーします**。

![yast-cluster-conntrackd.png](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

**[Next]\(次へ\)** をクリックします
![yast-cluster-service.png](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

既定のオプションでは起動はオフになっているので、Pacemaker が起動時に開始されるように "オン" に変更します。 セットアップの要件に基づいて選ぶことができます。
**[Next]\(次へ\)** をクリックしてクラスターの構成を完了します。

## <a name="4---setting-up-the-softdog-watchdog"></a>4. Softdog ウォッチドッグを設定する
このセクションでは、ウォッチドッグ (softdog) の構成について説明します。

4.1 次の行を、**両方**のノードの */etc/init.d/boot.local* に追加します。
```
modprobe softdog
```
![modprobe-softdog.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 **両方**のノードの */etc/sysconfig/sbd* ファイルを次のように更新します。
```
SBD_DEVICE="<SBD Device Name>"
```
![sbd-device.png](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 **両方**のノードで次のコマンドを実行してカーネル モジュールを読み込みます
```
modprobe softdog
```
![modprobe-softdog-command.png](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 **両方**のノードで次のようにして、softdog が実行されていることを確認します。
```
lsmod | grep dog
```
![lsmod-grep-dog.png](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 **両方**のノードで SBD デバイスを起動します
```
/usr/share/sbd/sbd.sh start
```
![sbd-sh-start.png](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 **両方**のノードで SBD デーモンをテストします。 **両方**のノードで構成した後は、2 つのエントリが表示されます
```
sbd -d <SBD Device Name> list
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 **一方**のノードにテスト メッセージを送信します
```
sbd  -d <SBD Device Name> message <node2> <message>
```
![sbd-list.png](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 **2 番目**のノード (node2) で、メッセージの状態を確認できます
```
sbd  -d <SBD Device Name> list
```
![sbd-list-message.png](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 sbd の構成を適用するには、 */etc/sysconfig/sbd* ファイルを次のように更新します。 **両方**のノードでファイルを更新します
```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10   **プライマリ ノード** (node1) で Pacemaker サービスを開始します
```
systemctl start pacemaker
```
![start-pacemaker.png](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Pacemaker サービスが "*失敗*" する場合は、「*シナリオ 5:Pacemaker サービスが失敗する*」を参照してください

## <a name="5---joining-the-cluster"></a>5. クラスターに参加する
このセクションでは、クラスターにノードを参加させる方法について説明します。

### <a name="51-add-the-node"></a>5.1 ノードを追加する
**node2** で次のコマンドを実行して、node2 をクラスターに参加させます。
```
ha-cluster-join
```
クラスターを参加させるときに "*エラー*" が発生する場合は、「*シナリオ 6:node2 がクラスターに参加できない*」を参照してください。

## <a name="6---validating-the-cluster"></a>6. クラスターの検証

### <a name="61-start-the-cluster-service"></a>6.1 クラスター サービスを開始する
確認し、必要に応じて**両方**のノードでクラスターを初めて開始します。
```
systemctl status pacemaker
systemctl start pacemaker
```
![systemctl-status-pacemaker.png](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
### <a name="62-monitor-the-status"></a>6.2 状態を監視する
*crm_mon* コマンドを実行し、**両方**のノードがオンラインであることを確認します。 クラスターの**どのノード**で実行してもかまいません
```
crm_mon
```
![crm-mon.png](media/HowToHLI/HASetupWithStonith/crm-mon.png) hawk にログインして、クラスターの状態 *https://\<ノード IP>:7630* を確認することもできます。 既定のユーザーは hacluster で、パスワードは linux です。 必要な場合は、*passwd* コマンドを使ってパスワードを変更できます。

## <a name="7-configure-cluster-properties-and-resources"></a>7.クラスターのプロパティとリソースを構成する 
このセクションでは、クラスター リソースを構成する手順について説明します。
この例では、次のリソースをセットアップします。残りは、SUSE HA のガイドを参考にして (必要に応じて) 構成できます。 この構成は、**1 つのノード**だけで実行します。 プライマリ ノードで行います。

- クラスターのブートストラップ
- STONITH デバイス
- 仮想 IP アドレス


### <a name="71-cluster-bootstrap-and-more"></a>7.1 クラスターのブートストラップその他
クラスターのブートストラップを追加します。 ファイルを作成し、次のようにテキストを追加します。
```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
構成をクラスターに追加します。
```
crm configure load update crm-bs.txt
```
![crm-configure-crmbs.png](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH デバイス
STONITH リソースを追加します。 ファイルを作成し、次のようにテキストを追加します。
```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
構成をクラスターに追加します。
```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 仮想 IP アドレス
仮想 IP リソースを追加します。 ファイルを作成し、次のようにテキストを追加します。
```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
構成をクラスターに追加します。
```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 リソースを確認する

*crm_mon* コマンドを実行すると、2 つのリソースが表示されます。
![crm_mon_command.png](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

また、*https://\<ノード IP アドレス>:7630/cib/live/state* で状態を確認することもできます

![hawlk-status-page.png](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8.フェールオーバー プロセスをテストする
フェールオーバー プロセスをテストするには、node1 の Pacemaker サービスを停止すると、リソースが node2 にフェールオーバーします。
```
Service pacemaker stop
```
次に、**node2** で Pacemaker サービスを停止すると、リソースが **node1** にフェールオーバーします

**フェールオーバー前**  
![Before-failover.png](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**フェールオーバー後**  
![after-failover.png](media/HowToHLI/HASetupWithStonith/after-failover.png)  
![crm-mon-after-failover.png](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9.トラブルシューティング
このセクションでは、セットアップ中に発生する可能性のあるいくつかの障害シナリオについて説明します。 これらの問題は必ず発生するとは限りません。

### <a name="scenario-1-cluster-node-not-online"></a>シナリオ 1:クラスター ノードがオンラインではない
クラスター マネージャーでいずれかのノードがオンラインと表示されない場合は、以下の方法でオンラインになる可能性があります。

iSCSI サービスを開始します
```
service iscsid start
```

その iSCSI ノードにログインできるようになります
```
iscsiadm -m node -l
```
予想される出力は次のとおりです
```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-does-not-show-graphical-view"></a>シナリオ 2: yast2 でグラフィカル ビューが表示されない
このドキュメントでは、yast2 のグラフィカル画面を使って高可用性クラスターをセットアップしています。 次のような yast2 のグラフィカル ウィンドウが開かず、Qt エラーがスローされる場合は、次の手順に従います。 グラフィカル ウィンドウが開く場合は、この手順を省略できます。

**Error**

![yast2-qt-gui-error.png](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**予想される出力**

![yast-control-center.png](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

yast2 のグラフィカル ビューが開かない場合は、以下の手順のようにします。

必要なパッケージをインストールします。 "root" ユーザーとしてログインし、SMT セットアップでパッケージをダウンロードしてインストールする必要があります。

パッケージをインストールするには、[yast] > [Software]\(ソフトウェア\) > [Software Management]\(ソフトウェア管理\) > [Dependencies]\(依存関係\) > [Install recommended packages…]\(推奨されるパッケージをインストールする...\) オプションを使います。 次のスクリーンショットは、予想される画面を示しています。
>[!NOTE]
>両方のノードから yast2 のグラフィカル ビューにアクセスできるように、両方のノードで手順を実行する必要があります。

![yast-sofwaremanagement.png](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

[Dependencies]\(依存関係\) で、[Install Recommended Packages]\(推奨されるパッケージをインストールする\) を選びます ![yast-dependencies.png](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

変更を確認して、[OK] をクリックします

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

パッケージのインストールが実行されます ![yast を実行する installation.png](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

[Next]\(次へ\) をクリックします

![yast-installation-report.png](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

[Finish]\(完了\) をクリックします

libqt4 パッケージと libyui-qt パッケージもインストールする必要があります。
```
zypper -n install libqt4
```
![zypper-install-libqt4.png](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
```
zypper -n install libyui-qt
```
![zypper-install-ligyui.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
![zypper-install-ligyui_part2.png](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png) 次のように、yast2 でグラフィカル ビューが開くようになります。
![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-does-not-high-availability-option"></a>シナリオ 3: yast2 に高可用性オプションが表示されない
yast2 コントロール センターに高可用性オプションが表示されるようにするには、追加パッケージをインストールする必要があります。

[Yast2] > [Software]\(ソフトウェア\) > [Software management]\(ソフトウェア管理\) で次のパターンを選びます

- SAP HANA サーバー ベース
- C/C++ コンパイラとツール
- 高可用性
- SAP アプリケーション サーバー ベース

次の画面では、パターンをインストールする手順を示します。

[yast2] > [Software]\(ソフトウェア\) > [Software management]\(ソフトウェア管理\) の使用

![yast2-control-center.png](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

パターンを選びます

![yast-pattern1.png](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)
![yast-pattern2.png](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

**[Accept]\(受け入れる\)** をクリックします

![yast-changed-packages.png](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

**[Continue]\(続行\)** をクリックします

![yast2-performing-installation.png](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

インストールが完了したら、 **[Next]\(次へ\)** をクリックします

![yast2-installation-report.png](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>シナリオ 4:HANA のインストールが gcc アセンブリ エラーで失敗する
HANA のインストールが次のエラーで失敗します。

![Hana-installation-error.png](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

この問題を修正するには、ライブラリ (libgcc_sl と libstdc++6) を次のようにしてインストールする必要があります。

![zypper-install-lib.png](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>シナリオ 5:Pacemaker サービスが失敗する

Pacemaker サービスの開始中に、次の問題が発生します。

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

これを修正するには、 */usr/lib/systemd/system/fstrim.timer* ファイルから次の行を削除します

```
Persistent=true
```

![Persistent.png](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>シナリオ 6:node2 がクラスターに参加できない

*ha-cluster-join* コマンドを使って node2 を既存のクラスターに参加させるときに、次のエラーが発生します。

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![ha-cluster-join-error.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

解決するには、両方のノードで次を実行します

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![ssh-keygen-node1.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![ssh-keygen-node2.PNG](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

上記の修正を実行すると、node2 はクラスターに追加されるようになります

![ha-cluster-join-fix.png](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="10-general-documentation"></a>10.一般的なドキュメント
SUSE HA のセットアップについて詳しくは、次の記事をご覧ください。 

- [SAP HANA SR パフォーマンス最適化シナリオ](https://www.suse.com/docrep/documents/ir8w88iwu7/suse_linux_enterprise_server_for_sap_applications_12_sp1.pdf )
- [ストレージ ベースのフェンス操作](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [SAP HANA 用 Pacemaker Cluster の使用 - パート 1 のブログ](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [SAP HANA 用 Pacemaker Cluster の使用 - パート 2 のブログ](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)
