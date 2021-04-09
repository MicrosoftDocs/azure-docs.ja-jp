---
title: Azure Lab Services - カスタム イメージを Shared Image Gallery にアップロードする
description: カスタム イメージを Shared Image Gallery にアップロードする方法について説明します。 大学の IT 部門は、イメージのインポートが特に有益であることがわかります。
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: 3e2f38b0cab87eab27181ddef79d0c02bd8c9bdb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98787165"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>カスタム イメージを Shared Image Gallery にアップロードする

Azure Lab Services でラボを作成するための独自のカスタム イメージをインポートするために、Shared Image Gallery を利用できます。 次のように理由で、大学の IT 部門の場合はイメージのインポートが特に有益です。 

* ラボのテンプレート VM を使用してイメージを手動で作成する必要はありません。
* SCCM、Endpoint Manager などの他のツールを使用して作成されたイメージをアップロードできます。

この記事では、カスタム イメージを作成して Azure Lab Services で使用するために実行できる手順について説明します。 

> [!IMPORTANT]
> 物理ラボ環境から Az Labs にイメージを移動する場合は、それらを適切に再構築する必要があります。 物理ラボの既存のイメージを単に再利用することは避けてください。 <br/>詳細については、ブログ記事「[物理ラボから Azure Lab Services への移動](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931)」を参照してください。

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>物理ラボ環境からのカスタム イメージの取り込み

以下の手順では、物理ラボ環境から開始するカスタム イメージをインポートする方法を示します。 その後、この環境から VHD を作成し、Azure の Shared Image Gallery にインポートして、Azure Lab Services で使用できるようにします。

物理ラボ環境からの VHD の作成については、多くのオプションがあります。 以下の手順では、Windows Hyper-V VM から VHD を作成する方法を示します。

1. ご自分のイメージから作成されたご自分の物理ラボ環境の Hyper-V VM から始めます。
    1. VM は第 1 世代の VM として作成されている必要があります。
    1. VM には固定ディスク サイズが使用されている必要があります。 また、このウィンドウでディスクのサイズを指定することもできます。 ディスクのサイズは 128 GB 以下である必要があります。<br/>    
    ディスク サイズが 128 GB より大きいイメージは、Azure Lab Services ではサポートされていません。 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="仮想ハード ディスクを接続する":::   
    1. 通常どおりに VM のイメージを作成します。
1. [VM に接続して Azure 用に準備します](../virtual-machines/windows/prepare-for-upload-vhd-image.md)。
    1. [Azure 用の Windows 構成を設定する](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)
    1. [VM の接続を確保するために最低限必要な Windows サービスを確認します](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services)
    1. [リモート デスクトップのレジストリ設定を更新する](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)
    1. [Windows ファイアウォール規則の構成](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)
    1. Windows 更新プログラムのインストール
    1. [ここで示されているように、Azure VM エージェントと追加の構成をインストールします](../virtual-machines/windows/prepare-for-upload-vhd-image.md#complete-the-recommended-configurations) 
    
    上記の手順を実行すると、特殊化されたイメージが作成されます。 一般化されたイメージを作成する場合は、[SysPrep](../virtual-machines/windows/prepare-for-upload-vhd-image.md#determine-when-to-use-sysprep) を実行する必要もあります。 <br/>
        イメージに含まれるソフトウェアで必要なユーザー ディレクトリ (ファイル、ユーザー アカウント情報などが含まれる場合があります) を維持する必要がある場合は、特殊化されたイメージを作成する必要があります。
1. **Hyper-V** の場合は既定で **VHDX** ファイルが作成されるため、これを VHD ファイルに変換する必要があります。
    1. **[Hyper-V マネージャー]**  ->  **[操作]**  ->  **[ディスクの編集]** に移動します。
    1. ここには、VHDX から VHD へのディスクの **[変換]** オプションがあります。
    1. ディスク サイズを拡張する場合は、128 GB を超えないようにしてください。        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="アクションの選択":::   
1. VHD を Azure にアップロードして、マネージド ディスクを作成します。
    1. Storage Explorer またはコマンド ラインから AzCopy を使用することができます。「[VHD を Azure にアップロードするか、他のリージョンにマネージド ディスクをコピーする](../virtual-machines/windows/disks-upload-vhd-to-managed-disk-powershell.md)」の説明を参照してください。        
    コンピューターがスリープ状態またはロック状態になった場合、アップロード プロセスが中断され、失敗する可能性があります。
    1. このステップを終えると、Azure portal で確認できるマネージド ディスクが作成されます。 
        Azure portal の [Size\Performance]\(サイズ\パフォーマンス\) タブを使用して、ディスク サイズを選択できます。 前述のように、サイズが 128 GB を超えないようにする必要があります。
1. マネージド ディスクのスナップショットを作成します。
    これは、PowerShell、Azure portal、または Storage Explorer を使用して行うことができます。「[ポータルまたは PowerShell を使用してスナップショットを作成する](../virtual-machines/windows/snapshot-copy-managed-disk.md)」を参照してください。
1. Shared Image Gallery で、イメージの定義とバージョンを作成します。
    1. [イメージの定義を作成します](../virtual-machines/windows/shared-images-portal.md#create-an-image-definition)。
    1. 特殊化されたイメージまたは一般化されたイメージのどちらを作成するかも指定する必要があります。
1. Azure Lab Services でラボを作成し、Shared Image Gallery からカスタム イメージを選択します。

    元の Hyper-V VM に OS をインストールした後でディスクを拡張した場合は、未割り当てのディスク領域を使用するように Windows の C ドライブを拡張する必要もあります。 これを行うには、ラボの作成後にテンプレート VM にログインし、「[ベーシック ボリュームを拡張する](/windows-server/storage/disk-management/extend-a-basic-volume)」で示されているような手順に従います。 UI または PowerShell を使用して行うことができます。

## <a name="next-steps"></a>次のステップ

* [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)
* [Shared Image Gallery を使用する方法](how-to-use-shared-image-gallery.md)