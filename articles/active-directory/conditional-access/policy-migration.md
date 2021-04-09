---
title: 条件付きアクセス ポリシーの移行 - Azure Active Directory
description: Azure Portal でクラシック ポリシーを移行するために知っておく必要があることについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1811d5b9ae4d3e34b48e1cdc156438f2bad98d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90601658"
---
# <a name="conditional-access-classic-policy-migration"></a>条件付きアクセスのクラシック ポリシーの移行

条件付きアクセスは、Azure Active Directory で使用されるツールです。このツールによって、シグナルをまとめて、決定を行い、組織のポリシーを適用することができます。 条件付きアクセスは、新しい ID ドリブン コントロール プレーンの中心になるものです。 目的は変わりませんが、新しい Azure portal のリリースにより、条件付きアクセスのしくみが大幅に改善されました。

次の理由から、Azure Portal で作成していないポリシーの移行を検討します。

- 以前は処理できなかったシナリオに対処できるようになった可能性があります。
- ポリシーを統合することで、管理する必要のあるポリシーの数を減らすことができます。
- 一元的な場所ですべての条件付きアクセス ポリシーを管理することができます。
- Azure クラシック ポータルは廃止されます。

この記事では、既存の条件付きアクセス ポリシーを新しいフレームワークに移行するために知っておく必要があることについて説明します。

## <a name="classic-policies"></a>クラシック ポリシー

[Azure portal](https://portal.azure.com) では、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** で、条件付きアクセス ポリシーを確認できます。 このページを使用して作成されていない古い条件付きアクセスポリシーが、組織に存在している場合もあります。 これらのポリシーは *クラシック ポリシー* と呼ばれます。 クラシック ポリシーは、次の場所で作成した条件付きアクセス ポリシーです。

- Azure クラシック ポータル
- Intune クラシック ポータル
- Intune App Protection ポータル

**[条件付きアクセス]** ページで、 **[管理]** セクションの [ **[クラシック ポリシー]**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) をクリックして、クラシック ポリシーにアクセスできます。 

![クラシック ポリシー ビューを示している Azure AD の条件付きアクセス](./media/policy-migration/71.png)

**[クラシック ポリシー]** ビューには以下を行うオプションがあります。

- クラシック ポリシーをフィルター処理します。
- クラシック ポリシーを無効にします。
- クラシック ポリシーの設定を確認してから無効にします。

   ![既存のポリシー構成を含むクラシック ポリシーの詳細](./media/policy-migration/74.png)

> [!WARNING]
> 無効にしたクラシック ポリシーを再度有効にすることはできません。

クラシック ポリシーの詳細ビューでは、設定の文書化、含まれるグループまたは除外されるグループの変更、およびポリシーの無効化を実行できます。

![ポリシーの詳細 - 含めるグループまたは除外するグループ](./media/policy-migration/75.png)

選択したグループを変更するか、特定のグループを除外することで、含まれるすべてのユーザーとグループに対してポリシーを無効にする前に、無効になったクラシック ポリシーの少数のテスト ユーザーに対する効果をテストできます。
 
## <a name="migration-considerations"></a>移行に関する考慮事項

この記事では、Azure AD の条件付きアクセス ポリシーを "*新しいポリシー*" とも呼びます。
クラシック ポリシーは、無効にするか削除するまで新しいポリシーと並行して動作を続けます。 

ポリシー統合のコンテキストでは次の側面が重要です。

- クラシック ポリシーは特定のクラウド アプリに関連付けられていますが、新しいポリシーで必要な場合はクラウド アプリをいくつでも選択できます。
- クラウド アプリのクラシック ポリシーと新しいポリシーのコントロールでは、すべてのコントロール (*AND*) を満たす必要があります。 
- 新しいポリシーでは、次の操作を実行できます。
   - シナリオで必要な場合に、複数の条件を結合します。 
   - 複数の許可要件をアクセス制御として選び、それらを論理 *OR* (選択したコントロールのいずれかが必要) または論理 *AND* (すべての選択したコントロールが必要) で結合します。

### <a name="exchange-online"></a>Exchange Online

**Exchange Active Sync** をクライアント アプリの条件として含む **Exchange Online** のクラシック ポリシーを移行する場合は、1 つの新しいポリシーに統合できないことがあります。 

たとえば、すべてのクライアント アプリの種類をサポートしない場合などです。 **Exchange Active Sync** をクライアント アプリの条件として持つ新しいポリシーでは、他のクライアント アプリを選択できません。

![条件付きアクセスでのクライアント アプリの選択](./media/policy-migration/64.png)

クラシック ポリシーに複数の条件が含まれている場合も、1 つの新しいポリシーに統合できません。 **Exchange Active Sync** がクライアント アプリの条件として構成されている新しいポリシーでは、他の条件はサポートされません。   

![Exchange ActiveSync では、選択された条件はサポートされません。](./media/policy-migration/08.png)

**Exchange Active Sync** がクライアント アプリの条件として構成されている新しいポリシーがある場合は、他のすべての条件が構成されていないことを確認する必要があります。 

![条件付きアクセスでの条件](./media/policy-migration/16.png)
 
**Exchange Active Sync** をクライアント アプリの条件として含む Exchange Online のアプリ ベースのクラシック ポリシーは、**サポートされている** デバイス プラットフォームにも、**サポートされていない** デバイス プラットフォームにも対応します。 関連する新しいポリシーで個々のデバイス プラットフォームを構成することはできませんが、サポートを[サポートされているデバイス プラットフォーム](concept-conditional-access-conditions.md#device-platforms)のみに制限できます。 

![条件付きアクセスでの Exchange ActiveSync の選択](./media/policy-migration/65.png)

以下がある場合は、**Exchange Active Sync** をクライアント アプリの条件として含む複数のクラシック ポリシーを統合できます。

- 条件としての **Exchange Active Sync** のみ 
- アクセス権の付与を構成するための複数の要件

1 つの一般的なシナリオは、以下の統合です。

- Azure クラシック ポータルからのデバイス ベースのクラシック ポリシー 
- Intune App Protection ポータルのアプリ ベースのクラシック ポリシー 
 
この場合、選択した両方の要件を持つ 1 つの新しいポリシーにクラシック ポリシーを統合できます。

![条件付きアクセスでの付与の制御](./media/policy-migration/62.png)

### <a name="device-platforms"></a>デバイス プラットフォーム

アプリ ベースのコントロールを持つクラシック ポリシーは、iOS と Android でデバイス プラットフォーム条件として事前に構成されています。 

新しいポリシーでは、個別にサポートする[デバイス プラットフォーム](concept-conditional-access-conditions.md#device-platforms)を選ぶ必要があります。

![条件付きアクセスでのデバイス プラットフォームの選択](./media/policy-migration/41.png)

## <a name="next-steps"></a>次のステップ

- [条件付きアクセスのレポート専用モードを使用して、新しいポリシー決定の影響を判断します。](concept-conditional-access-report-only.md)
- 条件付きアクセス ポリシーの構成方法については、「[一般的な条件付きアクセス ポリシー](concept-conditional-access-policy-common.md)」を参照してください。
- お使いの環境のための条件付きアクセス ポリシーを構成する準備ができている場合は、「[方法: Azure Active Directory の条件付きアクセスの展開を計画する](plan-conditional-access.md)」の記事を参照してください。 
