---
title: グループのメンバーを追加または作成するための一括アップロード - Azure Active Directory |Microsoft Docs
description: Azure Active Directory 管理センターでグループ メンバーを一括して追加します。
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
ms.openlocfilehash: 22d39a2ee66f2c63612ad2cb3cf9ae61f1660de3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96547748"
---
# <a name="bulk-add-group-members-in-azure-active-directory"></a>Azure Active Directory でのグループ メンバーの一括追加

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してグループ メンバーを一括インポートすることで、多数のメンバーをグループに追加できます。

## <a name="understand-the-csv-template"></a>CSV テンプレートについて

一括アップロード CSV テンプレートをダウンロードして入力し、Azure AD グループ メンバーを正常に一括追加します。 CSV テンプレートは、次の例のようになります。

![アップロード用のスプレッドシートと、各行および列の目的と値を説明する吹き出し](./media/groups-bulk-import-members/template-with-callouts.png)

### <a name="csv-template-structure"></a>CSV テンプレートの構造

ダウンロードした CSV テンプレート内の行は次のとおりです。

- **バージョン番号**: アップロード CSV の先頭行にバージョン番号を含める必要があります。
- **列見出し**:列見出しの形式は、&lt;*項目名*&gt; [PropertyName] &lt;*Required または空白*&gt; です。 たとえば、「 `Member object ID or user principal name [memberObjectIdOrUpn] Required` 」のように入力します。 以前のバージョンのテンプレートの中には、内容が少し異なるものがあります。 グループ メンバーシップを変更する場合は、使用する識別子として、メンバー オブジェクト ID またはユーザー プリンシパル名のいずれかを選択できます。
- **例の行**:このテンプレートには、各列に使用できる値のサンプル行が含まれています。 サンプル行を削除し、独自のエントリに置き換える必要があります。

### <a name="additional-guidance"></a>その他のガイダンス

- アップロード テンプレートの最初の 2 行を削除または変更することはできません。アップロードを処理することができなくなります。
- 必須の列が最初に示されています。
- テンプレートに新しい列を追加することはお勧めしません。 列を追加しても無視され、処理されません。
- できるだけ頻繁に最新バーションの CSV テンプレートをダウンロードすることをお勧めします。
- ファイルを正常にアップロードするには、少なくとも 2 人のユーザーの UPN またはオブジェクト ID を追加します。

## <a name="to-bulk-import-group-members"></a>グループ メンバーを一括インポートするには

1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。 グループの所有者も、所有しているグループのメンバーを一括インポートできます。
1. Azure AD で、 **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. メンバーを追加するグループを開き、 **[メンバー]** を選択します。
1. **[メンバー]** ページで、 **[メンバーのインポート]** を選択します。
1. **[グループ メンバーの一括インポート]** ページで、 **[ダウンロード]** を選択して、必要なグループ メンバーのプロパティを含む CSV ファイル テンプレートを取得します。

    ![[メンバーのインポート] コマンドは、グループの [プロファイル] ページにあります](./media/groups-bulk-import-members/import-panel.png)

1. CSV ファイルを開き、グループにインポートする各グループ メンバーの行を追加します (必要な値は、**メンバー オブジェクト ID** または **ユーザー プリンシパル名** です)。 そのうえでファイルを保存します。

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="CSV ファイルには、インポートするメンバーの名前と ID が含まれています":::

1. **[グループ メンバーの一括インポート]** ページの **[CSV ファイルをアップロード]** で、そのファイルを参照します。 ファイルを選択すると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証されると、一括インポート ページに "**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルが検証に合格したら、 **[送信]** を選択して、グループ メンバーをグループにインポートする Azure の一括操作を開始します。
1. インポート操作が完了すると、一括操作が成功したという通知が表示されます。

## <a name="check-import-status"></a>インポートの状態を確認する

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

[![[一括操作の結果] ページで状態を確認します。](./media/groups-bulk-import-members/bulk-center.png)](./media/groups-bulk-import-members/bulk-center.png#lightbox)

一括操作に含まれる各行の項目の詳細については、 **[成功数]** 、 **[失敗数]** 、 **[要求数合計]** の各列の値を選択してください。 エラーが発生した場合、その理由が表示されます。

## <a name="bulk-import-service-limits"></a>一括インポート サービスの制限

グループ メンバーの一覧をインポートする各一括アクティビティは、最大 1 時間実行できます。 これにより、最大 4 万人のメンバーの一覧をインポートできます。

## <a name="next-steps"></a>次のステップ

- [グループ メンバーを一括削除する](groups-bulk-remove-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
- [全グループの一覧をダウンロードする](groups-bulk-download.md)
