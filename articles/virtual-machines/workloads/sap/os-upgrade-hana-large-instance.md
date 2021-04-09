---
title: Azure SAP HANA のオペレーティング システムのアップグレード (ラージ インスタンス) | Microsoft Docs
description: Azure の SAP HANA のオペレーティング システムのアップグレードを実行します｡
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f77c16f16ddac01329a8315893021767a4120295
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179240"
---
# <a name="operating-system-upgrade"></a>オペレーティング システムのアップグレード
このドキュメントでは､HANA ラージ インスタンスでのオペレーティング システムについての詳細を説明します｡

>[!NOTE]
>OS のアップグレードはお客様の責任です｡Microsoft の運用サポートができることは､アップグレード中にどのような点に注意を払うべきかについてお答えすることです｡ アップグレードを計画する前にオペレーティング システムのベンダーにも相談するようにしてください｡

HLI ユニットのプロビジョニング中に、Microsoft の運用チームはオペレーティング システムをインストールします。
時間の経過と共に、HLI ユニット上でオペレーティング システムを保守することが必要になります (パッチ適用、チューニング、アップグレードなど)。

オペレーティング システムの大幅な変更 (SP1 から SP2 へのアップグレードなど) を行う前に、サポート チケットを開いて Microsoft Operations チームに連絡し、相談してください。

チケットに含める:

* HLI サブスクリプション ID。
* サーバー名。
* 適用を予定しているパッチ レベル。
* この変更を予定している日付。 

希望するアップグレードの少なくとも 1 週間前にこのチケットを開くことをお勧めします。これにより、必要なファームウェア バージョンについて運用チームが把握できるようになります。

各種 Linux バージョンと各種 SAP HANA バージョンのサポート マトリックスについては、[SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581) を確認してください。

## <a name="known-issues"></a>既知の問題

以下は､アップグレード中にいくつかある一般的な既知の問題です｡
- タイプ II クラスの SKU で､OS のアップグレード後に SFS が削除されます｡ OS のアップグレード後に互換性のある SFS を再インストールする必要があります。
- イーサネット カードのドライバー (ENIC および FNIC) が古いバージョンにロールバックします｡ アップグレード後に互換性のあるドライバー バージョンを再インストールする必要があります。

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA L インスタンス (タイプ I) の推奨構成

オペレーティングシステムの構成は、修正プログラムの適用、システムのアップグレード、顧客による変更により、時間の経過と共に、推奨される設定から逸脱する可能性があります。 また、Microsoft では、最適なパフォーマンスと回復性を確保するために、既存のシステムに必要な更新プログラムを特定しています。 次の手順では、ネットワーク パフォーマンス、システムの安定性、および最適な HANA パフォーマンスに関する推奨事項の概要を示します。

### <a name="compatible-enicfnic-driver-versions"></a>互換性のある eNIC/fNIC ドライバーのバージョン
  適切なネットワーク パフォーマンスとシステムの安定性を確保するために、次の互換性に関する表に示すように、OS 固有の適切なバージョンの eNIC および fNIC ドライバーがインストールされていることを確認することをお勧めします。 サーバーは、互換性のあるバージョンの顧客に配信されます。 OS/カーネルの修正プログラムの適用中に、ドライバーが既定のドライバー バージョンにロールバックされる場合があります。 OS/カーネルの修正プログラム適用後に、適切なドライバーのバージョンが実行されていることを確認してください。
       
      
  |  OS ベンダー    |  OS パッケージ バージョン     |  Firmware Version  |  eNIC ドライバー |  fNIC ドライバー | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP5            |   3.2.3i           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>ドライバーのアップグレードおよび古い rpm パッケージをクリーニングするためのコマンド

#### <a name="command-to-check-existing-installed-drivers"></a>インストールされている既存のドライバーを確認するコマンド
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>既存の eNIC/fNIC rpm を削除する
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>推奨される eNIC/fNIC ドライバー パッケージをインストールする
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>インストールを確認するコマンド
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>OS のアップグレード中に eNIC/fNIC ドライバーをインストールする手順

* OS バージョンをアップグレードします
* 古い rpm パッケージを削除します
* インストールされている OS バージョンに合わせて、互換性のある eNIC/fNIC ドライバーをインストールします
* システムを再起動します
* 再起動後に、eNIC/fNIC のバージョンを確認します


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB 更新エラー
アップグレード後、SAP on Azure HANA L インスタンス (タイプ I) は起動不可能な状態になることがあります。 以下の手順で、この問題を修正します。
#### <a name="execution-steps"></a>実行ステップ


*   コマンド `multipath -ll` を実行します。
*   サイズが約 50 G の LUN ID を取得するか、`fdisk -l | grep mapper` のコマンドを使用します。
*   行 `/dev/mapper/<LUN ID>`を使用して `/etc/default/grub_installdevice` ファイルを更新します。 例: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN ID はサーバーによって異なります。


### <a name="disable-edac"></a>EDAC の無効化 
   エラー検出およびエラー修正 (EDAC) モジュールは、メモリエラーの検出と修正に役立ちます。 ただし、SAP HANA on Azure L インスタンス (タイプ I) の基になるハードウェアは、既に同じ機能を実行しています。 ハードウェアおよびオペレーティングシステム (OS) レベルで同じ機能を有効にすると、競合が発生する可能性があるため、サーバーが予期せずシャットダウンする可能性があります。 そのため、OS からモジュールを無効化することをお勧めします。

#### <a name="execution-steps"></a>実行ステップ

* EDAC モジュールが有効になっているかどうかを確認します。 次のコマンドで出力が返された場合、モジュールが有効になっていることを意味します。 
```
lsmod | grep -i edac 
```
* ファイル `/etc/modprobe.d/blacklist.conf` に次の行を追加してモジュールを無効にする
```
blacklist sb_edac
blacklist edac_core
```
変更を行うには、再起動が必要です。 `lsmod` コマンドを実行し、出力にモジュールが存在しないことを確認します。

### <a name="kernel-parameters"></a>カーネル パラメーター
   `transparent_hugepage`、`numa_balancing`、`processor.max_cstate`、`ignore_ce`、および `intel_idle.max_cstate` の正しい設定が適用されていることを確認します。

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>実行ステップ

* これらのパラメーターを `/etc/default/grub` ファイルの `GRB_CMDLINE_LINUX` 行に追加する
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* 新しい grub ファイルを作成します。
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* システムを再起動します。


## <a name="next-steps"></a>次のステップ
- OS バックアップのタイプ I SKU クラスについては､[バックアップと復元](hana-overview-high-availability-disaster-recovery.md)を参照してください｡
- Type II SKU クラスについては、[リビジョン 3 スタンプの Type II SKU の OS バックアップ](os-backup-type-ii-skus.md) に関するページを参照してください。
