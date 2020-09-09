---
title: 追加 Azure サブスクリプションの作成
description: Azure portal で新しい Azure サブスクリプションを作成する方法を説明します。 課金アカウントのフォームに関する情報を参照して、使用可能なその他のリソースを確認してください。
author: amberbhargava
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: aa8cf0d2a48c75b71895eb75db362c4ec4e291c5
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88925047"
---
# <a name="create-an-additional-azure-subscription"></a>追加 Azure サブスクリプションの作成

[マイクロソフト エンタープライズ契約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)、[Microsoft 顧客契約](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)、[Microsoft Partner Agreement](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) の各課金アカウントには、Azure portal から追加でサブスクリプションを作成できます。 サブスクリプションの制限に達するのを回避したり、セキュリティを目的として独立した環境を作成したり、コンプライアンス上の理由からデータを分離したりするために、サブスクリプションを追加したい場合があります。

Microsoft Online Services プログラム (MOSP) の課金アカウントをお持ちの場合、[Azure のサインアップ ポータル](https://account.azure.com/signup?offer=ms-azr-0003p)から追加でサブスクリプションを作成できます。

課金アカウントの詳細および課金アカウントの種類の確認については、「[Azure portal での課金アカウントの表示](view-all-accounts.md)」を参照してください。

## <a name="permission-required-to-create-azure-subscriptions"></a>Azure サブスクリプションの作成に必要なアクセス許可

サブスクリプションを作成するためには次のアクセス許可が必要です。

|請求先アカウント  |権限  |
|---------|---------|
|Enterprise Agreement (EA) |  マイクロソフト エンタープライズ契約の加入契約におけるアカウント オーナー ロール。 詳細については、「[Understand Azure Enterprise Agreement administrative roles in Azure](understand-ea-roles.md)」(Azure の Azure Enterprise Agreement 管理者ロールを理解する) を参照してください。    |
|Microsoft 顧客契約 (MCA) |  請求書セクション、課金プロファイル、または課金アカウントにおける所有者ロールまたは投稿者ロール。 または、請求書セクションにおける Azure サブスクリプションの作成者ロール。  詳細については、「[サブスクリプションの課金ロールとタスク](understand-mca-roles.md#subscription-billing-roles-and-tasks)」を参照してください。    |
|Microsoft Partner Agreement (MPA) |   CSP パートナー組織の全体管理者ロールおよび管理者エージェント ロール。 詳細については、[パートナー センターでのユーザー ロールとアクセス許可の割り当て](https://docs.microsoft.com/partner-center/permissions-overview)に関するページを参照してください。  Azure サブスクリプションを作成するためには、ユーザーがパートナー テナントにサインインする必要があります。   |

## <a name="create-a-subscription-in-the-azure-portal"></a>Azure portal にサブスクリプションを作成する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **サブスクリプション**を検索します。

   ![サブスクリプション用のポータルでの検索を示すスクリーン ショット](./media/create-subscription/billing-search-subscription-portal.png)

1. **[追加]** を選択します。

   ![サブスクリプション ビューの追加ボタンを示すスクリーン ショット](./media/create-subscription/subscription-add.png)

1. 複数の課金アカウントにアクセスできる場合は、サブスクリプションの作成対象となる課金アカウントを選択します。

1. フォームに入力して **[作成]** をクリックします。 以下の表は、課金アカウントの種類ごとにフォーム上のフィールドを一覧にしたものです。

**マイクロソフトエンタープライズ契約**

|フィールド  |定義  |
|---------|---------|
|名前     | Azure portal でサブスクリプションを識別しやすくするための表示名。  |
|プラン     | 開発またはテスト用ワークロードにこのサブスクリプションを使用する場合は、[EA Dev/Test] を選択します。その他の場合には、[Microsoft Azure エンタープライズ] を使用します。 EA Dev/Test サブスクリプションを作成するためには、ご利用の登録アカウントに対して DevTest プランが有効になっている必要があります。|

**Microsoft 顧客契約**

|フィールド  |定義  |
|---------|---------|
|請求プロファイル     | サブスクリプションの料金は、選択した課金プロファイルに対して課金されます。 アクセスできる課金プロファイルが 1 つのみの場合は、この選択範囲はグレー表示されます。     |
|請求書セクション     | お使いのサブスクリプションの料金は、その課金プロファイルの請求書のこのセクションに表示されます。 アクセスできる請求書セクションが 1 つのみの場合は、この選択範囲はグレー表示されます。  |
|プラン     | 開発またはテスト用ワークロードにこのサブスクリプションを使用する場合は、[Dev/Test 用 Microsoft Azure プラン] を選択します。その他の場合には、[Microsoft Azure プラン] を使用します。 課金プロファイルで有効になっているプランが 1 つのみの場合は、この選択項目はグレー表示されます。  |
|名前     | Azure portal でサブスクリプションを識別しやすくするための表示名。  |

**Microsoft Partner Agreement**

|フィールド  |定義  |
|---------|---------|
|Customer    | サブスクリプションは、選択した顧客に対して作成されます。 顧客が 1 件しか存在しなければ、この選択項目はグレー表示されます。  |
|Reseller    | 顧客にサービスを提供するリセラー。 これはオプション フィールドです。CSP の2 層モデルの間接プロバイダーにのみ関係します。 |
|名前     | Azure portal でサブスクリプションを識別しやすくするための表示名。  |

## <a name="create-an-additional-azure-subscription-programmatically"></a>追加の Azure サブスクリプションをプログラムから作成する

追加サブスクリプションをプログラムで作成することもできます。 詳細については、[Azure サブスクリプションをプログラムで作成する方法](programmatically-create-subscription.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure サブスクリプション管理者を追加または変更する](add-change-subscription-administrator.md)
- [新しいリソース グループまたはサブスクリプションへのリソースの移動](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [リソースの整理と管理のための管理グループを作成する](../../governance/management-groups/create.md)
- [Azure のサブスクリプションの取り消し](cancel-azure-subscription.md)

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。
