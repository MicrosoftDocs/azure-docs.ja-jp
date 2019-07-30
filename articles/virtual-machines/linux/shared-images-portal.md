---
title: ポータルを使用して Linux 用の共有 Azure 仮想マシン イメージを作成する | Microsoft Docs
description: Azure portal を使用して、仮想マシン イメージを作成して共有する方法について説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/27/2019
ms.author: cynthn
ms.custom: ''
ms.openlocfilehash: ea0a5961aff5935efea3eb98fd53904ce864ec2c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708907"
---
# <a name="create-a-shared-image-gallery-using-the-azure-portal"></a>Azure portal を使用して共有イメージ ギャラリーを作成する

[共有イメージ ギャラリー](shared-image-galleries.md)により、組織全体でのカスタム イメージの共有が簡素化されます。 カスタム イメージは Marketplace のイメージに似ていますが、カスタム イメージは自分で作成します。 カスタム イメージは、デプロイ タスク (アプリケーションのプリロード、アプリケーションの構成、その他の OS 構成など) のブートストラップを実行するために使用できます。 

共有イメージ ギャラリーを使用すると、組織内、リージョン内またはリージョン間、AAD テナント内で、他のユーザーとご自身のカスタム VM イメージを共有できます。 どのイメージを共有するか、どのリージョンでそのイメージを使用できるようにするか、および、だれと共有するかを選択することができます。 複数のギャラリーを作成することで、共有イメージを論理的にグループ化できます。 

ギャラリーは最上位リソースで、完全なロールベースのアクセス制御 (RBAC) が可能です。 イメージのバージョン管理もできるため、Azure リージョンの別のセットに各イメージのバージョンをレプリケートできます。 ギャラリーは、マネージド イメージでのみ機能します。

共有イメージ ギャラリー機能には、リソースの種類が複数あります。 それらを、この記事の中で使用または作成していきます。

| Resource | 説明|
|----------|------------|
| **マネージド イメージ** | これは、単独で使用することも、イメージ ギャラリーに**イメージ バージョン**を作成するために使用することもできる基本的なイメージです。 マネージド イメージは、一般化された VM から作成されます。 マネージド イメージは、複数の VM を作成する際に使用できる特別な種類の VHD で、共有イメージ バージョンを作成する際にも使用できるようになりました。 |
| **イメージ ギャラリー** | Azure Marketplace などの **イメージ ギャラリー**は、イメージを管理して共有するためのリポジトリです。ただし、アクセス権の所有者を制御します。 |
| **イメージ定義** | イメージはギャラリー内で定義され、内部で使用するためにイメージと要件に関する情報を伝達します。 この情報には、イメージが Windows または Linux のどちらか、リリース ノート、および最小と最大のメモリ要件が含まれます。 これは、イメージの種類の定義です。 |
| **イメージ バージョン** | **イメージ バージョン**は、ギャラリーを利用している場合に、VM の作成に使用します。 お使いの環境に必要な複数のイメージ バージョンを保持できます。 マネージド イメージのように、**イメージ バージョン**を使用して VM を作成する場合、イメージ バージョンは VM 用の新しいディスクを作成するために使用されます。 イメージ バージョンは複数回、使用できます。 |


## <a name="before-you-begin"></a>開始する前に

この記事の例を実際に行うには、既存のマネージド イメージが必要です。 お持ちでない場合は、[Azure PowerShell で Azure VM のカスタム イメージを作成する方法に関するチュートリアル](tutorial-custom-images.md)に従って作成してください。 マネージド イメージにデータ ディスクが含まれている場合、データ ディスクのサイズが 1 TB を超えることはできません。

この記事の作業に出現するリソース グループと VM の名前は適宜置き換えてください。

 
[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]

## <a name="create-vms-from-an-image"></a>イメージから VM を作成する

イメージ バージョンが完成したら、新しい VM を作成することができます。 

> [!IMPORTANT]
> ポータルを使用して、別の Azure テナント内のイメージから VM をデプロイすることはできません。 テナント間で共有されているイメージから VM を作成するには、[Azure CLI](shared-images.md#create-a-vm) または [PowerShell](../windows/shared-images.md#create-vms-from-an-image) を使用する必要があります。


この例では、"*米国東部*" のデータセンターにある *myResourceGroup* に、*myVMfromImage* という名前の VM を作成します。

1. イメージ バージョンのページで、ページの上部にあるメニューから **[VM の作成]** を選択します。
1. **[リソース グループ]** で、 **[新規作成]** を選択し、名前として「*myResourceGroup*」と入力します。
1. **[仮想マシン名]** に、「*myVM*」と入力します。
1. **[リージョン]** で、 *[米国東部]* を選択します。
1. **[可用性オプション]** で、既定の *[インフラストラクチャ冗長は必要ありません]* のままにします。
1. **[イメージ]** の値は、イメージ バージョンのページから起動した場合に自動的に入力されるはずです。
1. **[サイズ]** では、利用可能なサイズの一覧から VM サイズを選択し、[選択] をクリックします。
1. **[管理者アカウント]** で、 **[パスワード]** または **[SSH 公開キー]** を選択した後、自分の情報を入力します。
1. VM へのリモート アクセスを許可する場合、 **[パブリック受信ポート]** で、 **[選択したポートを許可する]** を選択し、ドロップダウンから **[SSH (22)]** を選択します。 VM へのリモート アクセスを許可しない場合、 **[パブリック受信ポート]** で、 **[なし]** を選択したままにします。
1. 完了したら、ページの下部にある **[Review + create]\(確認と作成\)** ボタンを選択します。
1. VM が検証に合格したら、ページの下部にある **[作成]** を選択し、デプロイを開始します。


## <a name="clean-up-resources"></a>リソースのクリーンアップ

必要がなくなったら、リソース グループ、仮想マシン、およびすべての関連リソースを削除できます。 これを行うには、仮想マシンのリソース グループを選択し、 **[削除]** を選択して、削除するリソース グループの名前を確認します。

個々のリソースを削除する場合は、逆の順序でそれらを削除する必要があります。 たとえば、イメージ定義を削除するには、そのイメージから作成されたすべてのイメージ バージョンを削除する必要があります。

## <a name="next-steps"></a>次の手順

共有イメージ ギャラリー リソースは、テンプレートを使用して作成することもできます。 いくつかの Azure クイック スタート テンプレートが用意されています。 

- [共有イメージ ギャラリーを作成する](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [共有イメージ ギャラリーにイメージ定義を作成する](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [共有イメージ ギャラリーにイメージのバージョンを作成する](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [イメージ バージョンから VM を作成する](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

共有イメージ ギャラリーの詳細については、[概要](shared-image-galleries.md)のページをご覧ください。 問題が生じた場合は、「[共有イメージ ギャラリーのトラブルシューティング](troubleshooting-shared-images.md)」を参照してください。

