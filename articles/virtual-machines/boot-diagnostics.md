---
title: Azure のブート診断
description: Azure のブート診断とマネージ ブート診断の概要
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067088"
---
# <a name="azure-boot-diagnostics"></a>Azure のブート診断

ブート診断は、VM ブート エラーの診断を可能にする、Azure の仮想マシン (VM) のデバッグ機能です。 ブート診断を使用すると、ユーザーは、シリアル ログ情報とスクリーンショットを収集して、起動中の VM の状態を確認できます。

## <a name="boot-diagnostics-view"></a>ブート診断のビュー
仮想マシンのブレードにある [ブート診断] オプションは、Azure portal の *[サポートとトラブルシューティング]* セクションにあります。 [ブート診断] を選択すると、スクリーンショットとシリアル ログ情報が表示されます。 シリアル ログには、カーネル メッセージングが含まれています。スクリーンショットは、VM の現在の状態のスナップショットです。 VM で Windows と Linux のどちらが実行されているかによって、どのような外観のスクリーンショットが予想されるかが決まります。 Windows の場合、ユーザーにはデスクトップの背景が表示され、Linux の場合はログイン プロンプトが表示されます。

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Linux のブート診断のスクリーンショット":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Windows のブート診断のスクリーンショット":::


## <a name="limitations"></a>制限事項
- ブート診断は、Azure Resource Manager VM でのみ使用できます。 
- ブート診断は、Premium Storage アカウントをサポートしていません。ブート診断に Premium Storage アカウントを使用すると、ユーザーは VM の起動時に `StorageAccountTypeNotSupported` エラーを受け取ります。 

## <a name="next-steps"></a>次の手順

[Azure シリアル コンソール](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview)の詳細、およびブート診断を使用して [Azure の仮想マシンでトラブルシューティング](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)を行う方法について確認します。
