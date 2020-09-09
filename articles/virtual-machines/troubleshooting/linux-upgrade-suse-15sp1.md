---
title: SUSE Linux Enterprise Server を備えた Azure VM を SUSE 15 SP1 にアップグレードする | Microsoft Docs
description: この記事では、SUSE Distribution Migration System を使用して、SUSE Linux Enterprise Server を、Azure 仮想マシン用の SUSE 15 SP1 にアップグレードする一般的な手順について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: amkarmak
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 05/21/2020
ms.author: arremana
ms.openlocfilehash: 079574ab4c5846eae6266d1e13240baf99428a69
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84449245"
---
# <a name="upgrade-azure-vm-with-sles-12-to-sles-15-sp1"></a>SLES 12 の Azure VM を SLES 15 SP1 にアップグレードする

この記事では、SUSE Linux Enterprise Server (SLES) 12 を、Azure 仮想マシン (VM) 用の SUSE 15 SP1 にアップグレードする一般的な手順について説明します。 詳細については、[SUSE Distribution Migration System の使用](https://documentation.suse.com/suse-distribution-migration-system/1.0/single-html/distribution-migration-system/index.html)と [SUSE Linux Enterprise Server 15 SP1 アップグレード ガイド](https://documentation.suse.com/sles/15-SP1/single-html/SLES-upgrade/index.html#sec-update-preparation-update)に関するページをご覧ください。

## <a name="supported-upgrade-paths"></a>サポートされるアップグレード パス
Sles 15 SP1 に進む前に、現在の SLES バージョンが SLES 12 SP4 または 12 SP5 である必要があります。

![サポートされているアップグレード パスに関するスクリーンショット](./media/linux-upgrade-suse-15sp1/upgrade-path.png)

## <a name="prerequisites"></a>前提条件

- 承認されたダウンタイム期間に従って、移行アクティビティを計画します。 これは、移行中に VM が再起動されるためです。
- 移行アクティビティの前に、VM の完全バックアップを取得します。
- バックアップが構成されていない場合は、OS ディスクのスナップショット バックアップを作成します。
- [VM が V1 世代か V2 世代かを確認します](#check-the-generation-version-for-a-vm)。

## <a name="upgrade-from-suse-12-sp4-or-sp5-to-suse-15-sp1"></a>SUSE 12 SP4 または SP5 から SUSE 15 SP1 にアップグレードする

1. VM 用の最新のパッケージをインストールします。

    ```
    zypper clean --all
    zypper refresh
    zypper update
    ```

2. インストールの終了後、VM を再起動します。

3. カーネルと OS のバージョンを確認します。 バージョンが SUSE 12 SP4 または SUSE 12 SP5 であることを確認します。

    ```
    uname -a
    cat /etc/os-release
    ```

4. **suse-migration-sle15-activation** をインストールします。 パッケージ **suse-migration-sle15-activation** がインストールされると、別のパッケージ **SLES15-Migration** が依存関係パッケージとして自動的にインストールされます。 

   ```
   zypper install suse-migration-sle15-activation
   ```

5. インストールの終了後、`reboot` コマンドを実行して VM を再起動します。

6. [Azure portal](https://portal.azure.com) に移動し、VM を選択して、 **[Serial console]** を選択します。 システムが停止し、"reboot:Restarting system"\(再起動:システムを再起動しています\) というメッセージが表示されます。 このプロセスには 15 ～ 45 分くらいかかります。 第 2 世代の VM の場合、"reboot:Restarting system"\(再起動:システムを再起動しています\) 画面のまま停止することがあります。 この場合、45 分待機してください。 それでもまだ進行しない場合は、Azure portal の VM の **[概要]** ページに移動して、再起動します。

     ![シリアル コンソール内のメッセージに関するスクリーンショット](./media/linux-upgrade-suse-15sp1/reboot-message.png)

8. 新しいカーネルでシステムを再起動すると、次のメッセージが表示されます。

     ![シリアル コンソール内のメッセージに関するスクリーンショット](./media/linux-upgrade-suse-15sp1/output-message.png)
9. カーネルと OS のバージョンを確認して、システムが正常にアップグレードされたかどうかを確認します。

    ```
    uname -a
    cat /etc/os-release
    ```

## <a name="check-the-generation-version-for-a-vm"></a>VM の世代バージョンを確認する

次のいずれかの方法を使用して世代バージョンを確認できます。

- SLES ターミナルで、コマンド `dmidecode | grep -i hyper` を実行します。 V1 世代の VM の場合、出力は返されません。 V2 世代の VM の場合、次の出力が表示されます。

     ![第 2 世代の VM の出力に関するスクリーンショット](./media/linux-upgrade-suse-15sp1/output-gen2.png)
- [Azure portal](https://portal.azure.com) で、VM の **[プロパティ]** に移動し、 **[VM の世代]** フィールドをオンにします。
