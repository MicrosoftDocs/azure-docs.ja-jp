---
title: Azure マネージド アプリケーションでの Fortanix Confidential Computing Manager
description: Azure portal でマネージド アプリケーションに Fortanix Confidential Computing Manager (CCM) をデプロイする方法について説明します。
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563423"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Azure マネージド アプリケーションでの Fortanix Confidential Computing Manager

この記事では、Azure portal で Fortanix Confidential Computing Manager によって管理されるアプリケーションをデプロイする方法について説明します。

Fortanix は、Azure インフラストラクチャ上に構築された製品とサービスを提供するサードパーティ ソフトウェア ベンダーです。 Azure 上の類似したコンフィデンシャル コンピューティング サービスを提供するサードパーティ プロバイダーは他にもあります。

> [!NOTE]
>このドキュメントで言及されている製品は、Microsoft の管理下にありません。 Microsoft はこの情報を便宜上提供しているだけであり、これらの Microsoft 以外の製品への言及は、Microsoft による保証を意味しません。

## <a name="prerequisites"></a>前提条件

- 変換されたアプリケーション イメージをプッシュするためのプライベート Docker レジストリ。
- Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/)してください。

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Azure マネージド アプリケーションを使用して Confidential Computing Manager をデプロイする

1. [Azure ポータル](https://portal.azure.com/)にアクセスします。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="Azure portal。":::

2. 検索バーで「Fortanix Confidential Computing Manager」を検索すると、Fortanix CCM の Marketplace リストが表示されます。 **[Fortanix Confidential Computing Manager on Azure]\(Azure 上の Fortanix Confidential Computing Manager\)** を選択します。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Marketplace のリスト。":::

3. CCM マネージド アプリケーションを作成するページが開きます。 **[作成]** を選択します。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="アプリケーションを作成します。":::

4. すべての必須フィールドに入力します。
   1. [マネージド アプリケーションの詳細] セクションの **[管理対象リソース グループ]** フィールドには、ユーザーが必要に応じて変更できる既定値が設定されています。
   2. **リージョン** フィールドで、**オーストラリア東部**、**オーストラリア南東部**、**米国東部**、**米国西部 2**、**西ヨーロッパ**、**北ヨーロッパ**、**カナダ中部**、**カナダ東部**、または **［米国東部 2 EUAP** のいずれかを選択します。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="必須フィールド":::

   **[確認と作成]** を選択して、Fortanix CCM マネージド アプリケーションを作成します。

5. 詳細を確認し、検証に合格したら、 **[上記のご契約条件に同意します]** チェック ボックスをオンにし、 **[作成]** を選択してマネージド アプリケーションを作成します。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="詳細を確認します。":::

6. Fortanix CCM デプロイが開始され、デプロイが進行中であることが通知されます。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="デプロイの進行状況。":::

7. デプロイが完了したら、 **[リソースに移動]** ボタンを選択して、デプロイされた、CCM マネージド アプリケーションの [概要] ページに移動し、計算ノードを登録します。

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Azure portal でのデプロイの成功を示すスクリーンショット。":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Azure portal でのコンフィデンシャル コンピューティング リソースの概要を示すスクリーンショット。":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Fortanix CCM に計算ノードを登録する

1. 左側のナビゲーション メニューで **[Confidential Computing Manager]** を選択します。 **図 9** に示すように、Fortanix CCM にログインし、アカウントを作成します。

    CCM でのサインアップ、ログイン、およびアカウントの作成を行う方法の詳細については、[CCM の作業の開始](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in)に関する記事を参照してください。
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Fortanix Confidential Computing Manager のログインを示すスクリーンショット。":::
    
2. CCM 管理コンソールから参加トークンを取得するには、まず **[ENROLL NODE]\(ノードの登録\)** ボタンを選択します。 次に、[ENROLL NODE]\(ノードの登録\) ウィンドウで、 **[COPY]\(コピー\)** ボタンを選択して参加トークンをコピーします。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="参加トークンの取得を示すスクリーンショット。":::

3. ここでノード エージェントを登録するために、 **[Confidential Computing Node Agent]** タブを選択し、 **[追加]** を選択して CCM ノード エージェントを追加します。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="ノード エージェントの追加を示すスクリーンショット。":::

4.  CCM ノード エージェントのフォームで、すべての必須フィールドを入力します。 手順 2 でコピーした参加トークンを **[Join Token]\(参加トークン\)** に貼り付けます。 **[確認および送信]** を選択して、確認します。

    CCM 計算ノードを登録する方法の詳細については、「[計算ノードを登録する](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes)」を参照してください。
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="計算ノードの登録を示すスクリーンショット。":::
    
5. 検証に合格したら、 **[送信]** を選択して、ノード エージェントの作成を完了します。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="ノード エージェントが作成されることを示すスクリーンショット。":::

6. デプロイの状態を確認するには、 **[概要]** タブに移動し、 **[管理されているリソース グループ]** のリンクを選択します。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="ノードが登録されていることを示すスクリーンショット。":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="デプロイの状態の確認を示すスクリーンショット。":::

7. これで、デプロイの状態はまだ進行中であり、ノード エージェントが正常に登録されるまで数分かかることがわかります。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="デプロイが進行中であることを示すスクリーンショット。":::

8. ノード エージェントの登録が成功すると、状態が [成功] に変わります。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="デプロイが成功したことを示すスクリーンショット。":::

9. CCM マネージド アプリケーションで、[計算ノード] ページに移動すると、ノードが **[アクティブ]** 状態であり、正常に登録されていることがわかります。

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="ノードが正常に登録されたことを示すスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

ユーザーは、[Confidential Computing Node Agent] ページから CCM ノード エージェントを削除することもできます。 ノード エージェントを削除するには、ノード エージェントを選択し、上部のバーの **[削除]** ボタンを選択します。

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="ノード エージェントの削除を示すスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure マネージド アプリを使用してノードを Fortanix の Confidential Computing Manager に登録しました。 ノード登録により、アプリケーション イメージを変換して、コンフィデンシャル コンピューティング仮想マシン上で実行できます。 Azure におけるコンフィデンシャル コンピューティング仮想マシンの詳細については、「[仮想マシン上のソリューション](virtual-machine-solutions.md)」を参照してください。

Azure のコンフィデンシャル コンピューティング オファリングの詳細については、[Azure でのコンフィデンシャル コンピューティング](overview.md)に関する記事を参照してください。

Azure 上で ([Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) や [Scone](https://sconedocs.github.io)などの) 他のサードパーティ オファリングを使用して同様のタスクを実行する方法について学習します。

