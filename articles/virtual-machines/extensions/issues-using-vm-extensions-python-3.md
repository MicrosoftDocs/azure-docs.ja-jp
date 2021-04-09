---
title: Python 3 対応 Linux Azure Virtual Machines システムでの VM 拡張機能の使用に関する問題
description: Python 3 対応 Linux システムで VM 拡張機能を使用する方法について説明します
services: virtual-machines
documentationcenter: ''
author: v-miegge
ms.author: jparrel
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-resource-manager
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/25/2020
ms.assetid: 3cd520fd-eaf7-4ef9-b4d3-4827057e5028
ms.openlocfilehash: d38b41bc47c01b01fcbb1b05238396dca6d8eb4e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558646"
---
# <a name="issues-using-vm-extensions-in-python-3-enabled-linux-azure-virtual-machines-systems"></a>Python 3 対応 Linux Azure Virtual Machines システムでの VM 拡張機能の使用に関する問題

> [!NOTE]
> Microsoft では、ワークロードで **Python 2.x** サポートが必要な場合を除き、システムに **Python 3.x** を導入することをユーザーにお勧めします。 この要件の例としては、レガシ管理スクリプト、**Azure Disk Encryption** や **Azure Monitor** などの拡張機能があります。
>
> **Python 2.x** を運用環境にインストールする前に、Python 2.x の長期的なサポート (特にセキュリティ更新プログラムを受信する機能) に関する問題を考慮してください。 前述の拡張機能の一部を含め、**Python 3.8** サポートで製品が更新されると、Python 2.x の使用を中止する必要があります。

一部の Linux ディストリビューションは Python 3.8 に移行し、Python 用のレガシ `/usr/bin/python` エントリポイントは完全に削除されました。 この移行は、次の 2 つの条件で、特定の仮想マシン (VM) 拡張機能のすぐ使える自動デプロイに影響します。

- Python 3.x サポートにまだ移行中の拡張機能
- レガシ `/usr/bin/python` エントリポイントを使用する拡張機能

**Python 3.x** に移行した Linux ディストリビューション ユーザーは、これらの拡張機能を VM にデプロイする前に、レガシ `/usr/bin/python` エントリポイントが確実に存在するようにする必要があります。 そうしないと、拡張機能のデプロイが失敗する可能性があります。 

- 影響を受ける Linux ディストリビューションには、**Ubuntu Server 20.04 LTS** や **Ubuntu Pro 20.04 LTS** などがあります。

- 影響を受ける VM 拡張機能には、**Azure Disk Encryption**、**Log Analytics**、**VM アクセス** (パスワード リセットに使用)、**ゲスト診断** (追加のパフォーマンス カウンターに使用) などがあります。

**Ubuntu 18.04 LTS** から **Ubuntu 20.04 LTS** へのアップグレードなど、インプレース アップグレードでは、`/usr/bin/python` シンボリック リンクを保持し、影響を受けないようにする必要があります。

## <a name="resolution"></a>解決策

前の概要で説明した、影響を受ける既知のシナリオに拡張機能をデプロイする前に、次の一般的な推奨事項を検討してください。

1. 拡張機能をデプロイする前に、Linux ディストリビューション ベンダー提供の方法を使用して、`/usr/bin/python` のシンボリック リンクを復元します。

   - たとえば **Python 2.7** の場合は、`sudo apt update && sudo apt install python-is-python2` を使用します。

1. この推奨事項は Azure のお客様を対象としており、Azure Stack ではサポートされていません。

   - この問題を示すインスタンスを既にデプロイしている場合は、[VM] ブレードの [実行コマンド] 機能を使用して、前述のコマンドを実行します。 実行コマンド拡張機能自体は、Python 3.8 への移行の影響を受けません。

1. 新しいインスタンスをデプロイし、プロビジョニング時に拡張機能を設定する必要がある場合は、**cloud-init** ユーザー データを使用して前述のパッケージをインストールします。

   たとえば、Python 2.7 の場合は次のようにします。

   ```python
   # create cloud-init config
   cat > cloudinitConfig.json <<EOF
   #cloud-config
   package_update: true
    
   runcmd:
   - sudo apt update
   - sudo apt install python-is-python2 
   EOF

   # create VM
   az vm create \
       --resource-group <resourceGroupName> \
       --name <vmName> \
       --image <Ubuntu 20.04 Image URN> \
       --admin-username azadmin \
       --ssh-key-value "<sshPubKey>" \
       --custom-data ./cloudinitConfig.json
   ```

1. 組織のポリシー管理者が、拡張機能を VM にデプロイしないよう決定した場合は、プロビジョニング時に拡張機能のサポートを無効にすることができます。

   - REST API

     このプロパティを使用して VM をデプロイできる場合に拡張機能を無効および有効にするには、次のようにします。

     ```python
       "osProfile": {
         "allowExtensionOperations": false
       },
     ```

## <a name="next-steps"></a>次のステップ

詳細については、[「Other base system changes since 18.04 LTS」(18.04 LTS 以降のその他の基本的なシステム変更) の「Python 3 by default」(既定での Python 3) ](https://wiki.ubuntu.com/FocalFossa/ReleaseNotes#Python3_by_default)を参照してください。
