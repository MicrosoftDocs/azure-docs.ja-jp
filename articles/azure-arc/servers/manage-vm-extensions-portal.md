---
title: Azure portal から VM 拡張機能を有効にする
description: この記事では、Azure portal からハイブリッド クラウド環境で実行されている Azure Arc 対応サーバーに仮想マシン拡張機能をデプロイする方法について説明します。
ms.date: 01/22/2020
ms.topic: conceptual
ms.openlocfilehash: b0e114b314179d42ccd47b7d7bd534d3a824a411
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587655"
---
# <a name="enable-azure-vm-extensions-from-the-azure-portal"></a>Azure portal から Azure VM 拡張機能を有効にする

この記事では、Azure portal から Azure Arc 対応サーバーでサポートされている Azure VM 仮拡張機能の Linux または Windows ハイブリッド マシンへのデプロイとアンインストールの実行方法を示します。

> [!NOTE]
> Key Vault VM 拡張機能 (プレビュー) では、Azure portal からのデプロイはサポートされておらず、Azure CLI、Azure PowerShell、または Azure Resource Manager テンプレートを使用したデプロイのみがサポートされています。

## <a name="enable-extensions-from-the-portal"></a>ポータルから拡張機能を有効にする

Azure portal を使用して、Arc for server で管理されているマシンに VM 拡張機能を適用できます。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

2. ポータルで **[サーバー - Azure Arc]** に移動し、一覧からハイブリッド マシンを選択します。

3. **[拡張機能]** を選択し、 **[追加]** を選択します。 利用可能な拡張機能の一覧から目的の拡張機能を選択し、ウィザードの手順に従います。 この例では、Log Analytics VM 拡張機能をデプロイします。

    ![選択したマシンの VM 拡張機能を選択する](./media/manage-vm-extensions/add-vm-extensions.png)

    次の例では、Azure portal からの Log Analytics VM 拡張機能のインストールを示します。

    ![Log Analytics VM 拡張機能をインストールする](./media/manage-vm-extensions/mma-extension-config.png)

    インストールを完了するには、ワークスペース ID と主キーを指定する必要があります。 この情報の検索方法に慣れていない場合は、「[ワークスペース ID とキーを取得する](../../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)」を参照してください。

4. 表示される必要な情報を確認したら、 **[作成]** を選択します。 デプロイの概要が表示され、デプロイの状態を確認できます。

>[!NOTE]
>複数の拡張機能をまとめてバッチ処理することができますが、順番にインストールされます。 最初の拡張機能のインストールが完了すると、次の拡張機能のインストールが試行されます。

## <a name="list-extensions-installed"></a>インストールされている拡張機能を一覧表示する

Arc 対応サーバー上の VM 拡張機能の一覧を Azure portal から取得できます。 それらを表示するには、次の手順を実行します。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

2. ポータルで **[サーバー - Azure Arc]** に移動し、一覧からハイブリッド マシンを選択します。

3. **[拡張機能]** を選択すると、インストールされている拡張機能の一覧が返されます。

    ![選択されたマシンにデプロイされた VM 拡張機能を一覧表示する](./media/manage-vm-extensions/list-vm-extensions.png)

## <a name="uninstall-extension"></a>拡張機能をアンインストールする

Azure portal から、1 つ以上の拡張機能を Arc 対応サーバーから削除できます。 拡張機能を削除するには、次の手順のようにします。

1. お使いのブラウザーで [Azure portal](https://portal.azure.com) に移動します。

2. ポータルで **[サーバー - Azure Arc]** に移動し、一覧からハイブリッド マシンを選択します。

3. **[拡張機能]** を選択し、インストールされている拡張機能の一覧から拡張機能を選択します。

4. **[アンインストール]** を選択し、確認を求めるメッセージが表示されたら、 **[はい]** を選択して続行します。

## <a name="next-steps"></a>次の手順

- [Azure CLI](manage-vm-extensions-cli.md)、[PowerShell](manage-vm-extensions-powershell.md)、または [Azure Resource Manager テンプレート](manage-vm-extensions-template.md)を使用して、VM 拡張機能をデプロイ、管理、および削除できます。

- トラブルシューティングに関する情報は、[VM 拡張機能のトラブルシューティング ガイド](troubleshoot-vm-extensions.md)に記載されています。