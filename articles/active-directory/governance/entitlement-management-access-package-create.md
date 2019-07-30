---
title: Azure AD のエンタイトルメント管理 (プレビュー) で新しいアクセス パッケージを作成する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理 (プレビュー) で共有するリソースの新しいアクセス パッケージを作成する方法を説明します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/23/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83eee019ee8530297689b85e6f3300fed4392610
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68489178"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理 (プレビュー) で新しいアクセス パッケージを作成する

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

アクセス パッケージを使用すると、リソースとポリシーを 1 回だけ設定して、アクセス パッケージの存続期間中にアクセスを自動的に管理できます。 この記事では、新しいアクセス パッケージの作成方法について説明します。

## <a name="overview"></a>概要

すべてのアクセス パッケージは、カタログという名前のコンテナーに配置する必要があります。 カタログは、アクセス パッケージに追加できるリソースを定義します。 カタログを指定しない場合、アクセス パッケージは一般カタログに格納されます。 現時点では、既存のアクセス パッケージを別のカタログに移動することはできません。

すべてのアクセス パッケージには、少なくとも 1 つのポリシーが必要です。 ポリシーは、アクセス パッケージを要求できるユーザーを指定するほか、承認と有効期限の設定も指定します。 新しいアクセス パッケージを作成するときに、ディレクトリ内のユーザー、ディレクトリ内にいないユーザー、および管理者直接割り当てのみに対する初期ポリシーを作成することも、後でポリシーを作成することもできます。

次の図では、新しいアクセス パッケージを作成する手順の概要について説明します。

![アクセス パッケージの作成プロセス](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>新しいアクセス パッケージを開始する

**前提となるロール:** ユーザー管理者またはカタログ所有者

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. **[Azure Active Directory]** をクリックしてから **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックします。

    ![Azure portal でのエンタイトルメント管理](./media/entitlement-management-shared/elm-access-packages.png)

1. **[New access package]\(新しいアクセス パッケージ\)** をクリックします。

## <a name="basics"></a>基本

**[基本]** タブでは、アクセス パッケージの名前を指定し、アクセス パッケージを作成するカタログを指定します。

1. アクセス パッケージの表示名と説明を入力します。 ユーザーがアクセス パッケージの要求を送信するときに、この情報が表示されます。

1. **[カタログ]** ドロップダウン リストで、アクセス パッケージを作成するカタログを選択します。 たとえば、要求できるすべてのマーケティング リソースを管理するカタログ所有者が存在するとします。 この場合、マーケティング カタログを選択できます。

    アクセス パッケージを作成する権限を持つカタログのみが表示されます。 アクセス パッケージを既存のカタログ内に作成するには、少なくともユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャーである必要があります。

    ![[アクセス パッケージ] - [基本]](./media/entitlement-management-access-package-create/basics.png)

    アクセス パッケージを新しいカタログに作成する場合は、 **[新規作成]** をクリックします。 カタログの名前と説明を入力し、 **[作成]** をクリックします。

    作成するアクセス パッケージとそれに含まれるすべてのリソースが、新しいカタログに追加されます。 さらに、自動的にカタログの最初の所有者になります。 カタログ所有者をさらに追加できます。

    新しいカタログを作成するには、少なくともユーザー管理者またはカタログ作成者である必要があります。

1. **[次へ]** をクリックします。

## <a name="resource-roles"></a>リソース ロール

**[リソース ロール]** タブでは、アクセス パッケージに含めるリソースを選択します。

1. 追加するリソースの種類をクリックします ( **[グループ]** 、 **[アプリケーション]** 、または **[SharePoint サイト]** )。

1. 表示される選択ウィンドウで、一覧から 1 つまたは複数のリソースを選択します。

    ![アクセス パッケージ - リソース ロール](./media/entitlement-management-access-package-create/resource-roles.png)

    一般カタログまたは新しいカタログにアクセス パッケージを作成する場合は、自分が所有するディレクトリから任意のリソースを選択できます。 少なくともユーザー管理者またはカタログ作成者である必要があります。

    既存のカタログにアクセス パッケージを作成する場合は、カタログ内に既にある任意のリソースを、それを所有していなくても選択できます。

    ユーザー管理者またはカタログ所有者の場合は、まだカタログに存在しないが自分が所有するリソースを選択する追加のオプションがあります。 選択されたカタログ内に現在存在しないリソースを選択した場合、それらのリソースも、他のカタログ管理者がアクセス パッケージの構築に使用できるようにカタログに追加されます。 選択されたカタログ内に現在存在するリソースのみを選択する場合、[選択] ウィンドウの上部にある **[...のみが表示されます]** のチェックボックスをオンにします。

1. リソースを選択したら、 **[ロール]** 一覧で、リソースに対してユーザーに割り当てるロールを選択します。

    ![[アクセス パッケージ] - リソース ロールの選択](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. **[次へ]** をクリックします。

## <a name="policy"></a>ポリシー

**[ポリシー]** タブでは、アクセス パッケージを要求できるユーザーと、承認および有効期限の設定も指定する最初のポリシーを作成します。 後で追加のポリシーを作成して、追加のユーザーのグループが独自の承認と有効期限の設定でアクセス パッケージを要求できるようにすることができます。 ポリシーを後で作成することもできます。

1. **[Create first policy]\(最初のポリシーを作成する\)** トグルを **[Now]\(今すぐ\)** または **[Later]\(後で\)** に設定します。

    ![[アクセス パッケージ] - [ポリシー]](./media/entitlement-management-access-package-create/policy.png)

1. **[Later]\(後で\)** を選択した場合、[[確認と作成]](#review--create) セクションにスキップし、アクセス パッケージを作成します。

1. **[Now]\(今すぐ\)** を選択した場合、次のいずれかのポリシー セクションにある手順を実行します。

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>確認と作成

**[Review + create]\(確認と作成\)** タブでは、設定の確認と検証エラーのチェックを行うことができます。

1. アクセス パッケージの設定を確認します

    ![[アクセス パッケージ] - [ポリシー] - [ポリシーを有効にする] 設定](./media/entitlement-management-access-package-create/review-create.png)

1. **[作成]** をクリックしてアクセス パッケージを作成します。

    新しいアクセス パッケージがアクセス パッケージの一覧に表示されます。

## <a name="next-steps"></a>次の手順

- [既存のアクセス パッケージの編集および管理](entitlement-management-access-package-edit.md)
- [カタログ所有者またはアクセス パッケージ マネージャーを追加する](entitlement-management-delegate.md#add-a-catalog-owner-or-an-access-package-manager)
- [カタログを作成および管理する](entitlement-management-catalog-create.md)
