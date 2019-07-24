---
title: SAP HANA on Azure (L インスタンス) Type II SKU のオペレーティング システムのバックアップと復元 | Microsoft Docs
description: SAP HANA on Azure (L インスタンス) Type II SKU のオペレーティング システムのバックアップと復元を実行します
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c82c5c74fe13bad99528486be69089df5f477457
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436342"
---
# <a name="os-backup-and-restore-for-type-ii-skus"></a>Type II SKU の OS のバックアップと復元

このドキュメントでは、HANA L インスタンスの **Type II SKU** のオペレーティング システム ファイル レベルのバックアップと復元を実行する手順について説明します。 

>[!NOTE]
>OS バックアップ スクリプトは、サーバーにプレインストールされている ReaR ソフトウェアを使用します。  

Microsoft サービス管理チームによるプロビジョニングが完了すると、既定では、サーバーは、オペレーティング システムのファイル レベル バックアップを実行する 2 つのバックアップ スケジュールで構成されます。 次のコマンドを使用して、バックアップ ジョブのスケジュールを確認できます。
```
#crontab –l
```
次のコマンドを使用して、いつでもバックアップ スケジュールを変更できます。
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>手動バックアップを実行する方法

オペレーティング システムのファイル システム バックアップは、既に **cron ジョブ**を使用してスケジュールされています。 ただし、オペレーティング システムのファイル レベル バックアップを手動で実行することもできます。 手動バックアップを行うには、次のコマンドを実行します。

```
#rear -v mkbackup
```
次の画面は、手動バックアップのサンプルを示しています。

![方法](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>バックアップを復元する方法

バックアップから、完全バックアップまたは個々のファイルを復元できます。 復元するには、次のコマンドを使用します。

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
復元後、ファイルは現在の作業ディレクトリに回復されます。

次のコマンドは、バックアップ ファイル *backup.tar.gz* からのファイル */etc/fstab* の復元を示しています。
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>ファイルは、バックアップから復元された後、必要な場所にコピーする必要があります。

次のスクリーンショットは、完全バックアップの復元を示しています。

![HowtoRestoreaBackup.PNG](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>ReaR ツールをインストールして構成を変更する方法 

Relax-and-Recover (ReaR) パッケージは、HANA L インスタンスの **Type II SKU** に**プレインストール**されているため、ユーザーは何もする必要がありません。 オペレーティング システムのバックアップのために、ReaR の使用を直接開始することができます。
ただし、パッケージを自分でインストールする必要がある場合は、記載されている手順に従って、ReaR ツールをインストールして構成することができます。

**ReaR** バックアップ パッケージをインストールするには、以下のコマンドを使用します。

**SLES** オペレーティング システムでは、次のコマンドを使用します。
```
#zypper install <rear rpm package>
```
**RHEL** オペレーティング システムでは、次のコマンドを使用します。 
```
#yum install rear -y
```
ReaR ツールを構成するには、*/etc/rear/local.conf* ファイルの **OUTPUT_URL** パラメーターと **BACKUP_URL** パラメーターを更新する必要があります。
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

次のスクリーンショットは、完全バックアップの復元を示しています。![RearToolConfiguration.PNG](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
