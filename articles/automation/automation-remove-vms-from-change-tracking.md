---
title: Azure Automation の変更履歴とインベントリから VM を削除する
description: この記事では、変更履歴とインベントリから VM を削除する方法について説明します。
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 8e9b80c60c098e4daf247db07fa48baa322aa7f0
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2020
ms.locfileid: "84169456"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>変更履歴とインベントリから VM を削除する

環境内の VM への変更のデプロイが完了したら、これらの変更を[変更履歴とインベントリ](change-tracking.md)機能から削除できます。

## <a name="to-remove-your-vms"></a>VM を削除するには

1. Automation アカウントから、 **[構成管理]** の **[Change tracking]\(変更履歴\)** または **[インベントリ]** を選択します。

2. 次のコマンドを使用して、管理対象から削除する VM の UUID を識別します。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Log Analytics ワークスペースの **[全般]** で、保存したスコープの構成 `MicrosoftDefaultScopeConfig-ChangeTracking` の検索にアクセスします。

4. 保存された検索 `MicrosoftDefaultComputerGroup` で、右側の省略記号をクリックし、 **[編集]** を選択します。 

5. VM の UUID を削除します。

6. 他の VM に対して手順を繰り返して削除します。

7. 編集が完了したら、保存された検索を保存します。 

## <a name="next-steps"></a>次のステップ

* 変更履歴とインベントリを操作し続けるには、[変更履歴とインベントリの管理](change-tracking-file-contents.md)に関するページをご覧ください。
* 一般的な機能の問題を解決するには、「[変更履歴とインベントリの問題のトラブルシューティング](troubleshoot/change-tracking.md)」をご覧ください。