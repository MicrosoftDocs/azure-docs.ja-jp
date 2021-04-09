---
title: Data Science Virtual Machine を Ubuntu 18.04 にアップグレードする方法
titleSuffix: Azure Data Science Virtual Machine
description: CentOS および Ubuntu 16.04 から最新の Ubuntu 18.04 Data Science Virtual Machine にアップグレードする方法について説明します。
keywords: ディープ ラーニング, AI, データ サイエンス ツール, データ サイエンス仮想マシン, チーム データ サイエンス プロセス
services: machine-learning
ms.service: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 5b897ff7527d2d60234162ccbdeb08a00260bb1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659462"
---
# <a name="upgrade-your-data-science-virtual-machine-to-ubuntu-1804"></a>Data Science Virtual Machine を Ubuntu 18.04 にアップグレードする

Data Science Virtual Machine で Ubuntu 16.04 や CentOS などの古いリリースを実行している場合、DSVM を Ubuntu 18.04 に移行する必要があります。 移行することで、オペレーティング システムの修正プログラム、ドライバー、プレインストールされているソフトウェア、ライブラリのバージョンが最新であることが保証されます。 このドキュメントでは、古いバージョンの Ubuntu または CentOS を移行する方法について説明します。 

## <a name="prerequisites"></a>[前提条件]

- SSH と Linux コマンド ラインに関する知識

## <a name="overview"></a>概要

移行するには、次の 2 つの方法が考えられます。

- インプレース移行: "同じサーバー" の移行とも呼ばれます。 この移行では、新しい仮想マシンを作成せずに、既存の VM がアップグレードされます。 インプレース移行の方が Ubuntu 16.04 から Ubuntu 18.04 に簡単に移行できます。
- サイドバイサイド移行: "サーバー間" の移行とも呼ばれます。 この移行では、データが既存の仮想マシンから新しく作成された VM に転送されます。 サイドバイサイド移行は、Centos から Ubuntu 18.04 に移行する方法です。 古いインストールが必要以上に乱雑になっていると思われる場合は、Ubuntu バージョン間のアップグレードにサイドバイサイド移行をお勧めします。

## <a name="snapshot-your-vm-in-case-you-need-to-roll-back"></a>ロールバックが必要になる場合に備えて VM のスナップショットを作成する

Azure portal で、検索バーを使用して **[スナップショット]** 機能を検索します。 

:::image type="content" source="media/ubuntu_upgrade/azure-portal-search-bar.png" alt-text="Azure portal と検索バーを示すスクリーンショット、[スナップショット] が強調表示されている":::

1. **[追加]** を選択します。これにより、 **[スナップショットの作成]** ページに移動します。 ご利用の仮想マシンのサブスクリプションとリソース グループを選択します。 **[リージョン]** では、ターゲット ストレージが存在するリージョンと同じリージョンを選択します。 DSVM ストレージ ディスクと追加のバックアップ オプションを選択します。 このバックアップ シナリオのストレージの種類としては、 **[Standard HDD]** が適しています。

:::image type="content" source="media/ubuntu_upgrade/create-snapshot-options.png" alt-text="[スナップショットの作成] のオプションを示すスクリーンショット":::

2. すべての詳細を入力し、検証を通過したら、 **[確認と作成]** を選択して、スナップショットを検証し、作成します。 スナップショットが正常に完了したら、デプロイが完了したことを示すメッセージが表示されます。

## <a name="in-place-migration"></a>インプレース移行

