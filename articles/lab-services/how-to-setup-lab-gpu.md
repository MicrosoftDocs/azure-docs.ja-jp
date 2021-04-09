---
title: Azure Lab Services で GPU を使用してラボを設定する | Microsoft Docs
description: グラフィックス処理装置 (GPU) 仮想マシンを使用してラボを設定する方法について説明します。
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 8293ed1bfb53895b9631d9730fb75a2364457180
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96452374"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>GPU 仮想マシンを使用してラボを設定する

この記事では、次のタスクの手順について説明します。

- "*視覚化*" と "*コンピューティング*" のどちらのグラフィックス処理装置 (GPU) にするかを選択します。
- 適切な GPU ドライバーがインストールされていることを確認します。

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>視覚化またはコンピューティングの GPU サイズを選択する
ラボ作成ウィザードの最初のページの **[必要な仮想マシン サイズを選択してください。]** ドロップダウン リストで、クラスに必要な VM のサイズを選択します。  

![[新しいラボ] ウィンドウの VM サイズ選択のスクリーンショット](./media/how-to-setup-gpu/lab-gpu-selection.png)

このプロセスでは、**視覚化** GPU または **コンピューティング** GPU を選択できます。  学生が使用するソフトウェアに基づいて GPU の種類を選択することが重要です。  

次の表で説明するように、"*コンピューティング*" GPU サイズはコンピューティング集約型アプリケーションを対象としています。  たとえば、[自然言語処理でのディープ ラーニング クラスの種類](./class-type-deep-learning-natural-language-processing.md)では、**小規模 GPU (コンピューティング)** サイズを使用します。  学生は [Data Science Virtual Machine イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804)で提供されるディープ ラーニング フレームワークとツールを使用して、大量のデータのセットでディープ ラーニング モデルをトレーニングするので、この種類のクラスにはコンピューティング GPU が適しています。

| サイズ | コア | RAM | 説明 | 
| ---- | ----- | --- | ----------- | 
| Small GPU (Compute) | -&nbsp;6&nbsp;コア<br>-&nbsp;56&nbsp;GB&nbsp;RAM  | [Standard_NC6](../virtual-machines/nc-series.md) |このサイズは、人工知能 (AI) やディープ ラーニングなどのコンピューティング集中型アプリケーションに最適です。 |

"*視覚化*" GPU サイズは、グラフィックス集約型アプリケーションを対象としています。  たとえば、[SOLIDWORKS エンジニアリング クラスの種類](./class-type-solidworks.md)では、**小規模 GPU (視覚化)** サイズの使用が示されています。  学生はソリッド オブジェクトのモデリングと視覚化に SOLIDWORKS 3D コンピューター支援設計 (CAD) 環境を使用するため、この種類のクラスには視覚化 GPU が適しています。

| サイズ | コア | RAM | 説明 | 
| ---- | ----- | --- | ----------- | 
| Small GPU (視覚化) | -&nbsp;6&nbsp;コア<br>-&nbsp;56&nbsp;GB&nbsp;RAM  | [Standard_NV6](../virtual-machines/nv-series.md) | このサイズは、OpenGL や DirectX などのフレームワークを使用するリモート視覚化、ストリーミング、ゲーム、エンコードに最適です。 |
| Medium GPU (視覚化) | -&nbsp;12&nbsp;コア<br>-&nbsp;112&nbsp;GB&nbsp;RAM  | [Standard_NV12](../virtual-machines/nv-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) | このサイズは、OpenGL や DirectX などのフレームワークを使用するリモート視覚化、ストリーミング、ゲーム、エンコードに最適です。 |

