---
title: NIC が無効になっているために Azure 仮想マシンにリモート接続できない | Microsoft Docs
description: Azure VM の NIC が無効になっているため、RDP が失敗する問題をトラブルシューティングする方法について説明します | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/12/2018
ms.author: genli
ms.openlocfilehash: 742026a8ff35f318f58674ebc2fb5c03e45161a8
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133334"
---
#  <a name="cannot-remote-desktop-to-a-vm-because-the-network-interface-is-disabled"></a>ネットワーク インターフェイスが無効になっているために VM にリモート デスクトップ接続できない

この記事では、ネットワーク インターフェイスが無効になっている場合に、Azure Windows 仮想マシン (VM) にリモート デスクトップ接続できない問題を解決する方法を説明します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルが用意されています。[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、Resource Manager デプロイ モデルの使用方法について説明しています。最新のデプロイでは、クラシック デプロイ モデルではなくこのモデルを使用することをお勧めします。

## <a name="symptoms"></a>現象

VM でネットワーク インターフェイスが無効になっているため、Azure 上で VM に対して RDP 接続または他の任意のポートへの他の種類の接続を行うことができません。

## <a name="solution"></a>解決策

これらの手順を実行する前に、バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。

VM のインターフェイスを有効にするには、VM に対してシリアル コントロールを使用するか、[ネットワーク インターフェイスをリセット](##reset-network-interface)します。

### <a name="use-serial-control"></a>シリアル コントロールを使用する

1. [シリアル コンソールに接続し、CMD インスタンスを開きます](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
)。 VM でシリアル コンソールが有効になっていない場合は、「[ネットワーク インターフェイスをリセットする](#reset-network-interface)」をご覧ください。
2. ネットワーク インターフェイスの状態をチェックします。

        netsh interface show interface

    無効になっているネットワーク インターフェイスの名前をメモします。

3. ネットワーク インターフェイスを有効にします。

        netsh interface set interface name="interface Name" admin=enabled

    たとえば、ネットワーク インターフェイスの名前が "Ethernet 2" の場合は、次のコマンドを実行します。

        netsh interface set interface name=""Ethernet 2" admin=enabled


4.  ネットワーク インターフェイスの状態をもう一度チェックして、ネットワーク インターフェイスが有効になっていることを確認します。

        netsh interface show interface

    この時点で VM を再起動する必要はありません。 VM は到達可能に戻ります。

5.  VM に接続して、問題が解決されたかどうかを確認します。

## <a name="reset-network-interface"></a>ネットワーク インターフェイスをリセットする

ネットワーク インターフェイスをリセットするには、[IP アドレス] を、サブネット内で使用できる別の IP アドレスに変更します。 これを行うには、Azure portal または Azure PowerShell を使用します。 詳しくは、[ネットワーク インターフェイスのリセット](reset-network-interface.md)に関する記事をご覧ください。