---
title: "Resource Manager デプロイメントで Azure ストレージ アカウント、コンテナー、または VHD を削除するときに生じるエラーのトラブルシューティング | Microsoft Docs"
description: "Resource Manager デプロイメントで Azure ストレージ アカウント、コンテナー、または VHD を削除するときに生じるエラーのトラブルシューティング"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 75d5e82e2f4e747747f24376239e23f6512f916a


---
# <a name="troubleshoot-errors-when-you-delete-azure-storage-accounts-containers-or-vhds-in-a-resource-manager-deployment"></a>Resource Manager デプロイメントで Azure ストレージ アカウント、コンテナー、または VHD を削除するときに生じるエラーのトラブルシューティング
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

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

### <a name="step-1-identify-the-problem-vhd-and-the-associated-vm"></a>手順 1. 問題の VHD および関連付けられている VM を特定する
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **ハブ** メニューで、**[すべてのリソース]** を選択します。 削除するストレージ アカウントに移動し、**[BLOB]** > **[VHD]** を選択します。

    ![ストレージ アカウントと "vhds" コンテナーが強調表示されている、Portal のスクリーンショット](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/opencontainer.png)
3. コンテナー内の各 VHD のプロパティを確認します。 状態が **[リース中]** の VHD を見つけます。 次に、この VHD を使用している VM を特定します。 通常、VHD の名前を確認することで、VHD を保持する VM を特定できます。

   * OS ディスクは通常、"VMNameYYYYMMDDHHMMSS.vhd" という名前付け規則に従います。
   * データ ディスクは通常、"VMName-YYYYMMDD-HHMMSS.vhd" という名前付け規則に従います。

     ![VM の名前、[ロック済み] のリース ステータス、[リース中] のリース状態が強調表示されている、Portal のコンテナー情報のスクリーンショット](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/locatevm.png)

### <a name="step-2-remove-the-lease-from-the-vhd"></a>手順 2. VHD からリースを削除する
VHD を使用している VM を削除するには (OS ディスクの場合):

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **ハブ** メニューで **[Virtual Machines]** をクリックします。
3. VHD にリースを保持する VM を選択します。
4. 該当する仮想マシンを実際に使用していないこと、今後も必要としないことを確認してください。
5. **[VM details (VM の詳細)]** ブレードの上部で、**[削除]** を選択し、**[はい]** をクリックして確定します。
6. VM は削除されますが、VHD は保持されます。 ただし、VHD のリースは解除されます。 リースが解除されるまで数分かかる場合があります。 リースが解除されたかどうかを確認するには、**[すべてのリソース]** > **[ストレージ アカウント名]** > **[BLOB]** > **[VHD]** を選択します。 **[BLOB のプロパティ]** ウィンドウで、**[リース ステータス]** の値が **[ロック解除]** になっている必要があります。

VHD を使用している VM から VHD を切断するには (データ ディスクの場合):

1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. **ハブ** メニューで **[Virtual Machines]** をクリックします。
3. VHD にリースを保持する VM を選択します。
4. **[VM details (VM の詳細)]** ブレードで **[ディスク]** を選択します。
5. VHD にリースを保持するデータ ディスクを選択します。 VHD の URL を確認することで、ディスクに接続している VHD を特定できます。
6. このデータ ディスクが実際に使用されていないことを確認してください。
7. **[ディスクの詳細]** ブレードで、**[切断]** をクリックします。
8. ディスクが VM から切断され、VHD のリースは解除されます。 リースが解除されるまで数分かかる場合があります。 リースが解除されたかどうかを確認するには、**[すべてのリソース]** > **[ストレージ アカウント名]** > **[BLOB]** > **[VHD]** を選択します。 **[BLOB のプロパティ]** ウィンドウで、**[リース ステータス]** の値が **[ロック解除]** になっている必要があります。

## <a name="what-is-a-lease"></a>リースとは
リースは、BLOB (VHD など) へのアクセスを制御するのに使用できるロックです。 BLOB がリースされている場合、リースの所有者だけがその BLOB にアクセスできます。 リースが重要である理由を次に示します。

* 複数の所有者が BLOB の同じ部分に同時に書き込もうとした場合に、データが破損するのを防ぎます。
* VM などによって BLOB が実際に使用されている場合に、BLOB が削除されるのを防ぎます。
* VM などによってストレージ アカウントが実際に使用されている場合に、ストレージ アカウントが削除されるのを防ぎます。

## <a name="next-steps"></a>次のステップ
* [ストレージ アカウントの削除](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Microsoft Azure (PowerShell) で BLOB ストレージのロックされているリースを解除する方法)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)



<!--HONumber=Nov16_HO3-->


