---
title: 'Azure AD Connect 同期: 既定の構成について | Microsoft Docs'
description: この記事では、Azure AD Connect 同期の既定の構成について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ed876f22-6892-4b9d-acbe-6a2d112f1cd1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e55526e0a63a0c603e2b62ccb3ac0efed911cff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996629"
---
# <a name="azure-ad-connect-sync-understanding-the-default-configuration"></a>Azure AD Connect 同期: 既定の構成について
この記事では、既定の構成ルールについて説明します。 規則とそれが構成に与える影響について記載されています。 また、Azure AD Connect 同期の既定の構成についても説明します。この記事の目標は、宣言型のプロビジョニングと呼ばれる構成モデルのしくみを実例を用いて読者に理解してもらうことです。 この記事では、インストール ウィザードを使用して既に Azure AD Connect 同期をインストールし、構成していることを前提としています。

この構成モデルについて詳しくは、「 [Understanding Declarative Provisioning (宣言型のプロビジョニングについて)](concept-azure-ad-connect-sync-declarative-provisioning.md)」をご覧ください。

## <a name="out-of-box-rules-from-on-premises-to-azure-ad"></a>オンプレミスから Azure AD への既定のルール
次の表現は既定の構成にあります。

### <a name="user-out-of-box-rules"></a>ユーザーの既定のルール
これらの規則は iNetOrgPerson オブジェクト タイプにも適用されます。

ユーザー オブジェクトは次の要件を満たさないと同期されません。

* sourceAnchor が含まれている必要があります。
* オブジェクトを Azure AD で作成した後に sourceAnchor を変更することはできません。 オンプレミスで値が変更された場合、sourceAnchor が前の値に戻るまでオブジェクトは同期を停止します。
* accountEnabled (userAccountControl) 属性が入力されている必要があります。 オンプレミスの Active Directory があれば、この属性は常に存在し、入力されています。

次のユーザー オブジェクトは Azure AD に同期 **されません** 。

* `IsPresent([isCriticalSystemObject])`. 組み込み管理者アカウントなど、Active Directory の既定のオブジェクトの多くは同期されません。
* `IsPresent([sAMAccountName]) = False`. sAMAccountName 属性のないユーザー オブジェクトは同期されません。 このような局面は、現実的には NT4 からアップグレードされたドメインでのみ発生します。
* `Left([sAMAccountName], 4) = "AAD_"`, `Left([sAMAccountName], 5) = "MSOL_"`. Azure AD Connect 同期と以前のバージョンで使用されるサービス アカウントは同期しないでください。
* Exchange Online で機能しない Exchange アカウントは同期しないでください。
  * `[sAMAccountName] = "SUPPORT_388945a0"`
  * `Left([mailNickname], 14) = "SystemMailbox{"`
  * `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`
  * `(Left([sAMAccountName], 4) = "CAS_" && (InStr([sAMAccountName], "}")> 0))`
* Exchange Online で機能しないオブジェクトは同期しないでください。
  `CBool(IIF(IsPresent([msExchRecipientTypeDetails]),BitAnd([msExchRecipientTypeDetails],&H21C07000) > 0,NULL))`  
  このビットマスク (&H21C07000) で次のオブジェクトが除外されます。
  * メールが有効なパブリック フォルダー (バージョン 1.1.524.0 時点でプレビュー)
  * システム アテンダント メールボックス
  * メールボックス データベース メールボックス (システム メールボックス)
  * ユニバーサル セキュリティ グループ (ユーザーには適用されませんが、旧システムのために存在します)
  * 非ユニバーサル グループ (ユーザーには適用されませんが、旧システムのために存在します)
  * メールボックス プラン
  * 探索メールボックス
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. レプリケーション対象オブジェクトは同期しないでください。

次の属性ルールが適用されます。

