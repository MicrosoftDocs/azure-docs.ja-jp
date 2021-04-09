---
title: Azure CLI - プライベート リンクを使用してマネージド ディスクに対するインポートおよびエクスポート アクセスを制限する
description: Azure CLI を使用して、マネージド ディスクのプライベート リンクを有効にします。 対象の仮想ネットワーク内でのみディスクを安全にエクスポートおよびインポートできます。
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 68b96401fc4fc6ea8916664978dbd4c094d9e5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684527"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI - プライベート リンクを使用してマネージド ディスクに対するインポートおよびエクスポート アクセスを制限する

[プライベート エンドポイント](../../private-link/private-endpoint-overview.md)を使用すると、マネージド ディスクのエクスポートとインポートを制限し、Azure 仮想ネットワーク上のクライアントから[プライベート リンク](../../private-link/private-link-overview.md)を介してデータに安全にアクセスできます。 プライベート エンドポイントでは、対象のマネージド ディスク サービスのために仮想ネットワークのアドレス空間の IP アドレスを使用します。 仮想ネットワーク上のクライアントとマネージド ディスク間のネットワーク トラフィックは、仮想ネットワークおよび Microsoft バックボーン ネットワーク上のプライベート リンク経由でのみ送信され、パブリック インターネットから公開されることはなくなります。

プライベート リンクを使用してマネージド ディスクをエクスポートまたはインポートするには、ディスク アクセス リソースを作成した後、プライベート エンドポイントを作成することによってこれを同じサブスクリプション内の仮想ネットワークにリンクします。 次に、ディスクまたはスナップショットをディスク アクセスのインスタンスに関連付けます。 最後に、ディスクまたはスナップショットの NetworkAccessPolicy プロパティを `AllowPrivate` に設定します。 これにより、対象の仮想ネットワークへのアクセスが制限されます。 

NetworkAccessPolicy プロパティを `DenyAll` に設定して、ディスクまたはスナップショットのデータをだれもエクスポートできないようにすることができます。 NetworkAccessPolicy プロパティの既定値は `AllowAll` です。

## <a name="limitations"></a>制限事項

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>サブスクリプションにログインし、変数を設定する

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Azure CLI を使用してディスク アクセスを作成する
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>仮想ネットワークを作成します

ネットワーク セキュリティ グループ (NSG) などのネットワーク ポリシーは、プライベート エンドポイントではサポートされていません。 特定のサブネットにプライベート エンドポイントをデプロイするには、そのサブネット上で明示的な無効化設定が必要です。 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>サブネットのプライベート エンドポイント ポリシーを無効にする

Azure では仮想ネットワーク内のサブネットにリソースがデプロイされるため、プライベート エンドポイントのネットワーク ポリシーを無効にするようにサブネットを更新する必要があります。 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>ディスク アクセス オブジェクトのプライベート エンドポイントを作成する

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>プライベート DNS ゾーンを構成する

ストレージ BLOB ドメイン用のプライベート DNS ゾーンを作成し、Virtual Network に対する関連付けリンクを作成します。また、プライベート エンドポイントをプライベート DNS ゾーンに関連付けるために、DNS ゾーン グループを作成します。 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>プライベート リンクを使用して保護されたディスクを作成する
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>プライベート リンクを使用して保護されたディスクのスナップショットを作成する
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>次のステップ

- [プライベート リンクに関する FAQ](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [CLI を使用して別のリージョンのストレージ アカウントに管理スナップショットを VHD としてエクスポート/コピーする](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)