---
title: クイック スタート:Azure portal を使用して Azure API for FHIR をデプロイする
description: このクイックスタートでは、Azure portal を使用して Azure API for FHIR をデプロイし、設定を構成する方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 03/15/2020
ms.author: cavoeg
ms.openlocfilehash: cabacd5204f4a9ac5c17c7bd66924482b5bf688a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018578"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-azure-portal"></a>クイック スタート:Azure portal を使用して Azure API for FHIR をデプロイする

このクイックスタートでは、Azure portal を使用して Azure API for FHIR をデプロイする方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-new-resource"></a>新しいリソースの作成

[Azure portal](https://portal.azure.com) を開いて **[リソースの作成]** をクリックします。

![リソースの作成](media/quickstart-paas-portal/portal-create-resource.png)

## <a name="search-for-azure-api-for-fhir"></a>Azure API for FHIR を検索する

Azure API for FHIR は、検索ボックスに「FHIR」と入力すると見つかります。

:::image type="content" source="media/quickstart-paas-portal/portal-search-healthcare-apis.png" alt-text="医療 API を検索する":::

## <a name="create-azure-api-for-fhir-account"></a>Azure API for FHIR アカウントを作成する

新しい Azure API for FHIR アカウントを作成するには、 **[作成]** を選択します。

:::image type="content" source="media/quickstart-paas-portal/portal-create-healthcare-apis.png" alt-text="Azure API for FHIR アカウントを作成する":::

## <a name="enter-account-details"></a>アカウントの詳細を入力する

既存のリソース グループを選択するか、リソース グループを新たに作成し、アカウントの名前を選択して、最後に **[確認と作成]** をクリックします。

:::image type="content" source="media/quickstart-paas-portal/portal-new-healthcareapi-details.png" alt-text="新しい医療 API の詳細":::

作成の確認を行い、FHIR API のデプロイを待ちます。

## <a name="additional-settings-optional"></a>追加の設定 (オプション)

**[次へ: 追加設定]** をクリックして認証設定を表示することもできます。 Azure API for FHIR の既定の構成では、[データ プレーン ロールの割り当てに Azure RBAC が使用されます](configure-azure-rbac.md)。 このモードに構成されている場合、FHIR サービスの "オーソリティ" は、サブスクリプションの Azure Active Directory テナントに設定されます。

:::image type="content" source="media/rbac/confirm-azure-rbac-mode-create.png" alt-text="既定の認証設定":::

この場合、ロール割り当ての構成に Azure RBAC を使用するため、許可されたオブジェクト ID を入力するためのボックスが淡色表示されていることに注意してください。

外部またはセカンダリの Azure Active Directory テナントを使用するように FHIR サービスを構成する場合は、オーソリティを変更し、サーバーへのアクセスを許可するユーザーおよびグループのオブジェクト ID を入力します。 詳細については、[ローカル RBAC の構成](configure-local-rbac.md)に関するガイドを参照てください。

## <a name="fetch-fhir-api-capability-statement"></a>FHIR API の機能ステートメントをフェッチする

新しい FHIR API アカウントがプロビジョニングされていることを確認するには、ブラウザーに `https://<ACCOUNT-NAME>.azurehealthcareapis.com/metadata` を指定して機能ステートメントをフェッチします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループ、Azure API for FHIR、およびすべての関連リソースは、不要になったら削除できます。 そのためには、Azure API for FHIR アカウントがあるリソース グループを選択し、 **[リソース グループの削除]** を選択して、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次のステップ

このクイックスタート ガイドでは、ご利用のサブスクリプションに Azure API for FHIR をデプロイしました。 Azure API for FHIR に対してその他の設定を行うには、追加設定の攻略ガイドに進んでください。 Azure API for FHIR を使い始める準備ができたら、アプリケーションの登録方法についての詳しい情報をご覧ください。

>[!div class="nextstepaction"]
>[Azure API for FHIR の追加設定](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[アプリケーションの登録の概要](fhir-app-registration.md)
