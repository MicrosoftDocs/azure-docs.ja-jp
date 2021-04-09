---
title: カスタマー マネージド キーを使用した Azure Data Factory の暗号化
description: Bring Your Own Key (BYOK) を使用して Data Factory セキュリティを強化する
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: c6c376e44c6135a800e6f7e281f8ea85b828329a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102443882"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>カスタマー マネージド キーを使用した Azure Data Factory の暗号化

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory では、エンティティの定義や実行中にキャッシュされたデータなど、保存データを暗号化します。 既定では、データは、ランダムに生成されてデータ ファクトリに一意に割り当てられる Microsoft 管理のキーで暗号化されます。 セキュリティをさらに保証するために、Azure Data Factory のカスタマー マネージド キー機能を使用して Bring Your Own Key (BYOK) を有効にできるようになりました。 カスタマー マネージド キーを指定すると、Data Factory により、ファクトリ システム キーと CMK の __両方__ を使用して顧客データが暗号化されます。 どちらかがない場合は、データとファクトリへのアクセスが拒否されます。

カスタマー マネージド キーを格納するには、Azure Key Vault が必要です。 独自のキーを作成してキー コンテナーに格納することも、Azure Key Vault API を使ってキーを生成することもできます。 キー コンテナーと Data Factory は同じ Azure Active Directory (Azure AD) テナントで同じリージョンに存在する必要がありますが、サブスクリプションは異なっていてもかまいません。 Azure Key Vault の詳細については、「 [What is Azure Key Vault? (Azure Key Vault とは)](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>カスタマー マネージド キーの概要

次の図は、Data Factory で Azure Active Directory と Azure Key Vault を使用して、カスタマー マネージド キーを用いて要求を行う方法を示しています。

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Azure Data Factory のカスタマー マネージド キーのしくみを示す図。":::

次の一覧では、図の番号付きの手順について説明します。

1. Azure Key Vault 管理者が、Data Factory に関連付けられているマネージド ID に暗号化キーへのアクセス許可を付与します
1. Data Factory 管理者が、ファクトリでカスタマー マネージド キー機能を有効にします
1. Data Factory により、ファクトリに関連付けられているマネージド ID を使用して、Azure Active Directory 経由で Azure Key Vault へのアクセスが認証されます
1. Data Factory により、Azure Key Vault のカスタマー キーを使用してファクトリ暗号化キーがラップされます
1. 読み取り/書き込み操作の場合、Data Factory により、Azure Key Vault に要求が送信され、暗号化と暗号化の解除の操作を実行できるようにアカウント暗号化キーのラップが解除されます。

データ ファクトリにカスタマー マネージド キーの暗号化を追加する方法は 2 つあります。 1 つはファクトリの作成中に Azure portal を使うもの、もう 1 つはファクトリの作成後に Data Factory の UI を使うものです。

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>前提条件 - Azure Key Vault を構成してキーを生成する

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Azure Key Vault で論理的な削除と消去しないを有効にする

Data Factory でカスタマー マネージド キーを使用するには、Key Vault に __論理的な削除__ と __消去しない__ の 2 つのプロパティが設定されている必要があります。 これらのプロパティは、新規または既存のキー コンテナーで、PowerShell または Azure CLI を使用して有効にすることができます。 既存のキー コンテナーでここに挙げたプロパティを有効にする方法については、「[論理的な削除と消去保護を使用した Azure Key Vault の回復の管理](../key-vault/general/key-vault-recovery.md)」を参照してください

Azure portal を使用して新しい Azure Key Vault を作成している場合は、次のように __論理的な削除__ と __消去しない__ を有効にすることができます。

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="キー コンテナーの作成時に論理的な削除と消去保護を有効にする方法を示したスクリーンショット。":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Azure Key Vault への Data Factory アクセスを許可する

Azure Key Vault と Azure Data Factory が、同じ Azure Active Directory (Azure AD) テナントかつ "_同じリージョン_" に存在することを確認します。 Azure Key Vault のアクセスの制御から、データ ファクトリに "_取得_"、"_キーの折り返しを解除_"、"_キーを折り返す_" のアクセス許可を付与します。 これらのアクセス許可は、Data Factory でカスタマー マネージド キーを有効にするために必要です。

* カスタマー マネージド キーの暗号化を[ファクトリの作成後に Data Factory の UI で](#post-factory-creation-in-data-factory-ui)追加する場合には、データ ファクトリのマネージド サービス ID (MSI) にキー コンテナーに対する上の 3 つのアクセス許可が付与されていることを確認してください
* カスタマー マネージド キーの暗号化を[ファクトリの作成時に Azure portal で](#during-factory-creation-in-azure-portal)追加する場合には、ユーザー割り当てマネージド ID (UA-MI) にキー コンテナーに対する上の 3 つのアクセス許可が付与されていることを確認してください

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="データ ファクトリにキー コンテナーに対するアクセス許可を有効にする方法を示したスクリーンショット。":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>カスタマー マネージド キーを生成または Azure Key Vault にアップロードする

キーは独自のものを作成し、キー コンテナーに保存しておくことができます。 このほか、Azure Key Vault API を使用してキーを生成することもできます。 Data Factory 暗号化では、2048 ビットの RSA キーのみがサポートされています。 詳細については、「[About keys, secrets, and certificates (キー、シークレット、証明書について)](../key-vault/general/about-keys-secrets-certificates.md)」を参照してください。

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="カスタマー マネージド キーを作成する方法を示したスクリーンショット。":::

## <a name="enable-customer-managed-keys"></a>カスタマー マネージド キーを有効にする

### <a name="post-factory-creation-in-data-factory-ui"></a>ファクトリの作成後に Data Factory の UI を使用する

このセクションでは、ファクトリを作成した "_後_" に Data Factory の UI を使ってカスタマー マネージド キーの暗号化を追加する手順を説明します。

> [!NOTE]
> カスタマー マネージド キーは、空のデータ ファクトリでのみ構成することができます。 データ ファクトリには、リンクされたサービス、パイプライン、データ フローなどのリソースを含めることはできません。 ファクトリの作成直後に、カスタマー マネージド キーを有効にすることをお勧めします。

> [!IMPORTANT]
> この方法は、マネージド仮想ネットワークが有効になっているファクトリでは機能しません。 そのようなファクトリを暗号化する場合には、[もう一方の方法](#during-factory-creation-in-azure-portal)の使用を検討してください。

1. データ ファクトリのマネージド サービス ID (MSI) に、キー コンテナーを対象とした "_取得_"、"_キーの折り返しを解除_"、"_キーを折り返す_" のアクセス許可が付与されていることを確認します。

1. Data Factory を確実に空にします。 データ ファクトリには、リンクされたサービス、パイプライン、データ フローなどのリソースを含めることはできません。 ここで、空でないファクトリにカスタマー マネージド キーをデプロイすると、エラーが発生します。

1. Azure portal でキーの URI を検索するには、Azure Key Vault に移動して、[キー] 設定を選択します。 目的のキーを選択し、キーを選択してそのバージョンを表示します。 キーのバージョンを選択して設定を表示します

1. [キー識別子] フィールドの値をコピーします。この値が、URI です。:::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="キー コンテナーからキーの URI を取得しているスクリーンショット。":::

1. Azure Data Factory ポータルを起動し、左側のナビゲーション バーを使用して Data Factory 管理ポータルに移動します

1. __カスタマー マネージド キー__ のアイコンをクリックします。:::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="Data Factory の UI でカスタマー マネージド キーを有効にする方法のスクリーンショット。":::

1. 先ほどコピーしたカスタマー マネージド キーの URI を入力します

1. __[保存]__ をクリックすると、Data Factory でカスタマー マネージド キーの暗号化が有効になります

### <a name="during-factory-creation-in-azure-portal"></a>ファクトリの作成時に Azure portal を使用する

このセクションでは、ファクトリのデプロイ "_時_" に Azure portal 内でカスタマー マネージド キーの暗号化を追加する手順を説明します。

Data Factory では、ファクトリを暗号化するうえでまず、キー コンテナーからカスタマー マネージド キーを取得する必要があります。 ファクトリのデプロイが途中であるため、まだキー コンテナーに対する認証にマネージド サービス ID (MSI) を使用することができません。 そのため、この方法を使用するには、データ ファクトリにユーザー割り当てマネージド ID (UA-MI) を割り当てる必要があります。 Microsoft では、ロールが UA-MI で定義されているものと想定し、キー コンテナーに対する認証を実施します。

ユーザー割り当てマネージド ID の詳細については、「[マネージド ID の種類](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)」と[ユーザー割り当てマネージド ID のロールの割り当て](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)に関するページを参照してください。

1. ユーザー割り当てマネージド ID (UA-MI) に、キー コンテナーを対象とした "_取得_"、"_キーの折り返しを解除_"、"_キーを折り返す_" のアクセス許可が付与されていることを確認します

1. __[詳細設定]__ タブで _[Enable encryption using a customer managed key]\(カスタマー マネージド キーを使用した暗号化を有効にする\)_ のチェック ボックスをオンにします
  :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text="Azure portal 内のデータ ファクトリ作成エクスペリエンスの [詳細設定] タブのスクリーンショット。":::

1. キー コンテナーに保存されているカスタマー マネージド キーの URL を指定します

1. キー コンテナーに対する認証に使用する適切なユーザー割り当てマネージド ID を選択します

1. ファクトリのデプロイを進めます

## <a name="update-key-version"></a>キーのバージョンを更新する

キーの新しいバージョンを作成した場合、その新しいバージョンを使用するには、データ ファクトリを更新します。 手順は、[Data Factory の UI](#post-factory-creation-in-data-factory-ui) に関するセクションの内容とよく似ています。具体的には次のとおりです。

1. Azure Key Vault ポータルを使用して、新しいキー バージョンの URI を検索します

1. __[カスタマー マネージド キー]__ 設定に移動します

1. 新しいキーの URI を置き換えて貼り付けます

1. __[保存]__ をクリックすると、Data Factory により、新しいキーのバージョンで暗号化されます

## <a name="use-a-different-key"></a>別のキーを使用する

Data Factory の暗号化に使用するキーを変更するには、Data Factory の設定を手動で更新する必要があります。 手順は、[Data Factory の UI](#post-factory-creation-in-data-factory-ui) に関するセクションの内容とよく似ています。具体的には次のとおりです。

1. Azure Key Vault ポータルを使用して、新しいキーの URI を検索します

1. __[カスタマー マネージド キー]__ 設定に移動します

1. 新しいキーの URI を置き換えて貼り付けます

1. __[保存]__ をクリックすると、Data Factory により、新しいキーで暗号化されます

## <a name="disable-customer-managed-keys"></a>カスタマー マネージド キーを無効にする

設計上、カスタマー マネージド キー機能を有効にすると、追加のセキュリティ手順を削除することはできません。 ユーザーが、ファクトリとデータを暗号化するキーを提供されることを常にお勧めします。

## <a name="next-steps"></a>次のステップ

より多くのシナリオで Data Factory を使用する方法については、[チュートリアル](tutorial-copy-data-dot-net.md)を参照してください。