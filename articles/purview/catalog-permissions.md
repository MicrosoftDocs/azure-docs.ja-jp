---
title: カタログのアクセス許可 (プレビュー)
description: この記事では、Azure Purview でロールベースのアクセス制御 (RBAC) を構成する方法の概要について説明します。
author: yarong
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: b351be1e7212dc9923f701599dd951a73254afe0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98610371"
---
# <a name="role-based-access-control-in-azure-purviews-data-plane"></a>Azure Purview のデータ プレーンでのロールベースのアクセス制御

この記事では、Azure Purview の[データ プレーン](../azure-resource-manager/management/control-plane-and-data-plane.md#data-plane)でロールベースのアクセス制御 (RBAC) を実装する方法について説明します。

> [!IMPORTANT]
> Purview アカウントを作成したプリンシパルには、そのデータ プレーンのロールに関係なく、すべてのデータ プレーンのアクセス許可が自動的に付与されます。 他のユーザーが Azure Purview で何かを実行するには、定義済みのデータ プレーン ロールの少なくとも 1 つに割り当てられている必要があります。

## <a name="azure-purviews-pre-defined-data-plane-roles"></a>Azure Purview の定義済みのデータ プレーン ロール

Azure Purview には定義済みのデータ プレーン ロールのセットが用意されており、Azure Purview で誰が何にアクセスできるかを制御するのに使用できます。 これらのロールは次のとおりです。

* **Purview データ閲覧者ロール** - Purview ポータルにアクセスでき、スキャン バインディングを除く Azure Purview のすべてのコンテンツを読み取ることができます。
* **Purview データ キュレーター ロール** - Purview ポータルにアクセスでき、スキャン バインディングを除く Azure Purview のすべてのコンテンツを読み取ることができます。また、資産に関する情報の編集、分類の定義や用語集の用語の編集、分類や用語の資産への適用もできます。
* **Purview データ ソース管理者ロール** - Purview ポータルにはアクセスできません (アクセスするには、データ閲覧者またはデータ キュレーターのロールも必要です)。Azure Purview にデータをスキャンする際にあらゆる側面を管理できますが、スキャンに関連するもの以外は、Azure Purview のコンテンツの読み取りおよび書き込みができません。

## <a name="understanding-how-to-use-azure-purviews-data-plane-roles"></a>Azure Purview のデータ プレーン ロールの使用方法について

Azure Purview アカウントの作成時、作成者は Purview データ キュレーターと Purview データ ソース管理者の両方のロールに含まれているものとして扱われます。 ただし、アカウント作成者はロール ストア内のこれらのロールには割り当てられせん。 Azure Purview により、プリンシパルはアカウントの作成者であることが認識され、ID に基づいてこれらの能力がそれらに拡張されます。

他のすべてのユーザーは、これらのロールの少なくとも 1 つに配置されている場合にのみ、Azure Purview アカウントを使用できます。 つまり、前に定義されたロールの 1 つ以上に割り当てられるまで、Azure Purview アカウントの作成時には、作成者のみがアカウントへのアクセスとその API の使用を実行できます。

Purview データ ソース管理者ロールでは、2 つのシナリオがサポートされます。 最初のシナリオは、スキャンを作成できる必要もある Purview データ閲覧者または Purview データ キュレーターに既に割り当てられているユーザーの場合です。 これらのユーザーは、少なくとも、Purview データ閲覧者または Purview データ キュレーターであるとともに、Purview データ ソース管理者ロールにも割り当てられている必要があります。

Purview データ ソース管理者のもう 1 つのシナリオは、スキャンを設定して監視する必要があるが、カタログのデータにはアクセスできない、プログラムによるプロセス (サービス プリンシパルなど) のシナリオです。

このシナリオを実装するには、他の 2 つのロールのいずれにも割り当てずに、サービス プリンシパルを Purview データ ソース管理者ロールに割り当てます。 プリンシパルは Purview ポータルにはアクセスできませんが、問題はありません。 これはプログラム プリンシパルであり、API 経由でのみ通信するためです。

## <a name="putting-users-into-roles"></a>ロールへのユーザーの割り当て

Azure Purview アカウントを作成した後に最初にすることは、ユーザーをこれらのロールに割り当てることです。

ロールの割り当ては [Azure の RBAC](../role-based-access-control/overview.md) で管理されます。

ユーザー ロールを割り当てることができるのは、Azure に組み込まれている 2 つのコントロール プレーン ロール、つまり所有者またはユーザーアクセス管理者だけです。 そのため、ユーザーをこれらの Azure Purview のロールに割り当てるには、所有者またはユーザー アクセス管理者を見つけるか、自分がそのいずれかになる必要があります。

### <a name="an-example-of-assigning-someone-to-a-role"></a>ユーザーをロールに割り当てる例

1. https://portal.azure.com にアクセスし、Azure Purview アカウントに移動します。
1. 左側の [アクセス制御 (IAM)] をクリックします。
1. 次に、[こちら](../role-based-access-control/quickstart-assign-role-user-portal.md#create-a-resource-group)に記載されている一般的な手順に従います。

## <a name="role-definitions-and-actions"></a>ロールの定義とアクション

ロールはアクションのコレクションとして定義されます。 ロールの定義方法の詳細については、[こちら](../role-based-access-control/role-definitions.md)を参照してください。 Azure Purview のロールのロール定義については、[こちら](../role-based-access-control/built-in-roles.md)を参照してください。

## <a name="getting-added-to-a-data-plane-role-in-an-azure-purview-account"></a>Azure Purview アカウントでのデータ プレーン ロールへの追加

Azure Purview アカウントへのアクセス権を付与され、そのスタジオを使用する、またはその API を呼び出すことができるようになるには、Azure Purview データ プレーン ロールに追加してもらう必要があります。 これを行うことができるのは、Azure Purview アカウントの所有者またはユーザー アクセス管理者であるユーザーだけです。 ほとんどのユーザーの場合、次の手順は、アクセス権を付与できるユーザーを見つけるのを手助けしてくれる、ローカル管理者を見つけることです。

会社の [Azure portal](https://portal.azure.com) にアクセスできるユーザーの場合は、参加する Azure Purview アカウントを見つけ、その [アクセス制御 (IAM)] タブをクリックして、所有者またはユーザー アクセス管理者 (UAA) が誰かを確認します。 ただし、Azure Active Directory グループまたはサービス プリンシパルが所有者または UAA となっている場合があります。その場合、直接連絡することはできません。 代わりに、支援してくれる管理者を見つける必要があります。

## <a name="who-should-be-assigned-to-what-role"></a>ロールに割り当てられるユーザー

|ユーザー シナリオ|適切なロール|
|-------------|-----------------|
|資産を検索するのみで、何も編集する必要がない|Purview データ閲覧者ロール|
|資産に関する情報の編集、それらに対する分類の設定、用語集エントリへの関連付けなどを行う必要がある|Purview データ キュレーター ロール|
|用語集を編集するか、新しい分類の定義を設定する必要がある|Purview データ キュレーター ロール|
|アプリケーションのサービス プリンシパルが Azure Purview にデータをプッシュする必要がある|Purview データ キュレーター ロール|
|Purview Studio を使用してスキャンを設定する必要がある|Purview データ ソース管理者ロールに加え、Purview データ閲覧者ロールまたは Purview データ キュレーター ロール|
|サービス プリンシパルまたは他のプログラムの ID を有効にして、Azure Purview でのスキャンを設定し、監視する必要がある (プログラムの ID にはカタログ情報へのアクセスを許可しない) |Purview データ ソース管理者ロール|
|Azure Purview のロールにユーザーを割り当てる必要がある | 所有者またはユーザー アクセス管理者 |

セキュリティ プリンシパルをロールに追加する方法の詳細については、[クイックスタートのAzure Purview アカウントの作成](create-catalog-portal.md)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

* [データの分析情報](concept-insights.md)
