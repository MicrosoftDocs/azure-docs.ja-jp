---
title: Azure クイックスタート - Azure portal を使用して Key Vault との間で証明書の設定と取得を行う | Microsoft Docs
description: Azure portal を使用して Azure Key Vault との間で証明書の設定と取得を行う方法を紹介したクイックスタート
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2020
ms.author: mbaldwin
ms.openlocfilehash: e55c0832638105ad681f74cbeb6429a6704b7fb2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97935140"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Azure Key Vault から証明書の設定と取得を行う

Azure Key Vault は、シークレットのセキュリティで保護されたストアを提供するクラウド サービスです。 キー、パスワード、証明書、およびその他のシークレットを安全に保管することができます。 Azure Key Vault は、Azure Portal を使用して作成および管理できます。 このクイックスタートでは、キー コンテナーを作成し、それを使用して証明書を格納します。 Key Vault の詳細については、[概要](../general/overview.md)に関する記事をご覧ください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ( https://portal.azure.com ) にサインインします。

## <a name="create-a-vault"></a>コンテナーの作成

1. Azure portal メニューまたは **[ホーム]** ページで、 **[リソースの作成]** を選択します。
2. 検索ボックスに「**Key Vault**」と入力します。
3. 結果の一覧の **[Key Vault]** を選択します。
4. Key Vault のセクションで、 **[作成]** を選択します。
5. **[キー コンテナーの作成]** セクションで、次の情報を入力します。
    - **Name**:一意の名前が必要です。 このクイックスタートでは、**Example-Vault** を使用します。 
    - **サブスクリプション**:サブスクリプションを選択します。
    - **[リソース グループ]** で、 **[新規作成]** を選択し、リソース グループ名を入力します。
    - **[場所]** プルダウン メニューで場所を選択します。
    - 他のオプションは既定値のままにしておきます。
6. 上記の情報を指定したら、 **[作成]** を選択します。

次の 2 つのプロパティをメモしておきます。

* **Vault Name**:この例では、これは **Example-Vault** です。 この名前は他の手順で使用します。
* **Vault URI (コンテナー URI)** :この例では、これは `https://example-vault.vault.azure.net/` です。 その REST API から資格情報コンテナーを使用するアプリケーションは、この URI を使用する必要があります。

この時点で、使用している Azure アカウントが、この新しいコンテナーで操作を実行することを許可されている唯一のアカウントになります。

![Key Vault の作成が完了した後の出力](../media/certificates/quick-create-portal/vault-properties.png)

## <a name="add-a-certificate-to-key-vault"></a>証明書を Key Vault に追加する

証明書をコンテナーに追加するには、いくつかの追加の手順を実行する必要があります。 この例では、アプリケーションで使用できる自己署名証明書を追加します。 証明書の名前は **ExampleCertificate** です。

1. Key Vault のプロパティ ページで、 **[証明書]** を選択します。
2. **[Generate/Import]\(生成/インポート\)** をクリックします。
3. **[証明書の作成]** 画面で、次の値を選択します。
    - **[証明書の作成方法]** : [生成]。
    - **[証明書名]** : ExampleCertificate。
    - **Subject**:CN=ExampleDomain
    - 他の値は既定値のままにしておきます。 **Create** をクリックしてください。

証明書が正常に作成されたことを示すメッセージが表示されたら、一覧でそのシークレットをクリックできます。 すると、いくつかのプロパティを確認できます。 現在のバージョンをクリックすると、前の手順で指定した値が表示されます。

![証明書のプロパティ](../media/certificates/quick-create-portal/current-version-hidden.png)

## <a name="export-certificate-from-key-vault"></a>Key Vault から証明書をエクスポートする
[CER 形式でダウンロード] または [PFX/PEM 形式でダウンロード] ボタンをクリックすると、証明書をダウンロードできます。 

![証明書のダウンロード](../media/certificates/quick-create-portal/current-version-shown.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Key Vault に関する他のクイック スタートとチュートリアルは、このクイック スタートに基づいています。 後続のクイック スタートおよびチュートリアルを引き続き実行する場合は、これらのリソースをそのまま残しておくことをお勧めします。
不要になったら、リソース グループを削除します。これにより、Key Vault と関連リソースが削除されます。 ポータルを使用してリソース グループを削除するには:

1. ポータル上部にある検索ボックスにリソース グループの名前を入力します。 このクイック スタートで使用されているリソース グループが検索結果に表示されたら、それを選択します。
2. **[リソース グループの削除]** を選択します。
3. **[リソース グループ名を入力してください:]** ボックスにリソース グループの名前を入力し、 **[削除]** を選択します。


## <a name="next-steps"></a>次のステップ

このクイックスタートでは、キー コンテナーを作成して証明書を格納しました。 Key Vault およびアプリケーションとの統合方法の詳細については、引き続き以下の記事を参照してください。

- [Azure Key Vault の概要](../general/overview.md)を確認する
- 「[Azure Key Vault 開発者ガイド](../general/developers-guide.md)」を参照する
- [Key Vault のセキュリティの概要](../general/security-overview.md)を確認する
