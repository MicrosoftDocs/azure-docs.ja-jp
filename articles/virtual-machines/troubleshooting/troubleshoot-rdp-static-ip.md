---
title: 静的 IP のために Azure Virtual Machines にリモート デスクトップ接続できない | Microsoft Docs
description: Microsoft Azure で静的 IP が原因で発生する RDP の問題をトラブルシューティングする方法について学習します。 | Microsoft Docs
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
ms.date: 11/08/2018
ms.author: genli
ms.openlocfilehash: 81a3064290e0aa720a4fe6b0fa0d8eb13cfe6903
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141800"
---
#  <a name="cannot-remote-desktop-to-azure-virtual-machines-because-of-static-ip"></a>静的 IP のために Azure Virtual Machines にリモート デスクトップ接続できない

この記事では、Azure Windows Virtual Machines (VM) に静的 IP を構成した後で VM にリモート デスクトップ接続できない問題について説明します。

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)の 2 種類のデプロイ モデルがあります。 この記事では、Resource Manager デプロイ モデルの使用方法について説明しています。最新のデプロイでは、クラシック デプロイ モデルではなくこのモデルを使用することをお勧めします。

## <a name="symptoms"></a>現象

Azure の VM に RDP 接続を行うと、次のようなエラー メッセージを受け取ります。

**[Remote Desktop can't connect to the remote computer for one of these reasons:]\(リモート デスクトップが次のいずれかの理由のためにリモート コンピューターに接続できません。\)**

1. **[Remote access to the server is not enabled]\(サーバーへのリモートアクセスが無効です\)**

2. **[The remote Computer is turned off]\(リモート コンピューターの電源が入っていません\)**

3. **[The remote computer is not available on the network]\(リモート コンピューターがネットワーク上にありません\)**

**[Make sure the remote computer is turned on and connected to the network, and that remote access is enabled.]\(リモート コンピューターの電源が入っており、ネットワークに接続されていること、リモート アクセスが有効なことを確認してください。\)**

Azure portal で[ブート診断](../troubleshooting/boot-diagnostics.md)のスクリーンショットを確認すると、VM は正常に起動し、ログイン画面で資格情報を待っていることがわかります。

## <a name="cause"></a>原因

VM には、Windows 内のネットワーク インターフェイスで定義されている静的 IP アドレスが設定されています。 この IP アドレスが、Azure portal で定義されているアドレスと異なります。

## <a name="solution"></a>解決策

これらの手順を実行する前に、バックアップとして、影響を受ける VM の OS ディスクのスナップショットを取得します。 詳細については、[ディスクのスナップショット](../windows/snapshot-copy-managed-disk.md)に関する記事を参照してください。

この問題を解決するには、シリアル コントロールを使用して DHCP を有効にするか、または VM に対する[ネットワーク インターフェイスをリセット](reset-network-interface.md)します。

### <a name="use-serial-control"></a>シリアル コントロールを使用する

1. [シリアル コンソールに接続し、CMD インスタンスを開きます](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
)。 VM でシリアル コンソールが有効になっていない場合は、「[ネットワーク インターフェイスをリセットする](reset-network-interface.md)」をご覧ください。
2. ネットワーク インターフェイスで DHCP が無効になっているかどうかを確認します。

        netsh interface ip show config
3. DHCP が無効になっている場合は、DHCP を使用するようにネットワーク インターフェイスの構成を戻します。

        netsh interface ip set address name="<NIC Name>" source=dhc

    たとえば、ネットワーク インターフェイスの名前が "Ethernet 2" の場合は、次のコマンドを実行します。

        netsh interface ip set address name="Ethernet 2" source=dhc

4. 再度 IP 構成を照会して、ネットワーク インターフェイスが正しく設定されていることを確認します。 新しい IP アドレスは、Azure によって提供されるものと一致している必要があります。

        netsh interface ip show config

    この時点で、VM を再起動する必要はありません。 VM は到達可能に戻ります。

その後、VM に静的 IP を構成したい場合は、[VM への静的 IP アドレスの構成](../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md)に関するページをご覧ください。