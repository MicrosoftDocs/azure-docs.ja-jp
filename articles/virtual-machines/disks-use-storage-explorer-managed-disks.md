---
title: Azure Storage Explorer を使用して Azure マネージド ディスクを管理する
description: Azure Storage Explorer を使用して、リージョン間で Azure マネージド ディスクのアップロード、ダウンロード、移行を行う方法と、マネージド ディスクのスナップショットを作成する方法について説明します。
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 9dfce7b76eed5bfc9f4979c0e3041b6c65c28422
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88749384"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Azure Storage Explorer を使用して Azure マネージド ディスクを管理する

Storage Explorer 1.10.0 を使用すると、ユーザーは、マネージド ディスクのアップロード、ダウンロード、コピー、およびスナップショットの作成を行うことができます。 これらの追加機能により、Storage Explorer を使用して、オンプレミスから Azure にデータを移行したり、Azure リージョン間でデータを移行したりすることができます。

## <a name="prerequisites"></a>前提条件

この記事の手順を行うには、次のものが必要です。
- Azure サブスクリプション
- 1 つ以上の Azure マネージド ディスク
- 最新バージョンの [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Azure サブスクリプションに接続する

お使いの Storage Explorer が Azure に接続されていない場合、それを使用してリソースを管理することはできません。 このセクションでは、Storage Explorer を使用してリソースを管理できるよう、Azure アカウントに接続する方法について説明します。

1. Azure Storage Explorer を起動し、左側にある **プラグイン** アイコンをクリックします。

    ![プラグイン アイコンをクリックする](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. **[Azure アカウントを追加する]** を選択し、**[次へ]** をクリックします。

    ![Azure アカウントを追加する](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. **[Azure Sign In]\(Azure サインイン\)** ダイアログ ボックスで、Azure の資格情報を入力します。

    ![Azure サインイン ダイアログ ボックス](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. 一覧からサブスクリプションを選択し、 **[適用]** をクリックします。

    ![サブスクリプションを選択します](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>オンプレミスの VHD からマネージド ディスクをアップロードする

1. 左側のペインで **[ディスク]** を展開し、ディスクをアップロードするリソース グループを選択します。

    ![リソース グループ 1 を選択する](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. **[アップロード]** を選択します。

    ![[アップロード] を選択する](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. **[VHD のアップロード]** で、ソース VHD、ディスクの名前、OS の種類、ディスクのアップロード先のリージョン、およびアカウントの種類を指定ます。 一部のリージョンでは可用性ゾーンがサポートされていますが、それらのリージョンでは任意のゾーンを選択できます。
1. **[作成]** を選択し、ディスクのアップロードを開始します。

    ![VHD のアップロード ダイアログ](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. アップロードの状態が **[アクティビティ]** に表示されます。

    ![アップロードの状態](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. アップロードが完了し、右側のペインにディスクが表示されない場合は、**[Refresh]\(最新の情報に更新\)** を選択します。

## <a name="download-a-managed-disk"></a>マネージド ディスクをダウンロードする

次の手順では、オンプレミスの VHD にマネージド ディスクをダウンロードする方法について説明します。 ダウンロードするには、ディスクの状態が **[未接続]** になっている必要があります。**[接続済み]** のディスクをダウンロードすることはできません。

1. 左側のペインで、まだ展開していない場合は **[ディスク]** を展開し、ディスクをダウンロードするリソース グループを選択します。

    ![リソース グループ 1 を選択する](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 右側のペインで、ダウンロードするディスクを選択します。
1. **[ダウンロード]** を選択し、ディスクを保存する場所を選択します。

    ![マネージド ディスクをダウンロードする](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. **[保存]** を選択すると、ディスクのダウンロードが開始されます。 ダウンロードの状態が **[アクティビティ]** に表示されます。

    ![ダウンロードの状態](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>マネージド ディスクをコピーする

Storage Explorer を使うと、リージョン内またはリージョン間でマネージド ディスクをコピーできます。 ディスクをコピーするには:

1. 左側の **[ディスク]** ドロップダウンから、コピーするディスクが含まれているリソース グループを選択します。

    ![リソース グループ 1 を選択する](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 右側のペインで、コピーするディスクを選択し、**[コピー]** を選択します。

    ![マネージド ディスクをコピーする](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. 左側のペインで、ディスクを貼り付けるリソース グループを選択します。

    ![リソース グループ 2 を選択する](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. 右側のペインで **[貼り付け]** を選択します。

    ![マネージド ディスクを貼り付ける](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. **[ディスクの貼り付け]** ダイアログで、値を入力します。 サポートされているリージョンの可用性ゾーンを指定することもできます。

    ![[ディスクの貼り付け] ダイアログ](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. **[貼り付け]** を選択すると、ディスクのコピーが開始され、**[アクティビティ]** に状態が表示されます。

    ![コピーの貼り付けの状態](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>スナップショットの作成

1. 左側の **[ディスク]** ドロップダウンから、スナップショットを作成するディスクが含まれているリソース グループを選択します。

    ![リソース グループ 1 を選択する](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. 右側で、スナップショットを作成するディスクを選択し、**[スナップショットの作成]** を選択します。

    ![スナップショットの作成](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. **[スナップショットの作成]** で、スナップショットの名前と、作成するリソース グループを指定します。 **[作成]** を選択します。

    ![[スナップショットの作成] ダイアログ](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. スナップショットが作成されたら、**[アクティビティ]** の **[ポータルで開く]** を選択し、Azure portal でスナップショットを表示できます。

    ![ポータルでスナップショットを開く](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>次のステップ


[Azure portal を使用して VHD から VM を作成する](windows/create-vm-specialized-portal.md)方法について説明します。

[Azure portal を使用して Windows VM にマネージド データ ディスクを接続する](windows/attach-managed-disk-portal.md)方法について説明します。