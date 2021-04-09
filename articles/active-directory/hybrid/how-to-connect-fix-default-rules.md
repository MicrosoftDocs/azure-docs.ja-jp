---
title: 既定の規則に加えた変更の修正方法 - Azure AD Connect | Microsoft Docs
description: Azure AD Connect に付属する既定の規則に加えた変更の修正方法について説明します。
services: active-directory
author: billmath
manager: daveba
editor: curtand
ms.reviewer: darora10
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0fc1bc3158e04c9b1f677af7ef2375ac3ed2ce7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91320049"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Azure AD Connect の既定の規則に加えた変更の修正

Azure Active Directory (Azure AD) Connect では、同期のために既定の規則が使用されます。  残念ながら、これらの規則はすべての組織に普遍的には適用されません。 要件に基づいて、それらを変更する必要があります。 この記事では、最も一般的なカスタマイズの例を 2 つ検討し、正しくカスタマイズを行う方法について説明します。

>[!NOTE] 
> 必要なカスタマイズを実現するために既存の既定の規則を変更することはサポートされていません。 行った場合、将来のリリースでそれらの規則を最新バージョンに更新できなくなります。 必要なバグ修正や新機能が手に入りません。 このドキュメントでは、既存の既定の規則を変更せずに、同じ結果を実現する方法を説明します。 

## <a name="how-to-identify-modified-default-rules"></a>変更された既定の規則を識別する方法
バージョン 1.3.7.0 以降の Azure AD Connect では、変更された既定の規則を簡単に識別できます。 **デスクトップのアプリ** に移動して、 **[Synchronization Rules Editor]\(同期規則エディター\)** を選択します。

![[Synchronization Rules Editor]\(同期規則エディター\) が強調表示されている Azure AD Connect](media/how-to-connect-fix-default-rules/default1.png)

エディターでは、変更されている既定の規則は名前の前の警告アイコンで示されます。

![警告アイコン](media/how-to-connect-fix-default-rules/default2.png)

 その隣には、無効になっている同じ名前の規則も表示されます (これは標準の既定の規則です)。

