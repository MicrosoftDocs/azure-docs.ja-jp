---
title: "AzCopy を使用して Azure DevTest Labs に VHD ファイルをアップロードする | Microsoft Docs"
description: "AzCopy を使用してラボのストレージ アカウントに VHD ファイルをアップロードします"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: cc14f7747c4a24acea434f62b7615d10819bd619
ms.openlocfilehash: f79e76d2efbd71cdd828dbdf43353076c063f23b


---

# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>AzCopy を使用してラボのストレージ アカウントに VHD ファイルをアップロードする

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs では、VHD ファイルを使用してカスタム イメージを作成でき、そのイメージを使用して仮想マシンをプロビジョニングできます。 次の手順は、AzCopy コマンド ライン ユーティリティを使用して、VHD ファイルをラボのストレージ アカウントにアップロードする方法を示しています。 VHD ファイルをアップロードしたら、[「次のステップ」セクション](#next-steps)の記事に従って、アップロードした VHD ファイルからカスタム イメージを作成できます。 Azure でのディスクと VHD の詳細については、「[仮想マシン用のディスクと VHD について](../storage/storage-about-disks-and-vhds-linux.md)」を参照してください。

> [!NOTE] 
>  
> AzCopy は、Windows 限定のコマンド ライン ユーティリティです。

## <a name="step-by-step-instructions"></a>詳細な手順

次の手順は、[AzCopy](http://aka.ms/downloadazcopy) を使用して VHD ファイルを Azure DevTest Labs にアップロードする方法を示しています。 

1. Azure Portal を使用して、ラボのストレージ アカウントを取得します。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。

1. **[その他のサービス]** を選択し、一覧の **[DevTest Labs]** を選択します。

1. ラボの一覧で目的のラボを選択します。  

1. ラボのブレードで、 **[構成]**を選択します。 

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
 
1. [最新バージョンの AzCopy をダウンロードしてインストールします](http://aka.ms/downloadazcopy)。

1. コマンド ウィンドウを開き、AzCopy のインストール ディレクトリに移動します。 必要に応じて、AzCopy のインストール先をシステム パスに追加できます。 既定では、AzCopy は、次のディレクトリにインストールされます。

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. ストレージ アカウント キーと BLOB コンテナーの URI を使用して、コマンド プロンプトで次のコマンドを実行します。 "*vhdFileName*" 値は引用符で囲む必要があります。 VHD ファイルのアップロードは、VHD ファイルのサイズと接続速度によっては、時間がかかる場合があります。   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>次のステップ

- [Azure Portal を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-template.md)
- [PowerShell を使用して VHD ファイルから Azure DevTest Labs にカスタム イメージを作成する](devtest-lab-create-custom-image-from-vhd-using-powershell.md)


<!--HONumber=Feb17_HO3-->


