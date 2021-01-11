---
title: Azure Automation の Update Management から VM を削除する
description: この記事では、Update Management で管理されているマシンを削除する方法について説明します。
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449650"
---
# <a name="remove-vms-from-update-management"></a>Update Management から VM を削除する

環境内の VM に対する更新プログラムの管理が完了したら、[Update Management](update-mgmt-overview.md) 機能を使用して VM の管理を停止できます。

## <a name="sign-into-the-azure-portal"></a>Azure portal にサインインする

[Azure portal](https://portal.azure.com) にサインインします。

## <a name="to-remove-your-vms"></a>VM を削除するには

1. Automation アカウントから、 **[更新の管理]** で **[Update Management]** を選択します。

2. 次のコマンドを使用して、管理から削除するマシンの UUID を特定します。

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Log Analytics ワークスペースの **[全般]** で、保存したスコープの構成 `MicrosoftDefaultScopeConfig-Updates` の検索にアクセスします。

4. 保存された検索 `MicrosoftDefaultComputerGroup` で、右側の省略記号をクリックし、 **[編集]** を選択します。

5. VM の UUID を削除します。

6. 削除する他の VM についても、この手順を繰り返します。

7. 編集が完了したら、保存された検索を保存します。

>[!NOTE]
>マシンは登録解除された後も表示されます。これは、過去 24 時間以内に評価されたすべてのマシンについて報告するためです。 マシンの接続を切断した後、マシンが一覧に表示されなくなるには 24 時間待機する必要があります。

## <a name="next-steps"></a>次のステップ

仮想マシンの管理を再度有効にするには、[Azure portal の参照による Update Management の有効化](update-mgmt-enable-portal.md)に関するページまたは「[Azure VM から Update Management を有効にする](update-mgmt-enable-vm.md)」を参照してください。