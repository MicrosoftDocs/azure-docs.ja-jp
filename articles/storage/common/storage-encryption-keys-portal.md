---
title: Azure portal から Azure Storage 暗号化用にカスタマー マネージド キーを構成する
description: Azure portal を使って Azure Storage 暗号化用のカスタマー マネージド キーを構成する方法について説明します。 カスタマー マネージド キーを使用すると、アクセス制御の作成、ローテーション、無効化、および取り消しを行うことができます。
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 892cbe66222626a6847ad7a5b6c990d23991c182
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002267"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Azure portal から Azure Storage 暗号化用にカスタマー マネージド キーを構成する

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

この記事では、[Azure portal](https://portal.azure.com/) を使って、カスタマー マネージド キーでキー コンテナーを構成する方法について説明します。 Azure portal を使ってキー コンテナーを作成する方法を学習するには、「[クイック スタート: Azure portal を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../../key-vault/quick-create-portal.md)」をご覧ください。 


> [!IMPORTANT]
> Azure Storage 暗号化でカスタマー マネージド キーを使うには、キー コンテナーで 2 つの必須プロパティ **[論理的削除]** と **[Do Not Purge]\(削除しない\)** が構成されている必要があります。 Azure portal で新しいキー コンテナーを作成すると、これらのプロパティは既定で有効になります。 ただし、既存のキー コンテナーでこれらのプロパティを有効にする必要がある場合は、PowerShell または Azure CLI を使う必要があります。
> RSA キーで 2048 のキー サイズのみがサポートされています。

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

Azure portal でカスタマー マネージド キーを有効にするには、次の手順のようにします。

1. ストレージ アカウントに移動します。
1. ストレージ アカウントの **[設定]** ブレードで、 **[暗号化]** をクリックします。 以下の図に示すように、 **[Use your own key]\(独自のキーの使用\)** オプションを選択します。

    ![暗号化オプションが表示されているポータルのスクリーンショット](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>キーを指定する

カスタマー マネージド キーを有効にした後は、キーとストレージ アカウントの関連付けを指定することができます。

### <a name="specify-a-key-as-a-uri"></a>URI としてキーを指定する

URI としてキーを指定するには、次の手順のようにします。

1. Azure portal でキーの URI を調べるには、キー コンテナーに移動して、 **[キー]** 設定を選択します。 目的のキーを選択し、キーをクリックしてその設定を表示します。 URI を示している **[キー識別子]** フィールドの値をコピーします。

    ![キー コンテナーのキー URI が表示されているスクリーンショット](media/storage-encryption-keys-portal/key-uri-portal.png)

1. ストレージ アカウントの **[暗号化]** の設定で、 **[キー URI を入力]** オプションを選択します。
1. **[キー URI]** フィールドで、URI を指定します。

   ![キー URI の入力方法が示されているスクリーンショット](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>キー コンテナーのキーを指定する

キー コンテナーからキーを指定するには、まず、キーが含まれるキー コンテナーがあることを確認します。 キー コンテナーからキーを指定するには、次の手順のようにします。

1. **[Select from Key Vault]\(キー コンテナーから選択\)** オプションを選択します。
2. 使用するキーを含むキー コンテナーを選択します。
3. キー コンテナーからキーを選択します。

   ![カスタマー マネージド キーのオプションが示されているスクリーンショット](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>キーのバージョンを更新する

キーの新しいバージョンを作成した場合、その新しいバージョンを使用するには、ストレージ アカウントを更新する必要があります。 次の手順に従います。

1. お使いのストレージ アカウントに移動し、 **[暗号化]** の設定を表示します。
1. 新しいキーのバージョンの URI を指定します。 または、キー コンテナーとキーを再び選択して、バージョンを更新してもかまいません。

## <a name="next-steps"></a>次の手順

- [保存データに対する Azure Storage 暗号化](storage-service-encryption.md)
- [Azure Key Vault とは](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
