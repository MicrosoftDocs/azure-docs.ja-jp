---
title: "Azure ストレージ アカウント、コンテナー、または VHD を削除するときに生じるエラーのトラブルシューティング | Microsoft Docs"
description: "Azure ストレージ アカウント、コンテナー、または VHD を削除するときに生じるエラーのトラブルシューティング"
services: storage
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/13/2017
ms.author: genli
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 11944dd38b1cc30106c0b76a108480c018ca39d4
ms.contentlocale: ja-jp
ms.lasthandoff: 08/22/2017

---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds"></a>Azure ストレージ アカウント、コンテナー、または VHD を削除するときに生じるエラーのトラブルシューティング

[Azure Portal](https://portal.azure.com) で Azure ストレージ アカウント、コンテナー、または仮想ハード ディスク (VHD) を削除しようとすると、エラーが表示される場合があります。 この記事は、Azure Resource Manager デプロイメントにおける問題を解決するのに役立つトラブルシューティング ガイダンスです。

この記事で Azure の問題に対処できない場合は、 [MSDN と Stack Overflow ](https://azure.microsoft.com/support/forums/) の Azure フォーラムを参照してください。 問題をこれらのフォーラムまたは Twitter の @AzureSupport に投稿できます。 また、 **Azure サポート** サイトの [[サポートの要求]](https://azure.microsoft.com/support/options/) を選択して、Azure サポート要求を提出することもできます。

## <a name="symptoms"></a>現象
### <a name="scenario-1"></a>シナリオ 1
Resource Manager デプロイメントでストレージ アカウントの VHD を削除しようとすると、次のようなエラー メッセージが表示される。

**BLOB 'vhds/BlobName.vhd' を削除できませんでした。エラー: There is currently a lease on the blob and no lease ID was specified in the request. (現在、BLOB にリースがありますが、リクエストでリース ID が指定されていませんでした。)**

この問題は、削除しようとしている VHD に仮想マシン (VM) がリースを保持していることが原因で発生する場合があります。

### <a name="scenario-2"></a>シナリオ 2
Resource Manager デプロイメントでストレージ アカウントのコンテナーを削除しようとすると、次のようなエラー メッセージが表示される。

**ストレージ コンテナー 'vhds' を削除できませんでした。エラー: There is currently a lease on the container and no lease ID was specified in the request. (現在、コンテナーにリースがありますが、リクエストでリース ID が指定されていませんでした。)**

この問題は、コンテナーにリース状態でロックされている VHD が格納されていることが原因で発生する場合があります。

### <a name="scenario-3"></a>シナリオ 3
Resource Manager デプロイメントでストレージ アカウントを削除しようとすると、次のようなエラー メッセージが表示される。

**ストレージ アカウント 'StorageAccountName' を削除できませんでした。エラー: ストレージ アカウントのアーティファクトが使用中であるため、このストレージ アカウントを削除できません。**

この問題は、ストレージ アカウントにリース中の VHD が格納されていることが原因で発生する場合があります。

## <a name="solution"></a>解決策 
これらの問題を解決するには、エラーの原因である VHD と、関連付けられている VM を特定する必要があります。 次に、VM から VHD を切断するか (データ ディスクの場合)、VHD を使用している VM を削除します (OS ディスクの場合)。 この操作により、VHD からリースを削除し、VHD を削除できるようになります。 

これを行うには、次のいずれかの方法を使用します。

### <a name="method-1---use-azure-storage-explorer"></a>方法 1: Azure ストレージ エクスプローラーを使用する

### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>手順 1. ストレージ アカウントの削除を妨げている VHD を識別する

1. ストレージ アカウントを削除するときに、次のようなメッセージ ダイアログが表示されます。 

    ![ストレージ アカウントの削除時のメッセージ](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. **[ディスク URL]** を調べて、ストレージ アカウントとストレージ アカウントの削除を妨げている VHD を識別します。 次の例では、".blob.core.windows.net" の直前の文字列がストレージ アカウント名であり、"SCCM2012-2015-08-28.vhd" が VHD 名です。  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

### <a name="step-2-delete-the-vhd-by-using-azure-storage-explorer"></a>手順 2. Azure ストレージ エクスプ ローラーを使用して VHD を削除する

1. 最新バージョンの [Azure ストレージ エクスプローラー](http://storageexplorer.com/) をダウンロードしてインストールします。 このツールは、Windows、macOS、および Linux で Azure Storage のデータを簡単に操作できる Microsoft のスタンドアロン アプリです。
2. Azure ストレージ エクスプローラーを開き ![左側のバーで [アカウント] アイコンを選択し、](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/account.png) Azure 環境を選択してサインインします。

3. すべてのサブスクリプションを選択するか、削除するストレージ アカウントを含むサブスクリプションを選択します。

    ![サブスクリプションの追加](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/addsub.png)

4. 先ほどディスク URL で見つけたストレージ アカウントに移動し、**[BLOB コンテナー]** > **[VHD]** を選択し、ストレージ アカウントの削除を妨げている VHD を探します。
5. VHD が見つかったら、**[VM 名]** を調べて、この VHD を使用している VM を見つけます。

    ![VM を調べる](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/check-vm.png)

6. Azure ポータルを使用して、VHD からリースを削除します。 詳細については、「[VHD からリースを削除する](#remove-the-lease-from-the-vhd)」を参照してください。 

7. Azure ストレージ エクスプローラーに移動し、VHD を右クリックし、[削除] を選択します。

8. ストレージ アカウントを削除します。

### <a name="method-2---use-azure-portal"></a>方法 2: Azure ポータルを使用する 

#### <a name="step-1-identify-the-vhd-that-prevent-deletion-of-the-storage-account"></a>手順 1. ストレージ アカウントの削除を妨げている VHD を識別する

1. ストレージ アカウントを削除するときに、次のようなメッセージ ダイアログが表示されます。 

    ![ストレージ アカウントの削除時のメッセージ](././media/storage-resource-manager-cannot-delete-storage-account-container-vhd/delete-storage-error.png) 

2. **[ディスク URL]** を調べて、ストレージ アカウントとストレージ アカウントの削除を妨げている VHD を識別します。 次の例では、".blob.core.windows.net" の直前の文字列がストレージ アカウント名であり、"SCCM2012-2015-08-28.vhd" が VHD 名です。  

        https://portalvhds73fmhrw5xkp43.blob.core.windows.net/vhds/SCCM2012-2015-08-28.vhd

2. [Azure ポータル](https://portal.azure.com)にサインインします。
3. ハブ メニューで、**[すべてのリソース]** を選択します。 ストレージ アカウントに移動し、**[BLOB]** > **[VHD]** を選択します。

    ![ストレージ アカウントと "vhds" コンテナーが強調表示されている、Portal のスクリーンショット](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)

4. 先ほどディスク URL で見つけた VHD を探します。 次に、この VHD を使用している VM を特定します。 通常、VHD の名前を確認することで、VHD を保持する VM を特定できます。

Resource Manager デプロイ モデル内の VM

   * 通常、OS ディスクは次の命名規則に従っています。VMName-YYYY-MM-DD-HHMMSS.vhd
   * 通常、データ ディスクは次の命名規則に従っています。VMName-YYYY-MM-DD-HHMMSS.vhd

クラシック デプロイ モデル内の VM

   * 通常、OS ディスクは次の命名規則に従っています。CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd
   * 通常、データ ディスクは次の命名規則に従っています。CloudServiceName-VMName-YYYY-MM-DD-HHMMSS.vhd

#### <a name="step-2-remove-the-lease-from-the-vhd"></a>手順 2. VHD からリースを削除する

[VHD からリースを削除](#remove-the-lease-from-the-vhd)した後、ストレージ アカウントを削除します。

## <a name="what-is-a-lease"></a>リースとは
リースは、BLOB (VHD など) へのアクセスを制御するのに使用できるロックです。 BLOB がリースされている場合、リースの所有者だけがその BLOB にアクセスできます。 リースが重要である理由を次に示します。

* 複数の所有者が BLOB の同じ部分に同時に書き込もうとした場合に、データが破損するのを防ぎます。
* VM などによって BLOB が実際に使用されている場合に、BLOB が削除されるのを防ぎます。
* VM などによってストレージ アカウントが実際に使用されている場合に、ストレージ アカウントが削除されるのを防ぎます。

### <a name="remove-the-lease-from-the-vhd"></a>VHD からリースを削除する
VHD が OS ディスクの場合、リースを削除するには VM を削除する必要があります。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **ハブ** メニューで **[Virtual Machines]** をクリックします。
3. VHD にリースを保持する VM を選択します。
4. 該当する仮想マシンを実際に使用していないこと、今後も必要としないことを確認してください。
5. **[VM details (VM の詳細)]** ブレードの上部で、**[削除]** を選択し、**[はい]** をクリックして確定します。
6. VM は削除されますが、VHD は保持できます。 ただし、VHD のリースは解除されます。 リースが解除されるまで数分かかる場合があります。 リースが解除されたかどうかを確認するには、**[すべてのリソース]** > **[ストレージ アカウント名]** > **[BLOB]** > **[VHD]** を選択します。 **[BLOB のプロパティ]** ウィンドウで、**[リース ステータス]** の値が **[ロック解除]** になっている必要があります。

VHD がデータ ディスクの場合、リースを削除するには VHD を VM からデタッチします。

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **ハブ** メニューで **[Virtual Machines]** をクリックします。
3. VHD にリースを保持する VM を選択します。
4. **[VM details (VM の詳細)]** ブレードで **[ディスク]** を選択します。
5. VHD にリースを保持するデータ ディスクを選択します。 VHD の URL を確認することで、ディスクに接続している VHD を特定できます。
6. このデータ ディスクが実際に使用されていないことを確認してください。
7. **[ディスクの詳細]** ブレードで、**[切断]** をクリックします。
8. ディスクが VM から切断され、VHD のリースは解除されます。 リースが解除されるまで数分かかる場合があります。 リースが解除されたかどうかを確認するには、**[すべてのリソース]** > **[ストレージ アカウント名]** > **[BLOB]** > **[VHD]** を選択します。 **[BLOB のプロパティ]** ウィンドウで、**[リース ステータス]** の値が **[ロック解除]** になっている必要があります。

## <a name="next-steps"></a>次のステップ
* [ストレージ アカウントの削除](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Microsoft Azure (PowerShell) で BLOB ストレージのロックされているリースを解除する方法)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

