---
title: カスタム コンテナーへの CI/CD
description: Azure App Service でのカスタムの Windows または Linux コンテナーへの継続的配置を設定します。
keywords: Azure App Service, Linux, Docker, acr, oss
author: msangapu-msft
ms.assetid: a47fb43a-bbbd-4751-bdc1-cd382eae49f8
ms.topic: article
ms.date: 03/12/2021
ms.author: msangapu
ms.custom: seodec18
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: bc36325b55f049eebef823d836768fccc39a7615
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103472166"
---
# <a name="continuous-deployment-with-custom-containers-in-azure-app-service"></a>Azure App Service でのカスタム コンテナーを使用した継続的配置

このチュートリアルでは、管理された [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) リポジトリまたは [Docker Hub](https://hub.docker.com) からのカスタム コンテナー イメージの継続的なデプロイを構成します。

## <a name="1-go-to-deployment-center"></a>1.デプロイ センターに移動する

[Azure portal](https://portal.azure.com) で、App Service アプリの管理ページに移動します。

左側のメニューで、 **[デプロイ センター]**  >  **[設定]** の順にクリックします。 

::: zone pivot="container-linux"
## <a name="2-choose-deployment-source"></a>2.デプロイ ソースの選択

デプロイ　ソースの **選択** は、シナリオによって異なります。
- **[コンテナー レジストリ]** では、コンテナー レジストリと App Service の間に CI/CD が設定されます。
- **[GitHub Actions]** オプションは、GitHub でコンテナー イメージのソース コードを維持する場合に使用します。 デプロイ アクションは、GitHub リポジトリへの新しいコミットによってトリガーされ、`docker build` および `docker push` をコンテナー レジストリに直接実行した後、App Service アプリを更新して新しいイメージを実行できます。 詳細については、「[CI/CD と GitHub Actions の連携のしくみ](#how-cicd-works-with-github-actions)」を参照してください。
- **Azure Pipelines** で CI/CD を設定するには、[Azure Pipelines からの Azure Web アプリ コンテナーのデプロイ](/devops/pipelines/targets/webapp-on-container-linux)に関する記事を参照してください。

> [!NOTE]
> Docker Compose アプリの場合は、 **[Container Registry]** を選択します。

GitHub Actions を選択した場合は、 **[承認]** **をクリック** して承認プロンプトに従います。 既に GitHub での承認が済んでいる場合は、 **[アカウントの変更]** をクリックして別のユーザーのリポジトリからデプロイできます。

GitHub で Azure アカウントを承認したら、デプロイ元となる **[組織]** 、 **[リポジトリ]** 、 **[ブランチ]** を **選択** します。
::: zone-end  

::: zone pivot="container-windows"
## <a name="2-configure-registry-settings"></a>2.レジストリ設定を構成する
::: zone-end  
::: zone pivot="container-linux"
## <a name="3-configure-registry-settings"></a>3.レジストリ設定を構成する

複数コンテナー (Docker Compose) アプリをデプロイするには、 **[コンテナーの種類]** で **[Docker Compose]** を **選択** します。

**[コンテナーの種類]** ドロップダウンが表示されない場合は、 **[ソース]** までスクロールして **[Container Registry]** を **選択** します。
::: zone-end

**[Registry source]\(レジストリ ソース\)** で、コンテナー レジストリがある場所を **選択** します。 Azure Container Registry でも Docker Hub でもない場合は、 **[プライベート レジストリ]** を **選択** します。

::: zone pivot="container-linux"
> [!NOTE]
> 複数コンテナー (Docker Compose) アプリで複数のプライベート イメージを使用する場合は、プライベート イメージが同じプライベート レジストリにあり、同じユーザー資格情報でアクセスできることを確認してください。 複数コンテナー アプリでパブリック イメージのみを使用する場合は、一部のイメージが Docker Hub にない場合でも、**Docker Hub** を **選択** します。
::: zone-end  

選択内容に合致するタブを選択して、次の手順に従います。

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

**[レジストリ]** ドロップダウンには、アプリと同じサブスクリプション内のレジストリが表示されます。 必要なレジストリを **選択** します。

> [!NOTE]
> 別のサブスクリプションのレジストリからデプロイするには、代わりに **[Registry source]\(レジストリ ソース\)** で **[プライベート レジストリ]** を **選択** します。

::: zone pivot="container-windows"
デプロイする **イメージ** と **タグ** を **選択** します。 必要に応じて、 **[スタートアップ ファイル]** にスタートアップ コマンドを **入力** します。 
::: zone-end
::: zone pivot="container-linux"
**[コンテナーの種類]** に応じて、次の手順に従います。
- **[Docker Compose]** の場合、プライベート イメージのレジストリを **選択** します。 **[ファイルの選択]** を **クリック** して [Docker Compose ファイル](https://docs.docker.com/compose/compose-file/)をアップロードするか、Docker Compose ファイルの内容を **[構成]** に **貼り付け** ます。
- **[単一コンテナー]** の場合、デプロイする **イメージ** と **タグ** を **選択** します。 必要に応じて、 **[スタートアップ ファイル]** にスタートアップ コマンドを **入力** します。 
::: zone-end

App Service は、コンテナーを起動するときに、 **[スタートアップ ファイル]** の文字列を [`docker run` コマンドの末尾に (`[COMMAND] [ARG...]` セグメントとして)](https://docs.docker.com/engine/reference/run/) 追加します。

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

::: zone pivot="container-windows"
**[リポジトリ アクセス]** で、デプロイするイメージがパブリックかプライベートかを **選択** します。
::: zone-end
::: zone pivot="container-linux"
**[リポジトリ アクセス]** で、デプロイするイメージがパブリックかプライベートかを **選択** します。 1 つ以上のプライベート イメージを含む Docker Compose アプリの場合は、 **[プライベート]** を **選択** します。
::: zone-end

プライベート イメージを選択した場合は、Docker アカウントの **[ログイン]** (ユーザー名) と **[パスワード]** を **指定** します。

::: zone pivot="container-windows"
イメージとタグの名前を **[Full Image Name and Tag]\(完全なイメージ名とタグ\)** に **指定** し、`:` で区切ります (例: `nginx:latest` )。 必要に応じて、 **[スタートアップ ファイル]** にスタートアップ コマンドを **入力** します。 
::: zone-end
::: zone pivot="container-linux"
**[コンテナーの種類]** に応じて、次の手順に従います。
- **[Docker Compose]** の場合、プライベート イメージのレジストリを **選択** します。 **[ファイルの選択]** を **クリック** して [Docker Compose ファイル](https://docs.docker.com/compose/compose-file/)をアップロードするか、Docker Compose ファイルの内容を **[構成]** に **貼り付け** ます。
- **[単一コンテナー]** の場合、イメージとタグの名前を **[Full Image Name and Tag]\(完全なイメージ名とタグ\)** に **指定** し、`:` で区切ります (例: `nginx:latest`)。 必要に応じて、 **[スタートアップ ファイル]** にスタートアップ コマンドを **入力** します。 
::: zone-end

App Service は、コンテナーを起動するときに、 **[スタートアップ ファイル]** の文字列を [`docker run` コマンドの末尾に (`[COMMAND] [ARG...]` セグメントとして)](https://docs.docker.com/engine/reference/run/) 追加します。

# <a name="private-registry"></a>[プライベート レジストリ](#tab/private)

**[サーバー URL]** に、**https://** で始まるサーバーの URL を **入力** します。

**[ログイン]** と **[パスワード]** に、プライベート レジストリのログイン資格情報を **入力** します。

::: zone pivot="container-windows"
イメージとタグの名前を **[Full Image Name and Tag]\(完全なイメージ名とタグ\)** に **指定** し、`:` で区切ります (例: `nginx:latest` )。 必要に応じて、 **[スタートアップ ファイル]** にスタートアップ コマンドを **入力** します。 
::: zone-end
::: zone pivot="container-linux"
**[コンテナーの種類]** に応じて、次の手順に従います。
- **[Docker Compose]** の場合、プライベート イメージのレジストリを **選択** します。 **[ファイルの選択]** を **クリック** して [Docker Compose ファイル](https://docs.docker.com/compose/compose-file/)をアップロードするか、Docker Compose ファイルの内容を **[構成]** に **貼り付け** ます。
- **[単一コンテナー]** の場合、イメージとタグの名前を **[Full Image Name and Tag]\(完全なイメージ名とタグ\)** に **指定** し、`:` で区切ります (例: `nginx:latest`)。 必要に応じて、 **[スタートアップ ファイル]** にスタートアップ コマンドを **入力** します。 
::: zone-end

App Service は、コンテナーを起動するときに、 **[スタートアップ ファイル]** の文字列を [`docker run` コマンドの末尾に (`[COMMAND] [ARG...]` セグメントとして)](https://docs.docker.com/engine/reference/run/) 追加します。

-----

::: zone pivot="container-windows"
## <a name="3-enable-cicd"></a>3.CI/CD を有効にする
::: zone-end
::: zone pivot="container-linux"
## <a name="4-enable-cicd"></a>4.CI/CD を有効にする
::: zone-end

App Service では、Azure Container Registry および Docker Hub との CI/CD 統合がサポートされています。 これを有効にするには、 **[継続的デプロイ]** で **[オン]** を **選択** します。

::: zone pivot="container-linux"
> [!NOTE]
> **[ソース]** で **[GitHub Actions]** を選択した場合、CI/CD は GitHub Actions によって直接処理されるため、このオプションは表示されません。 代わりに **[ワークフロー構成]** セクションが表示され、ここで **[ファイルのプレビュー]** を **クリック** してワークフロー ファイルを検査できます。 Azure によって、選択した GitHub ソース リポジトリにこのファイルがコミットされて、ビルドとデプロイのタスクが処理されます。 詳細については、「[CI/CD と GitHub Actions の連携のしくみ](#how-cicd-works-with-github-actions)」を参照してください。
::: zone-end

このオプションを有効にすると、App Service では Azure Container Registry または Docker Hub のリポジトリに webhook が追加されます。 選択したイメージが `docker push` で更新されるたびに、リポジトリがこの webhook に送信されます。 webhook によって、App Service アプリが再起動され、`docker pull` を実行して更新されたイメージを取得します。

**他のプライベート レジストリの場合**、webhook に手動で、または CI/CD パイプラインの手順として送信できます。 **[Webhook URL]** で、 **[コピー]** ボタンを **クリック** して、webhook の URL を取得します。

::: zone pivot="container-linux"
> [!NOTE]
> 複数コンテナー (Docker Compose) アプリのサポートには制限があります。 
> - Azure Container Registry の場合、App Service では、レジストリをスコープとして、選択したレジストリに webhook を作成します。 レジストリ内の任意のリポジトリ (Docker Compose ファイルで参照されていないものを含む) への `docker push` により、アプリの再起動がトリガーされます。 範囲が狭くなるよう [webhook を変更する](../container-registry/container-registry-webhook.md)ことができます。
> - Docker Hub では、レジストリ レベルで webhook がサポートされません。 Docker Compose ファイルで指定されたイメージに、webhook を手動で **追加** する必要があります。
::: zone-end

::: zone pivot="container-windows"
## <a name="4-save-your-settings"></a>4.設定を保存する
::: zone-end
::: zone pivot="container-linux"
## <a name="5-save-your-settings"></a>5.設定を保存する
::: zone-end

**[保存]** を **クリック** します。

::: zone pivot="container-linux"

## <a name="how-cicd-works-with-github-actions"></a>CI/CD と GitHub Actions の連携のしくみ

**[ソース]** で **[GitHub Actions]** を選択した場合 (「[デプロイ ソースの選択](#2-choose-deployment-source)」を参照)、App Service では次の方法で CI/CD を設定します。

- GitHub Actions ワークフロー ファイルを GitHub リポジトリに保管して、App Service のビルドとデプロイのタスクを処理します。
- プライベート レジストリの資格情報を GitHub シークレットとして追加します。 生成されたワークフロー ファイルは、[Azure/docker-login](https://github.com/Azure/docker-login) アクションを実行してプライベート レジストリでサインインし、`docker push` を実行してそれにデプロイします。
- アプリの発行プロファイルを GitHub シークレットとして追加します。 生成されたワークフロー ファイルでは、App Service で認証するためにこのシークレットが使用され、その後、更新されたイメージを構成するために [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) アクションが実行され、これによって、更新されたイメージをプルするためにアプリの再起動がトリガーされます。
- [ワークフロー実行ログ](https://docs.github.com/actions/managing-workflow-runs/using-workflow-run-logs)から情報をキャプチャし、それをアプリの **デプロイ センター** の **[ログ]** タブに表示します。

GitHub Actions ビルド プロバイダーは、次の方法でカスタマイズできます。

- GitHub リポジトリでワークフロー ファイルが生成されたら、それをカスタマイズします。 詳しくは、「[GitHub Actions のワークフロー構文](https://docs.github.com/actions/reference/workflow-syntax-for-github-actions)」を参照してください。 アプリの再起動をトリガーするために、ワークフローが [Azure/webapps-deploy](https://github.com/Azure/webapps-deploy) アクションで終了していることを確認してください。
- 選択したブランチが保護されている場合でも、構成を保存せずに引き続きワークフロー ファイルをプレビューしてから、これと必要な GitHub シークレットを手動でリポジトリに追加することができます。 この方法では、Azure portal とのログ統合は行われません。
- 発行プロファイルではなく、Azure Active Directory で[サービス プリンシパル](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)を使用してデプロイします。

#### <a name="authenticate-with-a-service-principal"></a>サービス プリンシパルでの認証

このオプション構成では、既定の認証が、生成されたワークフロー ファイル内の発行プロファイルに置き換えられます。

[Azure CLI](/cli/azure/) で [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) コマンドを使用して、サービス プリンシパルを **作成** します。 次の例では、 *\<subscription-id>* 、 *\<group-name>* 、 *\<app-name>* を実際の値に置き換えます。 最上位の `{}` を含め、次の手順の JSON 出力全体を **保存** します。

```azurecli-interactive
az ad sp create-for-rbac --name "myAppDeployAuth" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

> [!IMPORTANT]
> セキュリティのために、サービス プリンシパルに最低限必要なアクセス権を付与してください。 前の例の範囲は、リソース グループ全体ではなく、特定の App Service アプリに限定されます。

[GitHub](https://github.com/) でご自分のリポジトリを **参照** し、 **[設定]、[シークレット]、[Add a new secret]\(新しいシークレットの追加\)** の順に **選択** します。 Azure CLI コマンドからの JSON 出力全体をシークレットの値フィールドに **貼り付け** ます。 シークレットに `AZURE_CREDENTIALS` などの名前を **指定** します。

**デプロイ センター** によって生成されたワークフロー ファイルで、次の例のようなコードを使用して `azure/webapps-deploy` 手順を **修正** します。

```yaml
- name: Sign in to Azure 
# Use the GitHub secret you added
- uses: azure/login@v1
    with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
- name: Deploy to Azure Web App
# Remove publish-profile
- uses: azure/webapps-deploy@v2
    with:
    app-name: '<app-name>'
    slot-name: 'production'
    images: '<registry-server>/${{ secrets.AzureAppService_ContainerUsername_... }}/<image>:${{ github.sha }}'
    - name: Sign out of Azure
    run: |
    az logout
```

::: zone-end

## <a name="automate-with-cli"></a>CLI で自動化する

コンテナー レジストリと Docker イメージを構成するには、[az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) を **実行** します。

# <a name="azure-container-registry"></a>[Azure Container Registry](#tab/acr)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url 'https://<registry-name>.azurecr.io' --docker-registry-server-user '<username>' --docker-registry-server-password '<password>'
```

# <a name="docker-hub"></a>[Docker Hub](#tab/dockerhub)

```azurecli-interactive
# Public image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name>

# Private image
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

# <a name="private-registry"></a>[プライベート レジストリ](#tab/private)

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name '<image>:<tag>' --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

-----

::: zone pivot="container-linux"
複数コンテナー (Docker Compose) アプリを構成するには、Docker Compose ファイルをローカルに **準備** し、`--multicontainer-config-file` パラメーターを指定して [az webapp config container set](/cli/azure/webapp/config/container#az-webapp-config-container-set) を **実行** します。 Docker Compose ファイルにプライベート イメージが含まれている場合は、前の例で示したように `--docker-registry-server-*` パラメーターを **追加** します。

```azurecli-interactive
az webapp config container set --resource-group <group-name> --name <app-name> --multicontainer-config-file <docker-compose-file>
```
::: zone-end

コンテナー レジストリからアプリに CI/CD を構成するには、`--enable-cd` パラメーターを指定して [az webapp deployment container config](/cli/azure/webapp/deployment/container#az-webapp-deployment-container-config) を **実行** します。 コマンドを実行すると Webhook URL が出力されますが、別の手順でレジストリに Webhook を手動で作成する必要があります。 次の例では、アプリで CI/CD を有効にし、出力の Webhook URL を使用して Azure Container Registry に Webhook を作成します。

```azurecli-interactive
ci_cd_url=$(az webapp deployment container config --name <app-name> --resource-group <group-name> --enable-cd true --query CI_CD_URL --output tsv)

az acr webhook create --name <webhook-name> --registry <registry-name> --resource-group <group-name> --actions push --uri $ci_cd_url --scope '<image>:<tag>'
```

## <a name="more-resources"></a>その他のリソース

* [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
* [App Service on Linux での .NET Core Web アプリの作成](quickstart-dotnetcore.md)
* [クイック スタート: App Service でカスタム コンテナーを実行する](quickstart-custom-container.md)
* [App Service on Linux の FAQ](faq-app-service-linux.md)
* [カスタム コンテナーを構成する](configure-custom-container.md)
* [Azure にデプロイするためのアクション ワークフロー](https://github.com/Azure/actions-workflow-samples)
