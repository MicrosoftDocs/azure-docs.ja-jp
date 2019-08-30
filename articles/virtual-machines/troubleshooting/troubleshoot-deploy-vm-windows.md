---
title: Azure での Windows 仮想マシンのデプロイに関する問題のトラブルシューティング | Microsoft Docs
description: Azure Resource Manager デプロイ モデルでの Windows 仮想マシンのデプロイに関する問題のトラブルシューティング。
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: d963556a657f4367acca14d5d8340f384ef555a9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70080218"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Azure での Windows 仮想マシンのデプロイに関する問題のトラブルシューティング

Azure での仮想マシン (VM) のデプロイに関する問題のトラブルシューティングを行うため、よくあるエラーと解決策について、[代表的な問題](#top-issues)を確認します。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。

## <a name="top-issues"></a>代表的な問題
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>クラスターが要求された VM サイズをサポートできない
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- VM サイズを小さくして要求を再試行します。
- 要求した VM のサイズを変更できない場合は、次の手順を実行します。
    - 可用性セットのすべての VM を停止します。 **[リソース グループ]** > 対象とするリソース グループ > **[リソース]** > 対象とする可用性セット > **[仮想マシン]** > 対象とする仮想マシン > **[停止]** の順にクリックします。
    - すべての VM が停止したら、目的のサイズで VM を作成します。
    - 新しい VM を起動してから、停止している各 VM を選択し、[起動] をクリックします。


## <a name="the-cluster-does-not-have-free-resources"></a>クラスターに空きリソースがない
\<properties supportTopicIds="123456789" resourceTags="windows" productPesIds="1234, 5678" />
- 後で要求を再試行してください。
- 新しい VM を別の可用性セットに含めることができる場合
    - VM を (同じリージョンの) 別の可用性セットに作成します。
    - 新しい VM を同じ仮想ネットワークに追加します。

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Windows クライアント イメージを使用し、Azure にデプロイするには、どうすればよいですか?

適切な Visual Studio (以前は MSDN) サブスクリプションがある場合は、Azure で開発/テストのシナリオに Windows 7、Windows 8、または Windows 10 を使用できます。 Azure で Windows クライアントを実行するための資格要件については、この[記事](../windows/client-images.md)で説明しています。この記事では、Azure ギャラリーのイメージを使用します。

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Hybrid Use Benefit (HUB) を使用して仮想マシンをデプロイするには、どうすればよいですか?

Windows 仮想マシンを Azure Hybrid Use Benefit でデプロイする方法はいくつかあります。

Enterprise Agreement サブスクリプションの場合:

•   Azure Hybrid Use Benefit で事前構成済みの特定の Marketplace イメージから VM をデプロイします。

Enterprise Agreement の場合:

•   カスタム VM をアップロードし、Resource Manager テンプレートまたは Azure PowerShell を使用してデプロイします。

詳細については、次のリソースを参照してください。

 - [Azure Hybrid Use Benefit の概要](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [ダウンロード可能な FAQ](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Windows Server および Windows クライアント向け Azure Hybrid Use Benefit](../windows/hybrid-use-benefit-licensing.md)

 - [Hybrid Use Benefit の使用方法](https://blogs.msdn.microsoft.com/azureedu/2016/04/13/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Visual Studio Enterprise (BizSpark) 向けの月単位のクレジットを有効にするには、どうすればよいですか?

月単位のクレジットを有効にするには、この[記事](https://azure.microsoft.com/offers/ms-azr-0064p/)を参照してください。

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Window クライアント イメージにアクセスするために自分の Enterprise Agreement (EA) に Enterprise Dev/Test を追加するには、どうすればよいですか?

Enterprise Dev/Test オファーに基づいてサブスクリプションを作成できるユーザーは、エンタープライズ管理者によってそのアクセス許可を付与されたアカウント所有者に限定されます。 アカウント所有者は Azure アカウント ポータルを介してサブスクリプションを作成した後、アクティブな Visual Studio サブスクライバーを共同管理者として追加する必要があります。 そうすることで、開発とテストに必要なリソースを、その Visual Studio サブスクライバーが管理および使用できるようになります。 詳細については、「[エンタープライズ開発/テスト](https://azure.microsoft.com/offers/ms-azr-0148p/)」を参照してください。

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Windows N シリーズ VM のドライバーが見つかりません

Windows ベースの VM 用のドライバーは、[ここ](../windows/n-series-driver-setup.md)にあります。

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>N シリーズ VM 内で GPU インスタンスが見つかりません

Windows Server 2016 または Windows Server 2012 R2 を実行する Azure N シリーズ VM の GPU 機能を利用するには、デプロイ後に各 VM に NVIDIA グラフィック ドライバーをインストールする必要があります。 [Windows VM](../windows/n-series-driver-setup.md) 用と [Linux VM](../linux/n-series-driver-setup.md) 用のドライバー セットアップ情報を利用できます。

## <a name="is-n-series-vms-available-in-my-region"></a>自分のリージョンで N シリーズ VM を使用できますか?

[リージョンごとの使用可能な製品の表](https://azure.microsoft.com/regions/services)で、使用できるかどうかを確認できます。価格については、[こちら](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series)を参照してください。

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Azure で使用およびデプロイできるのは、どのクライアント イメージですか? また、それを入手するには、どうすればよいですか?

適切な Visual Studio (以前は MSDN) サブスクリプションがある場合は、Azure で Windows 7、Windows 8、または Windows 10 を開発/テストのシナリオに使用できます。 

- [対象となる開発/テスト プラン](../windows/client-images.md#eligible-offers)内で、Azure ギャラリーから Windows 10 イメージを使用できます。 
- また、任意の種類のプラン内にあるVisual Studio サブスクライバーは、64 ビットの Windows 7、Windows 8、または Windows 10 イメージを[適切に準備および作成](../windows/prepare-for-upload-vhd-image.md)し、[Azure にアップロード](../windows/upload-generalized-managed.md)することもできます。 その使用は、アクティブな Visual Studio サブスクライバーによって開発/テストに引き続き限定されます。

この[記事](../windows/client-images.md)では、Azure で Windows クライアントを実行するための資格要件と、Azure ギャラリーのイメージの使用について説明します。

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>VM のサイズ変更時に、必要な VM サイズ ファミリが表示されません。

VM は、実行されている場合、物理サーバーにデプロイされています。 Azure リージョンの物理サーバーは、一般的な物理ハードウェアのクラスターにグループ化されます。 別のハードウェア クラスターに VM を移動することが必要になる VM のサイズ変更は、VM のデプロイに使用したデプロイメント モデルに応じて異なります。

- クラシック デプロイ モデルでデプロイされた VM の場合、VM を他のサイズ ファミリのサイズに変更するには、クラウド サービス デプロイを削除し、再デプロイする必要があります。

- Resource Manager デプロイ モデルでデプロイされた VM の場合は、可用性セット内の VM のサイズを変更する前に、可用性セット内のすべての VM を停止する必要があります。

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>一覧の VM サイズが、可用性セットに対するデプロイでサポートされていません。

可用性セットのクラスターでサポートされているサイズを選択してください。 可用性セットを作成するときには、必要だと考えられる最大の VM サイズを選択し、それを可用性セットへの最初のデプロイにすることをお勧めします。

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>可用性セットに既存のクラシック VM を追加できますか?

はい。 既存のクラシック VM を、新規または既存の可用性セットに追加することができます。 詳細については、「[既存の仮想マシンを可用性セットに追加する](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine)」を参照してください。


## <a name="next-steps"></a>次の手順
この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。

または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
