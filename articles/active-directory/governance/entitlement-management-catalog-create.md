---
title: Azure AD エンタイトルメント管理 (プレビュー) でリソースのカタログを作成および管理する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理 (プレビュー) でリソースとアクセス パッケージの新しいコンテナーを作成する方法を説明します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1a6d7901368d19cf5ca8221bc00f426980e6f48
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169920"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理 (プレビュー) でリソースのカタログを作成および管理する

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="create-a-catalog"></a>カタログを作成する

カタログは、リソースとアクセス パッケージのコンテナーです。 関連するリソースとアクセス パッケージをグループ化するときは、カタログを作成します。 どのユーザーでも、カタログを作成すると、最初のカタログ所有者になります。 カタログ所有者は、別のカタログ所有者を追加できます。

**事前に必要なロール:** 全体管理者、ユーザー管理者、またはカタログ管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックします。

    ![Azure portal 内のエンタイトルメント管理カタログ](./media/entitlement-management-catalog-create/catalogs.png)

1. **[新しいカタログ]** をクリックします。

1. カタログの一意の名前と説明を入力します。

    この情報は、アクセス パッケージの詳細に表示されます。

1. このカタログ内のアクセス パッケージが作成されたらすぐにユーザーがそのパッケージを要求できるようにする場合は、 **[有効]** を **[はい]** に設定します。

1. 選択された外部ディレクトリ内のユーザーがこのカタログ内のアクセス パッケージを要求できるようにする場合は、 **[Enabled for external users]\(外部ユーザーに対して有効にする\)** を **[はい]** に設定します。

    ![新しいカタログ ペイン](./media/entitlement-management-catalog-create/new-catalog.png)

1. **[作成]** をクリックすると、カタログが作成されます。

## <a name="add-resources-to-a-catalog"></a>カタログにリソースを追加する

アクセス パッケージにリソースを含めるには、リソースがカタログ内に存在している必要があります。 追加できるリソースの種類は、グループ、アプリケーション、および SharePoint Online サイトです。 追加できるグループは、クラウドで作成された Office 365 グループ、またはクラウドで作成された Azure AD セキュリティ グループです。 追加できるアプリケーションは Azure AD エンタープライズ アプリケーションで、これには SaaS アプリケーションと、Azure AD にフェデレーションされた独自アプリケーションの両方が含まれます。 追加できるサイトは、SharePoint Online サイトまたは SharePoint Online サイト コレクションです。

**事前に必要なロール:** [リソースをカタログに追加するために必要なロール](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)に関するページを参照

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、リソースを追加するカタログを開きます。

1. 左側のメニューで **[リソース]** をクリックします。

1. **[リソースの追加]** をクリックします。

1. リソースの種類として、 **[Groups and Teams]\(Groups と Teams\)** 、 **[アプリケーション]** 、または **[SharePoint サイト]** のいずれかをクリックします。

    追加するリソースが表示されない、またはリソースを追加できない場合は、必要な Azure AD ディレクトリ ロールおよびエンタイトルメント管理ロールを持っていることを確認してください。 必要なロールを持つ人物に、カタログへのリソース追加を依頼することが必要な場合があります。 詳細については、[リソースをカタログに追加するために必要なロール](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)に関するページを参照してください。

1. カタログに追加する種類のリソースを 1 つ以上選択します。

    ![カタログにリソースを追加する](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 作業が終了したら、 **[追加]** をクリックします。

    これらのリソースをカタログ内のアクセス パッケージに含めることができるようになりました。

## <a name="remove-resources-from-a-catalog"></a>カタログからリソースを削除する

カタログからリソースを削除できます。 カタログからリソースを削除できるのは、そのリソースがカタログのどのアクセス パッケージでも使用されていない場合に限られます。

**事前に必要なロール:** [リソースをカタログに追加するために必要なロール](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)に関するページを参照

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、リソースを削除するカタログを開きます。

1. 左側のメニューで **[リソース]** をクリックします。

1. 削除するリソースを選択します。

1. **[削除]** をクリックします (または、省略記号 ( **...** ) をクリックして **[リソースの削除]** をクリックします)。

## <a name="add-additional-catalog-owners"></a>カタログ所有者をさらに追加する

カタログを作成したユーザーは最初のカタログ所有者になります。 カタログの管理を委任するには、カタログ所有者ロールにユーザーを追加します。 これは、カタログの管理責任を共有するのに役立ちます。 

次の手順でカタログ作成者ロールにユーザーを割り当てます。

**事前に必要なロール:** 全体管理者、ユーザー管理者、またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、管理者を追加するカタログを開きます。

1. 左側のメニューで **[ロールと管理者]** をクリックします。

    ![カタログのロールと管理者](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **[所有者の追加]** をクリックし、それらのロールのメンバーを選択します。

1. **[選択]** をクリックすると、これらのメンバーが追加されます。

## <a name="edit-a-catalog"></a>カタログを編集する

カタログの名前と説明を編集できます。 この情報は、アクセス パッケージの詳細に表示されます。

**事前に必要なロール:** 全体管理者、ユーザー管理者、またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、編集するカタログを開きます。

1. カタログの **[概要]** ページで **[編集]** をクリックします。

1. カタログの名前または説明を編集します。

1. **[Save]** をクリックします。

## <a name="delete-a-catalog"></a>カタログを削除する

カタログを削除できるのは、そのカタログにどのアクセス パッケージも含まれていない場合に限られます。

**事前に必要なロール:** 全体管理者、ユーザー管理者、またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、削除するカタログを開きます。

1. カタログの **[概要]** で **[削除]** をクリックします。

1. 表示されるメッセージ ボックスで、 **[はい]** をクリックします。

## <a name="next-steps"></a>次の手順

- [アクセス パッケージ管理者にアクセス ガバナンスを委任する](entitlement-management-delegate-managers.md)
