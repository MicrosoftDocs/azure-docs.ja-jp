---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 12/10/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 91889971e1ab8a9ea8341f6bc57735d973ea0e89
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125057"
---
## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。 

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。


## <a name="preview-register-the-feature"></a>更新:機能を登録する

共有イメージ ギャラリーはプレビュー段階ですが、使用するには、事前に機能を登録しておく必要があります。 共有イメージ ギャラリー機能を登録するには、次のコマンドを実行します。

```azurepowershell-interactive
Register-AzProviderFeature `
   -FeatureName GalleryPreview `
   -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

## <a name="get-the-managed-image"></a>マネージド イメージを取得する

リソース グループで利用できるイメージは、[Get-AzImage](https://docs.microsoft.com/powershell/module/az.compute/get-azimage) を使用して一覧表示できます。 イメージの名前とそれが属しているリソース グループがわかったら、もう一度 `Get-AzImage` を使用してイメージ オブジェクトを取得し、後で使用できるよう変数に格納することができます。 この例では、*myImage* という名前のイメージを "myResourceGroup" リソース グループから取得し、変数 *$managedImage* に割り当てています。 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```

## <a name="create-an-image-gallery"></a>イメージ ギャラリーを作成する 

イメージ ギャラリーは、イメージの共有を有効にするために使用されるプライマリ リソースです。 ギャラリー名は、お使いのサブスクリプション内で一意にする必要があります。 イメージ ギャラリーは、[New-AzGallery](https://docs.microsoft.com/powershell/module/az.compute/new-azgallery) を使用して作成します。 次の例では、*myGalleryRG* リソース グループに *myGallery* という名前のギャラリーを作成します。

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'West Central US'  
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```
   
## <a name="create-an-image-definition"></a>イメージ定義を作成する 

ギャラリー イメージの定義は、[New-AzGalleryImageDefinition](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) を使用して作成します。 この例では、ギャラリー イメージが *myGalleryImage* という名前になっています。

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```
### <a name="using-publisher-offer-and-sku"></a>パブリッシャー、オファー、SKU の使用 
共有イメージの実装を計画しているお客様の場合、**今後のリリースでは**、**-Publisher**、**-Offer**、**-Sku** の各値を使用してイメージ定義を検索、指定し、一致するイメージ定義から最新のイメージ バージョンを使用して VM を作成できるようになります。 たとえば、以下に示したのは 3 つのイメージ定義とその値です。

|イメージの定義|Publisher|プラン|Sku|
|---|---|---|---|
|myImage1|myPublisher|myOffer|mySku|
|myImage2|myPublisher|standardOffer|mySku|
|myImage3|テスト|standardOffer|testSku|

この 3 つは、それぞれ固有の値を有しています。 3 つの値のうち 1 つまたは 2 つを共有するイメージ バージョンを保有することはできますが、3 つ全部を共有するイメージ バージョンを保有することはできません。 **今後のリリースでは**、特定のイメージの最新バージョンを要求する目的で、これらの値を組み合わせることが可能となります。 **これは現在のリリースで動作しませんが**、将来的には提供される予定です。 リリースされた場合、上記の表から *myImage1* としてソース イメージを設定するために、次の構文を使用する必要があります。

```powershell
$vmConfig = Set-AzVMSourceImage `
   -VM $vmConfig `
   -PublisherName myPublisher `
   -Offer myOffer `
   -Skus mySku 
```

これは、パブリッシャー、オファー、SKU を [Azure Marketplace イメージ](../articles/virtual-machines/windows/cli-ps-findimage.md)に使用して最新バージョンの Marketplace イメージを取得するために現在指定できる方法に似ています。 このことを踏まえて、イメージ定義ごとに、これらの値の組み合わせが一意となるようにしてください。  

## <a name="create-an-image-version"></a>イメージ バージョンを作成する

イメージ バージョンは、[New-AzGalleryImageVersion](https://docs.microsoft.com/powershell/module/az.compute/new-azgalleryimageversion) を使用してマネージド イメージから作成します。 この例のイメージ バージョンは *1.0.0* で、"*米国中西部*" と "*米国中南部*" の両方のデータセンターにレプリケートされます。


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-01-01' `
   -asJob 
```

すべてのターゲット リージョンにイメージがレプリケートされるまでにしばらく時間がかかることがあるため、進行状況を追跡できるようジョブを作成しました。 ジョブの進行状況を確認するには、「`$job.State`」と入力します。

```azurepowershell-interactive
$job.State
```

