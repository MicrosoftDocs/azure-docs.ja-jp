---
title: "Azure DevTest Labs VM でアーティファクトの失敗を診断する | Microsoft Docs"
description: "DevTest Labs でアーティファクトの失敗をトラブルシューティングする方法を説明します"
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: 
ms.assetid: 115e0086-3293-4adf-8738-9f639f31f918
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/18/2017
ms.author: tarcher
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: e4f2946d0ba0756f36622aded0e8594acabb9527
ms.contentlocale: ja-jp
ms.lasthandoff: 08/24/2017

---
# <a name="diagnose-artifact-failures-in-the-lab"></a>ラボでアーティファクトの失敗を診断する 
アーティファクトの作成後、それが成功したか失敗したかを確認できます。 DevTest Labs のアーティファクト ログには、アーティファクトの失敗を診断するために使用できる情報が含まれています。 Windows VM のアーティファクト ログ情報を表示するには、いくつかの異なる方法があります。

> [!NOTE]
> 失敗を正しく特定し、説明するには、アーティファクトが適切に構造化されていることが重要です。 アーティファクトを正しく構築する方法については、[カスタム アーティファクトの作成](devtest-lab-artifact-author.md)に関するページを参照してください。 また、適切に構造化されたアーティファクトの例を見るには、この[パラメーターの型のテスト](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) アーティファクトを参照してください。

## <a name="troubleshoot-artifact-failures-using-the-azure-portal"></a>Azure Portal を使用してアーティファクトの失敗のトラブルシューティングを行う
Azure Portal を使用してアーティファクト作成中の失敗を診断するには、次の手順に従います。

1. リソースの一覧でラボを選択します。

2. 調査するアーティファクトが含まれている Windows VM を選択します。

3. 左側のパネルの **[全般]** で、**[アーティファクト]** を選択します。 その VM に関連付けられているアーティファクトの一覧が表示され、アーティファクトの名前と状態が示されます。

   ![Artifact git repo example](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure.png)

4. 状態が **[失敗]** と表示されているアーティファクトを選択します。 アーティファクトが開かれ、アーティファクトの失敗に関する詳細が含まれている拡張メッセージが表示されます。

   ![Artifact git repo example](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-vm"></a>VM 内からアーティファクトの失敗をトラブルシューティングする
仮想マシン内からアーティファクト ログを表示するには、次の手順に従います。

1. 診断するアーティファクトが含まれている VM にログインします。

2. C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.9\Status に移動します。ここで、"1.9" は CSE のバージョン番号です。

   ![Artifact git repo example](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status.png)

3. **status** ファイルを開き、その VM のアーティファクトの失敗を診断するために役立つ情報を確認します。




[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>関連するブログ記事
* [Azure DevTest Labs で Resource Manager テンプレートを使用して既存の AD ドメインに VM を参加させる](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>次のステップ
* [ラボへの Git リポジトリの追加](devtest-lab-add-artifact-repo.md)方法を学習します。


