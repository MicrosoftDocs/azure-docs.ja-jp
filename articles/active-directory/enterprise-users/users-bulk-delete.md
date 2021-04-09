---
title: Azure Active Directory ポータルでユーザーを一括削除する | Microsoft Docs
description: Azure Active Directory の Azure 管理センターでユーザーを一括削除する
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 12/02/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: b57d675003c1ebeb29927b86338f95cf7dd68090
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574465"
---
# <a name="bulk-delete-users-in-azure-active-directory"></a>Azure Active Directory でユーザーを一括削除する

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してユーザーを一括削除することで、グループの多数のメンバーを削除できます。

## <a name="understand-the-csv-template"></a>CSV テンプレートについて

CSV テンプレートをダウンロードして入力すると、Azure AD ユーザーを正常に一括削除できます。 ダウンロードする CSV テンプレートは、次の例のようになります。

![アップロード用のスプレッドシートと、各行および列の目的と値を説明する吹き出し](./media/users-bulk-delete/understand-template.png)

### <a name="csv-template-structure"></a>CSV テンプレートの構造

ダウンロードした CSV テンプレート内の行は次のとおりです。

- **バージョン番号**: アップロード CSV の先頭行にバージョン番号を含める必要があります。
- **列見出し**:列見出しの形式は、&lt;*項目名*&gt; [PropertyName] &lt;*Required または空白*&gt; です。 たとえば、「 `User name [userPrincipalName] Required` 」のように入力します。 テンプレートの古いバージョンの中には、微妙に異なるものもあります。
- **例の行**:このテンプレートには、各列に使用できる値のサンプル行が含まれています。 サンプル行を削除し、独自のエントリに置き換える必要があります。

### <a name="additional-guidance"></a>その他のガイダンス

- アップロード テンプレートの最初の 2 行を削除または変更することはできません。アップロードを処理することができなくなります。
- 必須の列が最初に示されています。
- テンプレートに新しい列を追加することはお勧めしません。 列を追加しても無視され、処理されません。
- できる限り、常に最新バージョンの CSV テンプレートをダウンロードすることをお勧めします。

## <a name="to-bulk-delete-users"></a>ユーザーを一括削除するには

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  >  **[一括削除]** の順に選択します。
1. **[ユーザーの一括削除]** ページで **[ダウンロード]** を選択して、ユーザー プロパティの有効な CSV ファイルを受信します。

   ![削除するユーザーをリストするローカル CSV ファイルを選択する](./media/users-bulk-delete/bulk-delete.png)

1. この CSV ファイルを開いて、削除するユーザーごとに 1 行を追加します。 唯一の必須値は、**ユーザー プリンシパル名** です。 そのうえでファイルを保存します。

   ![CSV ファイルには、削除するユーザーの名前と ID が含まれています。](./media/users-bulk-delete/delete-csv-file.png)

1. **[ユーザーの一括削除]** ページの **[CSV ファイルをアップロード]** で、そのファイルを参照します。 ファイルを選択して [送信] をクリックすると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証されると、"**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルの検証に合格したら、 **[送信]** を選択して、ユーザーを削除する Azure 一括操作を開始します。
1. 削除操作が完了すると、一括操作が成功したという通知が表示されます。

エラーがある場合は、 **[一括操作の結果]** ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。

## <a name="check-status"></a>状態の確認

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

   [![[一括操作の結果] ページで削除の状態を確認します。](./media/users-bulk-delete/bulk-center.png)](./media/users-bulk-delete/bulk-center.png#lightbox)

次に、削除したユーザーが Azure AD 組織に存在するかどうかを、Azure portal または PowerShell を使用して確認できます。

## <a name="verify-deleted-users-in-the-azure-portal"></a>Azure portal で削除済みのユーザーを確認する

1. 組織のユーザー管理者アカウントで、Azure portal にサインインします。
1. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[表示]** で **[すべてのユーザー]** のみを選択し、削除したユーザーがリストされなくなったことを確認します。

### <a name="verify-deleted-users-with-powershell"></a>PowerShell で削除済みユーザーを確認する

次のコマンドを実行します。

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

削除したユーザーがリストされなくなったことを確認します。

## <a name="next-steps"></a>次のステップ

- [ユーザーの一括追加](users-bulk-add.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
- [ユーザーの一括復元](users-bulk-restore.md)
