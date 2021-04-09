---
title: 接続されていない Azure ディスクを特定する - Azure portal
description: Azure portal を使用して、接続されていない Azure マネージド ディスクとアンマネージド ディスク (VHD/ページ BLOB) を見つけて削除する方法。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 4d31ce4b6086c44de913afd1083bae25fa3d44cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898157"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>接続されていない Azure マネージド ディスクとアンマネージド ディスクを見つけて削除する - Azure portal

Azure で仮想マシン (VM) を削除するとき、既定では、その VM に接続されているディスクはいずれも削除されません。 これは、意図せず VM を削除したことによるデータ損失を防ぐのに役立ちます。 VM が削除された後、接続されていないディスクに対する料金の支払いが続きます。 この記事では、接続されていないディスクを Azure portal で見つけて削除し、不要なコストを削減する方法を示します。 削除は永続的です。ディスクを削除すると、データを回復できなくなります。

## <a name="managed-disks-find-and-delete-unattached-disks"></a>マネージド ディスク:接続されていないディスクの検索と削除

以下では、接続されていないマネージド ディスクがあり、そのディスク上のデータがもう不要ない場合に、Azure portal でそれらを探す方法を説明します。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[ディスク]** を探して選択します。

    **[ディスク]** ブレードに、すべてのディスクの一覧が表示されます。 **[所有者]** 列が " **-** " になっているディスクは、接続されていないディスクです。

    [![[所有者] 列が "-" である場合は非接続ディスクであるマネージド ディスク ブレードのスクリーンショット。](media/disks-find-unattached-portal/managed-disk-unattached-owner.png)](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. 接続されていない削除するディスクを選択すると、ディスクのブレードが開きます。
1. ディスクのブレードで、ディスクの状態が非接続になっていることを確認し、 **[削除]** を選択します。

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="個々のマネージド ディスク ブレードのスクリーンショット。ディスクが接続されていない場合、このブレードにディスクの状態が非接続と表示されます。データを保持する必要がなくなった場合は、このディスクを削除できます":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>アンマネージド ディスク:接続されていないディスクの検索と削除

非管理対象ディスクは、[Azure Storage アカウント](../storage/common/storage-account-overview.md)で[ページ BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) として格納された VHD ファイルです。

VM に接続されていないアンマネージド ディスクがあり、そのディスク上のデータが不要であり、ディスクを削除したい場合は、次に説明するプロセスで、Azure portal からそれを行うことができます。

1. [Azure portal](https://portal.azure.com/) にサインインします。
1. **[ディスク (クラシック)]** を探して選択します。

    すべてのアンマネージド ディスクの一覧が表示されます。 **[接続先]** 列が " **-** " になっているディスクは、接続されていないディスクです。

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="アンマネージド ディスク ブレードのスクリーンショット。このブレードで [接続先] 列が - になっているディスクは接続されていません。":::

1. 接続されていない削除するディスクを選択すると、ディスクのブレードが表示されます。

1. ディスクのブレードでは、 **[接続先]** がまだ " **-** " になっていることで、ディスクが接続されていないことを確認できます。

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="個々のアンマネージド ディスク ブレードのスクリーンショット。接続されていない場合、[接続先] の値が - になります。このディスクのデータが必要ない場合は、ディスクを削除することができます。":::

1. **[削除]** を選択します。

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="[削除] が強調して示されている、個々のアンマネージド ディスク ブレードのスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

接続されていないストレージ アカウントを自動的に検索して削除する方法については、[CLI](linux/find-unattached-disks.md) または [PowerShell](windows/find-unattached-disks.md) の記事を参照してください。

詳細については、「[ストレージ アカウントを削除する](../storage/common/storage-account-create.md#delete-a-storage-account)」および [PowerShell を使用した孤立したディスクの特定](/archive/blogs/ukplatforms/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell)に関するページを参照してください。
