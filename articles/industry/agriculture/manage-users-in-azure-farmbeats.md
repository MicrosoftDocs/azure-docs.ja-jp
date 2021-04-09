---
title: Azure FarmBeats でユーザーを管理する
description: この記事では、Azure FarmBeats でユーザーを管理する方法について説明します。
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-ummehabiba
ms.openlocfilehash: de9a11cac9856417422bd72579cbca44233ab422
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179903"
---
# <a name="manage-users"></a>ユーザーの管理

Azure FarmBeats には、Azure Active Directory (Azure AD) インスタンスの一部であるユーザーに対するユーザー管理が含まれています。 Azure FarmBeats インスタンスにユーザーを追加して、API にアクセスしたり、生成されたマップを表示したり、ファームからのセンサー テレメトリにアクセスしたりできます。

## <a name="prerequisites"></a>前提条件

- Azure FarmBeats のインストールが必要です。 詳細については、「[Azure FarmBeats のインストール](install-azure-farmbeats.md)」を参照してください。
- Azure FarmBeats インスタンスに対して追加または削除するユーザーの電子メール ID。

## <a name="manage-azure-farmbeats-users"></a>Azure FarmBeats ユーザーを管理する

Azure FarmBeats では、認証、アクセス制御、およびロールのために Azure AD を使用します。 Azure AD テナントのユーザーを Azure FarmBeats のユーザーとして追加できます。

> [!NOTE]
> ユーザーが Azure AD テナント ユーザーでない場合は、 **[Azure AD ユーザーの追加]** セクションの手順に従って、セットアップを完了してください。

Azure FarmBeats では、2 種類のユーザー ロールがサポートされています。

 - **管理**: Azure FarmBeats データハブ API への完全なアクセス権。 このロールのユーザーは、すべての Azure FarmBeats データ ハブ オブジェクトにクエリを実行したり、すべての操作を FarmBeats Accelerator から実行したりできます。
 - **読み取り専用**: FarmBeats データハブ API への読み取り専用アクセス。 ユーザーは、データ ハブ API、Accelerator ダッシュボード、およびマップを表示できます。 読み取り専用アクセス権を持つユーザーは、マップの生成、デバイスの関連付け、ファームの作成などの操作を実行できません。

## <a name="add-users-to-azure-farmbeats"></a>Azure FarmBeats にユーザーを追加する

Azure FarmBeats にユーザーを追加するには:

1. Accelerator にサインインし、**[設定]** アイコンを選択します。
2. **[アクセス制御]** を選択します。

    ![[ファームの設定] ペイン](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. アクセスを付与する対象であるユーザーの電子メール ID を入力します。
4. 目的のロール (**[管理者]** または **[読み取り専用]**) を選択します。
5. **[ロールの追加]** を選択します。

これで、追加されたユーザーは Azure FarmBeats (データ ハブと Accelerator の両方) にアクセスできます。

## <a name="delete-users-from-azure-farmbeats"></a>Azure FarmBeats からユーザーを削除する

Azure FarmBeats システムからユーザーを削除するには:

1. Accelerator にサインインし、**[設定]** アイコンを選択します。
2. **[アクセス制御]** を選択します。
3. **[削除]** を選択します。

   ユーザーがシステムから削除されます。 次の確認メッセージが表示されます。

   ![Azure FarmBeats の確認メッセージ](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Azure AD ユーザーの追加

> [!NOTE]
> Azure FarmBeats ユーザーをアプリケーションやロールに割り当てるには、そのユーザーが Azure AD テナントに存在している必要があります。 ユーザーが Azure AD テナントに存在していない場合は、このセクションの手順に従ってください。 ユーザーが既に Azure AD テナントに存在している場合は、手順をスキップしてください。

Azure AD にユーザーを追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) にサインインします。
2. 右上で自分のアカウントを選択し、FarmBeats に関連付けられている Azure AD テナントに切り替えます。
3. **[Azure Active Directory]**  >  **[ユーザー]** を選択します。

    Azure AD ユーザーの一覧が表示されます。

4. ディレクトリにユーザーを追加するには、**[新しいユーザー]** を選択します。 外部ユーザーを追加するには、**[新しいゲスト ユーザー]** を選択します。

    ![[すべてのユーザー] ペイン](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. 新しいユーザーの名前を選択し、そのユーザーの必須フィールドに入力します。
6. **［作成］** を選択します

Azure AD ユーザーの管理の詳細については、[Azure AD でのユーザーの追加または削除](../../active-directory/fundamentals/add-users-azure-active-directory.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

使用している Azure FarmBeats インスタンスにユーザーが正常に追加されました。 次は、[ファームを作成して管理する](manage-farms-in-azure-farmbeats.md#create-farms)方法を確認します。