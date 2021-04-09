---
title: Azure Spring Cloud アプリケーションのシステム割り当てマネージド ID を有効にする
description: アプリケーションのシステム割り当てマネージド ID を有効にする方法。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 05/13/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 2fbdd46b872c4c70999355d457045f4aac8aa34c
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877427"
---
# <a name="how-to-enable-system-assigned-managed-identity-for-azure-spring-cloud-application"></a>Azure Spring Cloud アプリケーションのシステム割り当てマネージド ID を有効にする方法

**この記事の適用対象:** ✔️ Java ✔️ C#

Azure リソースのマネージド ID では、Azure Active Directory で自動的に管理される ID が、Azure Spring Cloud アプリケーションなどの Azure リソースに提供されます。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。

この記事では、Azure portal と CLI (バージョン0.2.4 から利用可能) を使用して、Azure Spring Cloud アプリのシステム割り当てマネージド ID を有効または無効にする方法を示します。

## <a name="prerequisites"></a>前提条件
Azure リソースのマネージド ID に習熟してない場合は、[概要に関するセクション](../active-directory/managed-identities-azure-resources/overview.md)を参照してください。
デプロイ済みの Azure Spring Cloud インスタンスが必要です。 [Azure CLI を使用してデプロイするためのクイックスタート](spring-cloud-quickstart.md)に従ってください。

## <a name="add-a-system-assigned-identity"></a>システム割り当て ID を追加する
システム割り当て ID を持つアプリを作成するには、アプリケーションに追加のプロパティを設定する必要があります。

### <a name="using-azure-portal"></a>Azure Portal の使用
[Azure portal](https://portal.azure.com/) でマネージド ID を設定するには、先にアプリケーションを作成し、その後でこの機能を有効にします。

1. ポータルを使って通常の方法でアプリを作成します。 ポータルでアプリに移動します。
2. 左側のナビゲーション ペインで、 **[設定]** グループまで下へスクロールします。
3. **[ID]** を選択します。
4. **[システム割り当て済み]** タブで、 **[状態]** を *[オン]* に切り替えます。 **[保存]** をクリックします。

 ![ポータルでのマネージド ID](./media/spring-cloud-managed-identity/identity-1.png)

### <a name="using-azure-cli"></a>Azure CLI の使用
システム割り当てマネージド ID は、アプリの作成時に有効にするか、既存のアプリに対して有効にできます。

**アプリの作成時にシステム割り当てマネージド ID を有効にする**

次の例では、`--assign-identity` パラメーターの要求どおりに、システム割り当てマネージド ID を持つ *app_name* という名前のアプリが作成されます。

```azurecli
az spring-cloud app create -n app_name -s service_name -g resource_group_name --assign-identity
```

**既存のアプリに対してシステム割り当てマネージド ID を有効にする** `az spring-cloud app identity assign` コマンドを使用して、既存のアプリに対してシステム割り当てID を有効にします。

```azurecli
az spring-cloud app identity assign -n app_name -s service_name -g resource_group_name
```

## <a name="obtain-tokens-for-azure-resources"></a>Azure リソースのトークンを取得する
アプリでは、そのマネージド ID を使って、Azure Key Vault など Azure Active Directory で保護されているその他のリソースにアクセスするトークンを取得できます。 これらのトークンは、アプリケーションの特定のユーザーではなく、リソースにアクセスするアプリケーションを表します。

[自分のアプリケーションからのアクセスを許可するようにターゲット リソースを構成する](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)ことが必要になる場合があります。 たとえば、Key Vault にアクセスするためのトークンを要求する場合は、お使いのアプリケーションの ID を含むアクセス ポリシーを追加していることを確認してください。 追加しないと、トークンを含めた場合でも、Key Vault の呼び出しは拒否されます。 Azure Active Directory トークンをサポートしているリソースの詳細については、「[Azure AD 認証をサポートしている Azure サービス](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)」をご覧ください。

Azure Spring Cloud では、トークンの取得に Azure Virtual Machine と同じエンドポイントが共有されます。 Java SDK または Spring Boot Starter を使用してトークンを取得することをお勧めします。  各種コードとスクリプトの例や、トークンの有効期限や HTTP エラーの処理などの重要なトピックに関するガイダンスについては、[VM トークンの使用方法](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)に関する記事を参照してください。

推奨: Java SDK または Spring Boot Starter を使用してトークンを取得します。  [次の手順](#next-steps)に含まれているサンプルを参照してください。

## <a name="disable-system-assigned-identity-from-an-app"></a>アプリからシステム割り当て ID を無効にする
システム割り当て ID を削除すると、Azure AD からも削除されます。 アプリ リソースを削除すると、Azure AD からシステム割り当て ID が自動的に削除されます。

### <a name="using-azure-portal"></a>Azure Portal の使用
不要になったシステム割り当てマネージド ID をアプリから削除するには:

1. Azure Spring Cloud インスタンスが含まれている Azure サブスクリプションに関連付けられているアカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインします。
1. 目的の仮想マシンに移動して **[ID]** を選択します。
1. **[システム割り当て済み]** / **[状態]** で **[オフ]** を選択し、 **[保存]** をクリックします。

 ![マネージド ID](./media/spring-cloud-managed-identity/remove-identity.png)

### <a name="using-azure-cli"></a>Azure CLI の使用
不要になったシステム割り当てマネージド ID をアプリから削除するには、次のコマンドを使用します。
```azurecli
az spring-cloud app identity remove -n app_name -s service_name -g resource_group_name
```

## <a name="next-steps"></a>次のステップ

* [Spring Boot Starter でマネージド ID を使用して Azure Key Vault にアクセスする](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter-keyvault-secrets/README.md#use-msi--managed-identities)
* [Azure リソースのマネージド ID に関する詳細情報](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Java SDK でマネージド ID を使用する方法](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples)
