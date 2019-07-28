---
title: Azure のクラシック ストレージ アカウント、コンテナー、VHD の削除時に発生するエラーのトラブルシューティング | Microsoft Docs
description: 接続された VHD を含むストレージ リソースを削除するときの問題をトラブルシューティングする方法について説明します。
services: storage
author: angshumannayakmsft
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 35f8a766c6d260e23ff854284d5b8ee047e64b42
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "64926150"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>クラシック ストレージ リソース削除エラーのトラブルシューティング
この記事では、Azure のクラシック ストレージ アカウント、コンテナー、*.vhd ページ BLOB ファイルを削除しようとして次のいずれかのエラーが発生した場合のトラブルシューティング ガイダンスを提供します。 


この記事で取り上げるのは、クラシック ストレージ リソースに関する問題のみです。 ユーザーが Azure portal、PowerShell、または CLI を使用してクラシック仮想マシンを削除しても、ディスクが自動的に削除されることはありません。 このユーザーは、"ディスク" リソースを削除するオプションを利用できるようになります。 このオプションが選択されていない場合、"ディスク" リソースにより、ストレージ アカウント、コンテナー、実際の *.vhd ページ BLOB ファイルの削除が妨げられます。

Azure ディスクについて詳しくは、[こちら](../../virtual-machines/windows/managed-disks-overview.md)をご覧ください。 Azure では、破損を防ぐため、VM に接続されているディスクは削除できません。 また、VM にアタッチされているページ BLOB があるコンテナーやストレージ アカウントも削除されません。 

## <a name="what-is-a-disk"></a>"ディスク" とは
"ディスク" リソースは、*.vhd ページ BLOB ファイルを OS ディスクまたはデータ ディスクとして仮想マシンにマウントする場合に使用されます。 OS ディスクまたはデータ ディスク リソースは、削除されるまで、*.vhd ファイルのリースを保持し続けます。 以下の画像で示されているパスに含まれているストレージ リソースは、"ディスク" リソースが指している場合、削除できません。

