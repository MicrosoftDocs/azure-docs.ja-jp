---
title: "Azure AD Connect: 設計概念 |Microsoft Docs"
description: "このトピックでは、特定の実装設計の各領域について詳しく説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 4114a6c0-f96a-493c-be74-1153666ce6c9
ms.service: active-directory
ms.custom: azure-ad-connect
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 02/07/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 28b5da6098316f8fbe84966e0dac88f5b7d2cb1d
ms.openlocfilehash: 177c622171c4b0e1813c85d1c22bda87aeafce06


---
# <a name="azure-ad-connect-design-concepts"></a>Azure AD Connect: 設計概念
このトピックの目的は、Azure AD Connect の実装設計時に検討する必要がある領域について説明することです。 ここでは特定の領域について詳しく説明しますが、これらの概念については、他のトピックでも簡単に説明しています。

## <a name="sourceanchor"></a>sourceAnchor
sourceAnchor 属性は、 *オブジェクトの有効期間中に変更できない属性*として定義されています。 この属性により、オブジェクトは、オンプレミスと Azure AD で同じオブジェクトとして一意に識別されます。 また、この属性は、 **immutableId** とも呼ばれており、この 2 つの名前のどちらを使ってもかまいません。

"immutable" (変更できない) という単語は、このトピックで大きな意味を持ちます。 この属性の値は、一度設定すると変更できないため、シナリオに対応する設計を選ぶことが重要です。

この属性は、次のシナリオで使用されます。

* 新しい同期エンジン サーバーを構築する場合、または障害復旧シナリオの後に再構築する場合、この属性によって、Azure AD 内の既存のオブジェクトがオンプレミスのオブジェクトとリンクされます。
* クラウド専用の ID から同期 ID モデルに移行する場合、この属性によって、Azure AD 内の既存のオブジェクトとオンプレミスのオブジェクトを "完全一致" させることができます。
* フェデレーションを使用する場合は、ユーザーを一意に識別するために、要求でこの属性と共に **userPrincipalName** を使用します。

ユーザーに関連するのは sourceAnchor であることから、このトピックではこの属性のみについて説明します。 すべてのオブジェクトの種類に同じ規則が適用されますが、通常この問題が関係するのはユーザーのみです。

### <a name="selecting-a-good-sourceanchor-attribute"></a>適切な sourceAnchor 属性の選択
属性の値は、次の規則に従う必要があります。

* 60 文字未満であること
  * a ～ z、A ～ Z、0 ～ 9 のいずれでもない文字はエンコードされ、3 文字としてカウントされます。
* 次の特殊文字が含まれていないこと: &#92; ! # $ % & * + / = ? ^ &#96; { } | ~ < > ( ) ' ; : , [ ] " @ _
* グローバルに一意であること
* 文字列、整数、バイナリのいずれかであること
* ユーザーの名前に基づかないこと (変更されるため)
* 大文字と小文字の区別がないこと、および大文字と小文字で異なる可能性がある値は避けること
* オブジェクトの作成時に割り当てること

選択された sourceAnchor が文字列型でない場合、Azure AD Connect では、特殊文字が表示されないように、属性値に対して Base64Encode を実行します。 ADFS とは別のフェデレーション サーバーを使用する場合、そのサーバーでも属性に対して Base64Encode を実行できることを確認してください。

sourceAnchor 属性では、大文字小文字が区別されます。 値 "JohnDoe" と "johndoe" は同じではありません。 ただし、大文字と小文字が異なるだけの 2 つのオブジェクトは作成しないでください。

オンプレミスの単一フォレストがある場合、使用する必要がある属性は **objectGUID**です。 これは、Azure AD Connect で簡単設定を使用する際に使用される属性でもあり、DirSync で使用される属性でもあります。

複数のフォレストがあり、フォレスト間およびドメイン間でユーザーを移動しない場合でも、 **objectGUID** 属性を使用することをお勧めします。

フォレスト間およびドメイン間でユーザーを移動する場合は、変更されない属性、または移動時にユーザーと共に移動できる属性を探す必要があります。 お勧めする方法は、合成属性を導入することです。 GUID のような情報を保持可能な属性が適しています。 オブジェクトの作成中に、新しい GUID が作成され、ユーザーに設定されます。 同期エンジン サーバーでは、 **objectGUID** に基づいてこの値を作成し、ADDS の選択した属性を更新するカスタム同期ルールを作成できます。 オブジェクトを移動するときは、この値の内容も必ずコピーしてください。

