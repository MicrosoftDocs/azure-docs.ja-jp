---
title: "Azure Linux VM の ID を取得する | Microsoft Docs"
description: "Azure Linux VM の一意の ID を取得して使う方法について説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: kmouss
manager: timlt
editor: 
ms.assetid: 136c5d28-ff6b-4466-b27f-7a29785b5d27
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2017
ms.author: kmouss
translationtype: Human Translation
ms.sourcegitcommit: 167863b0de66d89f1cec80b1c6b2aec64f48b790
ms.openlocfilehash: 70d1d3ee35c32bb95306c7ce6089574d035de6a6


---
# <a name="accessing-and-using-azure-vm-unique-id"></a>Azure VM の一意の ID へのアクセスと使用
Azure VM の一意の ID は 128 ビットの識別子であり、エンコードされてすべての Azure IaaS VM の SMBIOS に格納されており、現在はプラットフォームの BIOS コマンドを使用して読み取ることができます。

Azure VM の一意の ID は、読み取り専用のプロパティです。 Azure の一意の VM IDは、再起動シャット ダウン (計画的または非計画的)、割り当て解除の開始/停止、サービス復旧また復元などで変化しません。 ただし、VM がスナップショットであり、新しいインスタンスを作成するためにコピーされる場合は、新しい Azure VM ID が構成されます。

> [!NOTE]
> この新しい機能が展開 (2014 年 9 月 18 日) されてから古い VM が動き続けている場合は、VM を再起動して Azure の一意 ID を自動的に取得してください。
> 
> 

VM 内から Azure の一意の VM ID にアクセスするには:

## <a name="create-a-vm"></a>VM を作成します
詳細については、「[仮想マシンを作成する](virtual-machines-linux-creation-choices.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="connect-to-the-vm"></a>VM に接続します
詳細については、「[Linux からの SSH 接続](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」を参照してください。

## <a name="query-vm-unique-id"></a>VM の一意の ID をクエリします
コマンド (例では **Ubuntu**を使用):

```bash
sudo dmidecode | grep UUID
```

例の予想される結果:

```bash
UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
```

ビッグ エンディアン ビット順序のため、この場合の実際の一意の VM ID は次のようになります。

```bash
DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
```

Azure VM 一意 ID は VM が Azure またはオンプレミスのどちらで実行していても使用でき、Azure IaaS デプロイでのライセンス、レポート、一般的な追跡などで必要なときに役に立ちます。 Azure でアプリケーションを作成してそれを認定する多くの独立系ソフトウェア ベンダーでは、そのライフサイクルを通じて Azure VM を識別し、VM がAzure、オンプレミス、または他のクラウド プロバイダーで動作しているかどうかを特定することが必要になる場合があります。 このプラットフォーム ID は、たとえば、ソフトウェアが適切にライセンスされているかどうかを検出したり、適切なプラットフォームに適切なメトリックを設定して他の用途の中からこれらのメトリックを追跡して関連付けるために VM のデータとそのソースを関連付けたりするために使用できます。




<!--HONumber=Jan17_HO4-->


