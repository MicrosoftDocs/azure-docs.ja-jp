---
title: クイックスタート - コンテナー インスタンスに Docker コンテナーをデプロイする - ポータル
description: このクイック スタートでは、Azure portal を使用して、分離された Azure コンテナー インスタンスで実行されているコンテナー化された Web アプリをすばやくデプロイします
ms.topic: quickstart
ms.date: 08/24/2020
ms.custom: seodec18, mvc, devx-track-js
ms.openlocfilehash: c8477bd91c3a02a2cd02d341c38c16da251902ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004808"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>クイック スタート:Azure portal を使用してコンテナー インスタンスを Azure 内にデプロイする

サーバーレスの Docker コンテナーを Azure 内で簡単にすばやく実行するには、Azure Container Instances を使用します。 Azure Kubernetes Service のように完全なコンテナー オーケストレーション プラットフォームが不要な場合は、コンテナー インスタンス オンデマンドにアプリケーションをデプロイします。

このクイック スタートでは、Azure portal を使用して、分離された Docker コンテナーをデプロイし、そのアプリケーションを完全修飾ドメイン名 (FQDN) を介して使用できるようにします。 いくつかの設定を構成し、コンテナーをデプロイした後は、実行中のアプリケーションを参照できます。

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示":::

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][azure-free-account] を作成してください。

## <a name="create-a-container-instance"></a>コンテナー インスタンスの作成

**[リソースの作成]**  >  **[コンテナー]**  >  **[Container Instances]** を選択します。

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-01.png" alt-text="Azure ポータルで新しいコンテナー インスタンスの作成を開始":::

**[基本]** ページで、 **[リソース グループ]** 、 **[コンテナー名]** 、および **[コンテナー イメージ]** の各ボックスに次の値を入力します。 その他の値は既定値のままにして、**OK** をクリックします。

* リソース グループ: **新規作成** > `myresourcegroup`
* コンテナー 名: `mycontainer`
* イメージのソース: **クイックスタートのイメージ**
* コンテナー イメージ: `mcr.microsoft.com/azuredocs/aci-helloworld` (Linux)

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-03.png" alt-text="Azure ポータルで新しいコンテナー インスタンスの基本設定を構成":::

このクイックスタートでは、既定の設定を使用して、パブリックの Microsoft `aci-helloworld` イメージをデプロイします。 このサンプル Linux イメージには、静的な HTML ページを返す、Node.js で作成された小さな Web アプリがパッケージ化されています。 また、Azure Container Registry、Docker Hub、または他のレジストリに格納されている独自のコンテナー イメージを使用することもできます。

**[Networking]\(ネットワーク\)** ページで、コンテナーの **DNS 名ラベル** を指定します。 この名前は、コンテナー インスタンスを作成する Azure リージョン内で一意である必要があります。 コンテナーには、`<dns-name-label>.<region>.azurecontainer.io` でパブリックに到達できます。 エラー メッセージ "DNS 名ラベルは利用できません" が表示された場合は、別の DNS 名ラベルを試してください。

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-04.png" alt-text="Azure portal で新しいコンテナー インスタンスのネットワーク設定を構成する":::

他の設定は既定値のままにして、 **[確認と作成]** を選択します。

検証が完了すると、コンテナーの設定の概要が表示されます。 **[作成]** を選択して、コンテナーのデプロイ要求を送信します。

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-05.png" alt-text="Azure ポータルでの新しいコンテナー インスタンスの設定概要":::

デプロイが開始されると、デプロイが進行中であることを示す通知が表示されます。 コンテナー グループがデプロイされると、別の通知が表示されます。

**[リソース グループ]**  >  **[myresourcegroup]**  >  **[mycontainer]** の順に移動して、コンテナー グループの概要を開きます。 コンテナー インスタンスの **FQDN** (完全修飾ドメイン名) を書き留め、インスタンスの **[状態]** を確認します。

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-06.png" alt-text="Azure ポータルでのコンテナー グループ概要":::

**[状態]** が *[実行中]* になったら、ブラウザーでコンテナーの FQDN に移動します。

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-07.png" alt-text="Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示":::

お疲れさまでした。 いくつかの設定を構成するだけで、パブリックにアクセスできるアプリケーションが Azure Container Instances にデプロイされました。

## <a name="view-container-logs"></a>コンテナー ログの表示

コンテナー インスタンスのログを表示すると、コンテナーやコンテナーで実行されるアプリケーションの問題をトラブルシューティングする際に役立ちます。

コンテナーのログを表示するには、 **[設定]** で **[コンテナー]** を選択し、 **[ログ]** を選択します。 ブラウザーでアプリケーションを表示したときに生成された HTTP GET 要求が表示されます。

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-11.png" alt-text="Azure portal のコンテナー ログ":::


## <a name="clean-up-resources"></a>リソースをクリーンアップする

コンテナーを使い終えたら、*mycontainer* コンテナー インスタンスの **[概要]** を選択し、 **[削除]** を選択します。

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-09.png" alt-text="Azure portal でのコンテナー インスタンスの削除":::

確認のダイアログが表示されたら、 **[はい]** を選択します。

:::image type="content" source="media/container-instances-quickstart-portal/qs-portal-10.png" alt-text="Azure portal でのコンテナー インスタンスの削除の確認":::

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、パブリック Microsoft イメージから Azure コンテナー インスタンスを作成しました。 コンテナー イメージをビルドし、プライベート Azure コンテナー レジストリからデプロイする場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/