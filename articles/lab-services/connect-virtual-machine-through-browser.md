---
title: ブラウザーを使用して仮想マシンに接続する - Azure | Microsoft Docs
description: ブラウザーを使用して仮想マシンに接続する方法について説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 22d7afa889994068c3f906d0b9adbc1a74bc4839
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642015"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>ブラウザーを使用して仮想マシンに接続する 

DevTest Labs は [Azure Bastion](https://docs.microsoft.com/azure/bastion/) と統合されたことにより、ブラウザーを使用して仮想マシンに接続できます。 DevTest Labs でこの機能を有効にする方法の詳細については、「[ラボ仮想マシンでブラウザー接続を有効にする](enable-browser-connection-lab-virtual-machines.md)」を参照してください。

"*ブラウザー接続*" が有効になると、ラボ ユーザーはブラウザーを使用して仮想マシンにアクセスできます。  

> [!NOTE]
> ラボ仮想マシンでのブラウザー接続の有効化はプレビュー段階です。

## <a name="create-a-lab-virtual-machine"></a>ラボ仮想マシンの作成

まず、Bastion が構成されている VNet 内にラボ仮想マシンを作成する必要があります。 仮想マシンの作成中に **[詳細設定]** タブに移動すると、VNet を選択できます。

![仮想マシンの作成](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>ブラウザーでの仮想マシンの起動

仮想マシンを作成したら、 *[ブラウザー接続]* ボタンをクリックして、マシンのユーザー名とパスワードを入力すると、ブラウザーで起動できます。  

![ブラウザーでの起動](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>次の手順

[Azure DevTest Labs でラボに VM を追加する](devtest-lab-add-vm.md)