古い Ubuntu リリースを移行する場合は、インプレース移行を選択できます。 この移行では、新しい仮想マシンは作成されず、サイドバイサイド移行よりも手順が少なくなります。  より詳細に制御するため、または CentOS などの別のディストリビューションから移行しているためにサイドバイサイド移行を行う必要がある場合は、「[サイドバイサイド移行](#side-by-side-migration)」セクションに進んでください。 

1. Azure portal から、DSVM を起動し、SSH を使用してサインインします。 このためには、 **[接続]** と **[SSH]** を選択し、接続手順に従います。 

1. DSVM 上のターミナル セッションに接続したら、次のアップグレード コマンドを実行します。

    ```bash
    sudo do-release-upgrade
    ```

アップグレード プロセスが完了するまでしばらく時間がかかります。 終了したら、プログラムにより、仮想マシンを再起動するための許可が求められます。 **[はい]** と回答します。 システムが再起動されるため、SSH セッションから切断されます。

### <a name="if-necessary-regenerate-ssh-keys"></a>必要に応じて SSH キーを再生成する

> [!IMPORTANT] 
> アップグレードして再起動した後、SSH キーの再生成が必要になる場合があります。

VM をアップグレードし、再起動したら、SSH を介して VM に再度アクセスしてみてください。 再起動中に IP アドレスが変更された可能性があるため、接続を試みる前に、それを確認します。

エラー「**REMOTE HOST IDENTIFICATION HAS CHANGED**」 (リモート ホスト ID が変更されました) が発生した場合は、SSH 資格情報を再生成する必要があります。

:::image type="content" source="media/ubuntu_upgrade/remote-host-warning.png" alt-text="リモート ホスト ID が変更されたという警告を示す PowerShell のスクリーンショット":::

このためには、ローカル マシンで、次のコマンドを実行します。

```bash
ssh-keygen -R "your server hostname or ip"
```

以上で、SSH と接続できるようになります。 依然として問題が解決しない場合は、 **[接続]** ページで、 **[SSH の接続の問題のトラブルシューティング]** へのリンクに従います。

## <a name="side-by-side-migration"></a>横並びの移行

CentOS から移行する場合、またはクリーンな OS インストールが必要な場合、サイドバイサイド移行を行うことができます。 この種類の移行の方が手順は増えますが、どのファイルを引き継ぐかを厳密に制御できます。

同じ一連のアップストリーム ソース パッケージに基づいて他のシステムから移行するのは、比較的簡単です (たとえば、[FAQ/CentOS3](https://wiki.centos.org/FAQ/CentOS3) など)。

ファイル システムのオペレーティング システム部分をアップグレードして、ユーザー ディレクトリ (たとえば、`/home` など) はそのまま残すことを選択できます。 古いユーザー ホーム ディレクトリをそのまま残すと、GNOME/KDE メニューやその他のデスクトップ項目でいくつかの問題が発生する可能性があります。 新しいユーザー アカウントを作成し、移行後にユーザーの資料を参照、コピー、またはリンクするために、古いディレクトリをファイル システム内の別の場所にマウントするのが最も簡単な場合があります。

### <a name="migration-at-a-glance"></a>移行の概要

1.  前に説明したように、既存の VM のスナップショットを作成する

1.  そのスナップショットからディスクを作成する

1.  新しい Ubuntu Data Science Virtual Machine を作成する

1.  新しい仮想マシンでユーザー アカウントを再作成する

1.  スナップショットを作成した VM のディスクをデータ ディスクとして新しい Data Science Virtual Machine にマウントする

1.  必要なデータを手動でコピーする

### <a name="create-a-disk-from-your-vm-snapshot"></a>VM のスナップショットからディスクを作成する

前に説明したように VM のスナップショットをまだ作成していない場合は、作成します。 

1. Azure portal で、 **[ディスク]** を検索し、 **[追加]** を選択します。これにより、 **[ディスク]** ページが開きます。

:::image type="content" source="media/ubuntu_upgrade/portal-disks-search.png" alt-text="[ディスク] ページの検索と [追加] ボタンを示す Azure portal のスクリーンショット":::

2. **[サブスクリプション]** 、 **[リソース グループ]** 、 **[リージョン]** を、お使いの VM スナップショットの値に設定します。 作成するディスクの **[名前]** を選択します。

3. **[ソースの種類]** として **[スナップショット]** を選択し、 **[ソース スナップショット]** として VM のスナップショットを選択します。 ディスクを確認して作成します。 

:::image type="content" source="media/ubuntu_upgrade/disk-create-options.png" alt-text="オプションを示すディスク作成ダイアログのスクリーンショット":::

### <a name="create-a-new-ubuntu-data-science-virtual-machine"></a>新しい Ubuntu Data Science Virtual Machine を作成する

[Azure portal](https://portal.azure.com) または [ARM テンプレート](./dsvm-tutorial-resource-manager.md)を使用して、新しい Ubuntu Data Science Virtual Machine を作成します。 

### <a name="recreate-user-accounts-on-your-new-data-science-virtual-machine"></a>新しい Data Science Virtual Machine でユーザー アカウントを再作成する

古いコンピューターからデータをコピーするだけなので、新しいマシンで使用するユーザー アカウントとソフトウェア環境を再作成する必要があります。

Linux は十分な柔軟性を備えているため、新しいインストールのディレクトリとパスをカスタマイズして、古いマシンに倣うことができます。 しかし、通常は、最新の Ubuntu の推奨レイアウトを使用して、ユーザー環境とスクリプトを変更して適合させる方が簡単です。

詳細については、「[クイック スタート: Linux (Ubuntu) Data Science Virtual Machine を設定する](./dsvm-ubuntu-intro.md)」を参照してください。

### <a name="mount-the-disk-of-the-snapshotted-vm-as-a-data-disk-on-your-new-data-science-virtual-machine"></a>スナップショットを作成した VM のディスクをデータ ディスクとして新しい Data Science Virtual Machine にマウントする

1. Azure portal で、Data Science Virtual Machine が実行されていることを確認します。

2. Azure portal で、Data Science Virtual Machine のページに移動します。 左側のレールで **[ディスク]** を選択します。 **[既存のディスクを接続する]** を選択します。

3. **[ディスク名]** ドロップダウンで、古い VM のスナップショットから作成したディスクを選択します。

:::image type="content" source="media/ubuntu_upgrade/attach-data-disk.png" alt-text="ディスク接続オプションを示す DSVM オプション ページのスクリーンショット":::

4. **[保存]** を選択して、仮想マシンを更新します。

> [!Important]
> データ ディスクを接続するときに VM が実行されている必要があります。 VM が実行されていない場合、正しくない順序でディスクが追加される可能性があり、混乱を招き、システムが起動できなくなる可能性があります。 VM をオフにした状態でデータ ディスクを追加する場合は、データ ディスクの横にある **[X]** を選択し、VM を起動して再接続します。

### <a name="manually-copy-the-wanted-data"></a>必要なデータを手動でコピーする 

1. SSH を使用して、実行中の仮想マシンにサインオンします。

2. 次のコマンドを実行して、古い VM のスナップショットから作成されたディスクが接続されていることを確認します。

    ```bash
    lsblk -o NAME,HCTL,SIZE,MOUNTPOINT | grep -i 'sd'
    ```
    
    結果は次の図のようになります。 この図では、ディスク `sda1` はルートにマウントされており、`sdb2` は `/mnt` スクラッチ ディスクです。 古い VM のスナップショットから作成されたデータ ディスクは `sdc1` として識別されますが、マウント場所が示されていないことからわかるように、まだ使用できません。 実際の結果では識別子が異なる場合もありますが、同様のパターンが表示されます。
    
    :::image type="content" source="media/ubuntu_upgrade/lsblk-results.png" alt-text="マウントされていないデータ ドライブを示す lsblk 出力のスクリーンショット":::
    
3. データ ドライブにアクセスするには、データ ドライブの場所を作成してマウントします。 `/dev/sdc1` を、`lsblk` によって返された適切な値に置き換えます。

    ```bash
    sudo mkdir /datadrive && sudo mount /dev/sdc1 /datadrive
    ```
    
4. これで、`/datadrive` には、古い Data Science Virtual Machine のディレクトリとファイルが含まれます。 目的のディレクトリまたはファイルをデータ ドライブから新しい VM に移動またはコピーします。

詳細については、「[ポータルを利用し、データ ディスクを Linux VM に接続する](../../virtual-machines/linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)」を参照してください。

## <a name="connect-and-confirm-version-upgrade"></a>接続して、バージョンのアップグレードを確認する

インプレース移行またはサイドバイサイド移行のどちらを実行したかにかかわらず、アップグレードが成功したことを確認します。 ターミナル セッションから、次のように実行します。 

```bash
cat /etc/os-release
```

Ubuntu 18.04 が実行されていることがわかります。

:::image type="content" source="media/ubuntu_upgrade/ssh-os-release.png" alt-text="OS のバージョン データを示す Ubuntu ターミナルのスクリーンショット":::

バージョンの変更は、Azure portal にも表示されます。

:::image type="content" source="media/ubuntu_upgrade/portal-showing-os-version.png" alt-text="OS のバージョンを含む DSVM のプロパティを示す portal のスクリーンショット":::

## <a name="next-steps"></a>次の手順

- [Azure での Ubuntu Data Science Virtual Machine を使用したデータ サイエンス](./linux-dsvm-walkthrough.md)
- [Azure Data Science Virtual Machine に含まれるツール](./tools-included.md)