![ディスク (クラシック) の [プロパティ] ウィンドウが開かれている、ポータルのスクリーンショット](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>クラシック仮想マシンを削除する場合の手順 
1. クラシック仮想マシンを削除します。
2. [ディスク] チェック ボックスがオンになっている場合、ページ BLOB *.vhd に関連付けられた (上の図に示されている) **ディスク リース**は解除されます。 実際のページ BLOB *.vhd ファイルはまだストレージ アカウントに残っています。
![仮想マシン (クラシック) の "削除" エラー ウィンドウが開いている、ポータルのスクリーンショット](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. ディスクのリースが解除されると、ページ BLOB 自体を削除できます。 ストレージ アカウントまたはコンテナーは、それらに存在する "ディスク" リソースがすべて削除されると、削除できます。

>[!NOTE] 
>ユーザーが VM を削除しても VHD を削除しなければ、ページ BLOB の *.vhd ファイルに対して引き続きストレージ料金が発生します。 この料金は、ストレージ アカウントの種類に基づきます。詳細については、[価格のページ](https://azure.microsoft.com/pricing/details/storage/)を参照してください。 ユーザーは VHD を使用する予定がなくなったら、それらを削除してそれ以上課金されないようにしてください。 

## <a name="unable-to-delete-storage-account"></a>ストレージ アカウントを削除できない 

ユーザーが必要なくなったクラシック ストレージ アカウントを削除しようとすると、次の動作に気付く場合があります。

#### <a name="azure-portal"></a>Azure ポータル 
ユーザーが [Azure portal](https://portal.azure.com) でクラシック ストレージ アカウントに移動し、 **[削除]** をクリックすると、ユーザーには、次のメッセージが表示されます。 

仮想マシンにディスクが "アタッチされている" 場合

![仮想マシン (クラシック) の "削除" エラー ウィンドウが開いている、ポータルのスクリーンショット](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


仮想マシンにディスクが "アタッチされていない" 場合

![仮想マシン (クラシック) の "削除" エラーのないウィンドウが開いている、ポータルのスクリーンショット](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
ユーザーは、クラシック PowerShell コマンドレットを使用して、もう使用されていないストレージ アカウントを削除しようとします。 ユーザーには、次のメッセージが表示されます。

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: ストレージ アカウント myclassicaccount に、アクティブなイメージやディスクがあります (例:  
> myclassicaccount)。 このストレージ アカウントを削除する前に、これらのイメージやディスクを削除してください。</span>

## <a name="unable-to-delete-storage-container"></a>ストレージ コンテナーを削除できない

ユーザーが不要になったクラシック ストレージ BLOB コンテナーを削除しようとすると、次の動作に気付く場合があります。

#### <a name="azure-portal"></a>Azure ポータル 
Azure portal では、コンテナー内の *.vhd ページ BLOB ファイルを指す "ディスク" のリースが存在するため、ユーザーはそのコンテナーを削除できません。 仕様により、ディスクのリースを保持している vhd ファイルは誤って削除されないようになっています。 

![ストレージ コンテナー "リスト" ウィンドウが開いている、ポータルのスクリーンショット](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
ユーザーは、PowerShell を使用した削除を選択すると、結果として次のエラーが発生します。 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: リモート サーバーからエラーが返される:(412) There is currently a lease on the container and no lease ID was specified in the request. (現在、コンテナーにリースがありますが、リクエストでリース ID が指定されていませんでした。)HTTP 状態コード: 412 - HTTP エラー メッセージ: There is currently a lease on the container and no lease ID was specified in the request. (現在、コンテナーにリースがありますが、リクエストでリース ID が指定されていませんでした。)</span>

## <a name="unable-to-delete-a-vhd"></a>vhd を削除できない 

Azure の仮想マシンを削除した後、ユーザーが vhd ファイル (ページ BLOB) を削除しようとすると、次のメッセージが表示されます。

#### <a name="azure-portal"></a>Azure ポータル 
ポータルでは、削除対象として選択されている BLOB のリストに応じて、2 つのエクスペリエンスが生じる場合があります。

1. "リース中" の BLOB のみが選択されている場合、[削除] ボタンは表示されません。
![コンテナー BLOB の "リスト" ウィンドウが開いている、ポータルのスクリーンショット](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. "リース中" の BLOB と "使用可能な" BLOB の両方が選択されている場合は、[削除] ボタンが表示されます。 ただし、"削除" 操作では、ページ BLOB が残り、それらに対してディスクのリースが発生しています。 
![コンテナー BLOB "リスト" ウィンドウが開いている、ポータルのスクリーンショット](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![選択した blob をポータルのスクリーン ショット"delete"ウィンドウを開く](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
ユーザーは、PowerShell を使用した削除を選択すると、結果として次のエラーが発生します。 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"** </span>
> 
> <span style="color:red">Remove-AzureStorageBlob : リモート サーバーからエラーが返される:(412) There is currently a lease on the blob and no lease ID was specified in the request. (現在、BLOB にリースがありますが、リクエストでリース ID が指定されていませんでした。)HTTP 状態コード: 412 - HTTP エラー メッセージ: There is currently a lease on the blob and no lease ID was specified in the request. (現在、BLOB にリースがありますが、リクエストでリース ID が指定されていませんでした。)</span>


## <a name="resolution-steps"></a>解決手順

### <a name="to-remove-classic-disks"></a>クラシック ディスクを削除するには
Azure portal で次の手順に従います。
1.  [Azure Portal](https://portal.azure.com) に移動します。
2.  [ディスク (クラシック)] に移動します。 
3.  [ディスク] タブをクリックします。![コンテナー BLOB の "リスト" ウィンドウが開いている、ポータルのスクリーンショット](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  データ ディスクを選択し、[ディスクの削除] をクリックします。
 ![コンテナー BLOB の "リスト" ウィンドウが開いている、ポータルのスクリーンショット](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  前に失敗した削除操作を再試行します。
6.  ストレージ アカウントまたはコンテナーは、1 つのディスクが含まれている限り、削除できません。

### <a name="to-remove-classic-images"></a>クラシック イメージを削除するには   
Azure portal で次の手順に従います。
1.  [Azure Portal](https://portal.azure.com) に移動します。
2.  OS イメージ (クラシック) に移動します。
3.  イメージを削除します。
4.  前に失敗した削除操作を再試行します。
5.  ストレージ アカウントまたはコンテナーは、1 つのイメージが含まれている限り、削除できません。
