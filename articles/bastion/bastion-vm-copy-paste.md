---
title: 仮想マシンとの間のコピーと貼り付け:Azure Bastion | Microsoft Docs
description: この記事では、Azure Bastion を使用して Azure VM との間でコピーと貼り付けを行う方法を説明します。
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 9d69d1a9fae258c9546a8c794fc0b0c68b952049
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191334"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion-preview"></a>仮想マシンとの間のコピーと貼り付け:Azure Bastion (プレビュー)

この記事は、Azure Bastion を使用している場合に、仮想マシンとの間でテキストのコピーと貼り付けを行う際に役立ちます。 VM を操作する前に、[Azure Bastion ホストを作成する](bastion-create-host-portal.md)手順を実行済みであることを確認してください。 その後、[RDP](bastion-connect-vm-rdp.md) または [SSH](bastion-connect-vm-ssh.md) のいずれかを使用して、操作する VM に接続します。

> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
>

高度な Clipboard API アクセスをサポートするブラウザーでは、ローカル デバイス上のアプリケーション間でテキストをコピーして貼り付けるのと同じ方法で、ローカル デバイスとリモート セッションとの間でテキストをコピーして貼り付けることができます。 他のブラウザーでは、Azure Bastion クリップボード アクセス ツール パレットを使用することができます。

  ![クリップボードの許可](./media/bastion-vm-manage/allow.png)

テキストのコピー/貼り付けのみがサポートされています。 直接コピーと貼り付けを行う場合は、Azure Bastion セッションの初期化中に、クリップボードへのアクセスを求めるメッセージがブラウザーに表示されることがあります。 Web ページにクリップボードへのアクセスを**許可**します。

## <a name="to"></a>リモート セッションへのコピー

Azure Bastion プレビュー用の [Azure portal](https://aka.ms/BastionHost) を使用して仮想マシンに接続した後:

1. ローカル デバイスからローカル クリップボードにテキスト/コンテンツをコピーします。
1. リモート セッションの間に、2 つの矢印を選択して Azure Bastion クリップボード アクセス ツール パレットを起動します。 矢印は、セッションの左中央にあります。

    ![ツール パレット](./media/bastion-vm-manage/left.png)

    ![クリップボード](./media/bastion-vm-manage/clipboard.png)

1. 通常、コピーしたテキストは、Azure Bastion コピー貼り付けパレットに自動的に表示されます。 テキストが表示されていない場合は、パレットのテキスト領域にテキストを貼り付けます。
1. テキスト領域にテキストが表示されたら、それをリモート セッションに貼り付けることができます。

    ![貼り付け](./media/bastion-vm-manage/local.png)

## <a name="from"></a>リモート セッションからのコピー

Azure Bastion プレビュー用の [Azure portal](https://aka.ms/BastionHost) を使用して仮想マシンに接続した後:

1. (Ctrl + C を使用して) リモート セッションからリモート クリップボードにテキスト/コンテンツをコピーします。

    ![ツール パレット](./media/bastion-vm-manage/remote.png)

1. リモート セッションの間に、2 つの矢印を選択して Azure Bastion クリップボード アクセス ツール パレットを起動します。 矢印は、セッションの左中央にあります。

    ![クリップボード](./media/bastion-vm-manage/clipboard2.png)

1. 通常、コピーしたテキストは、Azure Bastion コピー貼り付けパレットに自動的に表示されます。 テキストが表示されていない場合は、パレットのテキスト領域にテキストを貼り付けます。
1. テキスト領域にテキストが表示されたら、それをローカル デバイスに貼り付けることができます。

    ![貼り付け](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>次の手順

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。