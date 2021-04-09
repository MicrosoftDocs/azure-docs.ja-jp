---
title: Azure DevTest Labs 仮想マシンでアーティファクトの失敗を診断する
description: DevTest Labs からは、アーティファクトの失敗の診断に使用できる情報が提供されます。 この記事では、アーティファクトのエラーのトラブルシューティング方法について説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 440ce6a537ac8d6a21ae8010bfbb3c38a82bf01e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85480815"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>ラボでアーティファクトの失敗を診断する 
アーティファクトの作成後、作成が成功したか失敗したかを確認できます。 Azure DevTest Labs のアーティファクト ログには、アーティファクトの失敗の診断に使用できる情報が含まれています。 Windows VM のアーティファクト ログ情報を表示する場合、次のいずれかを選択できます。

* Azure Portal で次の操作を行います。
* VM

> [!NOTE]
> 失敗を正しく特定し、説明できるようにするには、アーティファクトが適切に構造化されていることが重要です。 アーティファクトを正しく構築する方法については、[カスタム アーティファクトの作成](devtest-lab-artifact-author.md)に関するページを参照してください。 適切に構造化されたアーティファクトの例については、[パラメーターの型のテスト](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) アーティファクトを参照してください。

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Azure Portal を使用してアーティファクトの失敗のトラブルシューティングを行う

1. Azure Portal で、リソースの一覧からラボを選択します。
2. 調査するアーティファクトが含まれている Windows VM を選択します。
3. 左側のパネルの **[全般]** で、**[アーティファクト]** を選択します。 その VM に関連付けられているアーティファクトの一覧が表示されます。 アーティファクトの名前と状態が示されます。

   ![アーティファクトの状態](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. 状態が **[失敗]** と表示されているアーティファクトを選択します。 そのアーティファクトが開きます。 アーティファクトの失敗に関する詳細が含まれている拡張メッセージが表示されます。

   ![アーティファクトのエラー メッセージ](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>仮想マシン内からアーティファクトの失敗のトラブルシューティングを行う

1. 診断するアーティファクトが含まれている VM にサインインします。
2. C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status に移動します。*1.9* は Azure カスタム スクリプト拡張機能のバージョン番号です。

   ![状態ファイル](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. **状態** ファイルを開きます。

**Linux** VM でログ ファイルを見つける方法については、[Linux 仮想マシンでの Azure カスタム スクリプト拡張機能バージョン 2 の使用](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)に関する記事を参照してください。


## <a name="related-blog-posts"></a>関連するブログ記事
* [DevTest Labs で Resource Manager テンプレートを使用して既存の Active Directory ドメインに VM を参加させる](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>次のステップ
* [ラボへの Git リポジトリの追加](devtest-lab-add-artifact-repo.md)方法を学習します。

