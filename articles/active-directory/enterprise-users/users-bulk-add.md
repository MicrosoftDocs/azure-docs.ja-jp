---
title: Azure Active Directory ポータルでユーザーを一括作成する | Microsoft Docs
description: Azure Active Directory の Azure AD 管理センターでユーザーを一括追加します
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
ms.openlocfilehash: c653f3e8583ef3aadff26cb2b7a3266555d313a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547816"
---
# <a name="bulk-create-users-in-azure-active-directory"></a>Azure Active Directory でのユーザーの一括作成

Azure Active Directory (Azure AD) では、ユーザーの一括作成および削除操作がサポートされており、ユーザーのリストのダウンロードがサポートされています。 Azure AD ポータルからダウンロードできるコンマ区切り値 (CSV) テンプレートを入力するだけです。

## <a name="required-permissions"></a>必要なアクセス許可

管理ポータルでユーザーを一括作成するには、グローバル管理者またはユーザー管理者としてサインインしている必要があります。

## <a name="understand-the-csv-template"></a>CSV テンプレートについて

一括アップロード CSV テンプレートをダウンロードして入力すると、Azure AD ユーザーを正常に一括作成できます。 ダウンロードする CSV テンプレートは、次の例のようになります。

![アップロード用のスプレッドシートと、各行および列の目的と値を説明する吹き出し](./media/users-bulk-add/create-template-example.png)

> [!WARNING]
> CSV テンプレートを使用してエントリを 1 つだけ追加する場合は、行 3 を保持し、新しいエントリを行 4 に追加する必要があります。

### <a name="csv-template-structure"></a>CSV テンプレートの構造

ダウンロードした CSV テンプレート内の行は次のとおりです。

- **バージョン番号**: アップロード CSV の先頭行にバージョン番号を含める必要があります。
- **列見出し**:列見出しの形式は、&lt;*項目名*&gt; [PropertyName] &lt;*Required または空白*&gt; です。 たとえば、「 `Name [displayName] Required` 」のように入力します。 テンプレートの古いバージョンの中には、微妙に異なるものもあります。
- **例の行**:このテンプレートには、各列に使用できる値のサンプル行が含まれています。 サンプル行を削除し、独自のエントリに置き換える必要があります。

### <a name="additional-guidance"></a>その他のガイダンス

- アップロード テンプレートの最初の 2 行を削除または変更することはできません。アップロードを処理することができなくなります。
- 必須の列が最初に示されています。
- テンプレートに新しい列を追加することはお勧めしません。 列を追加しても無視され、処理されません。
- できる限り、常に最新バージョンの CSV テンプレートをダウンロードすることをお勧めします。
- フィールドの前後に意図しない空白がないか確認してください。 **ユーザー プリンシパル名** の場合、そのような空白があると、インポートに失敗します。

## <a name="to-create-users-in-bulk"></a>ユーザーを一括で作成する手順

1. 組織のユーザー管理者アカウントで、[ご自身の Azure AD 組織にサインイン](https://aad.portal.azure.com)します。
1. Azure AD で、 **[ユーザー]**  > [一括作成] **の順に選択します。**
1. **[ユーザーの一括作成]** ページで **[ダウンロード]** を選択し、ユーザー プロパティの有効な CSV (コンマ区切り値) ファイルを取得し、作成するユーザーを追加します。

   ![追加するユーザーをリストするローカル CSV ファイルを選択する](./media/users-bulk-add/upload-button.png)

1. CSV ファイルを開いて、作成するユーザーごとに 1 行を追加します。 必須値は、 **[名前]** 、 **[ユーザー プリンシパル名]** 、 **[初期パスワード]** 、および **[サインインのブロック (はい/いいえ)]** のみです。 そのうえでファイルを保存します。

   [![CSV ファイルには、作成するユーザーの名前と ID が含まれています。](./media/users-bulk-add/add-csv-file.png)](./media/users-bulk-add/add-csv-file.png#lightbox)

1. **[ユーザーの一括作成]** ページの [CSV ファイルをアップロード] で、そのファイルを参照します。 ファイルを選択して **[送信]** をクリックすると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証された後、"**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルが検証に合格したら、 **[送信]** を選択して、新しいユーザーをインポートする Azure の一括操作を開始します。
1. インポート操作が完了すると、一括操作ジョブの状況に関する通知が表示されます。

エラーがある場合は、 **[一括操作の結果]** ページで結果ファイルをダウンロードして表示できます。 このファイルには、各エラーの理由が含まれています。 ファイルの送信は、指定されたテンプレートと一致し、正確な列名が含まれている必要があります。

## <a name="check-status"></a>状態の確認

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

   [![[一括操作の結果] ページで作成の状態を確認する](./media/users-bulk-add/bulk-center.png)](./media/users-bulk-add/bulk-center.png#lightbox)

次に、作成したユーザーが Azure AD 組織に存在するかどうかを、Azure portal または PowerShell を使用して確認します。

## <a name="verify-users-in-the-azure-portal"></a>Azure portal でユーザーを確認する

1. 組織のユーザー管理者アカウントで、[Azure AD 管理センターにサインイン](https://aad.portal.azure.com)します。
1. ナビゲーション ペインで、 **[Azure Active Directory]** を選択します。
1. **[管理]** にある **[ユーザー]** を選択します。
1. **[表示]** で **[すべてのユーザー]** を選択し、作成したユーザーが一覧に表示されていることを確認します。

### <a name="verify-users-with-powershell"></a>PowerShell でユーザーを確認する

次のコマンドを実行します。

``` PowerShell
Get-AzureADUser -Filter "UserType eq 'Member'"
```

作成したユーザーがリストされているのを確認できます。

## <a name="bulk-import-service-limits"></a>一括インポート サービスの制限

ユーザー作成の一括操作は、それぞれ最大 1 時間かかる場合があります。 これにより、最小で 5 万ユーザーを一括作成できます。

## <a name="next-steps"></a>次のステップ

- [ユーザーの一括削除](users-bulk-delete.md)
- [ユーザーの一覧のダウンロード](users-bulk-download.md)
- [ユーザーの一括復元](users-bulk-restore.md)
