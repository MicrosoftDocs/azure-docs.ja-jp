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
ms.topic: conceptual
ms.date: 03/21/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f524e8cef3878816cec53575217bdb6d0fd9be7b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500933"
---
# <a name="fix-modified-default-rules-in-azure-ad-connect"></a>Azure AD Connect の既定の規則に加えた変更の修正

Azure AD Connect には、同期のための既定の規則が付属しています。  残念ながら、これらの規則はすべての組織に当てはまるわけではなく、要件に基づいた変更が必要になる場合もあるかもしれません。

 既定の規則を変更した場合や変更しようとしている場合は、少しの時間を取ってこのドキュメントを読んでください。

このドキュメントでは、ユーザーが行う最も一般的なカスタマイズの例を 2 つ紹介し、正しくカスタマイズを行う方法について説明します。

>[!NOTE] 
> 必要なカスタマイズを実現するために既存の既定の規則を変更することはサポートされていません。これを行うと、今後のリリースでこれらの規則が最新バージョンに更新されなくなります。 これにより、必要なバグ修正や新機能が得られなくなります。  このドキュメントでは、既存の既定の規則を変更せずに、同じ結果を実現する方法を説明します。 

## <a name="how-to-identify-modified-default-rules"></a>既定の規則に加えた変更を特定する方法
バージョン 1.3.7.0 以降の **Azure AD Connect** では、既定の規則に加えた変更を簡単に特定できるようになりました。 デスクトップのアプリに移動して、**[同期規則エディター]** をクリックします。

![エディター](media/how-to-connect-fix-default-rules/default1.png)

エディターでは、既定の規則に加えた変更がある場合、名前の前に次のアイコンが表示されます。

![icon](media/how-to-connect-fix-default-rules/default2.png)

 また、その隣には、無効になった同じ名前の規則が表示されます。これは標準の既定の規則です。

![既定の規則](media/how-to-connect-fix-default-rules/default2a.png)

## <a name="common-customizations"></a>一般的なカスタマイズ
次に示すのは、既定の規則に対する一般的なカスタマイズです。

