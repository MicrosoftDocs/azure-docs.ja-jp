---
title: Azure における Linux VM でのストレージ リソースの削除エラーをトラブルシューティングする | Microsoft Docs
description: 接続された VHD を含むストレージ リソースを削除するときの問題をトラブルシューティングする方法について説明します。
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 50ab4b0f1e676ffcba0ce69ab6aa957e4c77ab88
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058163"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>ストレージ リソースの削除エラーのトラブルシューティング

このシナリオでは、Azure Resource Manager デプロイで Azure ストレージ アカウント、コンテナー、または BLOB を削除しようとしたときに、次のいずれかのエラーが発生する場合があります。

> **ストレージ アカウント 'StorageAccountName' を削除できませんでした。エラー:ストレージ アカウントのアーティファクトが使用中であるため、このストレージ アカウントを削除できません。**
> 
> **# 個のコンテナー (# 個のコンテナーのうち) を削除できませんでした:<br>vhds:There is currently a lease on the container and no lease ID was specified in the request. (現在、コンテナーにリースがありますが、リクエストでリース ID が指定されていませんでした。)**
> 
> **# 個の BLOB (# 個の BLOB のうち) を削除できませんでした:<br>BlobName.vhd:There is currently a lease on the blob and no lease ID was specified in the request. (現在、BLOB にリースがありますが、リクエストでリース ID が指定されていませんでした。)**

Azure VM で使用される VHD は .vhd ファイルです。Azure では Standard または Premium Storage アカウントでページ BLOB としてこれらを格納します。 Azure ディスクについて詳しくは、「[マネージド ディスクの概要](../linux/managed-disks-overview.md)」をご覧ください。

Azure では、破損を防ぐため、VM に接続されているディスクは削除できません。 また、VM に接続されているページ BLOB があるコンテナーおよびストレージ アカウントも削除できません。 

