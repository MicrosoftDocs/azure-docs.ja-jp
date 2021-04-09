---
title: 'Azure AD Connect: 設計概念 |Microsoft Docs'
description: このトピックでは、特定の実装設計の各領域について詳しく説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: baa03499cc11bda24ead986dd64621572484cbb1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89279654"
---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: 設計概念
このドキュメントの目的は、Azure AD Connect の実装設計時に検討する必要がある領域について説明することです。 このドキュメントでは特定の領域について詳しく説明しますが、これらの概念については、他のドキュメントでも簡単に説明しています。

## <a name="sourceanchor"></a>sourceAnchor
sourceAnchor 属性は、 *オブジェクトの有効期間中に変更できない属性* として定義されています。 この属性により、オブジェクトは、オンプレミスと Azure AD で同じオブジェクトとして一意に識別されます。 また、この属性は、 **immutableId** とも呼ばれており、この 2 つの名前のどちらを使ってもかまいません。

"immutable" (変更できない) という単語は、このドキュメントで大きな意味を持ちます。 この属性の値は、一度設定すると変更できないため、シナリオに対応する設計を選ぶことが重要です。

この属性は、次のシナリオで使用されます。

* 新しい同期エンジン サーバーを構築する場合、またはディザスター リカバリー シナリオの後に再構築する場合、この属性によって、Azure AD 内の既存のオブジェクトがオンプレミスのオブジェクトとリンクされます。
* クラウド専用の ID から同期 ID モデルに移行する場合、この属性によって、Azure AD 内の既存のオブジェクトとオンプレミスのオブジェクトを "完全一致" させることができます。
* フェデレーションを使用する場合は、ユーザーを一意に識別するために、要求でこの属性と共に **userPrincipalName** を使用します。

ユーザーに関連するのは sourceAnchor であることから、このトピックではこの属性のみについて説明します。 すべてのオブジェクトの種類に同じ規則が適用されますが、通常この問題が関係するのはユーザーのみです。

### <a name="selecting-a-good-sourceanchor-attribute"></a>適切な sourceAnchor 属性の選択
属性の値は、次の規則に従う必要があります。

* 60 文字未満であること
  * a ～ z、A ～ Z、0 ～ 9 のいずれでもない文字はエンコードされ、3 文字としてカウントされます。
* 次の特殊文字が含まれていないこと: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " \@ _
* グローバルに一意であること
* 文字列、整数、バイナリのいずれかであること
* 変更される可能性があるためユーザーの名前に基づかないこと
* 大文字と小文字の区別がないこと、および大文字と小文字で異なる可能性がある値は避けること
* オブジェクトの作成時に割り当てること

選択された sourceAnchor が文字列型でない場合、Azure AD Connect では、特殊文字が表示されないように、属性値に対して Base64Encode を実行します。 ADFS とは別のフェデレーション サーバーを使用する場合、そのサーバーでも属性に対して Base64Encode を実行できることを確認してください。

sourceAnchor 属性では、大文字小文字が区別されます。 値 "JohnDoe" と "johndoe" は同じではありません。 ただし、大文字と小文字が異なるだけの 2 つのオブジェクトは作成しないでください。

オンプレミスの単一フォレストがある場合、使用する必要がある属性は **objectGUID** です。 これは、Azure AD Connect で簡単設定を使用する際に使用される属性でもあり、DirSync で使用される属性でもあります。

複数のフォレストがあり、フォレスト間およびドメイン間でユーザーを移動しない場合でも、 **objectGUID** 属性を使用することをお勧めします。

フォレスト間およびドメイン間でユーザーを移動する場合は、変更されない属性、または移動時にユーザーと共に移動できる属性を探す必要があります。 お勧めする方法は、合成属性を導入することです。 GUID のような情報を保持可能な属性が適しています。 オブジェクトの作成中に、新しい GUID が作成され、ユーザーに設定されます。 同期エンジン サーバーでは、 **objectGUID** に基づいてこの値を作成し、ADDS の選択した属性を更新するカスタム同期ルールを作成できます。 オブジェクトを移動するときは、この値の内容も必ずコピーしてください。

