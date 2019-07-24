---
title: AzCopy を使用して Azure DevTest Labs に VHD ファイルをアップロードする | Microsoft Docs
description: AzCopy を使用してラボのストレージ アカウントに VHD ファイルをアップロードします
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
ms.openlocfilehash: 8cd778762bebf4a9dda3688292ac0a3674e446e1
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/16/2019
ms.locfileid: "56330853"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>AzCopy を使用してラボのストレージ アカウントに VHD ファイルをアップロードする

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs では、VHD ファイルを使用してカスタム イメージを作成でき、そのイメージを使用して仮想マシンをプロビジョニングできます。 次の手順は、AzCopy コマンド ライン ユーティリティを使用して、VHD ファイルをラボのストレージ アカウントにアップロードする方法を示しています。 VHD ファイルをアップロードしたら、[「次のステップ」セクション](#next-steps)の記事に従って、アップロードした VHD ファイルからカスタム イメージを作成できます。 Azure のディスクと VHD の詳細については、「[マネージド ディスクの概要](../virtual-machines/linux/managed-disks-overview.md)」を参照してください。

> [!NOTE] 
>  
> AzCopy は、Windows 限定のコマンド ライン ユーティリティです。

## <a name="step-by-step-instructions"></a>詳細な手順

次の手順は、[AzCopy](https://aka.ms/downloadazcopy) を使用して VHD ファイルを Azure DevTest Labs にアップロードする方法を示しています。 

1. Azure Portal を使用して、ラボのストレージ アカウントを取得します。

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. **[すべてのサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。  

1. ラボのブレードで、 **[構成]** を選択します。 

1. ラボの **[構成]** ブレードで、**[カスタム イメージ (VHD)]** を選択します。

1. **[カスタム イメージ]** ブレードで、**[+追加]** を選択します。 

1. **[カスタム イメージ]** ブレードで、**[VHD]** を選択します。

1. **[VHD]** ブレードで、**[Upload a VHD file using PowerShell (PowerShell を使用して VHD ファイルをアップロードする)]** を選択します。

    ![PowerShell を使用した VHD のアップロード](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. **[PowerShell を使用したイメージのアップロード]** ブレードに、**Add-AzureVhd** コマンドレットへの呼び出しが表示されます。 最初のパラメーター ("*Destination*") には、BLOB コンテナー ("*uploads*") の URI が、次の形式で指定されます。

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. 後の手順で使用するため、この URI 全体をメモしておきます。

1. AzCopy を使用して VHD ファイルをアップロードします。
 
1. [最新バージョンの AzCopy をダウンロードしてインストールします](https://aka.ms/downloadazcopy)。

1. コマンド ウィンドウを開き、AzCopy のインストール ディレクトリに移動します。 必要に応じて、AzCopy のインストール先をシステム パスに追加できます。 既定では、AzCopy は、次のディレクトリにインストールされます。

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. ストレージ アカウント キーと BLOB コンテナーの URI を使用して、コマンド プロンプトで次のコマンドを実行します。 "*vhdFileName*" 値は引用符で囲む必要があります。 VHD ファイルのアップロードは、VHD ファイルのサイズと接続速度によっては、時間がかかる場合があります。   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>次の手順

- [Azure Portal を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-template.md)
- [PowerShell を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-custom-image-from-vhd-using-powershell.md)