---
title: Power BI テナントの登録とスキャン (プレビュー)
description: Azure Purview portal を使用して Power BI テナントの登録とスキャンを行う方法について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/19/2020
ms.openlocfilehash: 2ecc5df9db51bb6c923b9e0f47163e492bd76cfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695748"
---
# <a name="register-and-scan-a-power-bi-tenant-preview"></a>Power BI テナントの登録とスキャン (プレビュー)

この記事では、Azure Purview portal を使用して Power BI テナントの登録とスキャンを行う方法について説明します。

> [!Note]
> Purview インスタンスと Power BI テナントが同じ Azure テナントにある場合、Power BI テナントのスキャンの設定にはマネージド ID (MSI) 認証のみが使用できます。 

## <a name="create-a-security-group-for-permissions"></a>アクセス許可のためのセキュリティ グループの作成

認証を設定するには、セキュリティ グループを作成し、Purview のマネージド ID を追加します。

1. [Azure portal](https://portal.azure.com) で、**Azure Active Directory** を検索します。
1. 「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」に従って、Azure Active Directory で新しいセキュリティ グループを作成します。

    > [!Tip]
    > 使用するセキュリティ グループが既にある場合は、この手順を省略できます。

1. **[グループの種類]** として **[セキュリティ]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="セキュリティ グループの種類":::

1. Purview のマネージド ID をこのセキュリティ グループに追加します。 **[メンバー]** を選択してから **[+ メンバーの追加]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="カタログのマネージド インスタンスをグループに追加します。":::

1. Purview のマネージド ID を検索し、それを選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="カタログを検索して追加する":::

    追加されたことを示す成功通知が表示されます。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="カタログ MSI の追加成功":::

## <a name="associate-the-security-group-with-the-tenant"></a>セキュリティ グループのテナントへの関連付け

1. [Power BI 管理ポータル](https://app.powerbi.com/admin-portal/tenantSettings)にログインします。
1. **[テナント設定]** ページを選択します。

    > [!Important]
    > テナント設定のページを表示するには、Power BI 管理者である必要があります。

1. **[管理者 API 設定]**  >  **[Allow service principals to use read-only Power BI admin APIs (Preview)]\(読み取り専用 Power BI Admin API の使用をサービス プリンシパルに許可 (プレビュー)\)** を選択します。
1. **[特定のセキュリティ グループ]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="サービス プリンシパルが読み取り専用 Power BI Admin API のアクセス許可を取得できるようにする方法を示す画像":::

    > [!Caution]
    > 作成したセキュリティ グループ (Purview のマネージド ID をメンバーとして持つ) に読み取り専用 Power BI Admin API の使用を許可するときは、このテナント内のすべての Power BI 成果物のメタデータ (ダッシュボードやレポートの名前、所有者、説明など) へのアクセスも許可します。 メタデータが Azure Purview に取り込まれると、Purview のアクセス許可 (Power BI のアクセス許可ではない) によって、そのメタデータを表示できるユーザーが決まります。

    > [!Note]
    > 開発者向け設定からセキュリティ グループを削除することはできますが、以前に抽出されたメタデータは Purview アカウントから削除されません。 これは、必要に応じて個別に削除することができます。

## <a name="register-your-power-bi-and-set-up-a-scan"></a>Power BI の登録とスキャンの設定

Power BI テナントの Admin API に接続するための Purview のマネージド ID のアクセス許可が付与されたので、Azure Purview Studio からスキャンを設定できるようになりました。

まず、Purview URL に特別な機能フラグを追加します 

1. **管理センター** のアイコンを選択します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/management-center.png" alt-text="管理センターのアイコン。":::

1. 次に、 **[データ ソース]** で **[+ 新規]** を選択します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/data-sources.png" alt-text="新しいデータ ソースのボタンの画像":::

    データ ソースとして **[Power BI]** を選択します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="選択可能なデータ ソースの一覧を示す画像":::

3. Power BI インスタンスにフレンドリ名を付けます。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Power BI データ ソースのフレンドリ名を示す画像":::

    名前の長さは 3 から 63 文字で、文字、数字、アンダースコア、およびハイフンのみを使用できます。  スペースは使用できません。

    既定では、同じ Azure サブスクリプションに存在する Power BI テナントがシステムによって検出されます。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="登録された Power BI データ ソース":::

    > [!Note]
    > Power BI の場合、データ ソースの登録とスキャンは 1 つのインスタンスに対してのみ許可されています。


4. スキャンに名前を付けます。 次に、個人用ワークスペースを含める、または除外するオプションを選択します。 サポートされている認証方法は **[マネージド ID]** のみであることに留意してください。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Power BI スキャンの設定を示す画像":::

    > [!Note]
    > * 個人用ワークスペースを含める、または除外するようにスキャンの構成を切り替えると、PowerBI ソースのフル スキャンがトリガーされます
    > * スキャンの名前の長さは 3 から 63 文字で、文字、数字、アンダースコア、およびハイフンのみを使用できます。 スペースは使用できません。

5. スキャン トリガーを設定します。 オプションとしては、 **[1 度]** 、 **[7 日ごと]** 、 **[30 日ごと]** があります。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="スキャン トリガーの画像":::

6. **[Review new scan]\(新しいスキャンを確認\)** で **[保存および実行]** を選択してスキャンを開始します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI の保存および実行画面の画像":::

## <a name="next-steps"></a>次のステップ

- [Azure Purview データ カタログを参照する](how-to-browse-catalog.md)
- [Azure Purview データ カタログを検索する](how-to-search-catalog.md)