* `sourceAnchor <- IIF([msExchRecipientTypeDetails]=2,NULL,..)`. sourceAnchor 属性は、リンクされているメールボックスから提供されません。 リンクされているメールボックスが見つかった場合、後で実際のアカウントが結合されるものと想定されています。
* Exchange 関連属性は、属性 **mailNickName** に値が含まれる場合にのみ同期されます。
* 複数のフォレストが存在するとき、属性は次の順序で利用されます。
  1. サインイン関連の属性 (userPrincipalName など) はアカウントが有効になっているフォレストから提供されます。
  2. Exchange GAL (グローバル アドレス一覧) で見つかる属性は Exchange Mailbox のあるフォレストから提供されます。
  3. メールボックスが見つからない場合、これらの属性はあらゆるフォレストから取得できます。
  4. Exchange 関連の属性 (GAL で表示されない技術属性) は、 `mailNickname ISNOTNULL`の条件を満たすフォレストから提供されます。
  5. 以上の規則のいずれかを満たすフォレストが複数存在する場合、コネクタ (フォレスト) の作成順序 (日付/時刻) を利用し、属性を提供するフォレストが決定されます。 接続されている最初のフォレストが、最初に同期するフォレストになります。 

### <a name="contact-out-of-box-rules"></a>連絡先の既定のルール
連絡先オブジェクトは次の要件を満たさないと同期されません。

* 連絡先はメール対応である必要があります。 次のルールで検証されます。
  * `IsPresent([proxyAddresses]) = True)`. proxyAddresses 属性に入力する必要があります。
  * プライマリ電子メール アドレスは proxyAddresses 属性とメール属性のいずれかにあります。 \@ が存在することで、コンテンツが電子メールであることが確認されます。 これら 2 つの規則のいずれかを評価した結果、True になる必要があります。
    * `(Contains([proxyAddresses], "SMTP:") > 0) && (InStr(Item([proxyAddresses], Contains([proxyAddresses], "SMTP:")), "@") > 0))`. "SMTP:" が含まれるエントリはありますか。エントリがある場合、文字列に \@ は含まれますか。
    * `(IsPresent([mail]) = True && (InStr([mail], "@") > 0)`. メール属性は入力されますか。入力される場合、文字列に \@ は含まれますか。

次の連絡先オブジェクトは Azure AD に同期 **されません** 。

* `IsPresent([isCriticalSystemObject])`. クリティカルとしてマークされている連絡先オブジェクトは同期されないようにします。 既定の構成を含むオブジェクトにはなりません。
* `((InStr([displayName], "(MSOL)") > 0) && (CBool([msExchHideFromAddressLists])))`.
* `(Left([mailNickname], 4) = "CAS_" && (InStr([mailNickname], "}") > 0))`. これらのオブジェクトは Exchange Online では動作しません。
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. レプリケーション対象オブジェクトは同期しないでください。

### <a name="group-out-of-box-rules"></a>グループの既定のルール
グループ オブジェクトは次の要件を満たさないと同期されません。

* メンバーは 50,000 以下にする必要があります。 この数は、オンプレミス グループのメンバー数です。
  * 最初に同期を開始する前にこの数を超えるメンバーが含まれている場合、グループは同期されません。
  * 最初に作成したときからメンバーの数が増え、50,000 に到達すると、再び 50,000 以下になるまで同期は停止します。
  * 注:50,000 というメンバー数は Azure AD からも強制されます。 この規則を変更または削除した場合でも、メンバー数の多いグループを同期することはできません。
