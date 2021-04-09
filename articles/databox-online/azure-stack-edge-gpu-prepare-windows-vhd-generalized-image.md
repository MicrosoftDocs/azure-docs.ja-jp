---
title: Windows VHD の一般化されたイメージから Azure Stack Edge Pro GPU デバイス用の VM イメージを作成する
description: Windows VHD または VHDX から起動する一般化されたイメージから、VM イメージを作成する方法について説明します。 この一般化されたイメージを使用して、Azure Stack Edge Pro GPU デバイスにデプロイされた VM で使用する VM イメージを作成します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962379"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Windows VHD にある一般化されたイメージを使用して Azure Stack Edge Pro デバイス用の VM イメージを作成する

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro デバイスで VM をデプロイするには、VM の作成に使用するカスタム VM イメージを作成できる必要があります。 この記事では、一般化されたイメージを作成するため、Windows VHD または VHDX を準備するために必要な手順について説明します。 この一般化されたイメージは次に、Azure Stack Edge Pro デバイス用の VM イメージを作成するために使用されます。 

## <a name="about-preparing-windows-vhd"></a>Windows VHD の準備について

Windows VHD または VHDX を使用すると、"*一般化された*" イメージまたは "*特殊化された*" イメージを作成できます。 次の表は、"*一般化*" されたイメージと "*特殊化*" されたイメージの主な違いをまとめたものです。


|イメージの種類  |一般化されたイメージ  |専用イメージ  |
|---------|---------|---------|
|移行先     |どのシステムにもデプロイされる         | 特定のシステムが対象        |
|ブート後のセットアップ     | VM の初回ブート時にセットアップが必要。          | セットアップは不要。 <br> VM はプラットフォームによってオンにされる。        |
|構成     |ホスト名、管理者ユーザー、その他の VM 固有の設定が必要。         |完全に事前構成済み。         |
|どのような場合に使用するか     |同じイメージからの複数の新しい VM の作成。         |特定のマシンの移行または以前のバックアップからの VM の復元。         |


この記事では、一般化されたイメージからのデプロイに必要な手順を取り上げます。 特殊化されたイメージからデプロイするには、お使いのデバイスに[特殊化された Windows VHD を使用する](azure-stack-edge-placeholder.md)ことに関するページを参照してください。

> [!IMPORTANT]
> この手順では、ソース VHD がカスタムの構成と設定を使用して構成されている場合については説明しません。 たとえば、カスタムのファイアウォール規則やプロキシ設定が含まれる VHD を一般化するために、追加のアクションが必要になる場合があります。 これらの追加アクションの詳細については、[Azure Virtual Machines で、Azure にアップロードする Windows VHD を準備する](../virtual-machines/windows/prepare-for-upload-vhd-image.md)ことに関するページを参照してください。


## <a name="vm-image-workflow"></a>VM イメージのワークフロー

一般化されたイメージとして使用するために Windows VHD を準備するための大まかなワークフローには、以下の手順が含まれています。

1. ソース VHD または VHDX を固定サイズの VHD に変換します。
1. 固定 VHD を使用して Hyper-V 内に VM を作成します。
1. Hyper-V VM に接続します。
1. *sysprep* ユーティリティを使用して VHD を一般化します。 
1. 一般化されたイメージを BLOB ストレージにコピーします。
1. 一般化されたイメージを使用してデバイスに VM をデプロイします。 詳細については、[Azure portal から VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)方法に関するページ、または [PowerShell を使用して VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)方法に関するページを参照してください。


## <a name="prerequisites"></a>前提条件

Azure Stack Edge で一般化されたイメージとして使用するために Windows VHD を準備する前に、以下のことを確認してください。

- サポート対象バージョンの Windows が含まれる VHD または VHDX を用意してある。 Azure Stack Edge Pro で[サポートされているゲスト オペレーティング システム]()に関するページを参照してください。 
- Hyper-V マネージャーがインストールされている Windows クライアントにアクセスできる。 
- 準備後に VHD を格納するため、Azure の BLOB ストレージ アカウントにアクセスできる。

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>VHD から、一般化された Windows イメージを準備する

## <a name="convert-to-a-fixed-vhd"></a>固定 VHD に変換する 

お使いのデバイス用の VM イメージを作成するためには、固定サイズの VHD が必要です。 ソースの Windows VHD または VHDX を固定 VHD に変換する必要があります。 次の手順に従います。

