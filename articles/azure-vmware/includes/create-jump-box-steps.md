---
title: Azure VMware Solution のジャンプ ボックスを作成する
description: Azure VMware Solution のジャンプ ボックスを作成する手順。
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: f746e11763e1df1686f3134960dea167bf1c9908
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462257"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-access-private-cloud.md -->

1. リソース グループで、 **[+ 追加]** を選択します。次に、**Microsoft Windows 10** を検索して選択し、 **[作成]** を選択します。

   :::image type="content" source="../media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="ジャンプ ボックス用に新しい Windows 10 VM を追加する。" border="true":::

1. フィールドに必要な情報を入力し、 **[確認および作成]** を選択します。 

   フィールドの詳細については、次の表を参照してください。

   | フィールド | 値 |
   | --- | --- |
   | **[サブスクリプション]** | 値には、リソース グループが属しているサブスクリプションが事前に設定されています。 |
   | **リソース グループ** | 値には、前のチュートリアルで作成した現在のリソース グループが事前に設定されています。  |
   | **仮想マシン名** | VM の一意の名前を入力します。 |
   | **リージョン** | VM の地理的な場所を選択します。 |
   | **可用性オプション** | 既定値を選択したままにします。 |
   | **Image** | VM イメージを選択します。 |
   | **[サイズ]** | 既定のサイズ値をそのまま使用します。 |
   | **認証の種類**  | **[パスワード]** を選択します。 |
   | **ユーザー名** | VM にログオンするためのユーザー名を入力します。 |
   | **パスワード** | VM にログオンするためのパスワードを入力します。 |
   | **[パスワードの確認入力]** | VM にログオンするためのパスワードを入力します。 |
   | **パブリック インバウンド ポート** | **[なし]** を選択します。 [なし] を選択した場合は、[JIT アクセス](../../security-center/security-center-just-in-time.md#jit-configure)を使用して、VM にアクセスする必要があるときにのみ VM へのアクセスを制御できます。 あるいは、ネットワーク ポートを公開せずにインターネットから安全にジャンプ ボックス サーバーにアクセスするようにする場合は、[Azure Bastion](../../bastion/tutorial-create-host-portal.md) を使用することもできます。  |


1. 検証に合格したら、 **[作成]** を選択して仮想マシンの作成プロセスを開始します。

