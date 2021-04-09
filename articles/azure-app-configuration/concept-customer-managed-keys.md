---
title: カスタマー マネージド キーを使用して構成データを暗号化する
description: カスタマー マネージド キーを使用して、構成データを暗号化する
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930502"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>カスタマー マネージド キーを使用して App Configuration データを暗号化する
Azure App Configuration では、[保存されている機密情報を暗号化](../security/fundamentals/encryption-atrest.md)します。 カスタマー マネージド キーを使用すると、暗号化キーを管理できるため、データ保護が強化されます。  マネージド キー暗号化が使用されている場合、App Configuration 内のすべての機密情報が、ユーザー指定の Azure Key Vault キーで暗号化されます。  これにより、必要に応じて暗号化キーを交換することができます。  また、App Configuration インスタンスのキーへのアクセスを取り消すことによって、機密情報への Azure App Configuration のアクセスを取り消すことができます。

## <a name="overview"></a>概要 
Azure App Configuration では、Microsoft によって提供される 256 ビットの AES 暗号化キーを使用して、保存されている機密情報を暗号化します。 すべての App Configuration インスタンスには、サービスによって管理され、機密情報を暗号化するために使用される独自の暗号化キーがあります。 機密情報には、キーと値のペアで検出された値が含まれます。  カスタマー マネージド キーの機能が有効になっている場合、App Configuration では、App Configuration インスタンスに割り当てられたマネージド ID を使用して Azure Active Directory で認証を行います。 その後、マネージド ID で Azure Key Vault が呼び出され、App Configuration インスタンスの暗号化キーがラップされます。 ラップされた暗号化キーはその後、格納され、ラップされていない暗号化キーは App Configuration 内に 1 時間キャッシュされます。 App Configuration では、ラップされていないバージョンの App Configuration インスタンスの暗号化キーが 1 時間ごとに更新されます。 これにより、通常の運用条件下の可用性が保証されます。 

>[!IMPORTANT]
> App Configuration インスタンスに割り当てられた ID でインスタンスの暗号化キーのラップを解除することが承認されなくなった場合、またはマネージド キーが完全に削除されている場合は、App Configuration インスタンスに格納されている機密情報の暗号化を解除できなくなります。 Azure Key Vault の[論理的な削除](../key-vault/general/soft-delete-overview.md)機能を使用すると、暗号化キーを誤って削除する可能性が少なくなります。

ユーザーは、Azure App Configuration インスタンスでマネージド キー機能を有効にした場合、機密情報にアクセスするサービスの機能を制御することができます。 マネージド キーはルート暗号化キーとして機能します。 ユーザーは、キー コンテナーのアクセス ポリシーを変更することで、App Configuration インスタンスのマネージド キーへのアクセスを取り消すことができます。 このアクセスが取り消されると、App Configuration では 1 時間以内にユーザー データの暗号化を解除できなくなります。 この時点で、App Configuration インスタンスではすべてのアクセス試行を禁止します。 この状況は、もう一度サービスにマネージド キーへのアクセスを許可することで回復できます。  1 時間以内に、App Configuration ではユーザー データの暗号化を解除し、通常の条件下で動作できるようになります。

>[!NOTE]
>すべての Azure App Configuration データは、分離バックアップで最大 24 時間格納されます。 これには、ラップされていない暗号化キーが含まれます。 このデータは、サービスやサービス チームですぐに使用できるわけではありません。 緊急時の復元の場合、Azure App Configuration では、それ自体をマネージド キー データから再度取り消します。

## <a name="requirements"></a>必要条件
Azure App Configuration に対するカスタマー マネージド キー機能を正常に有効にするには、次のコンポーネントが必要です。
- Standard レベルの Azure App Configuration インスタンス
- 論理的な削除と消去保護機能が有効になっている Azure Key Vault
- Key Vault 内の RSA または RSA-HSM キー
    - キーの有効期限が切れていないこと、キーが有効になっていること、およびラップとその解除の両方の機能が有効になっていることが必要です