> [!NOTE]
> クラスルーム ラボを作成する際、これらの VM サイズの一部が一覧に表示されない場合があります。 このリストの内容は、ラボの場所の現在の容量に基づいて表示されます。 ラボ アカウントの作成者が、[ラボの作成者にラボの場所の選択を許可している](allow-lab-creator-pick-lab-location.md)場合は、ラボに対して別の場所を選択し、VM サイズが表示されるかどうかを確認してください。 VM を利用できるリージョンについては、「[リージョン別の利用可能な製品](https://azure.microsoft.com/regions/services/?products=virtual-machines)」を参照してください。

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>適切な GPU ドライバーがインストールされていることを確認する
ラボ VM の GPU 機能を利用するには、適切な GPU ドライバーがインストールされていることを確認します。  ラボ作成ウィザードで、GPU の VM サイズを選択するときに、 **[GPU ドライバーをインストールする]** オプションを選択できます。  

![[新しいラボ] の [GPU ドライバーをインストールする] オプションのスクリーンショット](./media/how-to-setup-gpu/lab-gpu-drivers.png)

上の図に示すように、このオプションは既定で有効になっており、選択した GPU とイメージの種類に対する "*最新の*" ドライバーが確実にインストールされます。
- "*コンピューティング*" GPU サイズを選択すると、ラボ VM は [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) GPU によって増強されます。  この場合、最新の [Compute Unified Device Architecture (CUDA)](http://developer.download.nvidia.com/compute/cuda/2_0/docs/CudaReferenceManual_2.0.pdf) ドライバーがインストールされ、ハイパフォーマンス コンピューティングが可能になります。
- "*視覚化*" GPU サイズを選択すると、ラボの VM は [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU と [GRID テクノロジ](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf)によって増強されます。  この場合、最新の GRID ドライバーがインストールされ、グラフィックス集約型アプリケーションを使用できるようになります。

### <a name="install-the-drivers-manually"></a>ドライバーを手動でインストールする
最新バージョン以外のドライバーのバージョンをインストールすることが必要になる場合があります。  このセクションでは、"*コンピューティング*" GPU または "*視覚化*" GPU のどちらを使用しているかに応じて、適切なドライバーを手動でインストールする方法を示します。

#### <a name="install-the-compute-gpu-drivers"></a>コンピューティング GPU ドライバーをインストールする

コンピューティング GPU サイズのドライバーを手動でインストールするには、次のようにします。

1. ラボ作成ウィザードで、[ラボを作成する](./how-to-manage-classroom-labs.md)ときに、 **[GPU ドライバーをインストールする]** の設定を無効にします。

1. ラボが作成された後、テンプレート VM に接続して、適切なドライバーをインストールします。

   ![NVIDIA ドライバーのダウンロード ページのスクリーンショット](./media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. ブラウザーで、[NVIDIA ドライバーのダウンロード ページ](https://www.nvidia.com/Download/index.aspx)にアクセスします。  
   b. **[Product Type]\(製品のタイプ\)** を **[Tesla]** に設定します。  
   c. **[Product Series]\(製品シリーズ\)** を **[K-Series]** に設定します。  
   d. **[Operating System]\(オペレーティング システム\)** を、ラボの作成時に選択した基本イメージの種類に従って設定します。  
   e. **[CUDA Toolkit]\(CUDA Toolkit\)** を、必要な CUDA ドライバーのバージョンに設定します。  
   f. **[Search]\(検索\)** を選択して、ドライバーを検索します。  
   g. **[Download]\(ダウンロード\)** を選択して、インストーラーをダウンロードします。  
   h. ドライバーがテンプレート VM にインストールされるように、インストーラーを実行します。  
1. 「[インストールされているドライバーを検証する](how-to-setup-lab-gpu.md#validate-the-installed-drivers)」セクションの手順に従って、ドライバーが正しくインストールされていることを検証します。 
1. ドライバーと、クラスに必要な他のソフトウェアをインストールした後、 **[発行]** を選択して学生の VM を作成します。

> [!NOTE]
> Linux イメージを使用している場合は、インストーラーをダウンロードした後、[Linux への CUDA ドライバーのインストール](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#install-cuda-drivers-on-n-series-vms)に関するページの手順に従って、ドライバーをインストールします。

#### <a name="install-the-visualization-gpu-drivers"></a>視覚化 GPU ドライバーをインストールする

視覚化 GPU サイズのドライバーを手動でインストールするには、次のようにします。

1. ラボ作成ウィザードで、[ラボを作成する](./how-to-manage-classroom-labs.md)ときに、 **[GPU ドライバーをインストールする]** の設定を無効にします。
1. ラボが作成された後、テンプレート VM に接続して、適切なドライバーをインストールします。
1. お使いのオペレーティング システムの指示に従って、Microsoft から提供されている GRID ドライバーをテンプレート VM にインストールします。
   -  [Windows NVIDIA GRID ドライバー](../virtual-machines/windows/n-series-driver-setup.md#nvidia-grid-drivers)
   -  [Linux NVIDIA GRID ドライバー](../virtual-machines/linux/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#nvidia-grid-drivers)
  
1. テンプレート VM を再起動します。
1. 「[インストールされているドライバーを検証する](how-to-setup-lab-gpu.md#validate-the-installed-drivers)」セクションの手順に従って、ドライバーが正しくインストールされていることを検証します。
1. ドライバーと、クラスに必要な他のソフトウェアをインストールした後、 **[発行]** を選択して学生の VM を作成します。

### <a name="validate-the-installed-drivers"></a>インストールされているドライバーを検証する
このセクションでは、GPU ドライバーが正しくインストールされていることを検証する方法について説明します。

#### <a name="windows-images"></a>Windows イメージ
1.  「[Windows を実行している N シリーズ VM に NVIDIA GPU ドライバーをインストールする](../virtual-machines/windows/n-series-driver-setup.md#verify-driver-installation)」の「ドライバーのインストールの確認」セクションの手順に従います。
1.  "*視覚化*" GPU を使用している場合は、次のようにすることもできます。
    - NVIDIA コントロール パネルで GPU の設定を表示および調整します。 これを行うには、**Windows コントロール パネル** で **[ハードウェア]** を選択してから、 **[NVIDIA Control Panel]\(NVIDIA コントロール パネル\)** を選択します。

      ![[NVIDIA Control Panel]\(NVIDIA コントロール パネル\) のリンクが示されている Windows コントロール パネルのスクリーンショット](./media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - **タスク マネージャー** を使用して、GPU のパフォーマンスを表示します。  そのためには、 **[パフォーマンス]** タブを選択し、 **[GPU]** オプションを選択します。

       ![タスク マネージャーの GPU パフォーマンス タブのスクリーンショット](./media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > NVIDIA コントロール パネルの設定には、"*視覚化*" GPU の場合にのみアクセスできます。  コンピューティング GPU の場合に NVIDIA コントロール パネルを開こうとすると、次のエラーが表示されます。"NVIDIA Display settings are not available.  You are not currently using a display attached to an NVIDIA GPU." (NVIDIA ディスプレイの設定は使用できません。現在、NVIDIA GPU に接続されているディスプレイは使用されていません。)  同様に、タスク マネージャーの GPU パフォーマンス情報は、視覚化 GPU に対してのみ提供されます。

#### <a name="linux-images"></a>Linux イメージ
「[Linux を実行している N シリーズ VM に NVIDIA GPU ドライバーをインストールする](../virtual-machines/linux/n-series-driver-setup.md#verify-driver-installation)」の「ドライバーのインストールの確認」セクションの手順に従います。

## <a name="next-steps"></a>次のステップ
次の記事をご覧ください。

- [ラボを作成して管理する](how-to-manage-classroom-labs.md)
- [SOLIDWORKS のコンピューター支援設計 (CAD) クラスの種類](class-type-solidworks.md)
- [MATLAB (matrix laboratory) クラスの種類](class-type-matlab.md)