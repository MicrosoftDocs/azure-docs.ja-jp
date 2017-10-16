---
title: "Azure Cloud Shell (プレビュー) でファイルを永続化する | Microsoft Docs"
description: "Azure Cloud Shell でファイルを永続化する方法についてのチュートリアルです。"
services: azure
documentationcenter: 
author: maertendmsft
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: damaerte
ms.openlocfilehash: 933af3860bfe087a0b4db7eff53d4b978a1475da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
[!include [features-introblock](../../includes/cloud-shell-persisting-shell-storage-introblock.md)]

## <a name="how-cloud-shell-works"></a>Cloud Shell のしくみ
Cloud Shell は、次の方法を使ってファイルを永続化します。 
* ファイル共有を直接操作できるように、指定されたファイル共有を `$Home` ディレクトリに `clouddrive` としてマウントします。

## <a name="list-cloud-drive-file-shares"></a>クラウド ドライブ ファイル共有の一覧の取得
`Get-CloudDrive` コマンドは、Cloud Shell のクラウド ドライブによって現在マウントされているファイル共有の情報を取得します。 <br>
![Get-CloudDrive の実行](media/persisting-shell-storage-powershell/Get-Clouddrive.png)

## <a name="unmount-cloud-drive"></a>クラウド ドライブのマウント解除
Cloud Shell にマウントされたファイル共有は、いつでもマウントを解除することができます。 ファイル共有が削除された場合は、次回のセッション時に新しいファイル共有を作成してマウントするように求められます。

`Dismount-CloudDrive` コマンドは、現在のストレージ アカウントからファイル共有をマウント解除します。 クラウド ドライブをマウント解除すると、現在のセッションが終了します。 次のセッションでは、新しいファイル共有を作成してマウントするように求められます。
![Dismount-CloudDrive の実行](media/persisting-shell-storage-powershell/Dismount-Clouddrive.png)

[!include [features-endblock](../../includes/cloud-shell-persisting-shell-storage-endblock.md)]

## <a name="next-steps"></a>次のステップ
[PowerShell のクイックスタート](quickstart-powershell.md) <br>
[Azure File Storage について](https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) <br>
[ストレージのタグについて](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) <br>