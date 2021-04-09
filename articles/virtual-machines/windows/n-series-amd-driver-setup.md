---
title: Windows 用 Azure N シリーズ AMD GPU ドライバーのセットアップ
description: Azure で Windows Server または Windows を実行する N シリーズ VM 用の AMD GPU ドライバーの設定方法
author: vikancha-MSFT
manager: jkabat
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/4/2019
ms.author: vikancha
ms.openlocfilehash: 62723a0fee6a3f696c517bc642fdac8cfa80a6b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557422"
---
# <a name="install-amd-gpu-drivers-on-n-series-vms-running-windows"></a>Windows を実行している N シリーズ VM に AMD GPU ドライバーをインストールする

Windows を実行している新しい Azure NVv4 シリーズ VM の GPU 機能を利用するには、AMD GPU ドライバーがインストールされている必要があります。 [AMD GPU ドライバー拡張機能](../extensions/hpccompute-amd-gpu-windows.md)は、NVv4 シリーズ VM に AMD GPU ドライバーをインストールします。 この拡張機能は、Azure Portal または Azure PowerShell や Azure Resource Manager テンプレートなどのツールを使用してインストールまたは管理します。 サポートされるオペレーティング システムおよびデプロイ手順については、[AMD GPU ドライバー拡張機能のドキュメント](../extensions/hpccompute-amd-gpu-windows.md)を参照してください。

AMD GPU ドライバーを手動でインストールすることを選択した場合、この記事では、サポートされるオペレーティング システム、ドライバー、インストールおよび検証手順について説明します。

NVv4 VM では、Microsoft によって公開された GPU ドライバーのみがサポートされます。 他のソースから GPU ドライバーをインストールしないでください。

基本仕様、ストレージの容量、およびディスクの詳細については、「[GPU Windows VM のサイズ](../sizes-gpu.md?toc=/azure/virtual-machines/windows/toc.json)」を参照してください。



## <a name="supported-operating-systems-and-drivers"></a>サポートされているオペレーティング システムとドライバー

| OS | Driver |
| -------- |------------- |
| Windows 10 Enterprise マルチセッション - ビルド 1909 <br/><br/>Windows 10 - ビルド 1909<br/><br/>Windows Server 2016<br/><br/>Windows Server 2019 | [20.Q4](https://download.microsoft.com/download/f/1/6/f16e6275-a718-40cd-a366-9382739ebd39/AMD-Azure-NVv4-Driver-20Q4.exe) (.exe) |

 > [!NOTE]
   >  ビルド 1903 または 1909 を使用する場合は、最適なパフォーマンスを得るために、次のグループ ポリシーの更新が必要になることがあります。 これらの変更は、他の Windows ビルドには必要ありません。
   >  
   >  [コンピューターの構成]->[ポリシー]->[Windows の設定]->[管理用テンプレート]->[Windows コンポーネント]->[リモート デスクトップ サービス]->[リモート デスクトップ セッション ホスト]->[リモート セッション環境] の順に選択し、ポリシー [Use WDDM graphics display driver for Remote Desktop Connections]\(リモート デスクトップ接続に WDDM グラフィックス表示ドライバーを使用する\) を [無効] に設定します。
   >  


## <a name="driver-installation"></a>ドライバーのインストール

1. リモートデスクトップで、各 NVv4 シリーズ VM に接続します。

2. 以前のバージョンのドライバーをアンインストールする必要がある場合は、[こちら](https://download.microsoft.com/download/4/f/1/4f19b714-9304-410f-9c64-826404e07857/AMDCleanupUtilityni.exe)から AMD クリーンアップ ユーティリティをダウンロードします。以前のバージョンのドライバーに付属しているユーティリティは使用しないでください。

3. 最新のドライバーをダウンロードしてインストールします。

4. VM を再起動してください。

## <a name="verify-driver-installation"></a>ドライバーのインストールの確認

ドライバーのインストールはデバイス マネージャーで確認できます。 次の例は、Azure NVv4 VM での Radeon Instinct MI25 カードの正常な構成を示しています。
<br />

![Azure NVv4 VM での Radeon Instinct MI25 カードの正常な構成を示すスクリーンショット。](./media/n-series-amd-driver-setup/device-manager.png)

dxdiag を使用して、ビデオ RAM などの GPU 表示プロパティを確認できます。 次の例は、Azure NVv4 VM での Radeon Instinct MI25 カードの 1/2 パーティションを示しています。
<br />
![Azure NVv4 VM での Radeon Instinct MI25 カードの 1/2 パーティションを示すスクリーンショット。](./media/n-series-amd-driver-setup/dxdiag-output-new.png)

Windows 10 ビルド1903 以降を実行している場合、dxdiag では [表示] タブに情報が表示されません。下部にある [すべての情報を保存する] オプションを使用してください。出力ファイルには、AMD MI25 GPU に関連する情報が表示されます。

![GPU ドライバーのプロパティ](./media/n-series-amd-driver-setup/dxdiag-details.png)
