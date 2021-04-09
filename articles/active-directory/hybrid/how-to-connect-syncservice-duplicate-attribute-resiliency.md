---
title: ID 同期と重複属性の回復性 | Microsoft Docs
description: Azure AD Connect によるディレクトリ同期中に UPN または ProxyAddress が競合しているオブジェクトを処理する方法の新しい動作です。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 537a92b7-7a84-4c89-88b0-9bce0eacd931
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e09dd6a127bd04ae698cb6cad2ffd7f35e3b51c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94413430"
---
# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>ID 同期と重複属性の回復性
重複属性の回復性は、Microsoft のいずれかの同期ツールを実行しているときに **UserPrincipalName** と SMTP **ProxyAddress** の競合によって引き起こされる不整合を避けるために Azure Active Directory に備えられている機能です。

この 2 つの属性は、通常、特定の Azure Active Directory テナント内のすべての **User**、**Group**、および **Contact** オブジェクトにわたって一意である必要があります。

> [!NOTE]
> UPN を持てるのは、User のみです。
> 
> 

この機能によって有効になる新しい動作は、同期パイプラインのクラウド部分内にあります。そのため、クライアントに依存せず、Azure AD Connect、DirSync、MIM + Connector などの任意の Microsoft 同期製品に対応できます。 このドキュメントでは、これらの製品のいずれかを表すために、一般的な用語 "同期クライアント" を使用します。

## <a name="current-behavior"></a>現在の動作
この一意性制約に違反する UPN または ProxyAddress 値で新しいオブジェクトをプロビジョニングしようとすると、Azure Active Directory はそのオブジェクトの作成をブロックします。 同様に、一意でない UPN または ProxyAddress でオブジェクトが更新されると、更新は失敗します。 プロビジョニングの試行または更新は、エクスポート サイクルごとに同期クライアントによって再試行され、競合が解決されるまでは失敗し続けます。 試行のたびにエラー レポートの電子メールが生成され、エラーが同期クライアントによって記録されます。

## <a name="behavior-with-duplicate-attribute-resiliency"></a>重複属性の回復性による動作
属性が重複するオブジェクトのプロビジョニングまたは更新を完全に失敗させる代わりに、Azure Active Directory は一意性の制約に違反する重複属性を "検疫" します。 この属性が、UserPrincipalName のように、プロビジョニングに必要な場合、サービスはプレースホルダー値を割り当てます。 これらの一時的な値の形式は、  
_**\<OriginalPrefix>+\<4DigitNumber>\@\<InitialTenantDomain>.onmicrosoft.com**_ です。

属性の回復性プロセスでは、UPN と SMTP **ProxyAddress** の値のみが処理されます。

**ProxyAddress** のように、この属性が必須でない場合、Azure Active Directory は競合属性を検疫し、オブジェクトの作成または更新を続行します。

属性の検疫時に、競合に関する情報は、従来の動作で使用されるのと同じエラー レポート電子メールで送信されます。 ただし、この情報は、検疫が発生した際にエラー レポートに 1 回表示されるだけで、その後の電子メールではログに記録されません。 また、このオブジェクトのエクスポートは成功しているため、同期クライアントはエラーをログに記録せず、後続の同期サイクルで作成/更新操作を再試行しません。

この動作をサポートするために、次の新しい属性が User、Group、および Contact オブジェクト クラスに追加されました:  
**DirSyncProvisioningErrors**

これは複数値の属性であり、普通に追加されたら一意性の制約に違反する、競合している属性を格納します。 Azure Active Directory で、解決された重複属性の競合を検出し、該当する属性を検疫から自動的に削除するための、1 時間ごとに実行されるバックグラウンド タイマー タスクが有効になりました。

### <a name="enabling-duplicate-attribute-resiliency"></a>重複属性の回復性の有効化
重複属性の回復性は、すべての Azure Active Directory テナント全体で新しい既定の動作になります。 2016 年 8 月 22 日以降は、最初に同期を有効にしたすべてのテナントについて既定で有効になります。 この日付より前に同期を有効にしたテナントでは、この機能がバッチ処理で有効になります。 この展開は 2016年 9 月に開始し、この機能が有効になると、電子メール通知が特定の日付に各テナントの技術的通知の連絡先に送信されます。

