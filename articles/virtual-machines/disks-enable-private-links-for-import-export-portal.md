---
title: Azure portal - プライベート リンクを使用してマネージド ディスクに対するインポートおよびエクスポート アクセスを制限する
description: Azure portal を使用して、マネージド ディスクのプライベート リンクを有効にします。 これにより、仮想ネットワーク内でディスクを安全にエクスポートおよびインポートできます。
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b80100216003e91fde54b5e555bafb755c942810
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98682921"
---
# <a name="use-the-azure-portal-to-restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure portal からプライベート リンクを使用してマネージド ディスクに対するインポートおよびエクスポート アクセスを制限する

マネージド ディスクでのプライベート リンクのサポートにより、マネージド ディスクのエクスポートとインポートを制限して、Azure 仮想ネットワーク内でのみ実行されるようにすることができます。 接続されていないマネージド ディスクおよびスナップショットに対して期限付きの Shared Access Signature (SAS) URI を生成して、リージョン拡張のための他のリージョンへのデータのエクスポート、ディザスター リカバリー、フォレンジック分析のためのデータの読み取りに使用することができます。 SAS URI は、オンプレミスから空のディスクに VHD を直接アップロードするために使用することもできます。 仮想ネットワーク上のクライアントとマネージド ディスク間のネットワーク トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベート リンク経由でのみ送信され、パブリック インターネットに公開されることはなくなります。

プライベート エンドポイントを作成することによって、ディスク アクセス リソースを作成した後で、それを同じサブスクリプション内の仮想ネットワークにリンクできます。 プライベート リンクを介してデータをエクスポートおよびインポートするには、ディスクまたはスナップショットをディスク アクセスに関連付ける必要があります。 また、ディスクまたはスナップショットの NetworkAccessPolicy プロパティを `AllowPrivate` に設定する必要があります。 

NetworkAccessPolicy プロパティを `DenyAll` に設定すれば、ディスクまたはスナップショットの SAS URI をだれも生成できないようにすることができます。 NetworkAccessPolicy プロパティの既定値は `AllowAll` です。

## <a name="limitations"></a>制限事項

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="create-a-disk-access-resource"></a>ディスク アクセス リソースを作成する

1. Azure portal にサインインし、[このリンク](https://aka.ms/disksprivatelinks)から **[Disk Access]\(ディスク アクセス\)** に移動します。

    > [!IMPORTANT]
    > [Disk Access]\(ディスク アクセス\) ブレードに移動するには、[提供されているリンク](https://aka.ms/disksprivatelinks)を使用する必要があります。 現時点では、このリンクを使用しないと、パブリック ポータルに表示されません。

1. **[+ 追加]** を選択して、新しいディスク アクセス リソースを作成します。
1. 作成ブレードで、ご自分のサブスクリプションとリソース グループを選択したうえで、名前を入力し、リージョンを選択します。
1. **[Review + create]\(レビュー + 作成\)** を選択します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-create-basics.png" alt-text="ディスク アクセスの作成ブレードのスクリーンショット。目的の名前を入力し、リージョンを選択してから、リソース グループを選択して続行します":::

リソースが作成されたら、直接そのリソースに移動します。

:::image type="content" source="media/disks-enable-private-links-for-import-export-portal/screenshot-resource-button.png" alt-text="ポータルの [リソースに移動] ボタンのスクリーンショット":::

## <a name="create-a-private-endpoint"></a>プライベート エンドポイントの作成

ディスク アクセス リソースが用意できたので、それを使用してディスクのエクスポートまたはインポートへのアクセスを処理できます。これは、プライベート エンドポイントを介して行われます。 したがって、プライベート エンドポイントを作成してディスク アクセス用に構成する必要があります。

1. ディスク アクセス リソースから、 **[プライベート エンドポイント接続]** を選択します。
1. **[+ プライベート エンドポイント]** を選択します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-main-private-blade.png" alt-text="ディスク アクセス リソースの [概要] ブレードのスクリーンショット。[プライベート エンドポイント接続] が強調表示されています。":::

1. リソース グループの選択
1. 名前を入力し、ディスク アクセス リソースが作成されたリージョンと同じリージョンを選択します。
1. **[Next:リソース >]** を選択します

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-first-blade.png" alt-text="プライベート エンドポイント作成ワークフローの最初のブレードのスクリーンショット。適切なリージョンを選択しないと、後で問題が発生する可能性があります。":::

1. **[リソース]** ブレードで、 **[マイ ディレクトリ内の Azure リソースに接続します]** を選択します。
1. **[リソースの種類]** で、 **[Microsoft.Compute/diskAccesses]** を選択します
1. **[リソース]** で、前の手順で作成したディスク アクセス リソースを選択します
1. **[対象サブリソース]** は **[ディスク]** のままにします
1. **次へ :構成 >** をクリックします。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-second-blade.png" alt-text="プライベート エンドポイント作成ワークフローの 2 番目のブレードのスクリーンショット。すべての値が強調表示されています ([リソースの種類]、[リソース]、[対象サブリソース])":::

1. ディスクのエクスポートを制限する仮想ネットワークを選択します。他の仮想ネットワークではディスクをエクスポートできなくなります。

    > [!NOTE]
    > 選択したサブネットで有効なネットワーク セキュリティ グループ (NSG) がある場合、それはこのサブネットのプライベート エンドポイントに対してのみ無効になります。 このサブネット上の他のリソースには引き続き NSG が強制的に適用されます。

1. 適切なサブネットを選択します
1. **[Review + create]\(レビュー + 作成\)** を選択します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-private-endpoint-third-blade.png" alt-text="プライベート エンドポイント作成ワークフローの 3 番目のブレードのスクリーンショット。仮想ネットワークとサブネットが強調されています。":::

## <a name="enable-private-endpoint-on-your-disk"></a>ディスク上でプライベート エンドポイントを有効にする

1. 構成するディスクに移動します
1. **[ネットワーク]** を選択します。
1. **[Private endpoint (through disk access)]\(プライベート エンドポイント (ディスク アクセス経由)\)** を選択し、前の手順で作成したディスク アクセスを選択します。
1. **[保存]** を選択します。

    :::image type="content" source="media/disks-enable-private-links-for-import-export-portal/disk-access-managed-disk-networking-blade.png" alt-text="マネージド ディスクの [ネットワーク] ブレードのスクリーンショット。プライベート エンドポイントの選択と、選択したディスク アクセスが強調表示されています。これを保存すると、このアクセス用にディスクが構成されます。":::

これで、マネージド ディスクをインポートまたはエクスポートするときに使用できるプライベート リンクの構成が完了しました。

## <a name="next-steps"></a>次のステップ

- [プライベート リンクに関する FAQ](./faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [PowerShell を使用して別のリージョンのストレージ アカウントにマネージド スナップショットを VHD としてエクスポートまたはコピーする](/previous-versions/azure/virtual-machines/scripts/virtual-machines-powershell-sample-copy-snapshot-to-storage-account)