---
title: Azure Files をスキャンする方法
description: このハウツーガイドでは、Azure Files をスキャンする方法の詳細について説明します。
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96550901"
---
# <a name="register-and-scan-azure-files"></a>Azure Files の登録とスキャン

## <a name="supported-capabilities"></a>サポートされる機能

Azure Files では、フル スキャンと増分スキャンがサポートされ、システムとお客様の分類に基づいて、メタデータがキャプチャされ、メタデータに分類が適用されます。

## <a name="prerequisites"></a>前提条件

- データ ソースを登録する前に、Azure Purview アカウントを作成します。 Purview アカウントの作成の詳細については、[Azure Purview アカウントの作成](create-catalog-portal.md)に関するクイックスタートを参照してください。
- スキャンを設定してスケジュールするには、データ ソース管理者である必要があります。詳細については、[カタログのアクセス許可](catalog-permissions.md)に関する記事を参照してください。

## <a name="register-an-azure-files-storage-account"></a>Azure Files ストレージ アカウントを登録する

新しい Azure Files アカウントをデータ カタログに登録するには、次の手順を実行します。

1. Purview Data Catalog に移動します。
1. 左側のナビゲーションで **[管理センター]** を選択します。
1. **[ソースとスキャン]** の **[データ ソース]** を選択し ます。
1. **[+新規]** を選択します。
1. **[ソースの登録]** で、 **[Azure Files]** を選択します。 **[続行]** をクリックします。

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="新しいデータ ソースの登録" border="true":::

**[ソースの登録 (Azure Files)]** 画面で、次の手順を実行します。

1. データ ソースがカタログに表示される際の **[名前]** を入力します。
1. 目的のストレージ アカウントを指し示す方法を選択します。
   1. **[Azure サブスクリプションから]** を選択して、 **[Azure サブスクリプション]** ドロップ ダウン ボックスから適切なサブスクリプションを選択し、 **[ストレージ アカウント名]** ドロップ ダウン ボックスから適切なストレージ アカウントを選択します。
   1. または、 **[手動で入力]** を選択して、サービス エンドポイント (URL) を入力することもできます。
1. **[完了]** を選択して、データ ソースを登録します。

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="ソースの登録のオプション" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>スキャンの認証を設定する

アカウント キーを使用して Azure Files ストレージの認証を設定するには、次の手順を実行します。

1. 認証方法として **[アカウント キー]** を選択します。
2. **[Azure サブスクリプションから]** オプションを選択します。
3. Azure Files アカウントが存在する Azure サブスクリプションを選択します。
4. 一覧からストレージ アカウント名を選択します。
5. **[完了]** をクリックします。

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)