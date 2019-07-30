---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/28/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 9832ae6010f262322328ec22876a35c89e1bedca
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850668"
---
VM を単一リージョンに配置すると、インスタンス間の物理的な距離が短縮されます。 また、単一可用性ゾーンに配置した場合も、それらの物理的な位置は近くなります。 ただし、Azure の占有領域が拡大するにつれ、単一可用性ゾーンが複数の物理的なデータ センターにまたがる可能性があるため、ネットワーク待ち時間がアプリケーションに影響を与えることがあります。 

各 VM をできるだけ近くに配置して、可能性のある最も短い待ち時間を実現するには、それらを近接通信配置グループ内にデプロイするようにしてください。

近接通信配置グループは、Azure コンピューティング リソースが互いに物理的に近くに配置されるようにするために使用される論理的なグループ化です。 近接通信配置グループは、短い待ち時間が要件であるワークロードに役立ちます。


- スタンドアロン VM 間の短い待ち時間。
- 1 つの可用性セットまたは仮想マシン スケール セット内の VM 間の短い待ち時間。 
- スタンドアロン VM、複数の可用性セット内の VM、または複数のスケール セット内の VM の間の短い待ち時間。 1 つの配置グループ内に複数のコンピューティング リソースを配置して多層アプリケーションを構成できます。 
- 異なるハードウェアの種類を使用した複数のアプリケーション層間の短い待ち時間。 たとえば、1 つの近接通信配置グループ内での、可用性セット内の M シリーズを使用したバックエンドとスケール セット内の D シリーズ インスタンス上のフロント エンドの実行。

> [!IMPORTANT]
> 近接通信配置グループは現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
>
> 近接通信配置グループは、プレビュー期間中、**東日本**、**オーストラリア東部**、**インド中部**の各リージョンでは使用できません。


## <a name="best-practices"></a>ベスト プラクティス 
- 最も短い待ち時間を実現するには、近接通信配置グループを高速ネットワークと共に使用します。 詳細については、「[高速ネットワークを使った Linux 仮想マシンの作成](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)」または「[高速ネットワークを使った Windows 仮想マシンの作成](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。
- すべての VM サイズを 1 つの Resource Manager テンプレートでデプロイします。 必要な VM SKU とサイズの一部がサポートされていないハードウェアに配置されることを回避するには、すべてのアプリケーション層を 1 つのテンプレートに含めて、すべてが同時にデプロイされるようにします。
- PowerShell、CLI、または SDK を使用してデプロイのスクリプトを作成している場合は、割り当てエラー `OverconstrainedAllocationRequest` が表示されることがあります。 この場合は、既存のすべての VM を停止して割り当てを解除し、デプロイ スクリプト内のシーケンスを、失敗した VM SKU/サイズで始まるように変更する必要があります。 
- VM が削除された既存の配置グループを再利用する場合は、その削除が完全に完了するまで待ってから VM を追加してください。
- 待ち時間が最優先事項である場合は、VM を近接通信配置グループ内に、ソリューション全体を可用性ゾーン内に配置します。 ただし、回復性が最優先事項である場合は、インスタンスを複数の可用性ゾーンにわたって分散させます (1 つの近接通信配置グループがゾーンにまたがることはできません)。