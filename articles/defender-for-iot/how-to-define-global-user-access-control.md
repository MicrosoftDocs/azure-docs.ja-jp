---
title: グローバル ユーザー アクセス制御の定義
description: 大規模な組織では、ユーザー アクセス許可が複雑になる可能性があり、標準のサイトとゾーンの構造に加えてグローバル組織構造によって決まることがあります。
ms.date: 12/08/2020
ms.topic: article
ms.openlocfilehash: 8f5f8df56a5dcb4152fc0ae9fcae3d504d6cf3e2
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784290"
---
# <a name="define-global-access-control"></a>グローバル アクセス制御の定義

大規模な組織では、ユーザー アクセス許可が複雑になる可能性があり、標準のサイトとゾーンの構造に加えてグローバル組織構造によって決まることがあります。

グローバルかつ複雑なユーザー アクセス許可の需要に応えるために、事業単位、リージョン、およびサイトに基づいたグローバル ビジネス トポロジを作成できます。 その後、これらのエンティティに関してユーザー アクセス許可を定義できます。

ビジネス トポロジ用のアクセス ツールを使用すると、Azure Defender for IoT プラットフォームでのユーザーによるデバイスの管理と分析を適切に制御できるようになり、組織がゼロ トラスト戦略を実装するのに役立ちます。

## <a name="about-access-groups"></a>アクセス グループについて

グローバル アクセス制御は、ユーザー アクセス グループを作成することによって確立されます。 アクセス グループは、どのユーザーが特定のビジネス エンティティにアクセスできるかというルールで構成されます。 グループを使用すると、関連する業務単位、リージョン、サイトの特定のユーザー ロールについて、Defender for IoT への表示および構成アクセスを制御できます。

たとえば、ある Active Directory グループのセキュリティ アナリストが、西ヨーロッパのすべての自動車製造ラインとガラス製造ラインに加え、1 つのリージョンのプラスチック ラインにもアクセスできるように許可します。

:::image type="content" source="media/how-to-define-global-user-access-control/sa-diagram.png" alt-text="セキュリティ アナリスト Active Directory グループの図。":::

アクセス グループを作成する前に、次のことをお勧めします。

- ビジネス トポロジを慎重に設定します。 ビジネス トポロジの詳細については、「[サイト マップ ビューの使用](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)」を参照してください。

- 作成するアクセス グループにどのユーザーを関連付けるかを計画します。 ユーザーをアクセス グループに割り当てるために次の 2 つのオプションがあります。

  - **Active Directory グループのグループの割り当て**:オンプレミス管理コンソールと統合する Active Directory インスタンスを設定していることを確認します。
  
  - **ローカル ユーザーの割り当て**:ユーザーを作成したことを確認します。 詳しくは、「[ユーザーを定義する](how-to-create-and-manage-users.md#define-users)」をご覧ください。

管理者ユーザーをアクセス グループに割り当てることはできません。 これらのユーザーは、既定ですべてのビジネス トポロジ エンティティにアクセスできます。

## <a name="create-access-groups"></a>アクセス グループを作成する

このセクションでは、アクセス グループを作成する方法について説明します。 既定のグローバル事業単位およびリージョンは、作成する最初のグループに対して作成されます。 最初のグループを定義するときに、既定のエンティティを編集することができます。

グループを作成するには:

1. オンプレミス管理コンソールのサイドメニューから **[アクセス グループ]** を選択します。

2. [:::image type="icon" source="media/how-to-define-global-user-access-control/add-icon.png" border="false":::] を選択します。 **[Add Access Group]\(アクセス グループの追加\)** ダイアログ ボックスで、アクセス グループの名前を入力します。 コンソールでは、64 文字がサポートされています。 このグループを他のグループと簡単に区別できる名前を割り当てます。

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="アクセス グループを作成するための [Add Access Group]\(アクセス グループの追加\) ダイアログ ボックス。":::

3. **[Assign an Active Directory Group]\(Active Directory グループを割り当てる\)** オプションが表示されたら、このアクセス グループに 1 つの Active Directory グループのユーザーを割り当てることができます。

   :::image type="content" source="media/how-to-define-global-user-access-control/add-access-group.png" alt-text="[Create Access Group]\(アクセス グループの作成\) ダイアログ ボックスで Active Directory グループを割り当てます。":::

   このオプションが表示されないが、Active Directory グループをアクセス グループに含めたい場合は、 **[システム設定]** を選択します。 **[統合]** ウィンドウで、グループを定義します。 Active Directory 構成に表示されるとおりに、小文字でグループ名を入力します。

5. **[ユーザー]** ウィンドウで、必要な数だけユーザーをグループに割り当てます。 また、ユーザーを別のグループに割り当てることもできます。 このように作業する場合は、アクセス グループとルールを作成して保存してから、 **[ユーザー]** ウィンドウでグループにユーザーを割り当てる必要があります。

   :::image type="content" source="media/how-to-define-global-user-access-control/role-management.png" alt-text="ユーザーのロールを管理し、必要に応じて割り当てます。":::

6. ビジネス トポロジのアクセス要件に基づいて、 **[Add Rules for *name*]\("name" へのルールの追加\)** ダイアログ ボックスでルールを作成します。 ここに表示されるオプションは、 **[Enterprise View]\(エンタープライズ ビュー\)** および **[サイトの管理]** ウィンドウで設計したトポロジに基づいています。 

   グループごとに複数のルールを作成できます。 複数のサイトで複雑なアクセス細分性を使用している場合は、グループごとに複数のルールを作成する必要がある場合があります。 

   :::image type="content" source="media/how-to-define-global-user-access-control/add-rule.png" alt-text="システムにルールを追加します。":::

作成したルールは、 **[Add Access Group]\(アクセス グループの追加\)** ダイアログ ボックスに表示されます。 そこで、それらを削除または編集できます。

:::image type="content" source="media/how-to-define-global-user-access-control/edit-access-groups.png" alt-text="このウィンドウで、すべてのアクセス グループを表示および編集します。":::

### <a name="about-rules"></a>ルールについて

ルールを作成するときは、次の情報に注意してください。

- アクセス グループに複数のルールが含まれている場合、ルール ロジックによってすべてのルールが集約されます。 たとえば、ルールでは AND ロジックが使用され OR ロジックは使用されません。

- ルールを適切に適用するには、 **[サイトの管理]** ウィンドウでセンサーをゾーンに割り当てる必要があります。

- ルールごとに要素を 1 つだけ割り当てることができます。 たとえば、各ルールには、1 つの事業単位、1 つのリージョン、および 1 つのサイトを割り当てることができます。 たとえば、ある Active Directory グループのユーザーがさまざまなリージョンのさまざまな事業単位にアクセスできるようにする場合、そのグループに対してさらにルールを作成します。

- エンティティを変更し、その変更がルール ロジックに影響を与える場合は、ルールが削除されます。 トポロジ エンティティに対して行われた変更によって、ルール ロジックが影響を受けて、すべてのルールが削除された場合、アクセス グループは残りますが、ユーザーはオンプレミス管理コンソールにサインインできません。 ユーザーは、サインインするために管理者に連絡するように通知されます。

- 事業単位またはリージョンが選択されていない場合、ユーザーは定義されているすべての事業単位とリージョンにアクセスできます。

## <a name="see-also"></a>関連項目

[About Defender for IoT コンソール ユーザーについて](how-to-create-and-manage-users.md)
