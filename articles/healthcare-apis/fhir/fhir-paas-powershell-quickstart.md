---
title: クイック スタート:PowerShell を使用して Azure API for FHIR をデプロイする
description: このクイックスタートでは、PowerShell を使用して Azure API for FHIR をデプロイする方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 10/15/2019
ms.author: cavoeg
ms.openlocfilehash: ca20346f4c2968eac5e7308cb05b59f968a1c63e
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103018568"
---
# <a name="quickstart-deploy-azure-api-for-fhir-using-powershell"></a>クイック スタート:PowerShell を使用して Azure API for FHIR をデプロイする

このクイックスタートでは、PowerShell を使用して Azure API for FHIR をデプロイする方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="register-the-azure-api-for-fhir-resource-provider"></a>Azure API for FHIR リソースプロバイダーを登録する

`Microsoft.HealthcareApis` リソースプロバイダーがまだサブスクリプションに登録されていない場合は、次のコマンドで登録できます。

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.HealthcareApis
```

## <a name="create-azure-resource-group"></a>Azure リソース グループを作成する

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroupName" -Location westus2
```

## <a name="deploy-azure-api-for-fhir"></a>Azure API for FHIR をデプロイする

```azurepowershell-interactive
New-AzHealthcareApisService -Name nameoffhirservice -ResourceGroupName myResourceGroupName -Location westus2 -Kind fhir-R4
```

> [!NOTE]
> インストールした `Az` PowerShell モジュールのバージョンによっては、プロビジョニング済みの FHIR サーバーが[ローカル RBAC](configure-local-rbac.md) を使用するように構成され、デプロイされた FHIR サービスの許可された ID オブジェクト ID の一覧に、現在サインインしている PowerShell ユーザーが設定されている場合があります。 今後、データ プレーン ロールの割り当てには [Azure RBAC の使用](configure-azure-rbac.md)をお勧めします。また、デプロイ後は、Azure RBAC モードを有効にするために、このユーザー オブジェクト ID を削除しなければならない場合があります。


## <a name="fetch-capability-statement"></a>機能ステートメントをフェッチする

Azure API for FHIR アカウントが実行されていることは、FHIR 機能ステートメントをフェッチすることで確認できます。

```azurepowershell-interactive
$metadata = Invoke-WebRequest -Uri "https://nameoffhirservice.azurehealthcareapis.com/metadata"
$metadata.RawContent
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションを引き続き使用しない場合は、次の手順でリソース グループ全体を削除します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupName
```

## <a name="next-steps"></a>次のステップ

このクイックスタート ガイドでは、ご利用のサブスクリプションに Azure API for FHIR をデプロイしました。 Azure API for FHIR に対してその他の設定を行うには、追加設定の攻略ガイドに進んでください。 Azure API for FHIR を使い始める準備ができたら、アプリケーションの登録方法についての詳しい情報をご覧ください。

>[!div class="nextstepaction"]
>[Azure API for FHIR の追加設定](azure-api-for-fhir-additional-settings.md)

>[!div class="nextstepaction"]
>[アプリケーションの登録の概要](fhir-app-registration.md)
