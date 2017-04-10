---
title: "クラシック Azure VM にディスクをアタッチする | Microsoft Docs"
description: "クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチし、初期化します。"
services: virtual-machines-windows, storage
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: be4e3e74-05bc-4527-969f-84f10a1d66a7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 4debed5ceed2c94b2e5b666d6fcebec5667bfda7
ms.lasthandoff: 03/27/2017


---
# <a name="attach-a-data-disk-to-a-windows-virtual-machine-created-with-the-classic-deployment-model"></a>クラシック デプロイ モデルを使用して作成された Windows 仮想マシンにデータ ディスクをアタッチする
<!--
Refernce article:
    If you want to use the new portal, see [How to attach a data disk to a Windows VM in the Azure portal](../../virtual-machines-windows-attach-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
-->

この記事では、Azure Portal を使用して、Azure クラシック デプロイメント モデルによって作成した新しいディスクと既存のディスクを Windows 仮想マシンに接続する方法を説明します。

[Azure Portal で Linux VM にデータ ディスクを接続する](../../virtual-machines-linux-attach-disk-portal.md)こともできます。

ディスクを接続する前に、次のヒントを確認します。

* 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。 詳細については、「 [仮想マシンのサイズ](../../virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

* Premium Storage を使用するには、DS シリーズまたは GS シリーズの仮想マシンが必要です。 これらの仮想マシンでは、Premium および Standard のストレージ アカウントのディスクを使用できます。 Premium Storage は特定のリージョンで使用できます。 詳細については、「 [Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

* 新しいディスクの場合、接続時に Azure で自動的に作成されるので、最初に作成する必要はありません。

[Powershell を使用してデータ ディスクを接続する](../../virtual-machines-windows-attach-disk-ps.md)こともできます。

> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。

## <a name="find-the-virtual-machine"></a>仮想マシンの検索
1. [Azure Portal](https://portal.azure.com/) にサインインします。
2. ダッシュ ボードに表示されているリソースから仮想マシンを選択します。
3. 画面左側の **[設定]** で **[ディスク]** をクリックします。

    ![ディスク設定を開く](./media/attach-disk/virtualmachinedisks.png)

次の手順に従って、[新しいディスク](#option-1-attach-a-new-disk)または[既存のディスク](#option-2-attach-an-existing-disk)を接続します。

## <a name="option-1-attach-and-initialize-a-new-disk"></a>オプション 1: 新しいディスクを接続し、初期化する

1. **[ディスク]** ブレードの **[新しいディスクの接続]** をクリックします。
2. 既定の設定を確認し、必要に応じて更新して、 **[OK]**をクリックします。

   ![ディスク設定を確認する](./media/attach-disk/attach-new.png)

3. Azure でディスクが作成され、仮想マシンに接続されると、仮想マシンのディスク設定の **[データ ディスク]**に新しいディスクが表示されます。

### <a name="initialize-a-new-data-disk"></a>新しいデータ ディスクの初期化

1. 仮想マシンへの接続 手順については、「 [Windows が実行されている Azure 仮想マシンに接続してログオンする方法](../../virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
2. 仮想マシンにログオンした後、 **サーバー マネージャー**を開きます。 左側のウィンドウで、 **[ファイル サービスと記憶域サービス]**を選択します。

    ![サーバー マネージャーを開く](../media/attach-disk-portal/fileandstorageservices.png)

3. **[ディスク]** を選択します。
4. **[ディスク]** セクションにディスクが一覧表示されます。 多くの場合、仮想マシンには、disk 0、disk 1、disk 2 があります。 disk 0 はオペレーティング システム ディスク、disk 1 は一時ディスク、disk 2 は新しく仮想マシンにアタッチしたデータ ディスクです。 データ ディスクではパーティションが **[不明]** として一覧表示されます。

 ディスクを右クリックし、 **[初期化]**を選択します。

5. ディスクの初期化時にすべてのデータが消去されることが通知されます。 **[はい]** をクリックして警告を了解し、ディスクを初期化します。 完了すると、パーティションは **[GPT]**と表示されます。 もう一度ディスクを右クリックし、 **[新しいボリューム]**を選択します。

6. 既定の値を使用してウィザードを完了します。 ウィザードが終了すると、 **[ボリューム]** セクションに新しいボリュームが表示されます。 ディスクがオンラインになり、データを格納できるようになります。

    ![ボリュームの初期化に成功](./media/attach-disk/newdiskafterinitialization.png)

## <a name="option-2-attach-an-existing-disk"></a>オプション 2: 既存のディスクを接続する
1. **[ディスク]** ブレードの **[既存のディスクの接続]** をクリックします。
2. **[既存のディスクの接続]** の **[場所]** をクリックします。

   ![既存のディスクを接続する](./media/attach-disk/attachexistingdisksettings.png)
3. **[ストレージ アカウント]**で、アカウントと、.vhd ファイルが格納されているコンテナーを選択します。

   ![VHD の場所を検索する](./media/attach-disk/existdiskstorageaccountandcontainer.png)

4. .vhd ファイルを選択します。
5. **[既存のディスクの接続]** の **[VHD ファイル]** に、選択したファイルが表示されます。 **[OK]**をクリックします。
6. Azure で仮想マシンにディスクが接続されると、仮想マシンのディスク設定の **[データ ディスク]**にそのディスクが表示されます。

## <a name="use-trim-with-standard-storage"></a>Standard Storage での TRIM の使用

Standard Storage (HDD) を使用する場合は、TRIM を有効にする必要があります。 TRIM はディスク上の未使用のブロックを破棄するため、実際に使用しているストレージにのみ課金されます。 TRIM を使用すると、大きいファイルを削除した結果生じる未使用ブロックなどのコストを削減できます。

次のコマンドを実行すると、TRIM の設定を確認できます。 Windows VM 上でコマンド プロンプトを開いて、次のように入力します。

```
fsutil behavior query DisableDeleteNotify
```

このコマンドが 0 を返す場合、TRIM は適切に有効化されています。 1 が返される場合は、次のコマンドを実行して TRIM を有効にします。
```
fsutil behavior set DisableDeleteNotify 0
```

## <a name="next-steps"></a>次のステップ
アプリケーションで D: ドライブを使用してデータを保存する必要がある場合は、[Windows 一時ディスクのドライブ文字を変更](../../virtual-machines-windows-change-drive-letter.md)できます。

## <a name="additional-resources"></a>その他のリソース
[仮想マシン用のディスクと VHD について](../../virtual-machines-linux-about-disks-vhds.md)

