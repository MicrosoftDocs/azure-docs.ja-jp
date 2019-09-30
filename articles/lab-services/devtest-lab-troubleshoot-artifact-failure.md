---
title: Azure DevTest Labs 仮想マシンでアーティファクトの失敗を診断する | Microsoft Docs
description: Azure DevTest Labs でアーティファクトの失敗のトラブルシューティングを行う方法を説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: 8b7a72c03d41d54aa80505e781b6f6d32cd2a2c0
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991361"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>ラボでアーティファクトの失敗を診断する 
アーティファクトの作成後、作成が成功したか失敗したかを確認できます。 Azure DevTest Labs のアーティファクト ログには、アーティファクトの失敗の診断に使用できる情報が含まれています。 Windows VM のアーティファクト ログ情報を表示する場合、次のいずれかを選択できます。

* Azure ポータルで次の操作を行います。
* VM

> [!NOTE]
> 失敗を正しく特定し、説明できるようにするには、アーティファクトが適切に構造化されていることが重要です。 アーティファクトを正しく構築する方法については、[カスタム アーティファクトの作成](devtest-lab-artifact-author.md)に関するページを参照してください。 適切に構造化されたアーティファクトの例については、[パラメーターの型のテスト](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) アーティファクトを参照してください。

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Azure Portal を使用してアーティファクトの失敗のトラブルシューティングを行う

1. Azure Portal で、リソースの一覧からラボを選択します。
2. 調査するアーティファクトが含まれている Windows VM を選択します。
3. 左側のパネルの **[全般]** で、 **[アーティファクト]** を選択します。 その VM に関連付けられているアーティファクトの一覧が表示されます。 アーティファクトの名前と状態が示されます。

   ![アーティファクトの状態](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. 状態が **[失敗]** と表示されているアーティファクトを選択します。 そのアーティファクトが開きます。 アーティファクトの失敗に関する詳細が含まれている拡張メッセージが表示されます。

   ![アーティファクトのエラー メッセージ](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>仮想マシン内からアーティファクトの失敗のトラブルシューティングを行う

1. 診断するアーティファクトが含まれている VM にサインインします。
2. C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status に移動します。*1.9* は Azure カスタム スクリプト拡張機能のバージョン番号です。

   ![状態ファイル](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. **状態**ファイルを開きます。

**Linux** VM でログ ファイルを見つける手順については、次の記事を参照してください。[Linux 仮想マシンで Azure カスタム スクリプト拡張機能バージョン 2 を使用する](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>関連するブログ記事
* [DevTest Labs で Resource Manager テンプレートを使用して既存の Active Directory ドメインに VM を参加させる](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>次の手順
* [ラボへの Git リポジトリの追加](devtest-lab-add-artifact-repo.md)方法を学習します。

