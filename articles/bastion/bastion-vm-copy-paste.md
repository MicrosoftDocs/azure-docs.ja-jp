---
title: 仮想マシンとの間のコピーと貼り付け:Azure Bastion
description: この記事では、Azure Bastion を使用して Azure VM との間でコピーと貼り付けを行う方法を説明します。
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 05/04/2020
ms.author: charwen
ms.openlocfilehash: 4e818617b70e874153e026bcf40b6dfee8a8e04e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84744240"
---
# <a name="copy-and-paste-to-a-virtual-machine-azure-bastion"></a>仮想マシンとの間のコピーと貼り付け:Azure Bastion

この記事は、Azure Bastion を使用している場合に、仮想マシンとの間でテキストのコピーと貼り付けを行う際に役立ちます。 VM を操作する前に、[Azure Bastion ホストを作成する](bastion-create-host-portal.md)手順を実行済みであることを確認してください。 その後、[RDP](bastion-connect-vm-rdp.md) または [SSH](bastion-connect-vm-ssh.md) のいずれかを使用して、操作する VM に接続します。

高度な Clipboard API アクセスをサポートするブラウザーでは、ローカル デバイス上のアプリケーション間でテキストをコピーして貼り付けるのと同じ方法で、ローカル デバイスとリモート セッションとの間でテキストをコピーして貼り付けることができます。 他のブラウザーでは、Azure Bastion クリップボード アクセス ツール パレットを使用することができます。

>[!NOTE]
>現在、テキストのコピー/貼り付けのみがサポートされています。
>

   ![クリップボードの許可](./media/bastion-vm-manage/allow.png)

テキストのコピー/貼り付けのみがサポートされています。 直接コピーと貼り付けを行う場合は、Azure Bastion セッションの初期化中に、クリップボードへのアクセスを求めるメッセージがブラウザーに表示されることがあります。 Web ページにクリップボードへのアクセスを**許可**します。

## <a name="copy-to-a-remote-session"></a><a name="to"></a>リモート セッションへのコピー

[Azure Portal](https://portal.azure.com) を使用して仮想マシンを接続した後で、次の手順を実行します。

1. ローカル デバイスからローカル クリップボードにテキスト/コンテンツをコピーします。
1. リモート セッションの間に、2 つの矢印を選択して Azure Bastion クリップボード アクセス ツール パレットを起動します。 矢印は、セッションの左中央にあります。

   ![ツール パレット](./media/bastion-vm-manage/left.png)

   ![クリップボード](./media/bastion-vm-manage/clipboard.png)
1. 通常、コピーしたテキストは、Azure Bastion コピー貼り付けパレットに自動的に表示されます。 テキストが表示されていない場合は、パレットのテキスト領域にテキストを貼り付けます。
1. テキスト領域にテキストが表示されたら、それをリモート セッションに貼り付けることができます。

   ![貼り付け](./media/bastion-vm-manage/local.png)

## <a name="copy-from-a-remote-session"></a><a name="from"></a>リモート セッションからのコピー

[Azure Portal](https://portal.azure.com) を使用して仮想マシンを接続した後で、次の手順を実行します。

1. (Ctrl + C を使用して) リモート セッションからリモート クリップボードにテキスト/コンテンツをコピーします。

   ![ツール パレット](./media/bastion-vm-manage/remote.png)
1. リモート セッションの間に、2 つの矢印を選択して Azure Bastion クリップボード アクセス ツール パレットを起動します。 矢印は、セッションの左中央にあります。

   ![クリップボード](./media/bastion-vm-manage/clipboard2.png)
1. 通常、コピーしたテキストは、Azure Bastion コピー貼り付けパレットに自動的に表示されます。 テキストが表示されていない場合は、パレットのテキスト領域にテキストを貼り付けます。
1. テキスト領域にテキストが表示されたら、それをローカル デバイスに貼り付けることができます。

   ![貼り付け](./media/bastion-vm-manage/local2.png)
 
## <a name="next-steps"></a>次のステップ

[Azure Bastion に関する FAQ](bastion-faq.md) を読む。
