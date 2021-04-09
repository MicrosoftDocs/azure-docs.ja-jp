---
title: クイックスタート - Azure portal を使用してキー コンテナーを作成する
description: Azure portal を使用して Azure キー コンテナーを作成する方法を示すクイックスタート
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/08/2020
ms.author: mbaldwin
ms.openlocfilehash: 729b6ccf1092679a71aa35f49be7d3199d4f4918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936279"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-portal"></a>クイック スタート:Azure portal を使用してキー コンテナーを作成する

Azure Key Vault は、[キー](../keys/index.yml)、[シークレット](../secrets/index.yml)、および[証明書](../certificates/index.yml)のセキュリティで保護されたストアを提供するクラウド サービスです。 Key Vault の詳細については、「[Azure Key Vault について](overview.md)」を参照してください。キー コンテナーに格納できる内容の詳細については、「[キー、シークレット、証明書について](about-keys-secrets-certificates.md)」を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

このクイックスタートでは、[Azure portal](https://portal.azure.com) を使用してキー コンテナーを作成します。 

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-vault"></a>コンテナーの作成

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。
2. 検索ボックスに「**Key Vault**」と入力します。
3. 結果の一覧の **[Key Vault]** を選択します。
4. Key Vault のセクションで、 **[作成]** を選択します。
5. **[キー コンテナーの作成]** セクションで、次の情報を入力します。
    - **Name**:一意の名前が必要です。 このクイックスタートでは、**Contoso-vault2** を使用します。 
    - **サブスクリプション**:サブスクリプションを選択します。
    - **[リソース グループ]** で、 **[新規作成]** を選択し、リソース グループ名を入力します。
    - **[場所]** プルダウン メニューで場所を選択します。
    - 他のオプションは既定値のままにしておきます。
6. 上記の情報を指定したら、 **[作成]** を選択します。

次の 2 つのプロパティをメモしておきます。

* **Vault Name**:この例では、これは **Contoso-Vault2** です。 この名前は他の手順で使用します。
* **Vault URI (コンテナー URI)** :この例では、これは https://contoso-vault2.vault.azure.net/ です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

この時点で、使用している Azure アカウントが、この新しいコンテナーで操作を実行することを許可されている唯一のアカウントになります。

![Key Vault の作成が完了した後の出力](../media/quick-create-portal/vault-properties.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Key Vault に関する他のクイック スタートとチュートリアルは、このクイック スタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。
不要になったら、リソース グループを削除します。これにより、Key Vault と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、 **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure portal を使用してキー コンテナーを作成しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](overview.md)を確認する
- 「[Azure Key Vault のセキュリティの概要](security-overview.md)」を確認する
- 「[Azure Key Vault 開発者ガイド](developers-guide.md)」を参照する
