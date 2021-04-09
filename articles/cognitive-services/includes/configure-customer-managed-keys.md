---
title: Azure portal を使用してカスタマー マネージド キーを構成する
titleSuffix: Cognitive Services
description: Azure portal を使用して、Azure Key Vault でカスタマー マネージド キーを構成する方法について説明します。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 05/28/2020
ms.author: egeaney
ms.openlocfilehash: c79846b0a5b675c34e4e7919e9ecd9d591bfefe5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96356360"
---
## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault でのカスタマー マネージド キー

カスタマー マネージド キーを格納するには、Azure Key Vault を使用する必要があります。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 Cognitive Services リソースとキー コンテナーは、同じリージョンの同じ Azure Active Directory (Azure AD) テナント内に存在する必要がありますが、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「[Azure Key Vault とは](../../key-vault/general/overview.md)」をご覧ください。

新しい Cognitive Services リソースが作成されるときには、常に Microsoft のマネージド キーを使用して暗号化されます。 リソースを作成するときに、カスタマー マネージド キーを有効にすることはできません。 カスタマー マネージド キーは Azure Key Vault に格納され、キー コンテナーは、Cognitive Services リソースに関連付けられているマネージド ID に対してキーのアクセス許可を付与するアクセス ポリシーを使用して、プロビジョニングする必要があります。 マネージド ID は、CMK に必要な価格レベルを使用してリソースを作成した後でのみ使用できます。

カスタマー マネージド キーを有効にすると、システムによって割り当てられる[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) (Azure AD の機能) も有効になります。 システム割り当てのマネージド ID が有効になると、このリソースは Azure Active Directory に登録されます。 登録された後、マネージド ID には、カスタマー マネージド キーのセットアップ時に選択されたキー コンテナーへのアクセス権が付与されます。 

> [!IMPORTANT]
> システム割り当てのマネージド ID を無効にすると、キー コンテナーへのアクセスは削除され、カスタマー キーで暗号化されたデータにはアクセスできなくなります。 このデータに依存する機能はすべて動作しなくなります。

> [!IMPORTANT]
> マネージド ID は現在、クロスディレクトリ シナリオをサポートしていません。 Azure portal でカスタマー マネージド キーを構成すると、内部でマネージド ID が自動的に割り当てられます。 その後、サブスクリプション、リソース グループ、またはリソースを 1 つの Azure AD ディレクトリから別のディレクトリに移動した場合、そのリソースに関連付けられているマネージド ID は新しいテナントに転送されないため、カスタマー マネージド キーが機能しなくなることがあります。 詳細については、「[Azure リソース用マネージド ID に関する FAQ と既知の問題](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)」の中の「**Azure AD ディレクトリ間のサブスクリプションの転送**」を参照してください。  

## <a name="configure-azure-key-vault"></a>Azure Key Vault を構成する

カスタマー マネージド キーを使用するには、キー コンテナーで "**論理的な削除**" と "**消去しない**" の 2 つのプロパティが設定されている必要があります。 これらのプロパティは既定では有効になっていませんが、新規または既存のキー コンテナーに対して PowerShell または Azure CLI を使用して有効にすることができます。

> [!IMPORTANT]
> "**論理的な削除**" と "**消去しない**" のプロパティを有効にしないで、キーを削除すると、Cognitive Services リソース内のデータを復旧できなくなります。

既存のキー コンテナーでこれらのプロパティを有効にする方法については、次のいずれかの記事の「**論理的な削除を有効にする**」および「**消去保護を有効にする**」を参照してください。

- [PowerShell で Key Vault の論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)
- [CLI で Key Vault の論理的な削除を使用する方法](../../key-vault/general/key-vault-recovery.md)

Azure Storage 暗号化では、サイズが 2048 の RSA キーのみがサポートされています。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](../../key-vault/general/about-keys-secrets-certificates.md)」の「**Key Vault のキー**」を参照してください。

