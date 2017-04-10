---
title: "Azure での Windows クライアント イメージの使用 | Microsoft Docs"
description: "Visual Studio サブスクリプションの特典を使用して、Azure で Windows 7、Windows 8、または Windows 10 を開発/テストのシナリオにデプロイする方法"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: fd35f1774ffda3d3751a6fa4b6e17f2132274916
ms.openlocfilehash: f0ca7996e52a9b295fff571a270c60520f58101e
ms.lasthandoff: 03/16/2017


---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Azure で Windows クライアントを開発/テスト シナリオに使用する
適切な Visual Studio (以前は MSDN) サブスクリプションがある場合は、Azure で Windows 7、Windows 8、または Windows 10 を開発/テストのシナリオに使用できます。 この記事では、Azure で実行中の Windows クライアントの資格要件と、Azure ギャラリーのイメージの使用について説明します。

## <a name="subscription-eligibility"></a>サブスクリプションの資格
アクティブな Visual Studio サブスクライバー (Visual Studio サブスクリプション ライセンスを取得したユーザー) は、Windows クライアントを開発とテストに使用できます。 Windows クライアントは、独自のハードウェアと、任意の種類の Azure サブスクリプションを実行している Azure Virtual Machines で使用できます。 Windows クライアントは、通常の運用環境での使用のために Azure 上でデプロイまたは使用したり、アクティブな Visual Studio サブスクライバーではないユーザーが使用したりすることはできません。

参考までに、 [対象となる開発/テスト プラン](#eligible-offers)内にある Azure ギャラリーから特定の Windows 10 イメージを使用できます。 また、任意の種類のプラン内にあるVisual Studio サブスクライバーは、64 ビットの Windows 7、Windows 8、または Windows 10 イメージを[適切に準備および作成](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)し、[Azure にアップロード](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)することもできます。 その使用は、アクティブな Visual Studio サブスクライバーによって開発/テストに引き続き限定されます。

## <a name="eligible-offers"></a>対象となるプラン
次の表に、Azure ギャラリーから Windows 10 をデプロイするための対象となるプラン ID の詳細を示します。 Windows 10 イメージは、次のプランにのみ表示されます。 別のプランの種類で Windows クライアントを実行する必要がある Visual Studio サブスクライバーは、64 ビットの Windows 7、Windows 8、または Windows 10 イメージを[適切に準備および作成](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)し、[Azure にアップロード](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)する必要があります。

| プラン名 | プラン番号 | 使用可能なクライアント イメージ |
|:--- |:---:|:---:|
| [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Visual Studio Enterprise (MPN) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Visual Studio Test Professional サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium with MSDN (特典)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Visual Studio Enterprise サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Visual Studio Enterprise (BizSpark) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/ofers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Azure サブスクリプションの確認
プラン ID を把握していない場合は、Azure ポータルまたはアカウント ポータルから取得できます。

サブスクリプションのプラン ID は、 Azure ポータル内の 'サブスクリプション' ブレードに示されています。

![Azure ポータルからのプラン ID の詳細](./media/virtual-machines-windows-client-images/offer_id_azure_portal.png) 

また、Azure アカウント ポータルの ['サブスクリプション' タブ](http://account.windowsazure.com/Subscriptions) からプラン ID を表示することもできます。

![Azure アカウント ポータルからのプラン ID の詳細](./media/virtual-machines-windows-client-images/offer_id_azure_account_portal.png) 

## <a name="next-steps"></a>次のステップ
これで、[PowerShell](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)、[Resource Manager テンプレート](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)、または [Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) を使用して VM をデプロイすることができます。


