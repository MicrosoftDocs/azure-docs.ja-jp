---
title: CSV ファイルをアップロードしてグループ メンバーを一括削除する - Azure Active Directory | Microsoft Docs
description: Azure 管理センターで、一括操作でグループメンバーを削除します。
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 11/15/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83b905faa892cc338b5bdf4f998ad95f7b8a484e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95488948"
---
# <a name="bulk-remove-group-members-in-azure-active-directory"></a>Azure Active Directory でのグループ メンバーの一括削除

Azure Active Directory (Azure AD) ポータルでは、コンマ区切り値 (CSV) ファイルを使用してグループ メンバーを一括削除することで、多数のメンバーをグループから削除できます。

## <a name="understand-the-csv-template"></a>CSV テンプレートについて

一括アップロード CSV テンプレートをダウンロードして入力し、Azure AD グループ メンバーを正常に一括追加します。 CSV テンプレートは、次の例のようになります。

![アップロード用のスプレッドシートと、各行および列の目的と値を説明する吹き出し](./media/groups-bulk-remove-members/template-example.png)

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

## <a name="to-bulk-remove-group-members"></a>グループ メンバーを一括削除するには

1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com) にサインインします。 グループの所有者も、所有しているグループのメンバーを一括削除できます。
1. Azure AD で、 **[グループ]**  >  **[すべてのグループ]** の順に選択します。
1. メンバーを削除するグループを開き、 **[メンバー]** を選択します。
1. **[メンバー]** ページで、 **[メンバーの削除]** を選択します。
1. **[グループ メンバーの一括削除]** ページで、 **[ダウンロード]** を選択して、必要なグループ メンバー プロパティを含む CSV ファイル テンプレートを取得します。

   ![[メンバーの削除] コマンドは、グループの [プロファイル] ページにあります](./media/groups-bulk-remove-members/remove-panel.png)

1. CSV ファイルを開き、グループから削除する各グループ メンバーの行を追加します (必要な値は、メンバー オブジェクト ID またはユーザー プリンシパル名です)。 そのうえでファイルを保存します。

    :::image type="content" source="./media/groups-bulk-import-members/csv-file.png" alt-text="CSV ファイルには、削除するグループ メンバーの名前と ID が含まれています":::

1. **[グループ メンバーの一括削除]** ページの **[csv ファイルをアップロードします]** で、そのファイルを参照します。 ファイルを選択すると、CSV ファイルの検証が開始されます。
1. ファイルの内容が検証されると、一括インポート ページに "**ファイルが正常にアップロードされました**" と表示されます。 エラーが存在する場合は、ジョブを送信する前にそれらを修正する必要があります。
1. ファイルが検証に合格したら、 **[送信]** を選択して、グループ メンバーをグループから削除する Azure の一括操作を開始します。
1. 削除操作が完了すると、一括操作が成功したという通知が表示されます。

## <a name="check-removal-status"></a>削除の状態を確認する

**[一括操作の結果]** ページでは、保留中のすべての一括要求の状態を確認できます。

[![[一括操作の結果] ページで状態を確認する](./media/groups-bulk-remove-members/bulk-center.png)](./media/groups-bulk-remove-members/bulk-center.png#lightbox)

一括操作に含まれる各行の項目の詳細については、 **[成功数]** 、 **[失敗数]** 、 **[要求数合計]** の各列の値を選択してください。 エラーが発生した場合、その理由が表示されます。

## <a name="bulk-removal-service-limits"></a>一括削除サービスの制限

グループ メンバーの一覧を削除する各一括アクティビティは、最大 1 時間実行できます。 これにより、少なくとも 4 万人のメンバーの一覧を削除できます。

## <a name="next-steps"></a>次のステップ

- [グループ メンバーを一括インポートする](groups-bulk-import-members.md)
- [グループ メンバーをダウンロードする](groups-bulk-download-members.md)
- [全グループの一覧をダウンロードする](groups-bulk-download.md)
