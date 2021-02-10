---
title: Azure Cloud Services (延長サポート) に利用可能なサイズ
description: Azure Cloud Services (延長サポート) のデプロイに利用可能なサイズ
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: c3515b559ef647b9a043a04282b4edfc6e7fa9be
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743966"
---
# <a name="available-sizes-for-azure-cloud-services-extended-support"></a>Azure Cloud Services (延長サポート) に利用可能なサイズ

この記事では、Cloud Services (延長サポート) インスタンスで使用可能な仮想マシンのサイズについて説明します。   

| SKU ファミリ |  ACU/コア | 
|---|---|
| [A5-7](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series)| 100 |
|[A8 ～ A11](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#a-series---compute-intensive-instances) | 225* |
|[Av2](https://docs.microsoft.com/azure/virtual-machines/av2-series) | 100 | 
|[D](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#d-series) | 160 | 
|[Dv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series) | 160 - 190* |
|[Dv3](https://docs.microsoft.com/azure/virtual-machines/dv3-dsv3-series) | 160 - 190* |
|[Ev3](https://docs.microsoft.com/azure/virtual-machines/ev3-esv3-series) | 160 - 190*
|[G](https://docs.microsoft.com/azure/virtual-machines/sizes-previous-gen?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#g-series) | 180 から 240* |
|[H](https://docs.microsoft.com/azure/virtual-machines/h-series) | 290 ～ 300* | 

>[!NOTE]
> \* が付いている ACU は、Intel® Turbo テクノロジを使用して CPU 周波数を上げ、パフォーマンスを増強します。 増強量は、VM のサイズ、ワークロード、および同じホストで実行されている他のワークロードによって変化します。

## <a name="configure-sizes-for-cloud-services-extended-support"></a>Cloud Services (延長サポート) のサイズを構成する

ロール インスタンスの仮想マシンのサイズを、サービス定義ファイル内のサービス モデルの一部として指定できます。 ロールのサイズによって、CPU コアの数、メモリ容量、ローカル ファイル システムのサイズが決まります。

たとえば、Web ロール インスタンスのサイズを `Standard_D2` に設定します。 

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2"> 
</WorkerRole> 
```

## <a name="change-the-size-of-an-existing-role"></a>既存のロールのサイズを変更する

既存のロールのサイズを変更するには、サービス定義ファイル (csdef) 内の仮想マシンのサイズを変更し、クラウド サービスを再パッケージ化して再デプロイします。 

## <a name="get-a-list-of-available-sizes"></a>利用可能なサイズの一覧を取得する 

利用可能なサイズの一覧を取得するには、「[リソース SKU - 一覧](https://docs.microsoft.com/rest/api/compute/resourceskus/list)」を参照し、次のフィルターを適用します。


`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用して、クラウド サービス (延長サポート) をデプロイします。