これらのエラーのいずれかが発生したときに、ストレージ アカウント、コンテナー、または BLOB を削除するプロセスは次のとおりです。 
1. VM に接続されている BLOB を特定します
2. [**OS ディスク**が接続されている VM を削除します](#step-2-delete-vm-to-detach-os-disk)
3. [残りの VM から**データ ディスク**をすべて切断します](#step-3-detach-data-disk-from-the-vm)

これらの手順が完了した後、ストレージ アカウント、コンテナー、または BLOB の削除をもう一度試みます。

## <a name="step-1-identify-blob-attached-to-a-vm"></a>手順 1:VM に接続されている BLOB を特定します

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>シナリオ 1:BLOB の削除 – 接続されている VM を特定する
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ハブ メニューで、 **[すべてのリソース]** を選択します。 ストレージ アカウントに移動し、 **[Blob service]** で **[コンテナー]** を選んで、削除する BLOB に移動します。
3. BLOB の **[リース状態]** が **[リース中]** の場合は、右クリックし、 **[メタデータの編集]** を選んで、[BLOB のメタデータ] ウィンドウを開きます。 

    ![ストレージ アカウント BLOB および右クリックの [メタデータの編集] が強調表示されているポータルのスクリーンショット](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. [BLOB のメタデータ] ウィンドウで、**MicrosoftAzureCompute_VMName** の値を確認して記録します。 この値は、VHD が接続されている VM の名前です (このフィールドが存在しない場合は、後の「**重要**」をご覧ください)。
5. [BLOB のメタデータ] ウィンドウで、**MicrosoftAzureCompute_DiskType** の値を確認して記録します。 この値は、接続されているディスクが OS ディスクかデータ ディスクかを示します (このフィールドが存在しない場合は、後の「**重要**」をご覧ください)。 

     ![ストレージの [BLOB のメタデータ] ウィンドウが開かれている Portal のスクリーンショット](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. BLOB ディスクの種類が **OSDisk** の場合は、「[ステップ 2:VM を削除して OS ディスクを切断する](#step-2-delete-vm-to-detach-os-disk)」に従ってください。 そうでない場合は、BLOB ディスクの種類が **DataDisk** の場合、「[ステップ 3:データ ディスクを VM から切断する](#step-3-detach-data-disk-from-the-vm)」に従ってください。 

> [!IMPORTANT]
> **MicrosoftAzureCompute_VMName** および **MicrosoftAzureCompute_DiskType** が BLOB のメタデータに表示されない場合は、BLOB が明示的にリースされていて、VM に接続されていないことを示します。 リースされた BLOB は、先にリースを解約してからでないと削除できません。 リースを解約するには、BLOB を右クリックして、 **[リースの解約]** を選びます。 リースされた BLOB が VM に接続されていない場合、BLOB を削除することはできませんが、コンテナーまたはストレージ アカウントは削除できます。

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>シナリオ 2:コンテナーの削除 - コンテナー内で VM に接続されている BLOB をすべて特定する
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ハブ メニューで、 **[すべてのリソース]** を選択します。 ストレージ アカウントに移動し、 **[Blob service]** で **[コンテナー]** を選んで、削除するコンテナーを探します。
3. コンテナーをクリックして開くと、その中にある BLOB の一覧が表示されます。 [BLOB の種類] が **[ページ BLOB]** で [リース状態] が **[リース中]** であるすべての BLOB を一覧から探します。 「シナリオ 1」に従って、これらの各 BLOB に関連付けられている VM を特定します。

    ![ストレージ アカウント BLOB と "リース中" の "リース状態" が強調表示されている、Portal のスクリーンショット](./media/troubleshoot-vhds/utd-disks-sm.png)

4. 「[ステップ 2](#step-2-delete-vm-to-detach-os-disk)」と「[ステップ 3](#step-3-detach-data-disk-from-the-vm)」に従って、**OSDisk** の VM を削除し、**DataDisk** を切断します。 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>シナリオ 3:ストレージ アカウントの削除 - ストレージ アカウント内で VM に接続されている BLOB をすべて特定する
1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ハブ メニューで、 **[すべてのリソース]** を選択します。 ストレージ アカウントに移動し、 **[Blob service]** で **[BLOB]** を選びます。
3. **[コンテナー]** ウィンドウで、 **[リース状態]** が **[リース中]** であるすべてのコンテナーを特定し、各**リース中**コンテナーについて[シナリオ 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) に従います。
4. 「[ステップ 2](#step-2-delete-vm-to-detach-os-disk)」と「[ステップ 3](#step-3-detach-data-disk-from-the-vm)」に従って、**OSDisk** の VM を削除し、**DataDisk** を切断します。 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>手順 2:VM を削除して OS ディスクを切断する
VHD が OS ディスクの場合は、接続された VHD を削除する前に、VM を削除する必要があります。 以下の手順を完了した後、同じ VM に接続されているデータ ディスクについては何も行う必要はありません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ハブ メニューで **[仮想マシン]** を選びます。
3. VHD が接続されている VM を選びます。
4. 該当する仮想マシンを実際に使用していないこと、今後も必要としないことを確認してください。
5. **[仮想マシンの詳細]** ウィンドウの上部で **[削除]** を選び、 **[はい]** をクリックして確定します。
6. VM は削除されますが、VHD は保持できます。 ただし、VHD は VM に接続されなくなり、リースを解除されます。 リースが解除されるまで数分かかる場合があります。 リースが解除されたことを確認するには、BLOB の場所の **[BLOB のプロパティ]** ウィンドウで、 **[リース ステータス]** が **[利用可能]** になっていることを確認します。

## <a name="step-3-detach-data-disk-from-the-vm"></a>手順 3:データ ディスクを VM から切断する
VHD がデータ ディスクの場合、リースを削除するには VHD を VM からデタッチします。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ハブ メニューで **[仮想マシン]** を選びます。
3. VHD が接続されている VM を選びます。
4. **[仮想マシンの詳細]** ウィンドウで、 **[ディスク]** を選びます。
5. VHD が接続されている削除するデータ ディスクを選びます。 VHD の URL を調べることで、ディスクで接続されている BLOB を特定できます。
6. BLOB の場所は、ディスクをクリックして **[VHD の URI]** フィールドのパスを調べることで確認できます。
7. **[ディスク]** ウィンドウの上部の **[編集]** を選びます。
8. 削除するデータ ディスクの**切断アイコン**をクリックします。

     ![ストレージの [BLOB のメタデータ] ウィンドウが開かれている Portal のスクリーンショット](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. **[保存]** を選択します。 これでディスクが VM からデタッチされ、VHD のリースが解除されます。 リースが解除されるまで数分かかる場合があります。 リースが解除されたことを確認するには、BLOB の場所の **[BLOB のプロパティ]** ウィンドウで、 **[リース ステータス]** の値が **[ロック解除]** または **[利用可能]** になっていることを確認します。

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