![標準の既定の規則と変更された既定の規則が示されている同期規則エディター](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>一般的なカスタマイズ
次に示すのは、既定の規則に対する一般的なカスタマイズです。

- 属性のフローを変更する
- スコープ フィルターを変更する
- 結合条件を変更する

規則を変更する前に:

- 同期スケジューラを無効にします。 スケジューラは、既定で 30 分ごとに実行されます。 変更中や、新しい規則のトラブルシューティング中は、スケジューラが起動されないようにします。 スケジューラを一時的に無効にするには、PowerShell を起動し、`Set-ADSyncScheduler -SyncCycleEnabled $false` を実行します。
 ![同期スケジューラを無効にする PowerShell コマンド](media/how-to-connect-fix-default-rules/default3.png)

- スコープ フィルターを変更すると、ターゲット ディレクトリ内のオブジェクトが削除される場合があります。 オブジェクトのスコープを変更する前によく注意してください。 アクティブ サーバーを変更するには、ステージング サーバーを変更することをお勧めします。
- 「[同期規則の検証](#validate-sync-rule)」セクションで説明されているように、新しい規則を追加した後は、単一のオブジェクトでプレビューを実行します。
- 新しい規則を追加した後やカスタム同期規則を変更した後は、完全同期を実行します。 この同期によって、すべてのオブジェクトに新しい規則が適用されます。

## <a name="change-attribute-flow"></a>属性のフローを変更する
属性フローの変更には 3 つの異なるシナリオがあります。
- 新しい属性を追加する。
- 既存の属性の値を上書きする。
- 既存の属性を同期しないことを選択する。

これらは、標準の既定の規則を変更しないで行うことができます。

### <a name="add-a-new-attribute"></a>新しい属性を追加する
属性がソース ディレクトリからターゲット ディレクトリにフローしていないことが分かった場合は、「[Azure AD Connect 同期: ディレクトリ拡張機能](how-to-connect-sync-feature-directory-extensions.md)」を使用してこれを修正します。

拡張機能が機能しない場合は、次のセクションで説明する 2 つの新しい同期規則を追加してみます。


#### <a name="add-an-inbound-sync-rule"></a>受信同期規則を追加する
受信同期規則は、属性のソースがコネクタ スペースであり、ターゲットがメタバースであることを意味します。 たとえば、オンプレミスの Active Directory から Azure Active Directory への新しい属性フローを追加するには、新しい受信同期規則を作成します。 **同期規則エディター** を起動し、方向として **[Inbound]\(受信\)** を選択して、 **[Add new rule]\(新しい規則の追加\)** を選択します。 

 ![[Inbound]\(受信\) と [Add new rule]\(新しい規則の追加\) が選択されている同期規則エディターを示すスクリーンショット。](media/how-to-connect-fix-default-rules/default3a.png)

独自の名前付け規則に従って規則の名前を指定します。 ここでは、「**Custom In from AD - User**」にします。 これは、規則がカスタム規則であり、Active Directory コネクタ スペースからメタバースへの受信規則であることを意味します。   

 ![受信方向の同期規則の作成](media/how-to-connect-fix-default-rules/default3b.png)

後で規則を簡単にメンテナンスできるように、規則の説明を提供します。 たとえば、規則の目的や必要な理由を基にして説明します。

**[Connected System]\(接続されているシステム\)** 、 **[Connected System Object Type]\(接続されているシステム オブジェクトの種類\)** 、 **[Metaverse Object Type]\(メタバース オブジェクトの種類\)** の各フィールドを選択します。

優先順位の値を 0 から 99 の範囲で指定します (値が小さいほど、優先順位が高くなります)。 **[Tag]\(タグ\)** 、 **[Enable Password Sync]\(パスワードの同期を有効にする\)** 、 **[Disabled]\(無効\)** の各フィールドは、既定の選択を使用します。

**[Scoping filter]\(スコープ フィルター\)** は空のままにします。 これは、Active Directory の接続されたシステムとメタバースの間のすべての結合されたオブジェクトに規則を適用することを意味します。

**[Join rules]\(結合規則\)** は空のままにします。 これは、この規則では標準の既定の規則で定義されている結合条件を使用することを意味します。 これは、標準の既定の規則を無効にしたり削除したりしてはならないもう 1 つの理由です。 結合条件がないと、属性はフローしません。 

属性に対する適切な変換を追加します。 定数を割り当てて、定数値がターゲット属性にフローするようにできます。 ソース属性またはターゲット属性の間に直接マッピングを使用できます。 または、属性に対して式を使用することができます。 使用できるさまざまな[式関数](./reference-connect-sync-functions-reference.md)は、こちらに掲載されています。

#### <a name="add-an-outbound-sync-rule"></a>送信同期規則を追加する
属性をターゲット ディレクトリにリンクするには、送信規則を作成する必要があります。 これは、ソースがメタバースであり、ターゲットが接続されたシステムであることを意味します。 送信規則を作成するには、**同期規則エディター** を起動し、 **[Direction]\(方向\)** を **[Outbound]\(送信\)** に変更して、 **[Add new rule]\(新しい規則の追加\)** を選択します。 

![同期規則エディター](media/how-to-connect-fix-default-rules/default3c.png)

受信規則と同様に、独自の名前付け規則を使用して規則に名前 を付けます。 Azure AD テナントとして **[Connected System]\(接続されたシステム\)** を選択し、属性値を設定する接続されたシステムのオブジェクトを選択します。 優先順位を 0 から 99 の間で設定します。 

![送信方向の同期規則の作成](media/how-to-connect-fix-default-rules/default3d.png)

**[Scoping filter]\(スコープ フィルター\)** と **[Join rules]\(結合規則\)** は空のままにします。 変換を定数、直接、または式として入力します。 

これで、ユーザー オブジェクトの新しい属性を Active Directory から Azure Active Directory にフローさせる方法がわかりました。 この手順を使用して、任意の属性を任意のオブジェクトからソースとターゲットにマッピングできます。 詳細については、[カスタム同期規則の作成](how-to-connect-create-custom-sync-rule.md)と[ユーザーをプロビジョニングする準備](/office365/enterprise/prepare-for-directory-synchronization)に関する説明を参照してください。

### <a name="override-the-value-of-an-existing-attribute"></a>既存の属性の値をオーバーライドする
既にマップされている属性の値をオーバーライドすることがあります。 たとえば、常に Azure AD の属性に null 値を設定したい場合は、単に受信規則のみを作成します。 定数値 `AuthoritativeNull` がターゲットの属性にフローするようにします。 

>[!NOTE] 
> この場合は、`AuthoritativeNull` の代わりに `Null` を使用します。 これは、優先順位が低い (規則の数値が大きい) 場合でも、null 以外の値は null 値に置き換わるためです。 一方で、`AuthoritativeNull` が他の規則によって null 以外の値に置き換えられることはありません。 

### <a name="dont-sync-existing-attribute"></a>既存の属性を同期しない
属性を同期から除外する場合は、Azure AD Connect が提供する属性フィルター機能を使用します。 デスクトップ アイコンから **Azure AD Connect** を起動し、 **[同期オプションのカスタマイズ]** を選択します。

![Azure AD Connect のその他のタスクのオプション](media/how-to-connect-fix-default-rules/default4.png)

 **[Azure AD アプリと属性フィルター]** が選択されていることを確認して、 **[次へ]** を選択します。

![Azure AD Connect オプション機能](media/how-to-connect-fix-default-rules/default5.png)

同期から除外する属性をオフにします。

![Azure AD Connect の属性](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="change-scoping-filter"></a>スコープ フィルターを変更する
ほとんどのオブジェクトは Azure AD Sync によって処理されます。 標準の既定の同期規則を変更することなく、オブジェクトのスコープを縮小し、エクスポートされるオブジェクトの数を減らすことができます。 

同期するオブジェクトのスコープを縮小するには、次のいずれかの方法を使用します。

- cloudFiltered 属性
- 組織単位のフィルター処理

同期されるユーザーのスコープを縮小した場合、フィルターで除外されるユーザーのパスワード ハッシュの同期も停止します。 オブジェクトが既に同期している場合、スコープを縮小した後で、フィルターによって除外されたオブジェクトはターゲット ディレクトリから削除されます。 このため、スコープは極めて慎重に設定してください。

>[!IMPORTANT] 
> Azure AD Connect によって構成されているオブジェクトのスコープを拡大することはお勧めしません。 そのようにすると、Microsoft サポート チームがカスタマイズを理解するのが困難になります。 オブジェクトのスコープを拡大する必要がある場合は、既存の規則を編集し、それを複製して、元の規則を無効にします。 

### <a name="cloudfiltered-attribute"></a>cloudFiltered 属性
Active Directory でこの属性を設定することはできません。 この属性の値を設定するには、新しい受信規則を追加します。 その後、**変換** と **式** を使用して、メタバースでこの属性を設定できます。 部署名が **HRD** (大文字と小文字の区別なし) で始まるすべてのユーザーを同期しない場合の例を次に示します。

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

最初に、ソース (Active Directory) からの部署を小文字に変換します。 次に、`Left` 関数を使って最初の 3 文字のみを取り出し、それを `hrd` と比較します。 一致する場合は、値を `True` に設定し、それ以外の場合は `NULL` にします。 値を null に設定しても、優先順位の低い (高い数値) 他の規則によって異なる条件が書き込まれる可能性があります。 「[同期規則の検証](#validate-sync-rule)」セクションで説明されているように、1 つのオブジェクトでプレビューを実行して同期規則を検証します。

![受信方向の同期規則の作成のオプション](media/how-to-connect-fix-default-rules/default7a.png)

### <a name="organizational-unit-filtering"></a>組織単位のフィルター処理
1 つまたは複数の組織単位 (OU) を作成し、同期しないオブジェクトをその OU に移動できます。 次に、Azure AD Connect で OU のフィルター処理を構成します。 デスクトップ アイコンから **Azure AD Connect** を起動し、次のオプションを選択します。 Azure AD Connect のインストール時に OU フィルタリングを構成することもできます。 

![Azure AD Connect のその他のタスク](media/how-to-connect-fix-default-rules/default8.png)

ウィザードの指示に従い、同期しない OU をオフにします。

![Azure AD Connect のドメインと OU のフィルター処理オプション](media/how-to-connect-fix-default-rules/default9.png)

## <a name="change-join-condition"></a>結合条件を変更する
Azure AD Connect によって構成された既定の結合条件を使用します。 既定の結合条件を変更すると、Microsoft サポートがカスタマイズ内容を理解し、製品をサポートするのが難しくなります。

## <a name="validate-sync-rule"></a>同期規則の検証
プレビュー機能を使用すると、完全同期サイクルを実行しなくても、新しく追加された同期規則を検証できます。 Azure AD Connect で **[Synchronization Service]\(同期サービス\)** を選択します。

![同期サービスが強調表示されている Azure AD Connect](media/how-to-connect-fix-default-rules/default10.png)

**[Metaverse Search]\(メタバースの検索\)** を選択します。 スコープ オブジェクトとして **[person]\(人\)** を選択し、 **[Add Clause]\(句の追加\)** を選択して、検索条件を指定します。 次に、 **[Search]\(検索\)** を選択し、検索結果内のオブジェクトをダブルクリックします。 このステップを実行する前に、フォレストでインポートと同期を実行して、Azure AD Connect のデータがそのオブジェクトの最新のものになるようにします。

![Synchronization Service Manager](media/how-to-connect-fix-default-rules/default11.png)

**[Metaverse Object Properties]\(メタバース オブジェクトのプロパティ\)** で **[Connectors]\(コネクタ\)** を選択し、対応するコネクタ (フォレスト) でオブジェクトを選択して、 **[Properties…]\(プロパティ...\)** を選択します。

![[Metaverse Object Properties (メタバース オブジェクトのプロパティ)]](media/how-to-connect-fix-default-rules/default12.png)

**[Preview…]\(プレビュー...\)** を選択します

![コネクタ スペース オブジェクトのプロパティ](media/how-to-connect-fix-default-rules/default13a.png)

[Preview]\(プレビュー\) ウィンドウで **[Generate Preview]\(プレビューの生成\)** を選択し、左側のウィンドウで **[Import Attribute Flow]\(インポート属性フロー\)** を選択します。

![[Import Attribute Flow]\(インポート属性フロー\) と [Generate Preview]\(プレビューの生成\) が選択されている [Preview]\(プレビュー\) ウィンドウを示すスクリーンショット。](media/how-to-connect-fix-default-rules/default14.png)
 
新しく追加した規則がオブジェクト上で実行され、`cloudFiltered` 属性が true に設定されていることがここでわかります。

![プレビュー](media/how-to-connect-fix-default-rules/default15a.png)
 
変更された規則と既定の規則を比較するには、両方の規則を個別にテキスト ファイルとしてエクスポートします。 これらの規則は、PowerShell スクリプト ファイルとしてエクスポートされます。 ファイル比較ツール (たとえば windiff) を使用してそれらを比較し、変更を確認できます。 
 
変更後の規則では、`msExchMailboxGuid` 属性の種類が **Direct** ではなく **Expression** に変更されていることに注意してください。 また、値が **NULL** および **ExecuteOnce** オプションに変更されています。 Identified と Precedence の相違は無視してかまいません。 

![windiff ツールの出力](media/how-to-connect-fix-default-rules/default17.png)
 
規則を修正して既定の設定に戻すには、変更された規則を削除し、既定の規則を有効にします。 実現しようとしているカスタマイズが失われないように確認します。 準備ができたら、**完全同期** を実行します。

## <a name="next-steps"></a>次のステップ
- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [簡単設定](how-to-connect-install-express.md)
- [カスタマイズした設定](how-to-connect-install-custom.md)