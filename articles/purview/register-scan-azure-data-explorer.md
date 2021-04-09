---
title: Azure Data Explorer をスキャンする方法
description: このハウツーガイドでは、Azure Data Explorer をスキャンする方法の詳細について説明します。
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 7adc7f568fb82692f2c96f610575076e397bd99c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98896114"
---
# <a name="register-and-scan-azure-data-explorer"></a>Azure Data Explorer の登録とスキャン

この記事では、Azure Purview に Azure Data Explorer アカウントを登録し、スキャンを設定する方法について説明します。

## <a name="supported-capabilities"></a>サポートされる機能

Azure Data Explorer では、フル スキャンと増分スキャンがサポートされ、メタデータとスキーマがキャプチャされます。 また、スキャンでは、システムおよびカスタムの分類規則に基づいてデータが自動的に分類されます。

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[クイック スタート: Azure Purview アカウントの作成](create-catalog-portal.md)に関する記事を参照してください。
- Azure Purview データ ソース管理者である必要があります

## <a name="setting-up-authentication-for-a-scan"></a>スキャンでの認証の設定

Azure Data Explorer の認証を設定する方法は 1 つだけです。

- サービス プリンシパル

### <a name="service-principal"></a>サービス プリンシパル

スキャンにサービス プリンシパルの認証を使用するには、既存のものを使用するか、新しく作成することができます。 

> [!Note]
> 新しいサービス プリンシパルを作成する必要がある場合は、次の手順に従います。
> 1. [Azure Portal](https://portal.azure.com) に移動します。
> 1. 左側のメニューで、 **[Azure Active Directory]** を選択します。
> 1. **[アプリの登録]** を選択します。
> 1. **[+ 新しいアプリケーションの登録]** を選択します。
> 1. **アプリケーション** の名前 (サービス プリンシパル名) を入力します。
> 1. **[この組織のディレクトリ内のアカウントのみ]** を選択します。
> 1. [リダイレクト URI] には **[Web]** を選択し、必要な URL を入力します。これは、実際の URL でなくてもよく、機能しなくてもかまいません。
> 1. 次に、 **[登録]** を選択します。

サービス プリンシパルのアプリケーション ID とシークレットを取得する必要があります。

1. [Azure portal](https://portal.azure.com) でサービス プリンシパルに移動します
1. **[概要]** から **[アプリケーション (クライアント) ID]** 、 **[証明書とシークレット]** から **[クライアント シークレット]** の値をコピーします。
1. お使いのキー コンテナーに移動する
1. **[設定] > [シークレット]** の順に選択します。
1. **[生成/インポート]** を選択し、サービス プリンシパルの **クライアント シークレット** として任意の **名前** と **値** を入力します
1. **[作成]** を選択して完了します。
1. キー コンテナーが Purview にまだ接続されていない場合は、[新しいキー コンテナーの接続を作成](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)する必要があります。
1. 最後に、サービス プリンシパルを使用して[新しい資格情報を作成](manage-credentials.md#create-a-new-credential)し、スキャンを設定します

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>サービス プリンシパルに Azure Data Explorer インスタンスへのアクセス権を付与する

1. Azure Portal に移動します。 次に、Azure Data Explorer インスタンスに移動します。

1. 次のスクリーンショットに示すように、 **[アクセス許可]** タブで、サービス プリンシパルを **[AllDatabasesViewer]** ロールに追加します。

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="[アクセス許可] でサービス プリンシパルを追加しているところのスクリーンショット" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Azure Data Explorer アカウントを登録する

新しい Azure Data Explorer (Kusto) アカウントをデータ カタログに登録するには、次の手順を実行します。

1. Purview アカウントに移動します
1. 左側のナビゲーションで **[ソース]** を選択します。
1. **[登録]** を選択します
1. **[ソースの登録]** で、 **[Azure Data Explorer]** を選択します
1. **[続行]** を選択します

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="新しいデータ ソースの登録" border="true":::

**[ソースの登録 (Azure Data Explorer (Kusto))]** 画面で、次の手順を実行します。

1. データ ソースがカタログに表示される際の **[名前]** を入力します。
1. 目的のストレージ アカウントを指し示す方法を選択します。
   1. **[Azure サブスクリプションから]** を選択して、 **[Azure サブスクリプション]** ドロップ ダウン ボックスから適切なサブスクリプションを選択し、 **[クラスター]** ドロップ ダウン ボックスから適切なクラスターを選択します。
   1. または、 **[手動で入力]** を選択して、サービス エンドポイント (URL) を入力することもできます。
1. **[完了]** を選択して、データ ソースを登録します。

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans-azure-data-explorer.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