> [!NOTE]
> 重複属性の回復性をオンにすると、無効にできません。

この機能がテナントで有効になっているかどうかを確認するには、Azure Active Directory PowerShell モジュールの最新バージョンをダウンロードして、次のように実行することによって、有効にすることができます。

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

> [!NOTE]
> テナントで重複属性の回復機能が有効になる前に、Set-MsolDirSyncFeature コマンドレットを使用してその機能を事前に有効にしておくことはできなくなりました。 この機能をテストするには、新しい Azure Active Directory テナントを作成する必要があります。

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>DirSyncProvisioningErrors を持つオブジェクトの特定
重複するプロパティの競合によってこれらのエラーが発生したオブジェクトを特定するための方法は、現在、2 つあります。それは、Azure Active Directory PowerShell と [Microsoft 365 管理センター](https://admin.microsoft.com) です。 今後のレポートに基づいてポータルを追加する拡張が予定されています。

### <a name="azure-active-directory-powershell"></a>Azure Active Directory PowerShell
このトピックの PowerShell コマンドレットには、以下のような特徴があります。

* 以下のすべてのコマンドレットが、大文字と小文字を区別します。
* **–ErrorCategory PropertyConflict** を常に含める必要があります。 現在、 **ErrorCategory** には他の型はありませんが、今後拡張される可能性があります。

まず、 **Connect-MsolService** を実行し、テナント管理者の資格情報を入力します。

次に、以下のコマンドレットと演算子を使用して、エラーをさまざまな方法で表示します。

1. [すべて表示](#see-all)
2. [プロパティの型ごと](#by-property-type)
3. [競合する値ごと](#by-conflicting-value)
4. [文字列検索を使用](#using-a-string-search)
5. 並べ替え
6. [制限した数、またはすべて](#in-a-limited-quantity-or-all)

#### <a name="see-all"></a>すべて表示
接続されたら、テナント内の属性プロビジョニング エラーの全般的な一覧を表示するために、次のように実行します。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

これにより、次のような結果が生成されます。  
 ![Get-MsolDirSyncProvisioningError](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  

#### <a name="by-property-type"></a>プロパティの型ごと
プロパティの型ごとにエラーを表示するには、**UserPrincipalName** 引数か **ProxyAddresses** 引数に、次のように **-PropertyName** フラグを追加します。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

または

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>競合する値ごと
特定のプロパティに関連するエラーを表示するには、次のように **-PropertyValue** フラグを追加します (このフラグを追加する場合は、 **-PropertyName** も使用する必要があります)。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`

#### <a name="using-a-string-search"></a>文字列検索を使用
広範な文字列検索を行うには、 **-SearchString** フラグを使用します。 このフラグは上記のすべてのフラグとは別に使用できますが、 **-ErrorCategory PropertyConflict** は例外です。このフラグとは常に一緒に使用する必要があります。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>制限した数、またはすべて
1. **MaxResults \<Int>** を使用して、クエリの値を特定の数までに制限できます。
2. **All** は、多数のエラーが存在する場合に使用すると、すべての結果を確実に取得できます。

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="microsoft-365-admin-center"></a>Microsoft 365 管理センター
Microsoft 365 管理センターでは、ディレクトリ同期エラーを表示できます。 Microsoft 365 管理センターのレポートには、これらのエラーを持つ **User** オブジェクトだけが表示されます。 **Group** と **Contact** の間の競合に関する情報は表示されません。

![Microsoft 365 管理センターでディレクトリ同期エラーを示すスクリーンショット。](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/1234.png "[アクティブ ユーザー]")

Microsoft 365 管理センターでディレクトリ同期エラーを表示する方法については、「[Microsoft 365 でディレクトリ同期エラーを確認する](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)」を参照してください。

### <a name="identity-synchronization-error-report"></a>ID 同期のエラー レポート
重複属性の競合があるオブジェクトがこの新しい動作で処理されると、テナントの技術的通知の連絡先に送信される標準の ID 同期のエラー レポート メールに、通知が含められます。 ただし、この動作には重要な変更があります。 以前は、重複属性の競合に関する情報が、競合が解決されるまで、後続のすべてのエラー レポートに含められました。 この新しい動作では、特定の競合のエラー通知は、競合する属性が検疫されたときに 1 回だけ表示されます。

ProxyAddress の競合に関する電子メール通知の例を、次に示します。  
    ![ProxyAddress の競合に関する電子メール通知の例を示すスクリーンショット。](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "[アクティブ ユーザー]")  

## <a name="resolving-conflicts"></a>競合の解決
これらのエラーのトラブルシューティングの方針と解決の方法は、以前の重複属性エラーの処理方法と変わりはありません。 唯一の違いは、タイマー タスクがサービス側のテナント全体をスイープして、競合が解決したら問題の属性を適切なオブジェクトに自動的に追加することです。

次の記事では、さまざまなトラブルシューティングと解決戦略の概要を示しています。[Office 365 でのディレクトリ同期を妨げる重複または無効な属性に関する記事](https://support.microsoft.com/kb/2647098)

## <a name="known-issues"></a>既知の問題
これらの既知の問題が、データの損失やサービスの低下を引き起こすことはありません。 いくつかは見た目の問題で、その他には、競合属性が検疫されずに、一般的な "*回復前*" 重複属性エラーがスローされる原因となる問題や、手動での修正を必要とするエラーを引き起こす問題があります。

**主要な動作:**

1. 特定の属性構成を持つオブジェクトは、検疫されている重複属性ではなく、エクスポート エラーを受信し続けます。  
   次に例を示します。
   
    a. AD で、UPN を **Joe\@contoso.com** および ProxyAddress **smtp:Joe\@contoso.com** として新しいユーザーが作成されました。
   
    b. このオブジェクトのプロパティが、ProxyAddress が **SMTP:Joe\@contoso.com** である既存の Group と競合します。
   
    c. エクスポート時に、競合属性を検疫するのではなく、**ProxyAddress 競合** エラーがスローされます。 回復性機能が有効になる前と同様に、後続の同期サイクルごとに操作が再試行されます。
2. オンプレミスで 2 つの Group が同じ SMTP アドレスで作成された場合、一方は標準の重複 **ProxyAddress** エラーのために、最初の試行でプロビジョニングに失敗します。 ただし、重複している値は、次の同期サイクル時に適切に検疫されます。

**Office ポータル レポート**:

1. UPN 競合セットの 2 つのオブジェクトの詳細なエラー メッセージは、同一です。 つまり、両方で UPN が変更/検疫されたと示されますが、実際には一方だけでデータが変更されています。
2. UPN 競合の詳細なエラー メッセージは、UPN を変更/検疫したユーザーの正しくない displayName を表示します。 次に例を示します。
   
    a. **ユーザー A** が最初に **UPN = User\@contoso.com** で同期を実行します。
   
    b. **ユーザー B** が次に **UPN = User\@contoso.com** で同期を試行します。
   
    c. **ユーザー B の** UPN が **User1234\@contoso.onmicrosoft.com** に変更され、**User\@contoso.com** が **DirSyncProvisioningErrors** に追加されます。
   
    d. **ユーザー B** のエラー メッセージには、**ユーザー A** が既に **User\@contoso.com** を UPN として持っていることを示す必要がありますが、実際には **ユーザー B** 自身の displayName が表示されます。

**ID 同期のエラー レポート**:

*この問題を解決する方法の手順* のリンクが正しくありません。  
    ![アクティブ ユーザー](./media/how-to-connect-syncservice-duplicate-attribute-resiliency/6.png "[アクティブ ユーザー]")  

指している必要があります[https://aka.ms/duplicateattributeresiliency]()。

## <a name="see-also"></a>関連項目
* [Azure AD Connect Sync](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
* [Microsoft 365 でディレクトリ同期エラーを確認する](https://support.office.com/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)