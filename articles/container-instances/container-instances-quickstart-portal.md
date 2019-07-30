---
title: クイック スタート - Azure Container Instances への Docker コンテナーのデプロイ - ポータル
description: このクイック スタートでは、Azure portal を使用して、分離された Azure コンテナー インスタンスで実行されているコンテナー化された Web アプリをすばやくデプロイします
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 04/17/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: bf3bea07fa2fcb67a467d4087ea9e2ccbfd95206
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325799"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-portal"></a>クイック スタート:Azure portal を使用してコンテナー インスタンスを Azure 内にデプロイする

サーバーレスの Docker コンテナーを Azure 内で簡単にすばやく実行するには、Azure Container Instances を使用します。 Azure Kubernetes Service のように完全なコンテナー オーケストレーション プラットフォームが不要な場合は、コンテナー インスタンス オンデマンドにアプリケーションをデプロイします。

このクイック スタートでは、Azure portal を使用して、分離された Docker コンテナーをデプロイし、そのアプリケーションを完全修飾ドメイン名 (FQDN) を介して使用できるようにします。 いくつかの設定を構成し、コンテナーをデプロイした後は、実行中のアプリケーションを参照できます。

![Azure Container Instances にデプロイされたアプリのブラウザーでの表示][aci-portal-07]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][azure-free-account] を作成してください。

## <a name="create-a-container-instance"></a>コンテナー インスタンスの作成

**[リソースの作成]**  >  **[コンテナー]**  >  **[Container Instances]** を選択します。

![Azure ポータルで新しいコンテナー インスタンスの作成を開始][aci-portal-01]

**[基本]** ページで、 **[リソース グループ]** 、 **[コンテナー名]** 、および **[コンテナー イメージ]** の各ボックスに次の値を入力します。 その他の値は既定値のままにして、**OK** をクリックします。

* リソース グループ: **新規作成** > `myresourcegroup`
* コンテナー 名: `mycontainer`
* [Container image] \(コンテナー イメージ): `mcr.microsoft.com/azuredocs/aci-helloworld`

![Azure ポータルで新しいコンテナー インスタンスの基本設定を構成][aci-portal-03]

このクイック スタートでは、 **[イメージの種類]** の設定に既定値の **[パブリック]** を使用して、パブリックの Microsoft `aci-helloworld` イメージをデプロイします。 この Linux イメージには、静的な HTML ページを返す、Node.js で作成された小さな Web アプリがパッケージ化されています。

**[Networking]\(ネットワーク\)** ページで、コンテナーの **DNS 名ラベル**を指定します。 この名前は、コンテナー インスタンスを作成する Azure リージョン内で一意である必要があります。 コンテナーには、`<dns-name-label>.<region>.azurecontainer.io` でパブリックに到達できます。 エラー メッセージ "DNS 名ラベルは利用できません" が表示された場合は、別の DNS 名ラベルを試してください。

![Azure ポータルでの新しいコンテナー インスタンスの構成][aci-portal-04]

他の設定は既定値のままにして、 **[確認と作成]** を選択します。

検証が完了すると、コンテナーの設定の概要が表示されます。 **[作成]** を選択して、コンテナーのデプロイ要求を送信します。

![Azure ポータルでの新しいコンテナー インスタンスの設定概要][aci-portal-05]

デプロイが開始されると、デプロイが進行中であることを示す通知が表示されます。 コンテナー グループがデプロイされると、別の通知が表示されます。

**[リソース グループ]**  >  **[myresourcegroup]**  >  **[mycontainer]** の順に移動して、コンテナー グループの概要を開きます。 コンテナー インスタンスの **FQDN** (完全修飾ドメイン名) を書き留め、インスタンスの **[状態]** を確認します。

![Azure ポータルでのコンテナー グループ概要][aci-portal-06]

**[状態]** が *[実行中]* になったら、ブラウザーでコンテナーの FQDN に移動します。

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][aci-portal-07]

お疲れさまでした。 いくつかの設定を構成するだけで、パブリックにアクセスできるアプリケーションが Azure Container Instances にデプロイされました。

## <a name="view-container-logs"></a>コンテナー ログの表示

コンテナー インスタンスのログを表示すると、コンテナーやコンテナーで実行されるアプリケーションの問題をトラブルシューティングする際に役立ちます。

コンテナーのログを表示するには、 **[設定]** で **[コンテナー]** を選択し、 **[ログ]** を選択します。 ブラウザーでアプリケーションを表示したときに生成された HTTP GET 要求が表示されます。

![Azure portal のコンテナー ログ][aci-portal-11]

## <a name="clean-up-resources"></a>リソースのクリーンアップ

コンテナーを使い終えたら、*mycontainer* コンテナー インスタンスの **[概要]** を選択し、 **[削除]** を選択します。

![Azure Portal でのコンテナー インスタンスの削除][aci-portal-09]

確認のダイアログが表示されたら、 **[はい]** を選択します。

![Azure Portal でのコンテナー インスタンスの削除の確認][aci-portal-10]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、パブリック Microsoft イメージから Azure コンテナー インスタンスを作成しました。 コンテナー イメージをビルドし、プライベート Azure コンテナー レジストリからデプロイする場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[aci-portal-01]: ./media/container-instances-quickstart-portal/qs-portal-01.png
[aci-portal-03]: ./media/container-instances-quickstart-portal/qs-portal-03.png
[aci-portal-04]: ./media/container-instances-quickstart-portal/qs-portal-04.png
[aci-portal-05]: ./media/container-instances-quickstart-portal/qs-portal-05.png
[aci-portal-06]: ./media/container-instances-quickstart-portal/qs-portal-06.png
[aci-portal-07]: ./media/container-instances-quickstart-portal/qs-portal-07.png
[aci-portal-08]: ./media/container-instances-quickstart-portal/qs-portal-08.png
[aci-portal-09]: ./media/container-instances-quickstart-portal/qs-portal-09.png
[aci-portal-10]: ./media/container-instances-quickstart-portal/qs-portal-10.png
[aci-portal-11]: ./media/container-instances-quickstart-portal/qs-portal-11.png

<!-- LINKS - External -->
[azure-free-account]: https://azure.microsoft.com/free/