* グループが **配布グループ** の場合、メール対応にする必要もあります。 このルールの強制については、「 [連絡先の既定のルール](#contact-out-of-box-rules) 」を参照してください。

次のグループ オブジェクトは Azure AD に同期 **されません** 。

* `IsPresent([isCriticalSystemObject])`. 組み込み管理者グループなど、Active Directory の既定のオブジェクトの多くは同期されません。
* `[sAMAccountName] = "MSOL_AD_Sync_RichCoexistence"`. DirSync で使用される旧グループ。
* `BitAnd([msExchRecipientTypeDetails],&amp;H40000000)`. ロール グループ。
* `CBool(InStr(DNComponent(CRef([dn]),1),"\\0ACNF:")>0)`. レプリケーション対象オブジェクトは同期しないでください。

### <a name="foreignsecurityprincipal-out-of-box-rules"></a>ForeignSecurityPrincipal の既定のルール
FSP はメタバースの "あらゆる" (\*) オブジェクトに参加します。 この結合は、現実的にはユーザーとセキュリティ グループのみで発生します。 この構成により、フォレスト間のメンバーシップが解決され、Azure AD に正しく表示されます。

### <a name="computer-out-of-box-rules"></a>コンピューターの既定のルール
コンピューター オブジェクトは次の要件を満たさないと同期されません。

* `userCertificate ISNOTNULL`. Windows 10 コンピューターでのみこの属性が入力されます。 この属性に値があるすべてのコンピューター オブジェクトが同期されます。

## <a name="understanding-the-out-of-box-rules-scenario"></a>既定のルールのシナリオを理解する
この例では、1 つのアカウント フォレスト (A)、1 つのリソース フォレスト (R)、1 つの Azure AD ディレクトリが含まれるデプロイを使用します。

![Picture with scenario description](./media/concept-azure-ad-connect-sync-default-configuration/scenario.png)

この構成では、アカウント フォレストには有効なアカウント、リンクされたメールボックスを持つリソース フォレストには無効なアカウントが、それぞれ存在すると仮定します。

この既定の構成での目標は次のとおりです。

* サインインに関連した属性を、有効なアカウントが存在するフォレストから同期する。
* GAL (グローバル アドレス一覧) で見つかる属性を、メールボックスを持つフォレストから同期する。 メールボックスが見つからない場合は、他のフォレストが使用されます。
* リンクされたメールボックスが見つかった場合、Azure AD にエクスポートされるオブジェクトのリンクされた有効なアカウントが見つかる。

### <a name="synchronization-rule-editor"></a>同期規則エディター
この構成は、同期規則エディター (SRE) ツールを使用して表示および変更できます。また、[スタート] メニューには、このツールのショートカットもあります。

![Synchronization Rules Editor icon](./media/concept-azure-ad-connect-sync-default-configuration/sre.png)

SRE は、リソース キット ツールで、Azure AD Connect 同期と共にインストールされます。このツールを起動できるようにするには、ADSyncAdmins グループのメンバーである必要があります。 ツールを起動すると、次のような画面が表示されます。

![同期規則、受信](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

このウィンドウには、構成に対して作成されたすべての同期規則が表示されます。 表内の各行は 1 つの同期規則です。 左側の [ルールの種類] の下に、[受信] と [送信] という 2 つの異なるタイプが表示されます。 [着信] および [送信] は、メタバースのビューに基づきます。 この概要では主に、受信に関する規則に注目します。 同期規則の一覧の実際の内容は、AD で検出されたスキーマに応じて異なります。 上の図のアカウント フォレスト (fabrikamonline.com) には、Exchange や Lync などのサービスがないため、これらのサービスの同期規則は作成されていません。 これに対して、リソース フォレスト (res.fabrikamonline.com) には、このようなサービスの同期規則があります。 規則の内容は、検出されたバージョンによって異なります。 たとえば、Exchange 2013 によるデプロイの場合、Exchange 2010 や Exchange 2007 よりも多くの属性フローが構成されています。

### <a name="synchronization-rule"></a>同期規則
同期規則は、条件が満たされた場合に、フローする属性のセットを含む構成オブジェクトです。 また、これを使用して、**結合** または **一致** と呼ばれる、コネクタ スペース内のオブジェクトとメタバース内のオブジェクトとの関連付けを記述することもできます。 同期規則には、互いがどのように関連するのかを示す優先順位値があります。 数値が低い同期規則ほど、優先順位が高くなります。属性のフローの競合が発生した場合には、優先順位の高い同期規則が優先される形で競合が解決されます。

例として、同期規則 " **AD からの受信 - ユーザー アカウント有効**" を使って説明します。 SRE でこの行をマークし、 **[編集]** を選択します。

この規則は既定の規則であるため、開くと警告が表示されます。 [既定の規則に変更を加える](how-to-connect-sync-best-practices-changing-default-configuration.md)ことは避ける必要があるため、その意図を尋ねるメッセージが表示されます。 今回の場合、必要なのは規則の表示のみです。 このため、 **[いいえ]** を選択します。

![Synchronization Rules warning](./media/concept-azure-ad-connect-sync-default-configuration/warningeditrule.png)

同期規則には、[説明]、[スコープ フィルター]、[結合規則]、[変換] という 4 つの構成セクションがあります。

#### <a name="description"></a>説明
最初のセクションでは、名前や説明などの基本的な情報を提供します。

![Description tab in Sync rule editor](./media/concept-azure-ad-connect-sync-default-configuration/syncruledescription.png)

この規則が関連する接続先システム、この規則の適用される接続先システム、およびメタバース オブジェクトのタイプの情報も示されています。 メタバース オブジェクトのタイプは、ソース オブジェクトのタイプがユーザーでも、iNetOrgPerson でも連絡先でも、必ず個人です。 メタバース オブジェクトの種類は、ジェネリック型として作成されるため、変更しないでください。 リンクの種類は、[結合]、[スティッキー結合]、または [プロビジョニング] に設定できます。 この設定は [Join Rules (結合規則)] セクションと共に機能します。この設定については、後で説明します。

また、ご覧のとおり、この同期規則はパスワード同期にも使用されます。ユーザーがこの同期規則のスコープに含まれる場合、パスワードがオンプレミスからクラウドに同期されます (パスワード同期機能を有効にした場合)。

#### <a name="scoping-filter"></a>スコープ フィルター
[スコープ フィルター] セクションは、同期規則の適用が必要なタイミングを構成するために使用されます。 ここで説明している同期規則の名前は、有効なユーザーのみに同期規則が適用されることを意味しているため、AD 属性 **userAccountControl** にビット 2 を設定しないようスコープが構成されます。 **userAccountControl** が 10 進数値 512 (有効な通常のユーザー) に設定されていると、同期エンジンが AD でユーザーを発見したときに、この同期規則が適用されます。 ユーザーが **userAccountControl** を 514 (無効な通常のユーザー) に設定していた場合には、この規則が適用されることはありません。

![[受信同期ルールの編集] ウィンドウの [スコープ フィルター] セクションのスクリーンショット。](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilter.png)

スコープ フィルターには、入れ子にできるグループおよび句があります。 同期規則を適用するには、グループ内のすべての句が満たされている必要があります。 複数のグループが定義されている場合、規則を適用するには少なくとも 1 つのグループが満たされている必要があります。 つまり、グループ間では論理 OR が評価され、グループ内では論理 AND が評価されます。 この構成の例は、送信の同期規則 "**AAD への送信 - グループ結合**" で確認することができます。 たとえば、同期フィルター グループはいくつかあります。たとえば、1 つはセキュリティ グループ向け (`securityEnabled EQUAL True`)、1 つは配布グループ向け (`securityEnabled EQUAL False`) です。

![Scoping tab in Sync rule editor](./media/concept-azure-ad-connect-sync-default-configuration/syncrulescopingfilterout.png)

この規則は、Azure AD にプロビジョニングするグループを定義するために使用されます。 配布グループを Azure AD と同期するにはメールを有効にする必要がありますが、セキュリティ グループの場合、メールは必須ではありません。

#### <a name="join-rules"></a>結合規則
3 番目のセクションは、コネクタ スペース内のオブジェクトと、メタバース内のオブジェクトの関連付けの構成に使用されます。 ここまでに説明した規則には結合規則の構成がないため、ここでは " **AD からの受信 - ユーザー結合**" について説明します。

![Join rules tab in Sync rule editor](./media/concept-azure-ad-connect-sync-default-configuration/syncrulejoinrules.png)

結合規則の内容は、インストール ウィザードで選択されている一致オプションによって異なります。 受信規則の場合、評価はソースのコネクタ スペースのオブジェクトで開始され、結合規則の各グループが順番に評価されます。 結合規則の 1 つを使用してソース オブジェクトを評価した結果、メタバースの 1 つのオブジェクトのみと一致した場合、これらのオブジェクトは結合されます。 すべての規則が評価されて一致が存在しない場合は、説明ページのリンクの種類が使用されます。 この構成が **[プロビジョニング]** に設定されている場合、結合条件に少なくとも 1 つの属性が存在する (値がある) と、ターゲットのメタバースに新しいオブジェクトが作成されます。 メタバースへの新しいオブジェクトのプロビジョニングは、メタバースへのオブジェクトの **投影** とも呼ばれています。

結合規則は、1 回のみ評価されます。 コネクタ スペース オブジェクトとメタバース オブジェクトが結合されている場合は、同期規則のスコープが引き続き満たされている限り、結合が維持されます。

同期規則を評価する際は、結合規則が定義されている同期規則が 1 つだけスコープ内に必要です。 1 つのオブジェクトに対して、結合規則を持つ複数の同期規則がある場合は、エラーがスローされます。 このため、複数の同期規則がオブジェクトのスコープ内にある場合は、結合を定義した同期規則を 1 つだけにすることがベスト プラクティスとなります。 Azure AD Connect 同期の標準の構成では、これらの規則は、名前で検索すると名前の末尾に **Join** という語が付いています。 同期規則に結合規則が定義されておらず、別の同期規則によってオブジェクトが結合されているか、ターゲットに新しいオブジェクトがプロビジョニングされている場合、その同期規則は属性フローを適用します。

上の図を見ると、この規則を使って **objectSID** を **msExchMasterAccountSid** (Exchange) および **msRTCSIP-OriginatorSid** (Lync) と結合しようとしていることがわかります。これは、アカウント リソース フォレスト トポロジで想定されます。 すべてのフォレストで同じ規則があることがわかります。 これは、フォレストがいずれもアカウント フォレストとリソース フォレストのどちらかであることが前提となっています。 この構成は、単一のフォレストに存在し、結合する必要がないアカウントが複数ある場合にも機能します。

#### <a name="transformations"></a>変換
変換セクションでは、オブジェクトが結合され、スコープ フィルターも満たしている場合にターゲット オブジェクトに適用されるすべての属性のフローを定義します。 もう一度 " **AD からの受信 - ユーザー アカウント有効** " 同期規則を見ると、次の変換があることがわかります。

![Transformations tab in Sync rule editor](./media/concept-azure-ad-connect-sync-default-configuration/syncruletransformations.png)

この構成を踏まえると、アカウント/リソース フォレストのデプロイでは、アカウント フォレストには有効なアカウントが、Exchange および Lync 設定のあるリソース フォレストには無効なアカウントが、それぞれ見つかると予想されます。 ここで説明している同期規則には、サインインに必要な属性が含まれており、有効なアカウントが存在するフォレストから、これらの属性がフローする必要があります。 これらすべての属性フローが 1 つの同期規則にまとめられます。

変換の種類には、定数、直接、式があります。

* 定数フローは常に、ハードコーディングされた値をフローします。 上に挙げたケースでは、**accountEnabled** という名前のメタバース属性に対して常に値 **True** が設定されます。
* 直接フローは常に、ソースの属性の値をそのままターゲット属性にフローします。
* 3 番目のフローの種類は式で、より高度な構成が可能です。

式の言語は VBA (Visual Basic for Applications) で、Microsoft Office または VBScript の経験があるユーザーであれば形式がわかります。 属性は、[attributeName] のように角かっこで囲みます。 属性名および関数名では、大文字と小文字が区別されます。同期規則エディターが式を評価し、式が無効な場合は警告を表示します。 すべての式は、関数が入れ子になって 1 行で表されます。 この構成言語の強力な機能を、次の pwdLastSet のフローで示しています。追加のコメントも挿入されています。

```
// If-then-else
IIF(
// (The evaluation for IIF) Is the attribute pwdLastSet present in AD?
IsPresent([pwdLastSet]),
// (The True part of IIF) If it is, then from right to left, convert the AD time format to a .NET datetime, change it to the time format used by Azure AD, and finally convert it to a string.
CStr(FormatDateTime(DateFromNum([pwdLastSet]),"yyyyMMddHHmmss.0Z")),
// (The False part of IIF) Nothing to contribute
NULL
)
```

属性フローに使用する式言語について詳しくは、「 [宣言型のプロビジョニングの式について](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) 」をご覧ください。

### <a name="precedence"></a>優先順位
個々の同期規則をいくつか見てきましたが、この構成では、これらの規則が一体となって動作します。 場合によっては、複数の同期規則の属性値が同じターゲット属性に渡されることもあります。 この場合、どの属性が優先されるかは属性の優先順位によって決まります。 例として、sourceAnchor 属性を見てみましょう。 この属性は、Azure AD にサインインできるようにするために重要な属性です。 この属性の属性フローは "**AD からの受信 - ユーザー アカウント有効**" と "**AD からの受信 - ユーザー共通**" の 2 つの同期規則にあります。 メタバース オブジェクトに複数のオブジェクトが結合されている場合は、同期規則の優先順位により、sourceAnchor 属性は、最初に、有効なアカウントを持つフォレストから提供されます。 有効なアカウントがない場合は、同期エンジンによって包括的な同期規則 " **AD からの受信 - ユーザー共通**" が使用されます。 この構成により、無効になっているアカウントであっても sourceAnchor が存在する状態を確保できます。

![同期規則、受信](./media/concept-azure-ad-connect-sync-default-configuration/syncrulesinbound.png)

同期規則の優先順位は、インストール ウィザードによってグループ単位で設定されます。 グループ内の規則はすべて同じ名前になりますが、接続されているディレクトリは異なります。 インストール ウィザードでは " **AD からの受信 – ユーザー結合** " 規則が最優先となり、接続されたすべての AD ディレクトリが繰り返し処理されます。 その後、定義された順番で、次のグループの規則に進みます。 グループ内では、コネクタがウィザードで追加された順序で規則が追加されます。 別のコネクタがウィザードで追加されると、同期規則の順序が変わり、新しいコネクタの規則が各グループの末尾に挿入されます。

### <a name="putting-it-all-together"></a>まとめ
ここまでの同期規則に関する説明で、構成がさまざまな同期規則でどのように動作するかを十分理解できるようになりました。 ユーザー、メタバースに影響する属性に注目すると、規則は次の順序で適用されます。

| 名前 | 解説 |
|:--- |:--- |
| AD からの受信 - ユーザー結合 |コネクタ スペース オブジェクトをメタバースと結合するための規則。 |
| AD からの受信 - ユーザー アカウント有効 |Azure AD と Microsoft 365 にサインインするために必要な属性。 これらの属性は有効なアカウントから取得します。 |
| AD からの受信 - Exchange からのユーザー共通 |グローバル アドレス一覧で見つかった属性。 ユーザーのメールボックスが見つかったフォレストのデータ品質が最も優れていると想定しています。 |
| AD からの受信 - ユーザー共通 |グローバル アドレス一覧で見つかった属性。 メールボックスが見つからなかった場合、それ以外の結合済みオブジェクトが属性値に影響する可能性があります。 |
| AD からの受信 - ユーザー Exchange |Exchange が検出された場合にのみ存在します。 インフラストラクチャの Exchange 属性がすべてフローされます。 |
| AD からの受信 - ユーザー Lync |Lync が検出された場合にのみ存在します。 インフラストラクチャの Lync 属性がすべてフローされます。 |

## <a name="next-steps"></a>次のステップ
* この構成モデルについて詳しくは、「 [Understanding Declarative Provisioning (宣言型のプロビジョニングについて)](concept-azure-ad-connect-sync-declarative-provisioning.md)」をご覧ください。
* 式言語について詳しくは、「 [宣言型のプロビジョニングの式について](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md)」をご覧ください。
* 既定の構成の動作についてさらに詳しく知りたい場合には、「 [ユーザーと連絡先について](concept-azure-ad-connect-sync-user-and-contacts.md)
* 宣言型のプロビジョニングを使用して現実に即した変更を実施する方法については、「 [既定の構成を変更する方法](how-to-connect-sync-change-the-configuration.md)」をご覧ください。

**概要トピック**

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)
* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)

