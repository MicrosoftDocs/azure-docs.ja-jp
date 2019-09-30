---
title: Azure Blockchain Workbench プレビューでのアプリケーションの使用
description: Azure Blockchain Workbench プレビューでのアプリケーション コントラクトの使用方法に関するチュートリアル。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: e1ba09c4fbba6492e2d5f870a6c31a3d5a6d8956
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845146"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>チュートリアル:Azure Blockchain Workbench でのアプリケーションの使用

Blockchain Workbench では、コントラクトを作成し、それらのコントラクトに対してアクションを実行できます。 また、状態やトランザクション履歴などのコントラクト詳細を表示することもできます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * 新しいコントラクトを作成する
> * コントラクトに対してアクションを実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* Blockchain Workbench のデプロイ。 デプロイについて詳しくは、[Azure Blockchain Workbench のデプロイ](deploy.md)に関するページをご覧ください
* Blockchain Workbench 内にデプロイされたブロックチェーン アプリケーション。 「[Azure Blockchain Workbench でブロックチェーン アプリケーションを作成する](create-app.md)」をご覧ください

ブラウザーで [Blockchain Workbench を開きます](deploy.md#blockchain-workbench-web-url)。

![Blockchain Workbench](./media/use/workbench.png)

Blockchain Workbench のメンバーとしてサインインする必要があります。 アプリケーションが表示されない場合は、Blockchain Workbench のメンバーではあるものの、まだどのアプリケーションのメンバーにもなっていないことになります。 アプリケーションにメンバーを割り当てることができるのは、Blockchain Workbench の管理者です。

## <a name="create-new-contract"></a>新しいコントラクトの作成

新しいコントラクトを作成するには、コントラクト **イニシエーター**として指定されているメンバーになる必要があります。 コントラクトのアプリケーション ロールとイニシエーターの定義については、[構成概要ページのワークフローに関するセクション](configuration.md#workflows)を参照してください。 アプリケーション ロールにメンバーを割り当てる方法については、[アプリケーションにメンバーを追加する](manage-users.md#add-member-to-application)方法に関するページを参照してください。

1. Blockchain Workbench のアプリケーション セクションで、作成するコントラクトを含んだアプリケーション タイルを選択します。 アクティブなコントラクトの一覧が表示されます。

2. 新しい契約を作成するには、 **[新しいコントラクト]** を選択します。

    ![[新しいコントラクト] ボタン](./media/use/contract-list.png)

3. **[新しいコントラクト]** ウィンドウが表示されます。 初期パラメーター値を指定します。 **作成** を選択します。

    ![[新しいコントラクト] ウィンドウ](./media/use/new-contract.png)

    新しく作成されたコントラクトが、その他のアクティブなコントラクトと共に一覧に表示されます。

    ![アクティブなコントラクトの一覧](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>コントラクトに対してアクションを実行する

コントラクトの状態によっては、コントラクトの次の状態に遷移するアクションをメンバーは行うことができます。 アクションは[状態](configuration.md#states)内で[遷移](configuration.md#transitions)として定義されています。 遷移に許可されるアプリケーションまたはインスタンス ロールに属するメンバーはアクションを行うことができます。 

1. Blockchain Workbench のアプリケーション セクションで、アクションを実行するコントラクトを含んだアプリケーション タイルを選択します。
2. 一覧からコントラクトを選択します。 コントラクトの詳細が各セクションに表示されます。 

    ![コントラクト詳細](./media/use/contract-details.png)

    | Section  | 説明  |
    |---------|---------|
    | Status | コントラクト ステージ内での現在の進捗状況が一覧表示されます |
    | 詳細 | コントラクトの現在の値です |
    | Action | 直近のアクションに関する詳細です |
    | アクティビティ | コントラクトのトランザクション履歴です |
    
3. **[アクション]** セクションで、 **[アクションの実行]** を選択します。

4. コントラクトの現在の状態に関する詳細がウィンに表示されます。 ドロップダウン リストから、実行するアクションを選択します。 

    ![アクションの選択](./media/use/choose-action.png)

5. **[アクションの実行]** を選択し、アクションを開始します。
6. アクションにパラメーターが必要な場合、アクションの値を指定します。

    ![アクションの実行](./media/use/take-action.png)

7. **[アクションの実行]** を選択し、アクションを実行します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench アプリケーションのバージョン管理](version-app.md)
