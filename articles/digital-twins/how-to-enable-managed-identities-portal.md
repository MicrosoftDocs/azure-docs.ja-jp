---
title: イベントをルーティングするためにマネージド ID を有効にする (プレビュー) - portal
titleSuffix: Azure Digital Twins
description: Azure portal を使用して、Azure Digital Twins のシステム割り当て ID を有効にし、それを使用してイベントを転送する方法を説明します。
author: baanders
ms.author: baanders
ms.date: 1/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 1211cd306f10fb349bd42568697443ff103a171c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101703093"
---
# <a name="enable-a-managed-identity-for-routing-azure-digital-twins-events-preview-azure-portal"></a>Azure Digital Twins のイベントをルーティングするためにマネージド ID を有効にする (プレビュー):Azure portal

[!INCLUDE [digital-twins-managed-service-identity-selector.md](../../includes/digital-twins-managed-service-identity-selector.md)]

この記事では、[Azure Digital Twins インスタンスのシステム割り当て ID](concepts-security.md#managed-identity-for-accessing-other-resources-preview) (現時点ではプレビュー段階) を有効にし、[Event Hubs](../event-hubs/event-hubs-about.md)、[Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) 、[Azure Storage コンテナー](../storage/blobs/storage-blobs-introduction.md)などのサポートされている送信先にイベントを転送するときに、その ID を使用します。

この記事では、[**Azure portal**](https://portal.azure.com) を使用したプロセスについて説明します。

この記事では、次の手順を説明します。 

1. システム割り当て ID を使用して Azure Digital Twins インスタンスを作成するか、既存の Azure Digital Twins インスタンスでシステム割り当て ID を有効にします。 
1. ID に適切なロール (1 つまたは複数) を追加します。 たとえば、エンドポイントが Event Hubs の場合は ID に **Azure Event Hubs データ送信者** ロールを割り当て、エンドポイントが Service Bus の場合は **Azure Service Bus データ送信者ロール** を割り当てます。
1. 認証にシステム割り当て ID を使用できるエンドポイントを Azure Digital Twins に作成します。

## <a name="enable-system-managed-identities-for-an-instance"></a>インスタンスでシステム マネージド ID を有効にする 

Azure Digital Twins インスタンスでシステム割り当て ID を有効にすると、Azure によって [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) にその ID が自動的に作成されます。 その後、その ID を使用して、イベント転送のために Azure Digital Twins エンドポイントに対する認証を行うことができます。

**インスタンスの初期セットアップの一部として** Azure Digital Twins インスタンスのシステム マネージド ID を有効にすることも、**既に存在するインスタンスで後から有効にする** こともできます。

どちらの作成方法でも、インスタンスに提供される構成オプションと結果は同じです。 このセクションでは、両方を行う方法について説明します。

### <a name="add-a-system-managed-identity-during-instance-creation"></a>インスタンスの作成時にシステム マネージド ID を追加する

このセクションでは、現在作成中の Azure Digital Twins インスタンスで、システム マネージド ID を有効にする方法について説明します。 このセクションでは、作成プロセスのマネージド ID ステップを中心に説明します。新しい Azure Digital Twins インスタンスの作成に関する完全なチュートリアルについては、[*インスタンスと認証を設定する方法*](how-to-set-up-instance-portal.md)に関するページを参照してください。

システム マネージド ID オプションは、インスタンスのセットアップの **[詳細]** タブにあります。

このタブで、 **[System managed identity]\(システム マネージド ID\)** の **[オン]** オプションを選択して、この機能を有効にします。

:::image type="content" source="media/how-to-enable-managed-identities/create-instance-advanced.png" alt-text="Azure Digital Twins の [リソースの作成] ダイアログの [詳細] タブが表示されている Azure portal のスクリーンショット。タブ名、[System managed identity]\(システム マネージド ID\) の [オン] オプション、ナビゲーション ボタンが強調表示されています ([確認および作成]、[前へ]、[次: 詳細])。":::

その後、下部にあるナビゲーション ボタンを使用して、インスタンスの残りのセットアップを続けることができます。

### <a name="add-a-system-managed-identity-to-an-existing-instance"></a>既存のインスタンスにシステム マネージド ID を追加する

このセクションでは、既に存在する Azure Digital Twins インスタンスにシステム マネージド ID を追加します。

1. まず、ブラウザーで [Azure portal](https://portal.azure.com) に移動します。

1. ポータルの検索バーでお使いのインスタンスの名前を検索し、それを選択して詳細を表示します。

1. 左側のメニューの **[ID (プレビュー)]** を選択します。

1. このページで、 **[オン]** オプションを選択してこの機能を有効にします。

1. **[保存]** ボタンを選択し、 **[はい]** を選択して確定します。

    :::image type="content" source="media/how-to-enable-managed-identities/identity-digital-twins.png" alt-text="Azure Digital Twins インスタンスの [ID (プレビュー)] ページが表示されている Azure portal のスクリーンショット。Azure Digital Twins インスタンス メニューのページ名、[状態] の [オン] オプション、[保存] ボタン、および [はい] 確認ボタンが強調表示されています。":::

変更が保存されると、新しい ID の **[オブジェクト ID]** と **[アクセス許可]** のフィールドがこのページに表示されます。

必要に応じてここから **オブジェクト ID** をコピーし、ID に割り当てられている Azure ロールを表示するには **[アクセス許可]** ボタンを使用します。 ロールを設定するには、次のセクションに進みます。

## <a name="assign-azure-roles-to-the-identity"></a>Azure ロールを ID に割り当てる 

Azure Digital Twins インスタンス用のシステム割り当て ID が作成されたら、サポートされている送信先にイベントを転送するためにさまざまな種類の[エンドポイント](concepts-route-events.md)で認証を行うため、それに適切なロールを割り当てる必要があります。 このセクションでは、ロールのオプションと、それらをシステム割り当て ID に割り当てる方法について説明します。

>[!NOTE]
> これは重要なステップです。行わないと、ID はエンドポイントにアクセスできず、イベントは配信されません。

### <a name="supported-destinations-and-azure-roles"></a>サポートされている配信先と Azure ロール 

送信先の種類に応じて、ID がエンドポイントにアクセスするために必要な最小限のロールを次に示します。 さらに高いアクセス許可を持つロール (データ所有者ロールなどの) も機能します。

| 到着地 | Azure ロール |
| --- | --- |
| Azure Event Hubs | Azure Event Hubs データ送信者 |
| Azure Service Bus | Azure Service Bus データ送信者 |
| [Azure ストレージ コンテナー] | ストレージ BLOB データ共同作成者 |

Azure Digital Twins でのルーティングでサポートされているエンドポイント、ルート、送信先の種類の詳細については、[*イベント ルートの概念*](concepts-route-events.md)に関するページを参照してください。

### <a name="assign-the-role"></a>ロールを割り当てる

[!INCLUDE [digital-twins-permissions-required.md](../../includes/digital-twins-permissions-required.md)]

ID にロールを割り当てるには、最初に [Azure portal](https://portal.azure.com) を開きます。

1. ポータルの検索バーで名前を検索して、お使いのエンドポイント リソース (イベント ハブ、Service Bus トピック、またはストレージ コンテナー) に移動します。 
1. 左側のメニューで **[アクセス制御 (IAM)]** を選択します。
1. **[+ 追加]** ボタンを選択して、新しいロールの割り当てを追加します。

    :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-1.png" alt-text="イベント ハブの [アクセス制御 (IAM)] ページが表示されている Azure portal のスクリーンショット。[+ 追加] ボタンが強調表示されています。" lightbox="media/how-to-enable-managed-identities/add-role-assignment-1.png":::

1. 次の **[ロールの割り当ての追加]** ページで、値を入力します。
    * **ロール**: ドロップダウン メニューから目的のロールを選択します。
    * **アクセスの割り当て先**: **[システム割り当てマネージド ID]** で、 **[Digital Twins]** を選択します。
    * **[サブスクリプション]**:サブスクリプションを選択します。 これにより、選択したサブスクリプション内のすべての Azure Digital Twins マネージド ID が表示されます。
    * **Select**:ここでは、ロールを割り当てる Azure Digital Twins インスタンスのマネージド ID を選択します。 マネージ ID の名前はインスタンスの名前と一致するため、Azure Digital Twins インスタンスの名前を選択します。 これを選択すると、インスタンスの ID がウィンドウの下部にある **[選択したメンバー]** セクションに表示されます。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-enable-managed-identities/add-role-assignment-2.png" alt-text="[ロールの割り当てを追加する] ダイアログに表示されたフィールドへの入力":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

詳細の入力が終わったら、 **[保存]** を選択します。

## <a name="create-an-endpoint-with-identity-based-authentication"></a>ID ベースの認証を使用してエンドポイントを作成する

Azure Digital Twins インスタンスのシステム マネージド ID を設定し、それに適切なロールを割り当てた後は、認証にその ID を使用できる Azure Digital Twins [エンドポイント](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)を作成できます。 このオプションは、種類が Event Hubs と Service Bus のエンドポイントでのみ使用できます (Event Grid ではサポートされていません)。

>[!NOTE]
> キー ベースの ID で既に作成されているエンドポイントを編集して、ID ベースの認証に変更することはできません。 最初にエンドポイントを作成するときに、認証の種類を選択する必要があります。

[Azure Digital Twins のエンドポイントを作成するには、こちらの手順](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)のようにします。

エンドポイントの種類に必要な詳細を設定するステップになったら、[認証の種類] で **[Identity-based]\(ID ベース\)** を必ず選択します。

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png" alt-text="種類が Event Hub のエンドポイントを作成するスクリーンショット。" lightbox="media/how-to-manage-routes-portal/create-endpoint-event-hub-authentication.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

エンドポイントの設定を終了し、 **[保存]** を選択します。

## <a name="considerations-for-disabling-system-managed-identities"></a>システム マネージド ID を無効にする場合の考慮事項

ID は、それを使用するエンドポイントとは別に管理されているため、ID またはそのロールを変更したときの Azure Digital Twins インスタンスのエンドポイントに対する影響について考慮することが重要です。 ID を無効にした場合、またはエンドポイントに必要なロールを削除した場合、エンドポイントにアクセスできなくなり、イベントのフローが中断される可能性があります。

設定されていたマネージド ID を無効にした後もエンドポイントを引き続き使用するには、エンドポイントを削除し、異なる認証の種類で[再作成する](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins)必要があります。 この変更を行った後、エンドポイントへのイベントの配信が再開されるまで、最大で 1 時間かかることがあります。

## <a name="next-steps"></a>次のステップ

Azure AD でのマネージド ID の詳細について確認します。 
* [*Azure リソースのマネージド ID*](../active-directory/managed-identities-azure-resources/overview.md)