- [属性フローの変更](#changing-attribute-flow)
- [スコープ フィルターの変更](#changing-scoping-filter)
- [結合条件の変更](#changing-join-condition)

## <a name="before-changing-any-rules"></a>規則を変更する前に
- 同期スケジューラを無効にします。  スケジューラは、既定で 30 分ごとに実行されます。 変更中や、新しい規則のトラブルシューティング中は、スケジューラが起動されないようにします。 スケジューラを一時的に無効にするには、PowerShell を起動し、`Set-ADSyncScheduler -SyncCycleEnabled $false` を実行します。
 ![既定の規則](media/how-to-connect-fix-default-rules/default3.png)

- スコープ フィルターを変更すると、ターゲット ディレクトリ内のオブジェクトが削除される場合があります。 オブジェクトのスコープに変更を加える場合は注意してください。 アクティブ サーバーに変更を加える前に、ステージング サーバーを変更することをお勧めします。
- 「[同期規則の検証](#validate-sync-rule)」のセクションでも説明しますが、新しい規則を追加した後は、単一のオブジェクトに対してプレビューを実行してください。
- 新しい規則を追加した後やカスタム同期規則を変更した後は、完全同期を実行してください。 この同期によって、すべてのオブジェクトに新しい規則が適用されます。

## <a name="changing-attribute-flow"></a>属性フローの変更
属性フローには、3 つの異なるシナリオがあります。標準の既定の規則を変更せずにそれを実現する方法を見てみましょう。
- 新しい属性を追加する
- 既存の属性の値を上書きする
- 既存の属性を同期しない

### <a name="adding-new-attribute"></a>新しい属性を追加する:
属性がソース ディレクトリからターゲット ディレクトリにフローしていないことが分かった場合は、「[Azure AD Connect 同期: ディレクトリ拡張機能](how-to-connect-sync-feature-directory-extensions.md)」を使って新しい属性をフローします。

最初の選択肢は、Azure AD Connect が提供し、すぐに使える機能である「[Azure AD Connect 同期: ディレクトリ拡張機能](how-to-connect-sync-feature-directory-extensions.md)」を使用することである点に注意してください。 ただし、これで解決できない場合は、次の手順を実行して既存の標準の既定の同期規則を変更せずに属性をフローします。これは、2 つの新しい同期規則を追加することで行うことができます。


#### <a name="add-an-inbound-sync-rule"></a>受信同期規則を追加する:
受信同期規則は、属性のソースがコネクタ スペースであり、ターゲットがメタバースであることを意味します。 たとえば、オンプレミスの Active Directory から Azure Active Directory に新しい属性をフローする場合は、**同期規則エディター**を起動して新しい受信同期規則を作成し、方向] として **[受信]** 選択して、**[新しい規則の追加]** をクリックします。 

 ![既定の規則](media/how-to-connect-fix-default-rules/default3a.png)

規則には、独自の名前付け規則に従って名前を付けます。ここでは、**Custom In from AD - User** を使用しました。これは、規則がカスタムの規則であり、AD コネクタ スペースからメタバースへの受信規則であることを意味しています。 

 ![既定の規則](media/how-to-connect-fix-default-rules/default3b.png)

将来の規則のメンテナンスが簡単になるように、規則に説明を追加します。たとえば、規則の目的や規則が必要な理由を記述します。
属性のソースとなる接続先システム (フォレスト) を選択します。 続いて、[接続先システム オブジェクトの種類] と [メタバース オブジェクトの種類] を選択します。

優先順位の値を 0 – 99 で指定します (数が少ないほど、優先順位が高くなります)。 [タグ]、[パスワードの同期を有効にする]、[無効] などの他のフィールドは既定値を保持します。

[スコープ フィルター] は空のままにします。これは、AD 接続先システムとメタバースの間に結合しているすべてのオブジェクトに規則を適用することを意味します。

[結合規則] は空のままにします。これは、この規則が標準の既定の規則で定義されている結合条件に便乗することを意味します。 これは、標準の既定の規則を無効化または削除しないもう 1 つの理由です。なぜなら、便乗する結合条件が存在しない場合、属性はフローしないからです。 

属性にとって適切な変換を追加します。定数をターゲット属性にフローさせたるために定数を割り当てたり、ソースまたはターゲットの属性の直接マッピングを割り当てたり、属性の式を割り当てたりすることができます。 使用できるさまざまな[式関数](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-sync-functions-reference)は、こちらに掲載されています。

#### <a name="add-an-outbound-sync-rule"></a>送信同期規則を追加する:
ここまでで行った受信同期規則だけを追加する手順では、属性がまだターゲット ディレクトリにリンクされていないので、作業は半分しか終わっていません。 属性をターゲット ディレクトリにリンクするには、送信規則を作成する必要があります。送信規則は、ソースがメタバースで、ターゲットは接続先システムになります。 送信規則を作成するには、**同期規則エディター**を起動し、**[方向]** を **[送信]** に変更して、**[新しい規則の追加]** をクリックします。 

![既定の規則](media/how-to-connect-fix-default-rules/default3c.png)

受信規則と同様に、独自の名前付け規則を使用して規則に **[名前]** を付けます。 Azure AD テナントとしての **[接続先システム]** を選択し、属性値を設定する接続先システム オブジェクトを選択します。 優先順位を 0 - 99 の間で設定します。 

![既定の規則](media/how-to-connect-fix-default-rules/default3d.png)

**[スコープ フィルター]** と **[結合規則]** は空のままにし、定数、直接または式として変換を入力します。 

この例では、ユーザー オブジェクトの新しい属性を Active Directory から Azure Active Directory にフローする方法を説明してきました。 この手順を使用して、任意の属性を任意のオブジェクトからソースとターゲットにマッピングできます。  詳細については、[カスタム同期規則の作成](how-to-connect-create-custom-sync-rule.md)と[ユーザーをプロビジョニングする準備](https://docs.microsoft.com/office365/enterprise/prepare-for-directory-synchronization)に関する説明を参照してください。

### <a name="overriding-value-of-existing-attribute"></a>既存の属性の値を上書きする
Azure AD のある属性に常に Null 値を設定したい場合など、既にマッピングされている属性の値を上書きする場合は、先ほどの手順で説明した受信規則のみを作成して、ターゲット属性に **AuthoritativeNull** 定数値をフローさせることができます。 この場合は、Null の代わりに AuthoritativeNull を使っていることに注意してください。 これは、優先順位が低い (規則の数値が大きい) 場合でも、Null 以外の値は Null 値に置き換わるためです。 ただし、AuthoritativeNull は Null として扱われ、他の規則によって Null 以外の値で置き換えられることはありません。 

### <a name="dont-sync-existing-attribute"></a>既存の属性を同期しない
属性を同期から除外する場合は、Azure AD Connect が提供する属性フィルター機能を使用できます。 デスクトップ アイコンから **Azure AD Connect** を起動し、**[同期オプションのカスタマイズ]** を選択します。

![既定の規則](media/how-to-connect-fix-default-rules/default4.png)

 **[Azure AD アプリと属性フィルター]** が選択されていることを確認して **[次へ]** をクリックします。

![既定の規則](media/how-to-connect-fix-default-rules/default5.png)

同期から除外する属性をオフにします。

![既定の規則](media/how-to-connect-fix-default-rules/default6a.png)

## <a name="changing-scoping-filter"></a>スコープ フィルターの変更
Azure AD Sync は、オブジェクトのほとんどの処理を行います。標準の既定の同期規則を変更せずに、少量のオブジェクトがサポートされている方法でエクスポートされるように、オブジェクトのスコープを縮小することができます。 その後、オブジェクトのスコープを拡大する場合は、既存の規則を **[編集]** し、それを複製して元の規則を無効にします。 Microsoft は、Azure AD Connect が構成したスコープを拡大しないことをお勧めします。 オブジェクトのスコープを拡大すると、サポート チームがカスタマイズ内容を理解し、製品をサポートするのが難しくなります。

Azure AD に同期するオブジェクトのスコープを縮小する方法を以下に示します。 同期されている**ユーザー**のスコープを縮小した場合、フィルター外のユーザーのパスワード ハッシュの同期も停止する点に注意してください。 オブジェクトが既に同期している場合、スコープを縮小するとフィルター外のオブジェクトはターゲット ディレクトリから削除されます。スコープを操作する場合は慎重に行ってください。
サポートされている方法で同期しているオブジェクトのスコープを縮小するには、次のようにします。

- [cloudFiltered 属性](#cloudfiltered-attribute)
- [OU フィルタリング](#ou-filtering)

### <a name="cloudfiltered-attribute"></a>cloudFiltered 属性
これは Active Directory で設定できる属性ではないことに注意してください。 「**既存の属性の値を上書きする**」セクションで説明したように、新しい受信規則を追加してこの属性の値を設定する必要があります。 その後、**変換**と**式**を使用すると、メタバースでこの属性を設定できます。 大文字と小文字の区別をせず、部署名が**HRD**で始まるすべてのユーザーを同期しない場合の例を次に示します。

`cloudFiltered <= IIF(Left(LCase([department]), 3) = "hrd", True, NULL)`

最初に、ソース (Active Directory) からの部署を小文字に変換します。 次に、Left 関数を使って最初の 3 文字のみを取り出し、hrd と比較します。 一致する場合は True を、それ以外の場合は NULL を設定します。 優先順位の低い (高い数値) の他の規則が別の条件を書き込めるように、NULL 値を設定している点に注意してください。 「[同期規則の検証](#validate-sync-rule)」のセクションでも説明しますが、同期規則を検証するために、1 つのオブジェクトでプレビューを実行してください。

![既定の規則](media/how-to-connect-fix-default-rules/default7a.png)



### <a name="ou-filtering"></a>OU フィルタリング
1 つまたは複数の OU を作成し、同期しないオブジェクトをその OU に移動できます。 その後、デスクトップ アイコンから **Azure AD Connect** を起動して次に示すオプションを選択し、Azure AD Connect で OU フィルタ リングを構成します。 Azure AD Connect のインストール時に OU フィルタリングを構成することもできます。 

![既定の規則](media/how-to-connect-fix-default-rules/default8.png)

ウィザードの指示に従い、同期しない OU の選択を解除します。

![既定の規則](media/how-to-connect-fix-default-rules/default9.png)

## <a name="changing-join-condition"></a>結合条件の変更
Microsoft は、Azure AD Connect が構成した既定の結合条件を使用することをお勧めします。 既定の結合条件を変更すると、サポート チームがカスタマイズ内容を理解し、製品をサポートするのが難しくなります。

## <a name="validate-sync-rule"></a>同期規則の検証
プレビュー機能を使用すると、完全同期サイクルを実行しなくても、新しく追加された同期規則を検証できます。 **Synchronization Service** UI を起動します。

![既定の規則](media/how-to-connect-fix-default-rules/default10.png)

**[メタバース検索]** をクリックしてスコープ オブジェクトで **[人]** を選択肢、**[句の追加]** を行って検索条件を指定します。 **[検索]** ボタンをクリックし、**[検索結果]** 内のオブジェクトをダブルクリックします。この手順を実行する前に、フォレストでインポートと同期を実行してください。これは、Azure AD Connect のデータがそのオブジェクトの最新であることを保証するためです。

![既定の規則](media/how-to-connect-fix-default-rules/default11.png)



**[コネクタ]** を選択し、対応するコネクタ (フォレスト) でオブジェクトを選択したうえで、**[プロパティ...]** をクリックします。

![既定の規則](media/how-to-connect-fix-default-rules/default12.png)

**[プレビュー...]** をクリックします。

![既定の規則](media/how-to-connect-fix-default-rules/default13a.png)

**[Generate Preview]** \(プレビューの生成\) をクリックし、左側のウィンドウで **[インポート属性フロー]** をクリックします。

![既定の規則](media/how-to-connect-fix-default-rules/default14.png)
 
新しく追加された規則がオブジェクト上で実行され、cloudFiltered 属性を True に設定していることがここでわかります。

![既定の規則](media/how-to-connect-fix-default-rules/default15a.png)
 
変更を加えた規則を既定の規則と比較する方法
両方の規則を個別にテキスト ファイルとしてエクスポートできます。 これらの規則は、PowerShell スクリプト ファイルとしてエクスポートされます。 任意のファイル比較ツールを使用してそれらを比較し、どのような種類の変更が行われたかを確認できます。 次の例では、windiff を使用して 2 つのファイルを比較しています。
 
ユーザーが変更を加えた規則では、msExchMailboxGuid 属性が **Direct** 型ではなく **Expression** 型の **NULL** 値となっており、**ExecuteOnce** オプションが指定されていることがわかります。 Identified と Precedence の相違は無視してかまいません。 

![既定の規則](media/how-to-connect-fix-default-rules/default17.png)
 
既定の規則に加えた変更を修正する方法
規則を修正して既定の設定に戻すには、次に示すように、変更を加えた規則を削除し、既定の規則を有効にします。その後、**[完全同期]** を実行します。 これを行う前に、行おうとしているカスタマイズが失われないように、上記の是正措置を実施します## 次の手順

## <a name="next-steps"></a>次の手順
- [ハードウェアおよび前提条件](how-to-connect-install-prerequisites.md) 
- [簡単設定](how-to-connect-install-express.md)
- [カスタマイズした設定](how-to-connect-install-custom.md)

