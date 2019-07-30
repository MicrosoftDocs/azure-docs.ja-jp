---
title: Azure で Pivotal Cloud Foundry クラスターを作成する
description: Azure で Pivotal Cloud Foundry (PCF) クラスターをプロビジョニングするために必要なパラメーターの設定方法について説明します
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: azure
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: f5ae599b516ac3ce6a9fcc40c0e26d242134e7d7
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226615"
---
# <a name="create-a-pivotal-cloud-foundry-cluster-on-azure"></a>Azure で Pivotal Cloud Foundry クラスターを作成する

このチュートリアルでは、Azure で Pivotal Cloud Foundry (PCF) クラスターをプロビジョニングするために必要なパラメーターを作成および生成する際の簡単な手順を示します。 Pivotal Cloud Foundry ソリューションを見つけるには、Azure [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) で検索します。

![Azure で Pivotal Cloud Foundry を検索する](media/deploy/pcf-marketplace.png)


## <a name="generate-an-ssh-public-key"></a>SSH 公開キーを生成する

Windows、Mac、または Linux を使用して公開セキュア シェル (SSH) キーを生成する方法はいくつかあります。

```Bash
ssh-keygen -t rsa -b 2048
```

詳細については、[Azure 上の Windows での SSH キーの使用](https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)に関するページを参照してください。

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成

> [!NOTE]
>
> サービス プリンシパルを作成するには、所有者アカウントのアクセス許可が必要です。 サービス プリンシパルの作成を自動化するスクリプトを記述することもできます。 たとえば、Azure CLI で [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest) を使用できます。

1. Azure アカウントにサインインします。

    `az login`

    ![Azure CLI のログイン](media/deploy/az-login-output.png )
 
    後で使用するために、**サブスクリプション ID** としての "id" 値と、"tenantId" 値をコピーします。

2. この構成の既定のサブスクリプションを設定します。

    `az account set -s {id}`

3. PCF 用の Azure Active Directory アプリケーションを作成します。 一意の英数字のパスワードを指定します。 後で使用するために、パスワードを **clientSecret** として格納します。

    `az ad app create --display-name "Svc Principal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    後で使用するために、出力内の "appId" 値を **clientID** としてコピーします。

    > [!NOTE]
    >
    > 独自のアプリケーションのホーム ページと識別子 URI (http\://www\.contoso.com など) を選択します。

4. 新しいアプリ ID でサービス プリンシパルを作成します。

    `az ad sp create --id {appId}`

5. サービス プリンシパルのアクセス許可のロールは、共同作成者として設定します。

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor”`

    以下を使用することもできます

    `az role assignment create --assignee {service-principal-name} --role “Contributor”`

    ![サービス プリンシパルのロールの割り当て](media/deploy/svc-princ.png )

6. アプリ ID、パスワード、およびテナント ID を使用して、サービス プリンシパルに正しくサインインできることを確認します。

    `az login --service-principal -u {appId} -p {your-password}  --tenant {tenantId}`

7. 次の形式の .json ファイルを作成します。 先ほどコピーした**サブスクリプション ID**、**tenantID**、**clientID**、**clientSecret** の各値を使用します。 ファイルを保存します。

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Pivotal Network トークンを取得する

1. [Pivotal Network](https://network.pivotal.io) アカウントに登録またはサインインします。
2. ページの右上隅にある自分のプロファイル名を選択します。 **[プロファイルの編集]** を選択します。
3. ページの一番下までスクロールして、**LEGACY API TOKEN** の値をコピーします。 この値は、後で使用する **Pivotal Network トークン**の値です。

## <a name="provision-your-cloud-foundry-cluster-on-azure"></a>Azure で Cloud Foundry クラスターをプロビジョニングする

これで、[Pivotal Cloud Foundry クラスターを Azure](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) でプロビジョニングするために必要なパラメーターがすべて揃いました。
パラメーターを入力し、PCF クラスターを作成します。

## <a name="verify-the-deployment-and-sign-in-to-the-pivotal-ops-manager"></a>デプロイを確認して Pivotal Ops Manager にサインインする

1. PCF クラスターに、デプロイの状態が表示されます。

    ![Azure のデプロイ状態](media/deploy/deployment.png )

2. 左側のナビゲーションの **[デプロイ]** リンクを選択して、PCF Ops Manager の資格情報を取得します。 次のページで **[デプロイ名]** を選択します。
3. 左側のナビゲーションで **[出力]** リンクを選択して、PCF Ops Manager の URL、ユーザー名、およびパスワードを表示します。 "OPSMAN-FQDN" 値が URL です。
 
    ![Cloud Foundry のデプロイの出力](media/deploy/deploy-outputs.png )
 
4. Web ブラウザーで URL を開始します。 前の手順の資格情報を入力してサインインします。

    ![Pivotal のサインイン ページ](media/deploy/pivotal-login.png )
         
    > [!NOTE]
    >
    > "サイトが安全でない" という警告メッセージのために Internet Explorer ブラウザーが失敗する場合は、 **[詳細]** を選択して Web ページに移動します。 Firefox の場合は、 **[Advance]** を選択し、証明書を追加して続行します。

5. PCF Ops Manager には、デプロイされた Azure インスタンスが表示されます。 これで、アプリケーションをここでデプロイして管理できます。
               
    ![Pivotal でデプロイされた Azure インスタンス](media/deploy/ops-mgr.png )
 
