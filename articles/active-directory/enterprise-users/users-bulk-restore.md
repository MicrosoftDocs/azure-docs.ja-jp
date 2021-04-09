---
title: Azure Active Directory ポータルで削除済みユーザーを一括復元する | Microsoft Docs
description: Azure Active Directory の Azure AD 管理センターで削除済みユーザーを一括復元する
services: active-directory
author: curtand
ms.author: curtand
manager: mtillman
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: be3947e3de18f8ccaf47382c4035e187521ac710
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571506"
---
# <a name="bulk-restore-deleted-users-in-azure-active-directory"></a>Azure Active Directory での削除済みユーザーの一括復元

Azure Active Directory (Azure AD) では、ユーザーの一括復元操作がサポートされており、ユーザー、グループ、およびグループ メンバーのリストのダウンロードがサポートされています。

## <a name="understand-the-csv-template"></a>CSV テンプレートについて

CSV テンプレートをダウンロードして入力し、Azure AD ユーザーを正常に一括復元できるようにします。 ダウンロードする CSV テンプレートは、次の例のようになります。

![アップロード用のスプレッドシートと、各行および列の目的と値を説明する吹き出し](./media/users-bulk-restore/understand-template.png)

### <a name="csv-template-structure"></a>CSV テンプレートの構造

ダウンロードした CSV テンプレート内の行は次のとおりです。

- **バージョン番号**: アップロード CSV の先頭行にバージョン番号を含める必要があります。
- **列見出し**:列見出しの形式は、&lt;*項目名*&gt; [PropertyName] &lt;*Required または空白*&gt; です。 たとえば、「 `Object ID [objectId] Required` 」のように入力します。 テンプレートの古いバージョンの中には、微妙に異なるものもあります。
- **例の行**:このテンプレートには、各列に使用できる値のサンプル行が含まれています。 サンプル行を削除し、独自のエントリに置き換える必要があります。

### <a name="additional-guidance"></a>その他のガイダンス

- アップロード テンプレートの最初の 2 行を削除または変更することはできません。アップロードを処理することができなくなります。
- 必須の列が最初に示されています。
- テンプレートに新しい列を追加することはお勧めしません。 列を追加しても無視され、処理されません。
- 出来る限り、常に最新の CSV テンプレートをダウンロードすることをお勧めします。

## <a name="to-bulk-restore-users"></a>ユーザーを一括復元するには

1. Azure AD 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  >  **[削除済み]** の順に選択します。
1. **[削除済みのユーザー]** ページで、 **[一括復元]** を選択して、復元するユーザーのプロパティの有効な CSV ファイルをアップロードします。

    ![[削除済みのユーザー] ページで一括復元コマンドを選択する](./media/users-bulk-restore/bulk-restore.png)

1. この CSV テンプレートを開いて、復元するユーザーごとに 1 行を追加します。 唯一の必須値は **ObjectID** です。 そのうえでファイルを保存します。

    :::image type="content" source="./media/users-bulk-restore/upload-button.png" alt-text="追加するユーザーをリストするローカル CSV ファイルを選択する":::

1. **[ユーザーの一括復元]** ページの **[CSV ファイルをアップロード]** で、そのファイルを参照します。 ファイルを選択して **[送信]** をクリックすると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証されると、"**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルの検証に合格したら、 **[送信]** を選択して、ユーザーを復元する Azure 一括操作を開始します。
1. 復元操作が完了すると、一括操作が成功したという通知が表示されます。

エラーがある場合は、 **[一括操作の結果]** ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

## <a name="check-status"></a>状態の確認

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

[![[一括操作の結果] ページで状態を確認します。](./media/users-bulk-restore/bulk-center.png)](./media/users-bulk-restore/bulk-center.png#lightbox)

次に、復元したユーザーが Azure AD 組織に存在するかどうかを、Azure portal または PowerShell を使用して確認できます。

## <a name="view-restored-users-in-the-azure-portal"></a>Azure portal で復元されたユーザーを表示する

1. 組織のユーザー管理者アカウントで、[Azure AD 管理センターにサインイン](https://aad.portal.azure.com)します。
1. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[表示]** で **[すべてのユーザー]** を選択し、復元したユーザーがリストされていることを確認します。

### <a name="view-users-with-powershell"></a>PowerShell でユーザーを表示する

次のコマンドを実行します。

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

復元したユーザーがリストされていることがわかります。

## <a name="next-steps"></a>次のステップ

- [ユーザーを一括インポートする](users-bulk-add.md)
- [ユーザーの一括削除](users-bulk-delete.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
