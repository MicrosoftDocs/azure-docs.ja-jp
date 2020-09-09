---
title: Azure portal を使用してカスタマー マネージド キーを構成する
titleSuffix: Azure Storage
description: Azure ポータルを使用して、Azure Storage 暗号化用に、Azure Key Vault でカスタマー マネージド キーを構成する方法について学びます。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 07/31/2020
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: a506f59d3f2d331e4c7680565f3c110b9cd12956
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88799173"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Azure portal を使用して Azure Key Vault でカスタマー マネージド キーを構成する

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

この記事では、[Azure portal](https://portal.azure.com/) を使って、カスタマー マネージド キーで Azure Key Vault を構成する方法について説明します。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../../key-vault/secrets/quick-create-portal.md)」をご覧ください。

## <a name="configure-azure-key-vault"></a>Azure Key Vault を構成する

Azure Storage 暗号化でカスタマー マネージド キーを使うには、キー コンテナーに **[論理的な削除]** と **[Do Not Purge]\(消去しない\)** の 2 つのプロパティが構成されている必要があります。 これらのプロパティは既定では有効になっていませんが、新規または既存のキー コンテナーに対して PowerShell または Azure CLI を使用して有効にすることができます。

既存のキー コンテナーでこれらのプロパティを有効にする方法については、次のいずれかの記事の「**論理的な削除を有効にする**」および「**消去保護を有効にする**」を参照してください。

- [PowerShell で Key Vault の論理的な削除を使用する方法](../../key-vault/general/soft-delete-powershell.md)
- [CLI で Key Vault の論理的な削除を使用する方法](../../key-vault/general/soft-delete-cli.md)

Azure Storage の暗号化では、2,048、3,072、および 4,096 のサイズの RSA キーと RSA-HSM キーがサポートされています。 キーの詳細については、「[Azure Key Vault のキー、シークレット、証明書について](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)」の「**Key Vault のキー**」を参照してください。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. ストレージ アカウントに移動します。
1. ストレージ アカウントの **[設定]** ブレードで、 **[暗号化]** をクリックします。 次の図に示すように、 **[カスタマー マネージド キー]** オプションを選択します。

    ![暗号化オプションが表示されているポータルのスクリーンショット](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>キーを指定する

カスタマー マネージド キーを有効にした後は、キーとストレージ アカウントの関連付けを指定することができます。 Azure Storage で、カスタマー マネージド キーを最新のバージョンに自動的に更新する必要があるかどうかや、キーのバージョンを手動で更新するかどうかも指定できます。

### <a name="specify-a-key-from-a-key-vault"></a>キー コンテナーのキーを指定する

キー コンテナーからカスタマー マネージド キーを選択すると、キーのバージョンの自動更新が有効になります。 キーのバージョンを手動で管理するには、代わりにキー URI を指定し、キーのバージョンを含めます。 詳細については、「[URI としてキーを指定する](#specify-a-key-as-a-uri)」を参照してください。

キー コンテナーからキーを指定するには、次の手順のようにします。

1. **[Select from Key Vault]\(キー コンテナーから選択\)** オプションを選択します。
1. **[Select a key vault and key] (キー コンテナーとキーを選択する)** を選択します。
1. 使用するキーを含むキー コンテナーを選択します。
1. キー コンテナーからキーを選択します。

   ![キー コンテナーとキーを選択する方法を示すスクリーンショット](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. 変更を保存します。

### <a name="specify-a-key-as-a-uri"></a>URI としてキーを指定する

Azure Storage では、暗号化に使用されているカスタマー マネージド キーを、最新のキーのバージョンを使用するように自動的に更新できます。 Azure Key Vault でカスタマー マネージド キーが交換されると、Azure Storage では、最新バージョンのキーを使用した暗号化が自動的に開始されます。

> [!NOTE]
> キーを交換するには、Azure Key Vault でキーの新しいバージョンを作成します。 Azure Storage では Azure Key Vault でのキーの交換は処理されないため、キーを手動で交換するか、スケジュールに基づいて交換する関数を作成する必要があります。

キー URI を指定する場合は、URI からキーのバージョンを省略して、最新のキーのバージョンへの自動更新を有効にします。 キー URI にキーのバージョンを含める場合は、自動更新が有効にされないため、自分でキーのバージョンを管理する必要があります。 キーのバージョンの更新の詳細については、「[キーのバージョンを手動で更新する](#manually-update-the-key-version)」を参照してください。

URI としてキーを指定するには、次の手順のようにします。

1. Azure portal でキーの URI を調べるには、キー コンテナーに移動して、 **[キー]** 設定を選択します。 目的のキーを選択し、キーをクリックしてそのバージョンを表示します。 そのバージョンの設定を表示するには、キーのバージョンを選択します。
1. URI を示している **[キー識別子]** フィールドの値をコピーします。

    ![キー コンテナーのキー URI が表示されているスクリーンショット](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. ストレージ アカウントの **[暗号化キー]** の設定で、 **[キー URI を入力]** オプションを選択します。
1. コピーした URI を **[キー URI]** フィールドに貼り付けます。 URI からキーのバージョンを省略して、キーのバージョンの自動更新を有効にします。

   ![キー URI の入力方法が示されているスクリーンショット](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. キー コンテナーを含むサブスクリプションを指定します。
1. 変更を保存します。

キーを指定した後は、Azure portal に、キーのバージョンの自動更新が有効になっているかどうかが示されて、現在暗号化に使用されているキーのバージョンが表示されます。

:::image type="content" source="media/storage-encryption-keys-portal/portal-auto-rotation-enabled.png" alt-text="キーのバージョンの自動更新が有効になっていることを示すスクリーンショット":::

## <a name="manually-update-the-key-version"></a>キーのバージョンを手動で更新する

既定では、Key Vault でカスタマー マネージド キーの新しいバージョンを作成すると、前のセクションで説明したように、Azure Storage で新しいバージョンが自動的に使用され、カスタマー マネージド キーでの暗号化が行われます。 キーのバージョンを自分で管理する場合は、新しいバージョンのキーを作成するたびに、ストレージ アカウントに関連付けられたキーのバージョンを更新する必要があります。

新しいキーのバージョンを使用するようにストレージ アカウントを更新するには、次の手順に従います。

1. お使いのストレージ アカウントに移動し、 **[暗号化]** の設定を表示します。
1. キーの新しいバージョンの URI を入力します。 または、キー コンテナーとキーを再び選択して、バージョンを更新してもかまいません。
1. 変更を保存します。

## <a name="switch-to-a-different-key"></a>異なるキーに切り替える

Azure Storage 暗号化に使用されるキーを変更するには、次の手順を実行します。

1. お使いのストレージ アカウントに移動し、 **[暗号化]** の設定を表示します。
1. 新しいキーの URI を入力します。 キーコンテナーを選択して新しいキーを選択することもできます。
1. 変更を保存します。

## <a name="disable-customer-managed-keys"></a>カスタマー マネージド キーを無効にする

カスタマー マネージド キーを無効にすると、ストレージ アカウントは、Microsoft が管理するキーを使用して再び暗号化されます。 カスタマー マネージド キーを無効にするには、次の手順を実行します。

1. お使いのストレージ アカウントに移動し、 **[暗号化]** の設定を表示します。
1. **[独自のキーを使用する]** 設定の横にあるチェック ボックスをオフにします。

## <a name="next-steps"></a>次のステップ

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