これらのリソースが構成されたら、次の 2 つの手順を行うことで、引き続き Azure App Configuration で Key Vault キーを使用できます。
1. Azure App Configuration インスタンスにマネージド ID を割り当てます
2. ターゲットの Key Vault のアクセス ポリシーで、ID の `GET`、`WRAP`、および `UNWRAP` アクセス許可を付与します。

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Azure App Configuration インスタンスに対してカスタマー マネージド キー暗号化を有効にする
開始するには、適切に構成された Azure App Configuration インスタンスが必要です。 使用可能な App Configuration インスタンスがまだない場合は、これらのクイックスタートのいずれかに従って設定します。
- [Azure App Configuration を使用して ASP.NET Core アプリを作成する](quickstart-aspnet-core-app.md)
- [Azure App Configuration を使用して .NET Core アプリを作成する](quickstart-dotnet-core-app.md)
- [Azure App Configuration を使用して .NET Framework アプリを作成する](quickstart-dotnet-app.md)
- [Azure App Configuration を使用して Java Spring アプリを作成する](quickstart-java-spring-app.md)

>[!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。これを使用して、この記事のコマンド ライン命令を実行することができます。  .NET Core SDK などの一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにログインしている場合は、shell.azure.com から [Azure Cloud Shell](https://shell.azure.com) を起動します。  Azure Cloud Shell の詳細については、[ドキュメントを参照](../cloud-shell/overview.md)してください

### <a name="create-and-configure-an-azure-key-vault"></a>Azure Key Vault を作成して構成する
1. Azure CLI を使用して、Azure Key Vault を作成します。  `vault-name` と `resource-group-name` はどちらもユーザー指定であり、一意である必要があることに注意してください。  これらの例では、`contoso-vault` と `contoso-resource-group` を使用します。

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Key Vault に対して論理的な削除と消去保護を有効にします。 手順 1 で作成された Key Vault (`contoso-vault`) とリソース グループ (`contoso-resource-group`) の名前に置き換えます。

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Key Vault キーを作成します。 このキーに一意の `key-name` を指定し、手順 1 で作成された Key Vault (`contoso-vault`) の名前に置き換えます。 `RSA` と `RSA-HSM` のどちらの暗号化を優先するかを指定します。

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    このコマンドからの出力には、生成されたキーのキー ID ("kid") が示されます。  この演習で後ほど使用するためにキー ID をメモしておきます。  キー ID の形式は `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` になります。  キー ID には、次の 3 つの重要なコンポーネントがあります。
    1. Key Vault URI: `https://{自分のキー コンテナー}.vault.azure.net
    1. Key Vault キーの名前: {キーの名前}
    1. Key Vault キーのバージョン: {キーのバージョン}

1. Azure CLI を使用して、システム割り当てマネージド ID を作成します。その際に、前の手順で使用された App Configuration インスタンスとリソース グループの名前に置き換えます。 マネージド ID は、マネージド キーにアクセスするために使用されます。 App Configuration インスタンスの名前を示すために、`contoso-app-config` を使用しています。
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    このコマンドの出力には、システム割り当て ID のプリンシパル ID ("principalId") とテナント ID ("tenandId") が含まれます。  これらの ID は、マネージド キーへの ID によるアクセスを許可するために使用されます。

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. キーの検証、暗号化、暗号化解除を行うには、Azure App Configuration インスタンスのマネージド ID でキーにアクセスできる必要があります。 アクセスが必要な特定のアクション セットには、キーの `GET`、`WRAP`、および `UNWRAP` が含まれます。  アクセスを許可するには、App Configuration インスタンスのマネージド ID のプリンシパル ID が必要です。 この値は前の手順で取得されたものです。 以下のように、`contoso-principalId` として示されます。 コマンド ラインを使用して、マネージド キーへのアクセス許可を付与します。

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Azure App Configuration インスタンスでマネージド キーにアクセスできるようになったら、Azure CLI を使用して、サービスでカスタマー マネージド キー機能を有効にすることができます。 キーの作成手順で記録された `key name` `key vault URI` プロパティを思い出してください。

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

これで、Azure App Configuration インスタンスは、Azure Key Vault に格納されているカスタマー マネージド キーを使用するように構成されました。

## <a name="next-steps"></a>次の手順
この記事では、暗号化にカスタマー マネージド キーを使用するように Azure App Configuration インスタンスを構成しました。  [サービスを Azure マネージド ID と統合する](howto-integrate-azure-managed-service-identity.md)方法について学習します。
