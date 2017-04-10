---
title: "クラシック デプロイメントでの Azure ストレージ アカウント、コンテナー、VHD の削除に関するトラブルシューティング | Microsoft Docs"
description: "クラシック デプロイメントでの Azure ストレージ アカウント、コンテナー、VHD の削除に関するトラブルシューティング"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: tysonn
tags: storage
ms.assetid: 0f7a8243-d8dc-432a-9d37-1272a0cb3a5c
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: d3d59e19ff654a953be43706dce926c4450c6179
ms.openlocfilehash: 6493230295dbfc939df3daf8504a7d8662083f51
ms.lasthandoff: 02/08/2017


---
# <a name="troubleshoot-deleting-azure-storage-accounts-containers-or-vhds-in-a-classic-deployment"></a>クラシック デプロイメントでの Azure ストレージ アカウント、コンテナー、VHD の削除に関するトラブルシューティング
[!INCLUDE [storage-selector-cannot-delete-storage-account-container-vhd](../../includes/storage-selector-cannot-delete-storage-account-container-vhd.md)]

[Azure Portal](https://portal.azure.com/) または [Azure クラシック ポータル](https://manage.windowsazure.com/)で Azure ストレージ アカウント、コンテナー、または VHD を削除しようとすると、エラーが表示される場合があります。 問題は、次の状況で発生する可能性があります。

* バーチャル マシンを削除すると、ディスクと VHD は自動的に削除されません。 これがストレージ アカウントの削除に失敗する理由である場合があります。 ディスクを別の VM にマウントできるように、マイクロソフトはディスクを削除しません。
* ディスクまたはディスクに関連付けられている BLOB のリースがまだ存在します。
* BLOB、コンテナー、またはストレージ アカウントを使用している VM イメージがまだ存在します。

この記事で Azure の問題に対処できない場合は、 [MSDN と Stack Overflow の Azure フォーラム](https://azure.microsoft.com/support/forums/)を参照してください。 問題をこれらのフォーラムまたは Twitter の @AzureSupport に投稿できます。 また、 **Azure サポート** サイトの [[サポートの要求]](https://azure.microsoft.com/support/options/) を選択して、Azure サポート要求を提出することもできます。

## <a name="symptoms"></a>現象
Azure ストレージ アカウント、コンテナー、または VHD を削除しようとするきに表示される可能性がある一般的なエラーを次のセクションに示します。

### <a name="scenario-1-unable-to-delete-a-storage-account"></a>シナリオ 1: ストレージ アカウントを削除できない
[Azure Portal](https://portal.azure.com/) で従来のストレージ アカウントに移動して **[削除]** を選択したときに、ストレージ アカウントの削除を妨げるオブジェクトの一覧が表示されることがあります。

  ![Image of error when delete the Storage account](./media/storage-cannot-delete-storage-account-container-vhd/newerror.png)

[Azure クラシック ポータル](https://manage.windowsazure.com/) のストレージ アカウントに移動して **[削除]** を選択すると、次のいずれかのエラーが表示される場合があります。

- *ストレージ アカウント StorageAccountName には、VM イメージが含まれています。このストレージ アカウントを削除する前に、これらの VM イメージを削除してください。*

- *ストレージ アカウント <vm-storage-account-name> を削除できませんでした。ストレージ アカウント <vm-storage-account-name> を削除できません。ストレージ アカウント <vm-storage-account-name> に、アクティブなイメージやディスクがあります。このストレージ アカウントを削除する前に、これらのイメージやディスクを削除してください。*

- *ストレージ アカウント <vm-storage-account-name> に、アクティブなイメージやディスクがあります (例: xxxxxxxxx- xxxxxxxxx-O-209490240936090599)。このストレージ アカウントを削除する前に、これらのイメージやディスクを削除してください。*

- *ストレージ アカウント <vm-storage-account-name> には、アクティブなイメージやディスク アーティファクトが含まれている 1 個のコンテナーがあります。このストレージ アカウントを削除する前に、これらのアーティファクトをイメージ リポジトリから削除してください*。

- *送信失敗ストレージ アカウント <vm-storage-account-name> には、アクティブなイメージやディスク アーティファクトが含まれている 1 個のコンテナーがあります。このストレージ アカウントを削除する前に、これらのアーティファクトをイメージ リポジトリから削除してください。ストレージ アカウントを削除しようとした際に、それに関連付けられているアクティブなディスクがまだ存在している場、削除する必要があるアクティブなディスクがあることを示すメッセージが表示されます*。

### <a name="scenario-2-unable-to-delete-a-container"></a>シナリオ 2: コンテナーを削除できない
ストレージ コンテナーを削除しようとしたときに、次のエラーが表示される場合があります。

*ストレージ コンテナー <container name> を削除できませんでした。エラー: 現在、コンテナーにリースがありますが、リクエストでリース ID が指定されていませんでした*。

または

"*次の仮想マシンのディスクでこのコンテナーの BLOB を使用するため、このコンテナーは削除できません: VirtualMachineDiskName1, VirtualMachineDiskName2, ...*"

### <a name="scenario-3-unable-to-delete-a-vhd"></a>シナリオ 3: VHD を削除できない
VM を削除してから、関連する VHD の BLOB を削除しようと、次のメッセージが表示される場合があります。

*BLOB 'path/XXXXXX-XXXXXX-os-1447379084699.vhd' を削除できませんでした。エラー: 現在、BLOB にリースがありますが、リクエストでリース ID が指定されていませんでした。*

または

"*BLOB ‘BlobName.vhd’ は仮想マシンのディスク ‘VirtualMachineDiskName’ として使用中のため、削除できません。*"

## <a name="solution"></a>解決策
最も一般的な問題を解決するには、次の方法をお試しください。

### <a name="step-1-delete-any-disks-that-are-preventing-deletion-of-the-storage-account-container-or-vhd"></a>手順 1: ストレージ アカウント、コンテナー、または VHD の削除を妨げているディスクを削除します。
1. [Azure クラシック ポータル](https://manage.windowsazure.com/)に切り替えます。
2. **[仮想マシン]** > **[ディスク]** を選択します。

    ![Azure クラシック ポータルでのバーチャル マシン上のディスクのイメージ。](./media/storage-cannot-delete-storage-account-container-vhd/VMUI.png)
3. 削除するストレージ アカウント、コンテナー、または VHD に関連付けられているディスクを検索します。 ディスクの場所をチェックすると、関連付けられたストレージ アカウント、コンテナー、または VHD が表示されます。

    ![Azure クラシック ポータルでディスクの場所情報を示すイメージ](./media/storage-cannot-delete-storage-account-container-vhd/DiskLocation.png)
4. 次のいずれかの方法を使ってディスクを削除します。

  - ディスクの **[接続先]** フィールドに VM がない場合は、ディスクを直接削除できます。

  - ディスクがデータ ディスクの場合は、次の手順に従います。

    1. ディスクが接続されている VM の名前を確認します。
    2. **[仮想マシン]** > **[インスタンス]** の順に移動して VM を見つけます。
    3. ディスクが現在使用されていないことを確認します。
    4. ポータルの下部にある **[ディスクの切断]** を選択してディスクを切断します。
    5. **[仮想マシン]** > **[ディスク]** の順に移動して、**[接続先]** フィールドが空白になるまで待ちます。 これは、ディスクが VM から正常に切断されたことを示します。
    6. **[仮想マシン]** > **[ディスク]** の順に移動し、下部にある **[削除]** を選択してディスクを削除します。

  - ディスクが OS ディスクであり (**[OS を含む]** フィールドの表示が Windows などになっている)、VM に接続されている場合は、次の手順に従って VM を削除します。 OS ディスクを切断できないため、VM を削除してリースを解放する必要があります。

    1. データ ディスクの接続先の仮想マシンの名前を確認します。  
    2. **[仮想マシン]** > **[インスタンス]** の順に移動して、ディスクの接続先の VM を選択します。
    3. 該当する仮想マシンを実際に使用していないこと、今後も必要としないことを確認してください。
    4. ディスクの接続先の VM を選択してから、**[削除]** > **[接続されたディスクの削除]** の順に選択します。
    5. **[仮想マシン]** > **[ディスク]** の順に移動して、ディスクが表示されなくなるまで待ちます。  表示されなくなるまでに少し時間がかかり、ページを更新することが必要になる場合があります。
    6. ディスクが非表示にならない場合は、**[接続先]** フィールドが空白になるまで待ちます。 これは、ディスクが VM から完全に切断されたことを示します。  その後、ディスクを選択し、ページの下部にある **[削除]** を選択してディスクを削除します。


   > [!NOTE]
   > ディスクが VM に接続されている場合は、ディスクを削除できません。 削除された VM からディスクが非同期で切断されます。 このフィールドの VM が削除された後に、クリアするまで数分間かかる場合があります。
   >
   >


### <a name="step-2-delete-any-vm-images-that-are-preventing-deletion-of-the-storage-account-or-container"></a>手順 2: ストレージ アカウントまたはコンテナーの削除を妨げている VM イメージを削除します。
1. [Azure クラシック ポータル](https://manage.windowsazure.com/)に切り替えます。
2. **[仮想マシン]** > **[イメージ]** を選択して、ストレージ アカウント、コンテナー、または VHD に関連付けられているイメージを削除します。

    その後、そのストレージ アカウント、コンテナー、または VHD をもう一度削除します。

> [!WARNING]
> アカウントを削除する前に、保存する必要のあるデータを必ずバックアップしてください。 VHD、BLOB、テーブル、キュー、またはファイルを削除すると、永続的に削除されます。 リソースが使用されていないことを確認します。
>
>

## <a name="about-the-stopped-deallocated-status"></a>停止済み (割り当て解除済み) について
従来のデプロイ モデルで作成されて保持されている VM は、[Azure Portal](https://portal.azure.com/) または [Azure クラシック ポータル](https://manage.windowsazure.com/)で **[停止済み (割り当て解除)]** 状態になります。

**Azure クラシック ポータル**:

![Azure ポータルでの [停止済み (割り当て解除)] 状態。](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo2.png)

**Azure ポータル**:

![Azure クラシック ポータルでの [停止済み (割り当て解除)] 状態。](./media/storage-cannot-delete-storage-account-container-vhd/moreinfo1.png)

[停止済み (割り当て解除)] 状態は、CPU、メモリ、およびネットワークなど、コンピューター リソースを解放します。 ただし、ディスクは引き続き維持されますので、必要に応じて VM を簡単にもう一度作成できます。 これらのディスクは、Azure ストレージによって支えられている VHD の上に作成されます。 ストレージ アカウントにはこれらの VHD があり、ディスクにはこれらの VHD のリースがあります。

## <a name="next-steps"></a>次のステップ
* [ストレージ アカウントの削除](storage-create-storage-account.md#delete-a-storage-account)
* [How to break the locked lease of blob storage in Microsoft Azure (PowerShell) (Microsoft Azure (PowerShell) で BLOB ストレージのロックされているリースを解除する方法)](https://gallery.technet.microsoft.com/scriptcenter/How-to-break-the-locked-c2cd6492)