## <a name="enable-customer-managed-keys-for-your-resource"></a>リソース用にカスタマー マネージド キーを有効にする

Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. Cognitive Services リソースに移動します。
1. Cognitive Services リソースの **[設定]** ブレードで、 **[暗号化]** をクリックします。 次の図に示すように、 **[カスタマー マネージド キー]** オプションを選択します。

    ![[カスタマー マネージド キー] を選択する方法を示すスクリーンショット](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>キーを指定する

カスタマー マネージド キーを有効にした後で、キーと Cognitive Services リソースの関連付けを指定することができます。

### <a name="specify-a-key-as-a-uri"></a>URI としてキーを指定する

URI としてキーを指定するには、次の手順のようにします。

1. Azure portal でキーの URI を調べるには、キー コンテナーに移動して、 **[キー]** 設定を選択します。 目的のキーを選択し、キーをクリックしてそのバージョンを表示します。 そのバージョンの設定を表示するには、キーのバージョンを選択します。
1. URI を示している **[キー識別子]** フィールドの値をコピーします。

    ![キー コンテナーのキー URI が表示されているスクリーンショット](../media/cognitive-services-encryption/key-uri-portal.png)

1. ストレージ アカウントの **[暗号化]** の設定で、 **[キー URI を入力]** オプションを選択します。
1. コピーした URI を **[キー URI]** フィールドに貼り付けます。

   ![キー URI の入力方法が示されているスクリーンショット](../media/cognitive-services-encryption/ssecmk2.png)

1. キー コンテナーを含むサブスクリプションを指定します。
1. 変更を保存します。

### <a name="specify-a-key-from-a-key-vault"></a>キー コンテナーのキーを指定する

キー コンテナーからキーを指定するには、まず、キーが含まれるキー コンテナーがあることを確認します。 キー コンテナーからキーを指定するには、次の手順のようにします。

1. **[Select from Key Vault]\(キー コンテナーから選択\)** オプションを選択します。
1. 使用するキーを含むキー コンテナーを選択します。
1. キー コンテナーからキーを選択します。

   ![カスタマー マネージド キーのオプションが示されているスクリーンショット](../media/cognitive-services-encryption/ssecmk3.png)

1. 変更を保存します。

## <a name="update-the-key-version"></a>キーのバージョンを更新する

キーの新しいバージョンを作成した場合、その新しいバージョンを使用するには、Cognitive Services リソースを更新します。 次の手順に従います。

1. Cognitive Services リソースに移動し、 **[暗号化]** の設定を表示します。
1. 新しいキーのバージョンの URI を入力します。 または、キー コンテナーとキーを再び選択して、バージョンを更新してもかまいません。
1. 変更を保存します。

## <a name="use-a-different-key"></a>別のキーを使用する

暗号化に使用されるキーを変更するには、次の手順のようにします。

1. Cognitive Services リソースに移動し、 **[暗号化]** の設定を表示します。
1. 新しいキーの URI を入力します。 キーコンテナーを選択して新しいキーを選択することもできます。
1. 変更を保存します。

## <a name="rotate-customer-managed-keys"></a>カスタマー マネージド キーをローテーションする

Azure Key Vault のカスタマー マネージド キーは、お使いのコンプライアンス ポリシーに従ってローテーションすることができます。 キーをローテーションするときは、新しいキー URI を使用するように Cognitive Services リソースを更新する必要があります。 Azure portal で新しいバージョンのキーを使用するようにリソースを更新する方法については、「[キーのバージョンを更新する](#update-the-key-version)」のセクションを参照してください。

キーをローテーションしても、リソースのデータの再暗号化はトリガーされません。 ユーザーがこれ以上操作を行う必要はありません。

## <a name="revoke-access-to-customer-managed-keys"></a>カスタマー マネージド キーへのアクセス権を取り消す

カスタマー マネージド キーへのアクセス権を取り消すには、PowerShell または Azure CLI を使用します。 詳細については、[Azure Key Vault PowerShell](/powershell/module/az.keyvault//) に関する記事、または [Azure Key Vault CLI](/cli/azure/keyvault) に関する記事を参照してください。 アクセス権を取り消すと、Cognitive Services で暗号化キーにアクセスできなくなるため、Cognitive Services リソース内のすべてのデータへのアクセスが事実上ブロックされます。

## <a name="disable-customer-managed-keys"></a>カスタマー マネージド キーを無効にする

カスタマー マネージド キーを無効にすると、Cognitive Services リソースはそれ以降、Microsoft マネージド キーを使用して暗号化されます。 カスタマー マネージド キーを無効にするには、次の手順を実行します。

1. Cognitive Services リソースに移動し、 **[暗号化]** の設定を表示します。
1. **[独自のキーを使用する]** 設定の横にあるチェック ボックスをオフにします。