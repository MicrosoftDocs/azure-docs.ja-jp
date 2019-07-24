---
title: Azure での Windows クライアント イメージの使用 | Microsoft Docs
description: Visual Studio サブスクリプションの特典を使用して、Azure で Windows 7、Windows 8、または Windows 10 を開発/テストのシナリオにデプロイする方法
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
ms.assetid: 91c3880a-cede-44f1-ae25-f8f9f5b6eaa4
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: f791b17f2729af3efd2dff5d7884a168f8377154
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57999761"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Azure で Windows クライアントを開発/テスト シナリオに使用する
適切な Visual Studio (以前は MSDN) サブスクリプションがある場合は、Azure で Windows 7、Windows 8、または Windows 10 Enterprise (x64) を開発/テストのシナリオに使用できます。 この記事では、Windows 7、Windows 8.1、Windows 10 Enterprise を Azure で実行するための資格要件と、次の Azure ギャラリーのイメージの使用について説明します。

![Azure Portal からのイメージの詳細](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> Azure ギャラリーの Windows 10 Pro および Windows 10 Pro N イメージについては、「[マルチテナント ホスティング権限を使用して Azure 上で Windows 10 をデプロイする方法](windows-desktop-multitenant-hosting-deployment.md)」をご覧ください。
>![Azure Portal からの Pro イメージの詳細](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>サブスクリプションの資格
アクティブな Visual Studio サブスクライバー (Visual Studio サブスクリプション ライセンスを取得したユーザー) は、Windows クライアントを開発とテストに使用できます。 Windows クライアントは、独自のハードウェアと、任意の種類の Azure サブスクリプションを実行している Azure Virtual Machines で使用できます。 Windows クライアントは、通常の運用環境での使用のために Azure 上でデプロイまたは使用したり、アクティブな Visual Studio サブスクライバーではないユーザーが使用したりすることはできません。

参考までに、[対象となる開発/テスト プラン](#eligible-offers)内にある Azure ギャラリーから特定の Windows 10 イメージを使用できます。 また、任意の種類のプラン内にあるVisual Studio サブスクライバーは、64 ビットの Windows 7、Windows 8、または Windows 10 イメージを[適切に準備および作成](prepare-for-upload-vhd-image.md)し、[Azure にアップロード](upload-generalized-managed.md)することもできます。 その使用は、アクティブな Visual Studio サブスクライバーによって開発/テストに引き続き限定されます。

## <a name="eligible-offers"></a>対象となるプラン
次の表に、Azure ギャラリーから Windows 10 をデプロイするための対象となるプラン ID の詳細を示します。 Windows 10 イメージは、次のプランにのみ表示されます。 別のプランの種類で Windows クライアントを実行する必要がある Visual Studio サブスクライバーは、64 ビットの Windows 7、Windows 8、または Windows 10 イメージを[適切に準備および作成](prepare-for-upload-vhd-image.md)し、[Azure にアップロード](upload-generalized-managed.md)する必要があります。

| プラン名 | プラン番号 | 使用可能なクライアント イメージ |
|:--- |:---:|:---:|
| [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Visual Studio Enterprise (MPN) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Visual Studio Professional サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Visual Studio Test Professional サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium with MSDN (特典)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Visual Studio Enterprise サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Visual Studio Enterprise (BizSpark) サブスクライバー](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Azure サブスクリプションの確認
プラン ID を把握していない場合は、次の 2 つの方法で Azure ポータルから取得できます。  

- *[サブスクリプション]* ウィンドウで:

  ![Azure ポータルからのプラン ID の詳細](./media/client-images/offer-id-azure-portal.png) 

- または、**[課金情報]** をクリックした後、サブスクリプション ID をクリックします。 *[課金情報]* ウィンドウにプラン ID が表示されます。

また、Azure アカウント ポータルの ['サブスクリプション' タブ](https://account.windowsazure.com/Subscriptions) からオファー ID を表示することもできます。

![Azure アカウント ポータルからのプラン ID の詳細](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>次の手順
これで、[PowerShell](quick-create-powershell.md)、[Resource Manager テンプレート](ps-template.md)、または [Visual Studio](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) を使用して VM をデプロイすることができます。