1. お使いのクライアント システムで Hyper-V マネージャーを開きます。 **[ディスクの編集]** に移動します。

    ![Hyper-V マネージャーを開きます](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. **[開始する前に]** ページで、 **[次へ>]** を選択します。

1. **[仮想ハード ディスクの場所]** ページで、変換するソース Windows VHD または VHDX の場所を参照します。 **[次へ>]** を選択します。

    ![[仮想ハード ディスクの場所] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. **[アクションの選択]** ページで、 **[変換]** を選択し、 **[次へ>]** を選択します。

    ![[アクションの選択] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. **[ディスク フォーマットの選択]** ページで、 **[VHD]** フォーマットを選択してから、 **[次へ>]** を選択します。

   ![[ディスク フォーマットの選択] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. **[ディスクの種類の選択]** ページで、 **[固定サイズ]** を選択し、 **[次へ>]** を選択します。

   ![[ディスクの種類の選択] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. **[ディスクの構成]** ページで、目的の場所に移動して、固定サイズの VHD ディスクの名前を指定します。 **[次へ>]** を選択します。

   ![[ディスクの構成] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. 概要を確認し、 **[完了]** を選択します。 VHD または VHDX の変換には、数分間かかります。 変換の時間は、ソース ディスクのサイズによって異なります。 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
この固定 VHD は、この記事の後続の手順すべてで使用します。


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>固定 VHD から Hyper-V VM を作成する

1. **Hyper-V マネージャー** のスコープ ペインで、システム ノードを右クリックしてコンテキスト メニューを開いてから、 **[新規]**  >  **[仮想マシン]** を選択します。

    ![[スコープ] ペインで新しい仮想マシンを選択します。](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. 仮想マシンの新規作成ウィザードの **[開始する前に]** ページで、 **[次へ]** を選択します。

1. **[名前と場所を指定]** ページで、仮想マシンの **名前** と **場所** を指定します。 **[次へ]** を選択します。

    ![VM の名前と場所を指定します](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. **[世代の指定]** ページで、.vhd デバイスのイメージの種類として **[第 1 世代]** を選択してから、 **[次へ]** を選択します。    

    ![世代を指定します](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. 必要なメモリとネットワークの構成を割り当てます。

1. **[仮想ハード ディスクの接続]** ページで、 **[既存の仮想ハード ディスクを使用する]** を選択し、先ほど作成した Windows 固定 VHD の場所を指定してから、 **[次へ]** をクリックします。

    ![[仮想ハード ディスクの接続] ページ](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. **[概要]** を確認してから、 **[完了]** を選択して仮想マシンを作成します。

仮想マシンの作成には、数分かかります。
     

## <a name="connect-to-the-hyper-v-vm"></a>Hyper-V VM に接続する

クライアント システムの仮想マシンの一覧に、VM が表示されます。 


1. 目的の VM を選択して右クリックし、 **[開始]** を選択します。 

    ![VM を選択して起動します](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. VM は、 **[実行中]** と表示されます。 目的の VM を選択して右クリックし、 **[接続]** を選択します。

    ![VM への接続](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

VM に接続したら、マシンのセットアップ ウィザードを完了してから VM にサインインします。


## <a name="generalize-the-vhd"></a>VHD を汎用化する  

*sysprep* ユーティリティを使用して VHD を一般化します。 

1. VM 内でコマンド プロンプトを開きます。
1. 次のコマンドを実行して VHD を一般化します。 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    詳細については、[sysprep (システムの準備) の概要](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)に関するページを参照してください。
1.  コマンドが完了すると、VM はシャットダウンされます。 **VM は再起動しないでください**。

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Azure BLOB ストレージに VHD をアップロードする

VHD を使用して、一般化されたイメージを Azure Stack Edge 上に作成できるようになりました。 

1. Azure BLOB ストレージに VHD をアップロードします。 [Azure Storage Explorer を使用して VHD をアップロードする](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md)ことに関するページの詳細な手順を参照してください。
1. アップロードが完了したら、アップロードされたイメージを使用して VM イメージや VM を作成できます。 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>次のステップ

デプロイの性質に応じて、次のいずれかの手順を選択できます。

- [Azure portal を使用して、一般化されたイメージから VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Azure PowerShell を使用して、一般化されたイメージから VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
