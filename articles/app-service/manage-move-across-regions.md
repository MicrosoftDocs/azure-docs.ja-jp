---
title: アプリを別のリージョンに移動する
description: リージョン間で App Service リソースを移動する方法を学習します。
ms.topic: how-to
ms.date: 02/27/2020
ms.custom: subject-moving-resources
ms.openlocfilehash: 87a2da31802a8b6858e875c23ef1dbd2d6d006bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86524856"
---
# <a name="move-an-app-service-resource-to-another-region"></a>App Service リソースを別のリージョンに移動する

この記事では、App Service リソースを別の Azure リージョンに移動する方法について説明します。 さまざまな理由により、リソースを別のリージョンに移動する場合があります。 たとえば、新しい Azure リージョンを利用するため、特定のリージョンでのみ利用可能な機能やサービスをデプロイするため、内部ポリシーとガバナンスの要件を満たすため、または容量計画の要件に応じるためです。

App Service リソースはリージョン固有のものであり、リージョン間で移動することはできません。 ターゲット リージョンに既存の App Service リソースのコピーを作成し、新しいアプリにコンテンツを移動する必要があります。 ソース アプリでカスタム ドメインを使用する場合は、完了時に[ターゲット リージョンの新しいアプリに移行する](manage-custom-dns-migrate-domain.md)ことができます。

アプリのコピーをより簡単にするために、別のリージョンの App Service プランに[個々の App Service アプリを複製する](app-service-web-app-cloning.md)ことはできますが、特に Linux アプリがサポートされないという[制限](app-service-web-app-cloning.md#current-restrictions)があります。

## <a name="prerequisites"></a>前提条件

- App Service アプリが、移動元の Azure リージョンにあることを確認します。
- ターゲット リージョンで App Service と、そのリソースを移動する関連サービスがサポートされていることを確認します。
<!-- - Domain bindings, certificates, and managed identities can't replicated using the **Export template** method. You must create them manually. -->

## <a name="prepare"></a>準備

現在使用しているすべての App Service リソースを特定します。 次に例を示します。

- App Service アプリ
- [App Service プラン](overview-hosting-plans.md)
- [デプロイ スロット](deploy-staging-slots.md)
- [Azure で購入したカスタム ドメイン](manage-custom-dns-buy-domain.md)
- [SSL 証明書](configure-ssl-certificate.md)
- [Azure Virtual Network の統合](web-sites-integrate-with-vnet.md)
- [ハイブリッド接続](app-service-hybrid-connections.md)。
- [マネージド ID](overview-managed-identity.md)
- [バックアップの設定](manage-backup.md)

インポートされた証明書やハイブリッド接続などの特定のリソースには、他の Azure サービスとの統合が含まれています。 これらのリソースをリージョン間で移動する方法については、それぞれのサービスのドキュメントを参照してください。

## <a name="move"></a>詳細ビュー

1. [ソース アプリのバックアップを作成します](manage-backup.md)。
1. [新しい App Service プランのアプリを、ターゲット リージョンに作成します](app-service-plan-manage.md#create-an-app-service-plan)。
2. [ターゲット アプリでバックアップを復元します](web-sites-restore.md)
2. カスタム ドメインを使用する場合は、`awverify.` を指定して[事前にターゲット アプリにバインド](manage-custom-dns-migrate-domain.md#bind-the-domain-name-preemptively)し、[ターゲット アプリでドメインを有効にします](manage-custom-dns-migrate-domain.md#enable-the-domain-for-your-app)。
3. ターゲット アプリ内の他のすべてを、ソース アプリと同じになるように構成し、構成を確認します。
4. カスタム ドメインでターゲット アプリをポイントする準備ができたら、[ドメイン名を再マッピング](manage-custom-dns-migrate-domain.md#remap-the-active-dns-name)します。

<!-- 1. Login to the [Azure portal](https://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source App Service resources and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Deploy** in the **Export template** blade.
5. Click **TEMPLATE** > **Edit template** to open the template in the online editor.
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"identity": {` to find any managed identity definition. The following is an example if you have a user-assigned managed identity.
    ```json
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/<group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<identity-name>": {
                "principalId": "00000000-0000-0000-0000-000000000000",
                "clientId": "00000000-0000-0000-0000-000000000000"
            }
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/sites/hostNameBindings` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/sites/hostNameBindings",
        "apiVersion": "2018-11-01",
        "name": "[concat(parameters('sites_webapp_name'), '/', parameters('sites_webapp_name'), '.azurewebsites.net')]",
        "location": "West Europe",
        "dependsOn": [
            "[resourceId('Microsoft.Web/sites', parameters('sites_webapp_name'))]"
        ],
        "properties": {
            "siteName": "<app-name>",
            "hostNameType": "Verified"
        }
    },
    ```
6. Click inside the online editor and type Ctrl+F (or ⌘+F on a Mac) and type `"Microsoft.Web/certificates` to find all hostname bindings. The following is an example if you have a user-assigned managed identity.
    ```json
    {
        "type": "Microsoft.Web/certificates",
        "apiVersion": "2018-11-01",
        "name": "[parameters('certificates_test2_cephaslin_com_name')]",
        "location": "West Europe",
        "properties": {
            "hostNames": [
                "[parameters('certificates_test2_cephaslin_com_name')]"
            ],
            "password": "[parameters('certificates_test2_cephaslin_com_password')]"
        }
    },
    ```
7. Delete the entire JSON block. Click **Save** in the online editor.
8. Click **BASICS** > **Create new** to create a new resource group. Type the group name and click **OK**.
9. In **BASICS** > **Location**, select the region you want.   -->

## <a name="clean-up-source-resources"></a>ソース リソースをクリーンアップする

ソース アプリと App Service プランを削除します。 [非 Free レベルの App Service プランでは、アプリが実行されていない場合でも料金が発生します。](app-service-plan-manage.md#delete-an-app-service-plan)

## <a name="next-steps"></a>次のステップ

[PowerShell を使用した Azure App Service アプリの複製](app-service-web-app-cloning.md)