別の方法は、変更されないことがわかっている既存の属性を選択することです。 一般的に使用される属性に **employeeID** があります。 文字が含まれる属性を採用する場合は、属性値の文字 (大文字と小文字) が変更される可能性がないことを確認します。 使用しない方がよい属性として、ユーザーの名前を含む属性があります。 名前は、結婚や離婚によって変更されることが予想されるため、この属性には使用できません。 これは、**userPrincipalName**、**mail**、**targetAddress** などの属性を Azure AD Connect のインストール ウィザードで選択できない理由の 1 つでもあります。 これらの属性に含まれる "\@" 文字も sourceAnchor では使用できません。

### <a name="changing-the-sourceanchor-attribute"></a>sourceAnchor 属性の変更
Azure AD でオブジェクトを作成して、ID を同期した後に、sourceAnchor 属性の値を変更することはできません。

このような理由から、Azure AD Connect には次の制限が適用されます。

* sourceAnchor 属性を設定できるのは、初回インストール時のみです。 インストール ウィザードを再実行すると、このオプションは読み取り専用になります。 この設定を変更する必要がある場合は、アンインストールして再インストールする必要があります。
* 別の Azure AD Connect サーバーをインストールする場合は、前に使用したのと同じ sourceAnchor 属性を選択する必要があります。 以前に DirSync を使用していて Azure AD Connect に移行する場合は、**objectGUID** を使用する必要があります。これは、DirSync で使用される属性です。
* Azure AD へのオブジェクトのエクスポート後に sourceAnchor の値を変更すると、Azure AD Connect Sync からエラーがスローされます。問題を解決してソース ディレクトリで sourceAnchor を元に戻すまでは、そのオブジェクトをそれ以上変更できなくなります。

