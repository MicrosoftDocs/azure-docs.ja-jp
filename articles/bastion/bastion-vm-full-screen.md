---
title: Azure Bastion で仮想マシン セッションのビューを全画面表示に変更する | Microsoft Docs
description: この記事では、ビューを全画面表示に変更する方法について説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 6a063d6c8891133126924bfb934770f7818db71a
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191254"
---
# <a name="change-to-full-screen-view-for-a-vm-session-azure-bastion-preview"></a>VM セッションを全画面表示に変更する:Azure Bastion (プレビュー)

この記事は、ブラウザーの仮想マシンのビューを全画面表示にしたり元に戻したりする際に役立ちます。 VM を操作する前に、[Azure Bastion ホストを作成する](bastion-create-host-portal.md)手順を実行済みであることを確認してください。 その後、[RDP](bastion-connect-vm-rdp.md) または [SSH](bastion-connect-vm-ssh.md) のいずれかを使用して、操作する VM に接続します。

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

## <a name="launch-the-clipboard-tool"></a>クリップボード ツールを起動する

リモート セッションの間に、画面の左中央にある 2 つの矢印を選択して Azure Bastion クリップボード アクセス ツール パレットを起動します。

![ツール](./media/bastion-vm-manage/left.png)

## <a name="select-full-screen"></a>全画面表示を選択する

**[全画面表示]** ボタンを選択して、セッションを全画面エクスペリエンスに切り替えます。 切り替えると、セッションが全画面表示に再初期化されます。

![全画面表示](./media/bastion-vm-manage/full-screen.png)
 
## <a name="next-steps"></a>次の手順

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
Azure VM との間で[コピーと貼り付け](bastion-vm-copy-paste.md)を行う方法を確認する。