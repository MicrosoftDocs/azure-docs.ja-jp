---
title: Azure で仮想マシンを作成する場合に VHD がサポートされない | Microsoft Docs
description: この記事は、Microsoft Azure で仮想マシンを実行するときの VHD エラーを修正するために役立ちます。
services: virtual-machines
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines
ms.assetid: 5488aba9-c3da-435d-b4a5-63470f455b07
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 06/29/2020
ms.author: genli
ms.openlocfilehash: 23f31e8d36d6ba615b21a4043c7d39716fc6d89a
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653496"
---
# <a name="vhd-is-not-supported-when-you-create-a-virtual-machine-in-azure"></a>Azure で仮想マシンを作成する場合に VHD がサポートされない

この記事は、Windows または Linux で仮想マシンを実行するときの VHD エラーを修正するために役立ちます。

## <a name="symptoms"></a>現象

アップロードされた VHD を使用して Microsoft Azure で仮想マシンを作成すると、デプロイが失敗し、次のエラー メッセージが返されます。 

```
New-AzureRmVM : Long running operation failed with status 'Failed'.
ErrorCode: InvalidVhd
ErrorMessage: The specified cookie value in VHD footer indicates that disk 'diskname' with blob https://xxxxxx.blob.core.windows.net/vhds/samplename.vhd is not a supported VHD. Disk is expected to have cookie value 'conectix'.
```

## <a name="cause"></a>原因

この問題は、次のいずれかの理由で発生します。

- VHD が 1 MB 配置 (オフセット) に準拠していない。 サポートされているディスク サイズは 1 MB * N です。たとえば、ディスクは 102,401 MB にする必要があります。
- VHD が破損しているか、サポートされていない。 

## <a name="resolution"></a>解決方法

> [!NOTE]
> 次の修正を実行するには、VHD を Azure にアップロードする前に、これらの手順を実行する必要があります。

この問題を解決するには、1 MB の配置に準拠するようにディスクのサイズを変更します。

- Windows で問題を解決するには、[Resize-VHD PowerShell コマンドレット](/powershell/module/hyper-v/resize-vhd)を使用します。 **Resize-VHD** は Azure PowerShell コマンドレットではないことに注意してください。

  1. [Windows Server に Hyper-V のロールをインストールする](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
  1. [仮想ディスクを容量固定の VHD に変換する](../windows/prepare-for-upload-vhd-image.md#convert-the-virtual-disk-to-a-fixed-size-vhd)

- Linux で問題を解決するには、[qemu-img コマンド](../linux/create-upload-generic.md)を使用します。

Azure VM を作成するための VHD を作成およびアップロードする方法については、以下の記事を参照してください。

- [Azure CLI 1.0 を使用してカスタム ディスク イメージをアップロードし、Linux VM を作成する](../linux/upload-vhd.md)
- [Windows Server VHD の作成と Azure へのアップロード](../windows/upload-generalized-managed.md)

引き続き問題が発生する場合は、VHD が破損している可能性があります。 このような場合は、VHD を最初から再構築することをお勧めします。

詳しくは、次の資料を参照してください。

- [VHD について](../managed-disks-overview.md)
