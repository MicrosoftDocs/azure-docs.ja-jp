---
title: "Azure AD と同期していないオブジェクトのトラブルシューティング | Microsoft Docs"
description: "オブジェクトが Azure AD と同期していない理由のトラブルシューティングを行います。"
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: 3c9642e5c2da92203be2bc23820e5b6f544de660
ms.lasthandoff: 03/04/2017

---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Azure AD と同期していないオブジェクトのトラブルシューティング

オブジェクトが Azure AD と期待どおりに同期していない場合は、いくつかの理由が考えられます。 Azure AD からエラー電子メールを受信した場合、または Azure AD Connect Health にエラーが表示される場合は、代わりに[エクスポート エラーのトラブルシューティング](active-directory-aadconnect-troubleshoot-sync-errors.md)に関するページをご覧ください。 しかし、オブジェクトが Azure AD にないという問題のトラブルシューティングを行う場合は、このトピックが役に立ちます。 オンプレミスのコンポーネントの Azure AD Connect 同期のエラーを検出する方法について説明します。

エラーを探すには、いくつかの異なる場所を次の順序で参照します。

1. インポートと同期中に同期エンジンによって特定されたエラーを探す場合は[操作ログ](#operations)。
2. 不足しているオブジェクトと同期エラーを探す場合は[コネクタ スペース](#connector-space-object-properties)。
3. データ関連の問題を探す場合は[メタバース](#metaverse-object-properties)。

これらの手順を開始する前に、[Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) を起動します。

## <a name="operations"></a>操作
トラブルシューティングは、Synchronization Service Manager の [操作] タブで開始する必要があります。 [操作] タブには、最近の操作の結果が表示されます。  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/operations.png)  

上半分にはすべての実行が時系列で表示されます。 既定では、操作ログには過去&7; 日間の情報が保持されますが、この設定は [スケジューラ](active-directory-aadconnectsync-feature-scheduler.md)で変更できます。 状態が成功になっていない実行を探したい場合があります。 見出しをクリックすると、並べ替えを変更できます。

**[Status]** (ステータス) 列は最も重要な情報であり、実行関連で最も深刻な問題を示します。 最も一般的なステータスを調査の優先度に基づいて簡単にまとめると次のようになります (* はエラー文字列が入ることを意味します)。

| [Status] | コメント |
| --- | --- |
| stopped-* |実行を完了できませんでした。 たとえば、リモート システムがダウンし、連絡できない場合などです。 |
| stopped-error-limit |エラーの数が 5,000 を超えています。 大量のエラーに起因し、実行が自動的に停止しました。 |
| completed-\*-errors |実行は完了しましたが、エラーが発生し (5,000 件未満)、調査が必要です。 |
| completed-\*-warnings |実行は完了しましたが、一部のデータが予想と異なります。 エラーがある場合には通常、このメッセージは単なる症状の&1; つにすぎません。 エラーを解決するまでは、警告を調査しないでください。 |
| 成功 |問題ありません。 |

行を選択すると、下の領域が更新され、実行の詳細が表示されます。 下の領域の左端には、 **Step #**(# は番号) という一覧が表示されることがあります。 これは、フォレストに複数のドメインがあり、手順が各ドメインを表す場合にのみ表示されます。 ドメイン名は **[Partition]**(パーティション) という見出しにあります。 **[Synchronization Statistics (同期統計)]**には、処理された変更の数に関する詳細が表示されます。 リンクをクリックすると、変更されたオブジェクトの一覧を取得できます。 オブジェクトにエラーがある場合、 **[Synchronization Errors (同期エラー)]**の下に表示されます。

### <a name="troubleshoot-errors-in-operations-tab"></a>[オペレーション] タブでエラーを解決する
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorsync.png)  
エラーがある場合、エラーのあるオブジェクトとエラー自体がリンクとなり、クリックすると詳細が表示されます。

まず、エラー文字列をクリックしてみます (上の画像の**sync-rule-error-function-triggered** )。 最初にオブジェクトの概要が表示されます。 実際のエラーを確認するには、 **[スタック トレース]**ボタンをクリックします。 このトレースにより、エラーのデバッグ レベル情報が表示されます。

**[コール スタック情報]** ボックスで右クリックし、**[すべて選択]** を選択し、**[コピー]** を選択します。 次に、スタックをコピーし、メモ帳など、普段利用しているエディターでエラーを確認できます。

* エラー元が **SyncRulesEngine**の場合、コール スタック情報の最初にオブジェクトの全属性の一覧が表示されます。 **InnerException =>** という見出しが表示されるまで下へスクロールします。  
  ![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/errorinnerexception.png)  
  その次の行には、エラーが表示されます。 上の図では、Fabrikam が作成したカスタムの同期規則がエラー元になっています。

エラー自体から十分な情報が得られない場合、データ自体を見る必要があります。 オブジェクト識別子が付いたリンクをクリックし、[コネクタ スペースのインポート済みオブジェクト](#cs-import)のトラブルシューティングを続行できます。

## <a name="connector-space-object-properties"></a>コネクタ スペース オブジェクトのプロパティ
[[操作]](#operations) タブにエラーが見つからない場合、次の手順は、Active Directory からメタバース、さらに Azure AD までコネクタ スペース オブジェクトを追跡することです。 このパスで、問題がある場所を探す必要があります。

### <a name="search-for-an-object-in-the-cs"></a>CS 内のオブジェクトの検索

**Synchronization Service Manager** で、**[コネクタ]** をクリックして Active Directory コネクタを選び、**[コネクタ スペースの検索]** を選択します。

**[Scope (スコープ)]** で、**[RDN]** (CN 属性を検索する場合) または **[DN or anchor (DN またはアンカー)]** (distinguishedName 属性を検索する場合) を選択します。 値を入力し、**[検索]** をクリックします。  
![コネクタ スペースの検索](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearch.png)  

探しているオブジェクトが見つからない場合は、[ドメイン ベースのフィルター処理](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering)または[組織単位ベースのフィルター処理](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering)でフィルターされた可能性があります。 フィルター処理が期待どおりに構成されていることを確認するには、[フィルター処理の構成](active-directory-aadconnectsync-configure-filtering.md)に関するトピックをご覧ください。

別の便利な検索は、Azure AD コネクタを選び、**[Scope (スコープ)]** で **[インポートを保留しています]** を選択し、**[追加]**チェックボックスをオンにすることです。 この検索によって、オンプレミスのオブジェクトに関連付けできない、Azure AD 内の同期されているすべてのオブジェクトが得られます。  
![コネクタ スペースの検索の孤立](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssearchorphan.png)  
これらのオブジェクトは、別の同期エンジンまたは別のフィルター処理構成を持つ同期エンジンによって作成されています。 このビューは、管理されなくなった**孤立した**オブジェクトの一覧です。 この一覧を確認し、[Azure AD PowerShell](http://aka.ms/aadposh) コマンドレットを使用してこれらのオブジェクトを削除することを検討する必要があります。

### <a name="cs-import"></a>CS インポート
cs オブジェクトを開くと、いくつかのタブが上部に表示されます。 **[インポート]** タブには、インポート後にステージングされるデータが表示されます。  
![CS オブジェクト](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/csobject.png)    
**[古い値]** には Connect に現在保存されているデータが、**[新しい値]** にはソース システムから受け取り、まだ適用されていないデータが表示されます。 オブジェクトにエラーがある場合、変更は処理されません。

**エラー**  
![CS オブジェクト](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cssyncerror.png)  
**[同期エラー]** タブは、オブジェクトに問題がある場合にのみ表示されます。 詳しくは、[同期エラーのトラブルシューティング](#troubleshoot-errors-in-operations-tab)に関するページをご覧ください。

### <a name="cs-lineage"></a>CS 系列
[Lineage] (系列) タブには、コネクタ スペース オブジェクトとメタバース オブジェクトの関係が表示されます。 コネクタが接続されているシステムから変更を最後にインポートしたタイミングと、メタバースにデータを入力するために適用された規則を確認できます。  
![CS 系列](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineage.png)  
**[アクション]** 列に、アクションが **[プロビジョニング]** である **[受信]** 同期規則が&1; つ表示されています。 これは、このコネクタ スペース オブジェクトが存在する限り、メタバース オブジェクトが残ることを示します。 同期規則の一覧に **[送信]** と **[Provision (プロビジョニング)]** が表示されている場合は、メタバース オブジェクトが削除されたときに、このオブジェクトが削除されることを示しています。  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/cslineageout.png)  
また、**[PasswordSync]** 列を見ると、1 つの同期規則の値が **[True]** に設定されており、受信コネクタ スペースに起因してパスワードが変更される可能性があることがわかります。 このパスワードはその後、受信ルールを経由して Azure AD に送信されます。

[Lineage (系列)] タブで [[Metaverse Object Properties (メタバース オブジェクトのプロパティ)]](#mv-attributes)をクリックすると、メタバースに移動できます。

すべてのタブの一番下に **[Preview (プレビュー)]** ボタンと **[Log (ログ)]** ボタンがあります。

### <a name="preview"></a>Preview
[プレビュー] ページは、1 つのオブジェクトの同期に使用されます。 カスタム同期ルールの問題を解決しているとき、1 つのオブジェクトに与える変更の影響を確認するのに便利です。 **完全同期**か**差分同期**を選択できます。 **[Generate Preview (プレビューの生成)]** か **[Commit Preview (プレビューのコミット)]** かも選択できます。生成の場合、変更はメモリにのみ保存されます。コミットの場合、メタバースが更新され、すべての変更がターゲット コネクタ スペースにステージングされます。  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/preview.png)  
オブジェクトを調べ、特定の属性フローに適用されたルールを確認できます。  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/previewresult.png)

### <a name="log"></a>ログ
ログ ページを使用して、パスワード同期の状態と履歴を確認できます。 詳しくは、[パスワード同期のトラブルシューティング](active-directory-aadconnectsync-troubleshoot-password-synchronization.md)に関するページをご覧ください。

## <a name="metaverse-object-properties"></a>メタバース オブジェクトのプロパティ
通常は、ソースの Active Directory [コネクタ スペース](#connector-space)から検索を開始することをお勧めします。 メタバースから検索を開始することもできます。

### <a name="search-for-an-object-in-the-mv"></a>MV 内のオブジェクトの検索
**Synchronization Service Manager** で、**[メタバース検索]** をクリックします。 ユーザーが見つかるとわかっているクエリを作成します。 accountName (sAMAccountName) や userPrincipalName などの一般的な属性を検索できます。 詳しくは、[メタバース検索](active-directory-aadconnectsync-service-manager-ui-mvsearch.md)に関する記事をご覧ください。
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvsearch.png)  

**[検索結果]** ウィンドウで、オブジェクトをクリックします。

オブジェクトが見つからなかった場合、それはまだメタバースに届いていません。 Active Directory [コネクタ スペース](#connector-space-object-properties)でオブジェクトの検索を続行します。 オブジェクトがメタバースに届くのを阻止している同期のエラーがあるか、フィルターが適用されている可能性があります。

### <a name="mv-attributes"></a>MV 属性
[属性] タブには、値と、値を提供したコネクタが表示されます。  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvobject.png)  

オブジェクトが同期していない場合は、メタバースで次の属性を参照します。
- 属性 **cloudFiltered** が存在し、**true** に設定されていますか。 設定されている場合は、[属性ベースのフィルター処理](active-directory-aadconnectsync-configure-filtering.md#attribute-based-filtering)の手順に従ってフィルターされています。
- 属性 **sourceAnchor** は存在しますか。 存在しない場合、アカウント リソース フォレスト トポロジはありますか。 オブジェクトがリンクされたメールボックスとして識別されている場合 (属性 **msExchRecipientTypeDetails** の値が 2)、sourceAnchor は有効になっている Active Directory アカウントを使用してフォレストによって提供されます。 マスター アカウントがインポートされ、正しく同期されていることを確認してください。 マスター アカウントがオブジェクトの[コネクタ](#mv-connectors)に一覧表示されている必要があります。

### <a name="mv-connectors"></a>MV コネクタ
[コネクタ] タブには、オブジェクトを表すすべてのコネクタ スペースが表示されます。  
![Sync Service Manager](./media/active-directory-aadconnectsync-troubleshoot-object-not-syncing/mvconnectors.png)  
次のものに対するコネクタが必要です。

- ユーザーが表現される各 Active Directory フォレスト。 この表現は、foreignSecurityPrincipals と Contact オブジェクトを含むことができます。
- Azure AD 内のコネクタ。

Azure AD にコネクタが存在しない場合は、[MV 属性](#MV-attributes)を読み取って、Azure AD にプロビジョニングされる条件を確認します。

このタブから[コネクタ スペース オブジェクト](#connector-space-object-properties)に移動することもできます。 行を選択し、**[プロパティ]** をクリックします。

## <a name="next-steps"></a>次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

