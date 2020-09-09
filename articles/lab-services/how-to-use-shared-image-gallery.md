---
title: Azure Lab Services で共有イメージ ギャラリーを使用する | Microsoft Docs
description: 共有イメージ ギャラリーを使用するようにラボ アカウントを構成して、ユーザーが他のユーザーとイメージを共有したり、他のユーザーがイメージを使用してラボでテンプレート VM を作成したりできるようにする方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4dc001a5e4c95db608c38f0a7c00190fd1859998
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445153"
---
# <a name="use-a-shared-image-gallery-in-azure-lab-services"></a>Azure Lab Services で共有イメージ ギャラリーを使用する
この記事では、教育者やラボ管理者がテンプレート仮想マシンのイメージを[共有イメージ ギャラリー](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)に保存して、他のユーザーがラボを作成するために使用できるようにする方法について説明します。 

## <a name="scenarios"></a>シナリオ
この機能でサポートされるいくつかのシナリオを次に示します。 

- ラボ アカウント管理者がラボ アカウントに共有イメージ ギャラリーをアタッチし、ラボのコンテキスト外で共有イメージ ギャラリーにイメージをアップロードします。 そうすると、ラボ作成者が共有イメージ ギャラリーからそのイメージを使用して、ラボを作成できます。 
- ラボ アカウント管理者が、ラボ アカウントに共有イメージ ギャラリーをアタッチします。 ラボ作成者 (インストラクター) が、カスタマイズ済みの自分のラボのイメージを共有イメージ ギャラリーに保存します。 そうすると、他のラボ作成者が共有イメージ ギャラリーからこのイメージを選択して、各自のラボ用のテンプレートを作成できます。 

    イメージを共有イメージ ギャラリーに保存すると、Azure Lab Services によって、同じ[地域](https://azure.microsoft.com/global-infrastructure/geographies/)内で使用可能な他のリージョンに、保存したイメージがレプリケートされます。 これにより、同じ地域内の他のリージョンで作成されたラボでイメージを使用できるようになります。 共有イメージ ギャラリーにイメージを保存すると、追加コストが発生します。これには、レプリケートされたすべてのイメージのコストが含まれます。 このコストは、Azure Lab Services の使用コストとは別のものです。 共有イメージ ギャラリーの価格について詳しくは、[共有イメージ ギャラリーの課金]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing)に関するページを参照してください。
    
## <a name="prerequisites"></a>前提条件
- [Azure PowerShell](../virtual-machines/windows/shared-images.md) または [Azure CLI](../virtual-machines/linux/shared-images.md) のいずれかを使用して、共有イメージ ギャラリーを作成します。
- あらかじめ共有イメージ ギャラリーをラボ アカウントにアタッチしておきます。 詳細な手順については、[共有イメージ ギャラリーをアタッチまたはデタッチする方法](how-to-attach-detach-shared-image-gallery.md)に関するページを参照してください。


## <a name="save-an-image-to-the-shared-image-gallery"></a>共有イメージ ギャラリーに既存のイメージを保存する
共有イメージ ギャラリーがアタッチされたら、ラボ アカウントの管理者または教師は、他の教師が再利用できるように共有イメージ ギャラリーにイメージを保存できます。 

1. ラボの **[テンプレート]** ページで、ツール バーにある **[Export to Shared Image Gallery]\(Shared Image Gallery にエクスポートする\)** を選択します。

    ![[Save image]\(イメージの保存\) ボタン](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-button.png)
2. **[Export to Shared Image Gallery]\(Shared Image Gallery にエクスポートする\)** ダイアログで**イメージの名前を入力**し、 **[エクスポート]** を選択します。 

    ![[Export to Shared Image Gallery]\(Shared Image Gallery にエクスポートする\) ダイアログ](./media/how-to-use-shared-image-gallery/export-to-shared-image-gallery-dialog.png)
3. この操作の進捗状況は **[テンプレート]** ページで確認できます。 この操作には時間がかかることがあります。 

    ![エクスポートが進行中](./media/how-to-use-shared-image-gallery/exporting-image-in-progress.png)
4. エクスポート操作に成功すると、次のメッセージが表示されます。

    ![エクスポート完了](./media/how-to-use-shared-image-gallery/exporting-image-completed.png)

    共有イメージ ギャラリーにイメージを保存すると、別のラボを作成するときにギャラリーからそのイメージを使用できるようになります。 ラボのコンテキスト外で共有イメージ ギャラリーにイメージをアップロードすることもできます。 詳細については、[共有イメージ ギャラリーの概要](../virtual-machines/windows/shared-images.md)に関するページを参照してください。 

    > [!IMPORTANT]
    > 共有イメージ ギャラリーに、Azure Lab Services 内の[ラボのテンプレート イメージを保存](how-to-use-shared-image-gallery.md#save-an-image-to-the-shared-image-gallery)すると、そのイメージは**特殊化されたイメージ**としてギャラリーにアップロードされます。 [特殊化されたイメージ](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#generalized-and-specialized-images)には、マシン固有の情報とユーザー プロファイルが保持されます。 一般化されたイメージは、引き続き Azure Lab Services の外部のギャラリーに直接アップロードできます。    

## <a name="use-an-image-from-the-shared-image-gallery"></a>共有イメージ ギャラリーのイメージを使用する
教師は、新しいラボの作成中に、共有イメージ ギャラリーから利用できるカスタム イメージをテンプレート用に選択できます。

![ギャラリーの仮想マシン イメージを使用する](./media/how-to-use-shared-image-gallery/use-shared-image.png)

> [!NOTE]
> Azure Lab Services の**一般化された**イメージと**特殊化された**イメージの両方に基づいて、テンプレート VM を作成できます。 


## <a name="next-steps"></a>次のステップ
共有イメージ ギャラリーの詳細については、[共有イメージ ギャラリー](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)に関するページを参照してください。