別の方法は、変更されないことがわかっている既存の属性を選択することです。 一般的に使用される属性に **employeeID**があります。 文字が含まれる属性を採用する場合は、属性値の文字 (大文字と小文字) が変更される可能性がないことを確認します。 使用しない方がよい属性として、ユーザーの名前を含む属性があります。 名前は、結婚や離婚によって変更されることが予想されるため、この属性には使用できません。 これは、**userPrincipalName**、**mail**、**targetAddress** などの属性を Azure AD Connect のインストール ウィザードで選択できない理由の 1 つでもあります。 これらの属性に含まれる "@" 文字も sourceAnchor では使用できません。

### <a name="changing-the-sourceanchor-attribute"></a>sourceAnchor 属性の変更
Azure AD でオブジェクトを作成して、ID を同期した後に、sourceAnchor 属性の値を変更することはできません。

このような理由から、Azure AD Connect には次の制限が適用されます。

* sourceAnchor 属性を設定できるのは、初回インストール時のみです。 インストール ウィザードを再実行すると、このオプションは読み取り専用になります。 この設定を変更する必要がある場合は、アンインストールして再インストールする必要があります。
* 別の Azure AD Connect サーバーをインストールする場合は、前に使用したのと同じ sourceAnchor 属性を選択する必要があります。 以前に DirSync を使用していて Azure AD Connect に移行する場合は、**objectGUID** を使用する必要があります。これは、DirSync で使用される属性です。
* Azure AD へのオブジェクトのエクスポート後に sourceAnchor の値を変更すると、Azure AD Connect Sync からエラーがスローされます。問題を解決してソース ディレクトリで sourceAnchor を元に戻すまでは、そのオブジェクトをそれ以上変更できなくなります。

## <a name="azure-ad-sign-in"></a>Azure AD のサインイン
オンプレミス ディレクトリを Azure AD に統合する場合、同期の設定がユーザーの認証方法に与える影響について理解することが重要です。 Azure AD では、userPrincipalName (UPN) がユーザーの認証に使用されます。 ただし、ユーザーを同期する場合は、userPrincipalName の値として使用される属性を慎重に選択する必要があります。

### <a name="choosing-the-attribute-for-userprincipalname"></a>userPrincipalName の属性を選択する
Azure で使用する UPN の値を指定するための属性を選択する場合、次のことを確認する必要があります。

* 属性値が UPN の構文 (RFC 822) に準拠していること (つまり、 username@domain
* 値のサフィックスが、Azure AD で確認済みのカスタム ドメインのいずれかに一致すること

簡単設定では、属性の値として userPrincipalName が想定されます。 Azure にサインインする必要のあるユーザーの値が userPrincipalName 属性に含まれていない場合は、 **カスタム インストール**を行う必要があります。

### <a name="custom-domain-state-and-upn"></a>カスタム ドメインの状態と UPN
確認済みのドメインを UPN のサフィックスとして使用することが重要です。

John は、contoso.com に属するユーザーです。 Azure AD ディレクトリ azurecontoso.onmicrosoft.com にユーザーを同期した後、John がオンプレミスの UPN である john@contoso.com を使って Azure にサインインする必要があるとします。 この場合は、ユーザーの同期を開始する前に、contoso.com を Azure AD にカスタム ドメインとして追加する必要があります。 John の UPN サフィックス (この例では contoso.com) が Azure AD で確認済みのドメインと一致しない場合、UPN サフィックスは azurecontoso.onmicrosoft.com に置き換えられます。

### <a name="non-routable-on-premises-domains-and-upn-for-azure-ad"></a>ルーティング不可能なオンプレミス ドメインと Azure AD の UPN
組織によっては、contoso.local のようにルーティング不可能なドメインや、contoso のような単純な単一ラベルのドメインなどが存在します。 ルーティング不可能なドメインは Azure AD では確認できません。 Azure AD Connect は、Azure AD で確認済みのドメインのみに対して同期できます。 Azure AD ディレクトリを作成すると、ルーティング可能なドメインが作成され、そのドメインが Azure AD の既定のドメインになります (例: contoso.onmicrosoft.com)。 そのため、既定の onmicrosoft.com ドメインに同期しないシナリオでは、他のルーティング可能なドメインを確認することが必要になります。

ドメインの追加と確認の詳細については、「 [Azure Active Directory へのカスタム ドメイン名の追加](../active-directory-add-domain.md) 」を参照してください。

ルーティング不可能なドメイン環境で実行している場合、その環境が Azure AD Connect で検出され、簡単設定を続行するかどうかについて適切な警告が表示されます。 ルーティング不可能なドメインで運用している場合は、ユーザーの UPN でもルーティング不可能なサフィックスが使用されている可能性があります。 たとえば、contoso.local で運用している場合、Azure AD Connect では簡単設定を使用するのではなく、カスタム設定を使用するように推奨されます。 カスタム設定を使用すると、ユーザーの Azure AD への同期後に Azure へのサインインで UPN として使用する属性を指定できます。

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。




<!--HONumber=Dec16_HO3-->