## <a name="using-ms-ds-consistencyguid-as-sourceanchor"></a>sourceAnchor としての ms-DS-ConsistencyGuid の使用
Azure AD Connect (バージョン 1.1.486.0 以前) では既定で、objectGUID が sourceAnchor 属性として使用されます。 ObjectGUID はシステムによって生成されます。 その値を、オンプレミスの AD オブジェクトを作成するときに自分で指定することはできません。 「[sourceAnchor](#sourceanchor)」セクションで説明したように、シナリオによっては、sourceAnchor の値を自分で指定する必要があります。 そのシナリオが自分に当てはまる場合は、設定によって変更できる AD 属性 (ms-DS-ConsistencyGuid など) を sourceAnchor 属性として使用してください。

Azure AD Connect (バージョン 1.1.524.0 以降) では、ms-DS-ConsistencyGuid 機能が sourceAnchor 属性として使用できるようになりました。 この機能を使用すると、次のことを行う同期規則が Azure AD Connect によって自動的に構成されます。

1. ユーザー オブジェクトの場合、sourceAnchor 属性として ms-DS-ConsistencyGuid が使用されます。 その他の種類のオブジェクトでは、ObjectGUID が使用されます。

2. ms-DS-ConsistencyGuid 属性の値が設定されていないオンプレミスの AD ユーザー オブジェクトについては、その objectGUID の値がオンプレミス Active Directory の ms-DS-ConsistencyGuid 属性に書き戻されます。 ms-DS-ConsistencyGuid 属性に値が設定された後、そのオブジェクトが Azure AD Connect によって Azure AD にエクスポートされます。

>[!NOTE]
> オンプレミス AD オブジェクトが Azure AD Connect にインポートされた (つまり、AD のコネクタ スペースにインポートされてメタバースに反映された) 後は、その sourceAnchor の値は変更できなくなります。 特定のオンプレミス AD オブジェクトに対して sourceAnchor の値を指定するには、そのオブジェクトが Azure AD Connect にインポートされる前に、その ms-DS-ConsistencyGuid 属性を構成してください。

### <a name="permission-required"></a>必要なアクセス許可
この機能を利用するためには、オンプレミス Active Directory との同期に使用する AD DS アカウントに、オンプレミス Active Directory 内の ms-DS-ConsistencyGuid 属性への書き込みアクセス許可を付与する必要があります。

### <a name="how-to-enable-the-consistencyguid-feature---new-installation"></a>ConsistencyGuid 機能を有効にする方法 - 新規インストール
新規インストール時に ConsistencyGuid の使用を sourceAnchor として有効にすることができます。 このセクションでは、高速インストールとカスタム インストールの両方を詳細に説明します。

  >[!NOTE]
  > 新規インストール時に ConsistencyGuid を sourceAnchor として使用することは、新しいバージョンの Azure AD Connect (1.1.524.0 以降) でのみサポートされています。

### <a name="how-to-enable-the-consistencyguid-feature"></a>ConsistencyGuid 機能を有効にする方法

#### <a name="express-installation"></a>高速インストール
Azure AD Connect を高速モードでインストールする場合、sourceAnchor 属性として最適な AD 属性が Azure AD Connect ウィザードによって自動的に決定されます。その際、以下のロジックが使用されます。

* まず、以前の Azure AD Connect インストール (存在する場合) で sourceAnchor 属性として使用されていた AD 属性を、Azure AD Connect ウィザードが Azure AD テナントに照会して取得します。 この情報が判明した場合は、同じ AD 属性が使用されます。

  >[!NOTE]
  > インストール時に使用される sourceAnchor 属性についての情報を Azure AD テナントに格納するのは、新しいバージョンの Azure AD Connect (1.1.524.0 以降) のみです。 それより前のバージョンの Azure AD Connect には該当しません。

* 使用されている sourceAnchor 属性の情報がない場合、ウィザードがオンプレミスの Active Directory で ms-DS-ConsistencyGuid 属性の状態を確認します。 この属性がディレクトリ内のどのオブジェクトに対しても構成されていない場合、ms-DS-ConsistencyGuid が sourceAnchor 属性として使用されます。 ディレクトリ内の少なくとも 1 つのオブジェクトに対してこの属性が構成済みであった場合は、この属性が他のアプリケーションによって使用されており、sourceAnchor 属性としては適さないと判断されます。

* その場合は、代わりに objectGUID が sourceAnchor 属性として使用されます。

* sourceAnchor 属性が決まると、その情報が Azure AD テナントに格納されます。 この情報は、その後 Azure AD Connect のインストールに使用されます。

高速インストールが完了すると、ソース アンカー属性として選択された属性が、ウィザードから通知されます。

![sourceAnchor として選択された AD 属性がウィザードから通知される](./media/plan-connect-design-concepts/consistencyGuid-01.png)

#### <a name="custom-installation"></a>カスタム インストール
カスタム モードで Azure AD Connect をインストールする場合、Azure AD Connect ウィザードで sourceAnchor 属性を構成するときに 2 つのオプションが表示されます。

![カスタム インストール - sourceAnchor 構成](./media/plan-connect-design-concepts/consistencyGuid-02.png)

| 設定 | 説明 |
| --- | --- |
| ソース アンカーの管理を Azure に任せる | Azure AD に属性を選択させる場合は、このオプションを選択します。 このオプションを選択した場合、Azure AD Connect ウィザードで[高速インストール時に使用される sourceAnchor 属性の選択ロジック](#express-installation)が同じように適用されます。 高速インストールと同様、どの属性がソース アンカー属性として選択されたかは、カスタム インストールの完了後、ウィザードに表示されます。 |
| 特有の属性 | sourceAnchor 属性として既存の AD 属性を指定する場合は、このオプションを選択します。 |

### <a name="how-to-enable-the-consistencyguid-feature---existing-deployment"></a>ConsistencyGuid 機能を有効にする方法 - 既存のデプロイ
ソース アンカー属性として objectGUID を使用する Azure AD Connect のデプロイが既にある場合は、代わりに ConsistencyGuid を使用するよう切り替えることができます。

>[!NOTE]
> ソース アンカー属性として ObjectGuid から ConsistencyGuid への切り替えをサポートするのは、新しいバージョンの Azure AD Connect (1.1.552.0 以降) のみです。

ソース アンカー属性を ObjectGuid から ConsistencyGuid に切り替えるには:

1. Azure AD Connect ウィザードを起動し、[**構成**] をクリックしてタスク画面に移動します。

2. [**Configure Source Anchor\(ソース アンカーを構成\)**] タスク オプションを選択して、[**次へ**] をクリックします。

   ![既存のデプロイで ConsistencyGuid を有効にする - 手順 2](./media/plan-connect-design-concepts/consistencyguidexistingdeployment01.png)

3. Azure AD の管理者資格情報を入力し、[**次へ**] をクリックします。

4. オンプレミスの Active Directory で ms-DS-ConsistencyGuid 属性の状態が Azure AD Connect ウィザードによって解析されます。 属性がディレクトリ内のどのオブジェクトにも構成されていない場合、Azure AD Connect はその属性を使用しているアプリケーションは現在なく、ソース アンカー属性として使用しても問題がないと判断します。 **[次へ]** をクリックして、続行します。

   ![既存のデプロイで ConsistencyGuid を有効にする - 手順 4](./media/plan-connect-design-concepts/consistencyguidexistingdeployment02.png)

5. [**構成の準備完了**] 画面で、[**構成**] をクリックし構成を変更します。

   ![既存のデプロイで ConsistencyGuid を有効にする - 手順 5](./media/plan-connect-design-concepts/consistencyguidexistingdeployment03.png)

6. 構成が完了すると、ウィザードに、ms-DS-ConsistencyGuid がソース アンカー属性として使用されていることが示されます。

   ![既存のデプロイで ConsistencyGuid を有効にする - 手順 6](./media/plan-connect-design-concepts/consistencyguidexistingdeployment04.png)

解析中 (手順 4) で、ディレクトリ内の少なくとも 1 つのオブジェクトに対してこの属性が構成済みであった場合は、この属性が他のアプリケーションによって使用されているとウィザードにより判断され、以下の図のようなエラーが返されます。 このエラーは、プライマリの Azure AD Connect サーバーで以前に ConsistencyGuid 機能を有効にしていて、同じ操作をステージング サーバーで実行しようした場合にも発生することがあります。

![既存のデプロイで ConsistencyGuid を有効にする - エラー](./media/plan-connect-design-concepts/consistencyguidexistingdeploymenterror.png)

 属性が他の既存のアプリケーションで使用されていないことがわかっている場合は、**/SkipLdapSearch** スイッチを指定して Azure AD Connect ウィザードを再起動することで、エラーを抑制することができます。 これを行うには、コマンド プロンプトで次のコマンドを実行します。

```
"c:\Program Files\Microsoft Azure Active Directory Connect\AzureADConnect.exe" /SkipLdapSearch
```

### <a name="impact-on-ad-fs-or-third-party-federation-configuration"></a>AD FS の構成またはサードパーティのフェデレーションの構成に対する影響
Azure AD Connect を使用してオンプレミス AD FS デプロイを管理している場合、同じ AD 属性を sourceAnchor として使用するように、要求規則が自動的に更新されます。 これによって、ADFS によって生成される ImmutableID 要求と Azure AD にエクスポートされる sourceAnchor 値との整合性が確実に保たれます。

Azure AD Connect を使わずに AD FS を管理している場合や、サードパーティのフェデレーション サーバーを認証に使用している場合は、ImmutableID 要求の要求規則を手動で更新して、Azure AD にエクスポートされる sourceAnchor 値との整合性を確保する必要があります (「[AD FS の要求規則を変更する](./how-to-connect-fed-management.md#modclaims)」を参照)。 インストールが完了するとウィザードから次の警告が返されます。

![サード パーティのフェデレーション構成](./media/plan-connect-design-concepts/consistencyGuid-03.png)

### <a name="adding-new-directories-to-existing-deployment"></a>既存のデプロイに対する新しいディレクトリの追加
既に ConsistencyGuid 機能を有効にして Azure AD Connect がデプロイされているとき、そのデプロイにもう 1 つディレクトリを追加する必要が生じたとしましょう。 ディレクトリを追加しようとすると、そのディレクトリ内の ms-DS-ConsistencyGuid 属性の状態が Azure AD Connect ウィザードによってチェックされます。 ディレクトリ内の少なくとも 1 つのオブジェクトに対してこの属性が構成済みであった場合は、この属性が他のアプリケーションによって使用されていると判断され、以下の図のようなエラーが返されます。 属性が既存のアプリケーションで使用されていないことがわかっている場合は、上記の説明のように **/SkipLdapSearch** スイッチを指定して Azure AD Connect ウィザードを再起動することでエラーを抑制できます。詳細については、サポートにお問い合わせください。

![既存のデプロイに対する新しいディレクトリの追加](./media/plan-connect-design-concepts/consistencyGuid-04.png)

## <a name="azure-ad-sign-in"></a>Azure AD のサインイン
オンプレミス ディレクトリを Azure AD に統合する場合、同期の設定がユーザーの認証方法に与える影響について理解することが重要です。 Azure AD では、userPrincipalName (UPN) がユーザーの認証に使用されます。 ただし、ユーザーを同期する場合は、userPrincipalName の値として使用される属性を慎重に選択する必要があります。

### <a name="choosing-the-attribute-for-userprincipalname"></a>userPrincipalName の属性を選択する
Azure で使用する UPN の値を指定するための属性を選択する場合、次のことを確認する必要があります。

* 属性値が UPN の構文 (RFC 822) に準拠していること (つまり、username\@domain の形式になっていること)
* 値のサフィックスが、Azure AD で確認済みのカスタム ドメインのいずれかに一致すること

簡単設定では、属性の値として userPrincipalName が想定されます。 Azure にサインインする必要のあるユーザーの値が userPrincipalName 属性に含まれていない場合は、 **カスタム インストール** を行う必要があります。

### <a name="custom-domain-state-and-upn"></a>カスタム ドメインの状態と UPN
確認済みのドメインを UPN のサフィックスとして使用することが重要です。

John は、contoso.com に属するユーザーです。 Azure AD ディレクトリ azurecontoso.onmicrosoft.com にユーザーを同期した後、John がオンプレミスの UPN である \@contoso.com を使って Azure にサインインする必要があるとします。 この場合は、ユーザーの同期を開始する前に、contoso.com を Azure AD にカスタム ドメインとして追加する必要があります。 John の UPN サフィックス (この例では contoso.com) が Azure AD で確認済みのドメインと一致しない場合、UPN サフィックスは azurecontoso.onmicrosoft.com に置き換えられます。

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>ルーティング不可能なオンプレミス ドメインと Azure AD の UPN
組織によっては、contoso.local のようにルーティング不可能なドメインや、contoso のような単純な単一ラベルのドメインなどが存在します。 ルーティング不可能なドメインは Azure AD では確認できません。 Azure AD Connect は、Azure AD で確認済みのドメインのみに対して同期できます。 Azure AD ディレクトリを作成すると、ルーティング可能なドメインが作成され、そのドメインが Azure AD の既定のドメインになります (例: contoso.onmicrosoft.com)。 そのため、既定の onmicrosoft.com ドメインに同期しないシナリオでは、他のルーティング可能なドメインを確認することが必要になります。

ドメインの追加と確認の詳細については、「 [Azure Active Directory へのカスタム ドメイン名の追加](../fundamentals/add-custom-domain.md) 」を参照してください。

ルーティング不可能なドメイン環境で実行している場合、その環境が Azure AD Connect で検出され、簡単設定を続行するかどうかについて適切な警告が表示されます。 ルーティング不可能なドメインで運用している場合は、ユーザーの UPN でもルーティング不可能なサフィックスが使用されている可能性があります。 たとえば、contoso.local で運用している場合、Azure AD Connect では簡単設定を使用するのではなく、カスタム設定を使用するように推奨されます。 カスタム設定を使用すると、ユーザーの Azure AD への同期後に Azure へのサインインで UPN として使用する属性を指定できます。

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。