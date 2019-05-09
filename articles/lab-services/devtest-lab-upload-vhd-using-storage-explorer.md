---
title: Microsoft Azure ストレージ エクスプローラーを使用して Azure DevTest Labs に VHD ファイルをアップロードする | Microsoft Docs
description: Microsoft Azure ストレージ エクスプローラーを使用して、ラボのストレージ アカウントに VHD ファイルをアップロードします
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 3c187d104334fe75ec9e0ce41a3fdc14b508dfb2
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521727"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Microsoft Azure ストレージ エクスプローラーを使用して、ラボのストレージ アカウントに VHD ファイルをアップロードします

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs では、VHD ファイルを使用してカスタム イメージを作成でき、そのイメージを使用して仮想マシンをプロビジョニングできます。 この記事では、[Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)を使用して、VHD ファイルをラボのストレージ アカウントにアップロードする方法を示します。 VHD ファイルをアップロードしたら、[「次のステップ」セクション](#next-steps)の記事に従って、アップロードした VHD ファイルからカスタム イメージを作成できます。 Azure のディスクと VHD の詳細については、「[マネージド ディスクの概要](../virtual-machines/linux/managed-disks-overview.md)」を参照してください。

## <a name="step-by-step-instructions"></a>詳細な手順

次の手順は、[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) を使用して VHD ファイルを Azure DevTest Labs にアップロードする方法を示しています。

1. [最新バージョンの Microsoft Azure ストレージ エクスプローラー](https://www.storageexplorer.com) をダウンロードしてインストールします。

1. Azure Portal を使用して、ラボのストレージ アカウントを取得します。

    1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。
    
    1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。
    
    1. ラボの一覧で目的のラボを選択します。  
    
    1. ラボのブレードで、 **[構成]** を選択します。 
    
    1. ラボの **[構成]** ブレードで、**[カスタム イメージ (VHD)]** を選択します。
    
    1. **[カスタム イメージ]** ブレードで、**[+追加]** を選択します。 
    
    1. **[カスタム イメージ]** ブレードで、**[VHD]** を選択します。
    
    1. **[VHD]** ブレードで、**[Upload a VHD file using PowerShell (PowerShell を使用して VHD ファイルをアップロードする)]** を選択します。
    
        ![PowerShell を使用した VHD のアップロード][0]
    
    1. **[PowerShell を使用したイメージのアップロード]** ブレードに、**Add-AzureVhd** コマンドレットへの呼び出しが表示されます。 最初のパラメーター ("*Destination*") には、ラボのストレージ アカウント名が、次の形式で指定されます。
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. 後の手順で使用するため、このストレージ アカウントをメモしておきます。
    
1. ストレージ エクスプローラーを使用する Azure サブスクリプション アカウントに接続します。

    > [!TIP] 
    > 
    > ストレージ エクスプローラーでは、複数の接続オプションがサポートされます。 このセクションでは、Azure サブスクリプションに関連付けられているストレージ アカウントへの接続を示します。 ストレージ エクスプローラーでサポートされるその他の接続オプションについては、「[ストレージ エクスプローラーの概要](../vs-azure-tools-storage-manage-with-storage-explorer.md)」を参照してください。
 
    1. ストレージ エクスプローラーを開きます。
    
    1. ストレージ エクスプローラーで、**[Azure アカウントの設定]** を選択します。 
    
        ![[Azure アカウントの設定]][1]
    
    1. 左側のウィンドウに、ログインしている Microsoft アカウントが表示されます。 別のアカウントに接続するには、 **[アカウントの追加]** を選択し、ダイアログに従って、少なくとも 1 つのアクティブな Azure サブスクリプションと関連付けられている Microsoft アカウントでサインインします。
    
        ![[アカウントの追加]][2]
    
    1. Microsoft アカウントでのサインインに成功すると、左側のウィンドウに、そのアカウントに関連付けられた Azure サブスクリプションが表示されます。 操作する Azure サブスクリプションを選択してから、**[適用]** を選択します  (**[すべてのサブスクリプション]** チェック ボックスをオンまたはオフにすることで、一覧の Azure サブスクリプションがすべて選択された状態と、1 つも選択されていない状態を切り替えることができます)。
    
        ![Azure サブスクリプションの選択][3]
    
    1. 左側のウィンドウに、選択した Azure サブスクリプションに関連付けられているストレージ アカウントが表示されます。
    
        ![選択された Azure サブスクリプション][4]

1. ラボのストレージ アカウントを見つけます。

    1. ストレージ エクスプローラーの左側のウィンドウで、ラボを所有している Azure サブスクリプションのノードを見つけて展開します。
    
    1. サブスクリプションのノードで、**[ストレージ アカウント]** を展開します。

    1. ラボの [ストレージ アカウント] ノードを展開して、**[BLOB コンテナー]**、**[ファイル共有]** **[キュー]**、および **[テーブル]** の各ノードを表示します。
    
    1. **[BLOB コンテナー]** ノードを展開します。
    
    1. uploads BLOB コンテナーを選択して、その内容を右側のウィンドウに表示します。
        
        ![アップロード ディレクトリ][5]

1. ストレージ エクスプローラーを使用して、VHD ファイルをアップロードします。

    1. ストレージ エクスプローラーの右側のウィンドウに、ラボのストレージ アカウントの **uploads** BLOB コンテナー内の BLOB が一覧表示されます。 BLOB エディター ツールバーで、**[アップロード]** を選択します。 
        
        ![[アップロード] ボタン][6]
    
    1. **[アップロード]** ドロップダウン メニューから、**[ファイルのアップロード...]** を選択します。
    
    1. **[ファイルのアップロード]** ダイアログで、省略記号を選択します。
        
        ![ファイルの選択][8]  

    1. **[アップロードするファイルの選択]** ダイアログで、目的の VHD ファイルを参照して選択してから **[開く]** を選択します。
    
    1. **[ファイルのアップロード]** ダイアログに戻ったら、**[BLOB の種類]** を **[ページ BLOB]** に変更します。
    
    1. **[アップロード]** を選択します。

        ![ファイルの選択][9]  
    
    1. ストレージ エクスプローラーの **[アクティビティ ログ]** ウィンドウに、ダウンロード ステータスが表示されます (アップロードをキャンセルするためのリンクも表示されます)。 VHD ファイルのアップロードは、VHD ファイルのサイズと接続速度によっては、時間がかかる場合があります。 

        ![ファイルのアップロードの状態][10]  

## <a name="next-steps"></a>次の手順

- [Azure Portal を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-template.md)
- [PowerShell を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
