---
title: Azure Automation の変更履歴とインベントリから VM を削除する
description: この記事では、変更履歴とインベントリから VM を削除する方法について説明します。
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 0b79fa22d3203504e63161aba03b32830d74d016
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93131277"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>変更履歴とインベントリから VM を削除する

環境内の VM に対する変更の追跡が完了したら、[変更履歴とインベントリ](overview.md)機能を使用してそれらの管理を停止できます。 この管理を停止するには、保存された検索クエリ `MicrosoftDefaultComputerGroup` を、自分の Automation アカウントにリンクされた Log Analytics ワークスペースで編集します。

## <a name="sign-into-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="to-remove-your-vms"></a>VM を削除するには

1. Azure portal で、Azure portal の上部のナビゲーションから **[Cloud Shell]** を起動します。 Azure Cloud Shell について初めて学ぶ場合は、「[Azure Cloud Shell の概要](../../cloud-shell/overview.md)」を参照してください。

2. 次のコマンドを使用して、管理から削除するマシンの UUID を特定します。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Azure portal で、 **[Log Analytics ワークスペース]** に移動します。 一覧からお使いのワークスペースを選択します。

4. Log Analytics ワークスペースで、 **[ログ]** を選択し、上部の [アクション] メニューから **[クエリ エクスプローラー]** を選択します。

5. 右側のウィンドウの **[クエリ エクスプローラー]** で、 **[Saved Queries\Updates]\(保存されたクエリ\更新\)** を展開し、保存されたクエリ `MicrosoftDefaultComputerGroup` を選択して編集します。

6. クエリ エディターで、クエリを確認して VM の UUID を見つけます。 VM の UUID を削除して、削除する他の VM にもこの手順を繰り返します。

7. 編集が完了したら、上部のバーから **[保存]** を選択して、保存された検索を保存します。

>[!NOTE]
>マシンは登録解除された後も表示されます。これは、過去 24 時間以内に評価されたすべてのマシンについて報告するためです。 マシンを削除した後、マシンが一覧に表示されなくなるには、24 時間待つ必要があります。

## <a name="next-steps"></a>次のステップ

この機能を再度有効にするには、「[Automation アカウントから変更履歴とインベントリを有効にする](enable-from-automation-account.md)」、[Azure portal の参照による変更履歴とインベントリの有効化](enable-from-portal.md)に関する記事、「[Runbook から変更履歴とインベントリを有効にする](enable-from-runbook.md)」、または「[Azure VM から変更履歴とインベントリを有効にする](enable-from-vm.md)」を参照してください。