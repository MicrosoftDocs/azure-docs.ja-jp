---
title: Azure Data Share で受信者を追加する
description: Azure Data Share で既存のデータ共有に受信者を追加する方法について説明します。
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680642"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>共有に受信者を追加する方法

受信者の追加は、新しい共有を作成するとき、または既存の共有に対して行えます。 Azure Data Share の UI から、ユーザーの Azure ログイン メールを使用して受信者を追加できます。  API から、ユーザーまたはサービス プリンシパルとテナント ID の組み合わせを使用できます。 テナント ID を指定した場合、招待を承諾できるのはこのテナントのみとなります。 API からは、受信者にメールを送信せずに招待を作成することもできます。 

## <a name="add-recipient-to-an-existing-share"></a>既存の共有に受信者を追加する

Azure Data Share で、送信した共有に移動し、 **[招待]** タブを選択します。ここには、このデータ共有への招待のすべての受信者が一覧表示されます。 新しい受信者を追加するには、 **[受信者の追加]** をクリックします。

![スクリーンショットは、[受信者の追加] が選択されて示されています。](./media/how-to/how-to-add-recipients/add-recipient.png)

ページの右側にパネルが表示されます。 **[受信者の追加]** をクリックし、空白行に新しい受信者のメールアドレスを入力します。 受信者の Azure ログイン用メール アドレスを使用してください (メール エイリアスは無効です)。 

![スクリーンショットは、招待を追加して送信できる [受信者の追加] ウィンドウを示しています。](./media/how-to/how-to-add-recipients/add-recipient-side.png)

**[Add and send invitation]\(招待を追加して送信\)** をクリックします。 この共有への招待メールが新しい受信者に送信されます。

## <a name="next-steps"></a>次のステップ
詳細については、[共有への招待を削除する](how-to-delete-invitation.md)方法に関する記述を参照してください。
