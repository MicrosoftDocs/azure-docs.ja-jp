---
title: Azure Active Directory と同期していないオブジェクトのトラブルシューティング |Microsoft Docs
description: Azure Active Directory と同期していないオブジェクトのトラブルシューティング。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416923"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Azure Active Directory と同期していないオブジェクトのトラブルシューティング

オブジェクトが Microsoft Azure Active Directory (Azure AD) と期待どおりに同期していない場合、いくつかの理由が考えられます。 Azure AD からエラー電子メールを受信した場合、または Azure AD Connect Health にエラーが表示される場合は、代わりに「[同期中のエラーのトラブルシューティング](tshoot-connect-sync-errors.md)」をお読みください。 しかし、オブジェクトが Azure AD にないという問題をトラブルシューティングする場合は、この記事が役に立ちます。 オンプレミス コンポーネントの Azure AD Connect 同期のエラーを検出する方法について説明します。

>[!IMPORTANT]
>Azure AD Connect のバージョン 1.1.749.0 以上のデプロイについては、ウィザードの[トラブルシューティング タスク](tshoot-connect-objectsync.md)を使用して、オブジェクトの同期問題をトラブルシューティングしてください。 

## <a name="synchronization-process"></a>同期プロセス

同期問題を調査する前に、Azure AD Connect の同期プロセスについて理解しておきましょう。

  ![Azure AD Connect 同期プロセスの図](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**用語集**

* **CS:** コネクタ スペース。データベース内のテーブル
* **MV:** メタバース。データベース内のテーブル

### <a name="synchronization-steps"></a>**同期のステップ**
同期プロセスでは、次のステップが実行されます。

1. **AD からインポートする:** Active Directory オブジェクトが Active Directory CS に取り込まれます。

2. **Azure AD からインポートする:** Azure AD オブジェクトが Azure AD CS に取り込まれます。

3. **同期:** 受信同期規則と送信同期規則は、優先順位の番号の低い方から順に実行されます。 同期規則は、デスクトップ アプリケーションから同期規則エディターにアクセスして表示することができます。 受信同期規則は、CS から MV にデータを取り込みます。 送信同期規則は、MV から CS にデータを移動します。

4. **AD にエクスポートする:** 同期の後、オブジェクトは Active Directory CS から Active Directory にエクスポートされます。

5. **Azure AD にエクスポートする:** 同期の後、オブジェクトは Azure AD CS から Azure AD にエクスポートされます。

## <a name="troubleshooting"></a>トラブルシューティング

エラーを見つけるには、いくつかの異なる場所を次の順序で探します。

1. インポートと同期中に同期エンジンによって特定されたエラーを探す場合は[操作ログ](#operations)。
2. 不足しているオブジェクトと同期エラーを探す場合は[コネクタ スペース](#connector-space-object-properties)。
3. データ関連の問題を探す場合は[メタバース](#metaverse-object-properties)。

これらの手順を開始する前に、[Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) を起動します。

## <a name="operations"></a>操作
トラブルシューティングは、Synchronization Service Manager の **[Operations]\(操作\)** タブから開始する必要があります。 このタブには、最近の操作の結果が表示されます。 

![[Operations]\(操作\) タブが選択されている Synchronization Service Manager のスクリーンショット](./media/tshoot-connect-object-not-syncing/operations.png)  

**[Operations]\(操作\)** タブの上半分にはすべての実行が時系列で表示されます。 既定では、操作ログには過去 7 日間の情報が保持されますが、この設定は [スケジューラ](how-to-connect-sync-feature-scheduler.md)で変更できます。 **success** の状態を示していない実行を探します。 見出しをクリックすると、並べ替えを変更できます。

**[Status]\(状態\)** 列は最も重要な情報を含んでおり、実行の最も重大な問題を示しています。 最も一般的な状態を調査の優先度に基づいて簡単にまとめると次のようになります (* はエラー文字列が入ることを意味します)。

| Status | Comment (コメント) |
| --- | --- |
| stopped-* |実行を完了できませんでした。 これは、たとえば、リモート システムがダウンして接続できない場合などに発生する可能性があります。 |
| stopped-error-limit |エラーの数が 5,000 を超えています。 大量のエラーに起因し、実行が自動的に停止しました。 |
| completed-\*-errors |実行は完了しましたが、エラーが発生し (5,000 件未満)、調査が必要です。 |
| completed-\*-warnings |実行は完了しましたが、一部のデータが予想と異なる状態になっています。 エラーがある場合、このメッセージは通常、症状の 1 つにすぎません。 エラーに対処するまでは、警告を調査しないでください。 |
| 成功 |問題ありません。 |

行を選択すると、**[Operations]\(操作\)** タブの下の領域が更新されて、実行の詳細が表示されます。 この領域の左端に、**[Step #]\(ステップ番号\)** というタイトルの一覧がある場合があります。 これは、フォレストに複数のドメインがあり、手順が各ドメインを表す場合にのみ表示されます。 ドメイン名は **[Partition]**(パーティション) という見出しにあります。 **[Synchronization Statistics]\(同期統計\)** 見出しの下には、処理された変更の数に関する詳細が表示されています。 リンクを選択すると、変更されたオブジェクトの一覧を取得できます。 エラーのオブジェクトがある場合、**[Synchronization Errors]\(同期エラー\)** 見出しの下にそれらのエラーが表示されます。

### <a name="errors-on-the-operations-tab"></a>[Operations]\(操作\) タブのエラー
エラーがある場合は、Synchronization Service Manager に、エラーのあるオブジェクトとエラー自体の両方が、詳細を提供するリンクとして表示されます。

![Synchronization Service Manager でのエラーのスクリーンショット](./media/tshoot-connect-object-not-syncing/errorsync.png)  
エラー文字列を選択して開始します。 (上の図では、**sync-rule-error-function-triggered** がエラー文字列です。)最初にオブジェクトの概要が表示されます。 実際のエラーを表示するには、**[Stack Trace]\(スタック トレース\)** を選択します。 このトレースにより、エラーのデバッグ レベル情報が表示されます。

**[Call Stack Information]\(コール スタック情報\)** ボックスを右クリックして **[Select All]\(すべて選択\)** をクリックし、**[Copy]\(コピー\)** を選択します。 次に、スタックをコピーし、メモ帳などの普段利用しているエディターでエラーを表示します。

エラー元が **SyncRulesEngine** の場合、コール スタック情報では、オブジェクトの全属性の一覧が最初に表示されます。 **InnerException =>** という見出しが表示されるまで下へスクロールします。  

  ![[InnerException =>] という見出しの下にエラー情報が表示されている Synchronization Service Manager のスクリーンショット](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
見出しの後の行に、エラーが表示されます。 前の図では、Fabrikam が作成した custom synchronization ルールがエラー元になります。

エラーに十分な情報が提供されない場合は、データ自体を確認します。 オブジェクト ID のあるリンクを選択して、[コネクタ スペースのインポート済みオブジェクト](#cs-import)のトラブルシューティングを続行します。

## <a name="connector-space-object-properties"></a>コネクタ スペース オブジェクトのプロパティ
[**[Operations]\(操作\)**](#operations) タブにエラーが表示されていない場合は、コネクタ スペース オブジェクトを Active Directory からメタバース、そして Azure AD へと追跡します。 このパスで、問題がある場所を探す必要があります。

### <a name="searching-for-an-object-in-the-cs"></a>CS 内のオブジェクトの検索

Synchronization Service Manager で、**[Connectors]\(コネクタ\)** を選択し、Active Directory コネクタを選択し、**[Search Connector Space]\(コネクタ スペースの検索\)** を選択します。

**[Scope]\(スコープ\)** ボックスで、**[RDN]** (CN 属性を検索する場合) または **[DN or anchor]\(DN またはアンカー\)**(**distinguishedName** 属性を検索する場合) を選択します。 値を入力して、**[Search]\(検索\)** を選択します。 
 
![コネクタ スペースの検索のスクリーンショット](./media/tshoot-connect-object-not-syncing/cssearch.png)  

探しているオブジェクトが見つからない場合は、[ドメイン ベースのフィルター処理](how-to-connect-sync-configure-filtering.md#domain-based-filtering)または [OU ベースのフィルター処理](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)によってフィルターされている可能性があります。 フィルター処理が予想どおりに構成されていることを確認するには、「[Azure AD Connect 同期:フィルター処理の構成](how-to-connect-sync-configure-filtering.md)」をお読みください。

Azure AD コネクタを選択して、もう 1 つの便利な検索を実行できます。 **[Scope]\(スコープ\)** ボックスで、**[Pending Import]\(保留中のインポート\)** を選び、**[Add]\(追加\)** チェック ボックスを選択します。 この検索で、オンプレミス オブジェクトに関連付けできない、Azure AD 内の同期されているすべてのオブジェクトが検出されます。  

![コネクタ スペース検索の孤立オブジェクトのスクリーンショット](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
これらのオブジェクトは、別の同期エンジン、または異なるフィルター処理構成を持つ同期エンジンによって作成されたものです。 これらの孤立オブジェクトはもう管理されていません。 この一覧を確認し、[Azure AD PowerShell](https://aka.ms/aadposh) コマンドレットを使用してこれらのオブジェクトを削除することを検討してください。

### <a name="cs-import"></a>CS インポート
CS オブジェクトを開くと、いくつかのタブが上部にあります。 **[インポート]** タブには、インポート後にステージングされるデータが表示されます。  

![[インポート] タブが選択された [Connector Space Object Properties]\(コネクタ スペース オブジェクトのプロパティ\) ウィンドウのスクリーンショット](./media/tshoot-connect-object-not-syncing/csobject.png)    

**[Old Value]\(古い値\)** 列には、現在 Connect に保存されているデータが表示され、**[New Value]\(新しい値\)** 列には、ソース システムから受け取って、まだ適用されていないデータが表示されます。 オブジェクトにエラーがある場合、変更は処理されません。

**[Synchronization Error]\(同期エラー\)** タブが **[Connector Space Object Properties]\(コネクタ スペース オブジェクトのプロパティ\)** ウィンドウに表示されるのは、オブジェクトに問題がある場合だけです。 詳細については、[**[Operations]\(操作\)** タブでの同期エラーのトラブルシューティング](#errors-on-the-operations-tab)方法を確認してください。

![[Connector Space Object Properties]\(コネクタ スペース オブジェクトのプロパティ\) ウィンドウのスクリーンショット](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS 系列
**[Connector Space Object Properties]\(コネクタ スペース オブジェクトのプロパティ\)** ウィンドウの **[Lineage]\(系列\)** タブには、コネクタ スペース オブジェクトとメタバース オブジェクトの関係が示されます。 コネクタが、接続されているシステムから最後に変更をインポートした日時と、メタバースにデータを取り込むために適用された規則を確認できます。  

![[Connector Space Object Properties]\(コネクタ スペース オブジェクトのプロパティ\) ウィンドウの [Lineage]\(系列\) タブを示すスクリーンショット](./media/tshoot-connect-object-not-syncing/cslineage.png)  

前の図で、**[Action]\(アクション\)** 列には、**[Provision]** アクションの受信同期規則が表示されています。 これは、このコネクタ スペース オブジェクトが存在する限り、メタバース オブジェクトが残ることを示します。 同期規則の一覧に、**[Provision]\(プロビジョン\)** アクションの送信同期規則が代わりに表示されている場合、このオブジェクトは、メタバース オブジェクトが削除されるときに削除されます。  

![[Connector Space Object Properties]\(コネクタ スペース オブジェクトのプロパティ\) ウィンドウの [Lineage]\(系列\) タブの [lineage]\(系列\) ウィンドウを示すスクリーンショット](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

前の図ではまた、**[PasswordSync]** 列で 1 つの同期規則の値が **[True]** に設定されているため、受信コネクタ スペースに起因してパスワードが変更される可能性があることも確認できます。 このパスワードは、受信規則により Azure AD に送信されます。

**[Lineage]\(系列\)** タブから、[**[Metaverse Object Properties]\ (メタバース オブジェクトのプロパティ\)**](#mv-attributes) を選択して、メタバースに移動できます。

### <a name="preview"></a>プレビュー
**[Connector Space Object Properties]\(コネクタ スペース オブジェクトのプロパティ\)** ウィンドウの左下隅に **[Preview]\(プレビュー\)** ボタンがあります。 このボタンを選択すると **[Preview]\(プレビュー\)** ページが表示され、そこで 1 つのオブジェクトを同期できます。 このページは、カスタム同期規則をトラブルシューティングしていて、1 つのオブジェクトに対する変更の影響を確認したい場合に便利です。 **[Full sync]\(完全同期\)** または **[Delta sync]\(差分同期\)** を選択できます。また、**[Generate Preview]\(プレビューの生成\)** も選択できます。これは、単にメモリ内の変更を保持します。 あるいは、**[Commit Preview]\(プレビューのコミット\)** を選択します。そうすると、メタバースが更新されて、すべての変更がターゲット コネクタ スペースにステージングされます。  

![[Start Preview]\(プレビューの開始\) が選択された[Preview]\(プレビュー\) ページのスクリーンショット](./media/tshoot-connect-object-not-syncing/preview.png)  

プレビューで、オブジェクトを調べて、特定の属性フローに適用された規則を確認できます。  

![インポート属性フローを示す [Preview]\(プレビュー\) ページのスクリーンショット](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>ログ
**[Preview]\(プレビュー\)** ボタンの横の **[Log]\(ログ\)** ボタンを選択すると、**[Log]\(ログ\)** ページが開きます。 ここでは、パスワードの同期の状態と履歴を確認することができます。 詳細については、「[Azure AD Connect Sync を使用したパスワード ハッシュ同期のトラブルシューティング](tshoot-connect-password-hash-synchronization.md)」を参照してください。

## <a name="metaverse-object-properties"></a>メタバース オブジェクトのプロパティ
通常は、ソースの Active Directory コネクタ スペースから検索を開始することをお勧めします。 メタバースから検索を開始することもできます。

### <a name="searching-for-an-object-in-the-mv"></a>MV 内のオブジェクトの検索
Synchronization Service Manager では、以下の図に示すように、**[Metaverse Search]\(メタバース検索\)** を選択します。 ユーザーが検出されるとわかっているクエリを作成します。 **accountName** (**sAMAccountName**) や **userPrincipalName** などの一般的な属性を検索します。 詳細については、「[Sync Service Manager のメタバース検索](how-to-connect-sync-service-manager-ui-mvsearch.md)」を参照してください。

![[Metaverse Search]\(メタバース検索\) タブが選択されている Synchronization Service Manager のスクリーンショット](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

**[検索結果]** ウィンドウで、オブジェクトをクリックします。

オブジェクトが検出されなかった場合は、まだメタバースに届いていません。 Active Directory [コネクタ スペース](#connector-space-object-properties)でオブジェクトの検索を続行します。 Active Directory コネクタ スペースでオブジェクトが検出される場合は、オブジェクトがメタバースに届くのを阻止している同期エラーがあるか、同期規則のスコープ フィルターが適用されている可能性があります。

### <a name="object-not-found-in-the-mv"></a>オブジェクトが MV に見つからない
オブジェクトが Active Directory CS には存在するが、MV には存在しない場合は、スコープ フィルターが適用されています。 スコープ フィルターを調べるには、デスクトップ アプリケーションのメニューに移動して、**[Synchronization Rules Editor]\(同期規則エディター\)** を選択します。 以下のフィルターを調整して、オブジェクトに該当する規則をフィルタリングしてください。

  ![受信同期規則検索を示す、Synchronization Rules Editor (同期規則エディター) のスクリーンショット](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

上のリストで各規則を表示し、**[スコープ フィルター]** を確認します。 以下のスコープ フィルターで、**isCriticalSystemObject** 値が null (または FALSE) または空の場合は、範囲内です。

  ![受信同期規則検索でのスコープ フィルターのスクリーンショット](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

[CS Import](#cs-import) 属性リストに移動して、オブジェクトが MV に移動するのを妨害しているフィルターを確認します。 **[Connector Space]\(コネクタ スペース\)** 属性リストには、null 以外の属性と空でない属性だけが表示されます。 たとえば、**isCriticalSystemObject** がリストに表示されない場合、この属性の値は null または空です。

### <a name="object-not-found-in-the-azure-ad-cs"></a>オブジェクトが Azure AD CS に見つからない
オブジェクトが Azure AD のコネクタ スペースにはないが、MV にはある場合は、対応するコネクタ スペースの送信規則のスコープ フィルターを調べて、[MV 属性](#mv-attributes)が基準を満たしていないためにオブジェクトがフィルターで除外されているかどうかを確認します。

送信スコープ フィルターを調べるには、以下のフィルターを調整してオブジェクトに該当する規則を選択します。 それぞれの規則を表示して、対応する [MV 属性](#mv-attributes)の値を調べてください。

  ![Synchronization Rules Editor (同期規則エディター) での送信同期規則検索のスクリーンショット](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV 属性
**[Attributes]\(属性\)** タブでは、値とそれらを提供したコネクタを確認できます。  

![[Attributes]\(属性\) タブが選択された [Metaverse Object Properties]\(メタバース オブジェクトのプロパティ\) ウィンドウのスクリーンショット](./media/tshoot-connect-object-not-syncing/mvobject.png)  

オブジェクトが同期していない場合は、メタバース内の属性の状態について以下のことを確認してください。
- 属性 **cloudFiltered** が存在しており、**True** に設定されていますか。 設定されている場合は、[属性ベースのフィルター処理](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)の手順に従ってフィルターされています。
- 属性 **sourceAnchor** は存在しますか。 存在しない場合、アカウント リソース フォレスト トポロジはありますか。 オブジェクトがリンクされたメールボックスとして識別されている場合 (属性 **msExchRecipientTypeDetails** の値が **2**)、**sourceAnchor** は、有効な Active Directory アカウントを含むフォレストによって提供されます。 マスター アカウントがインポートされて、正しく同期されていることを確認してください。 マスター アカウントはオブジェクトの[コネクタ](#mv-connectors)に一覧表示されている必要があります。

### <a name="mv-connectors"></a>MV コネクタ
**[Connectors]\(コネクタ\)** タブには、オブジェクトの表現を含むすべてのコネクタ スペースが表示されます。 
 
![[Connectors]\(コネクタ\) タブが選択された [Metaverse Object Properties]\(メタバース オブジェクトのプロパティ\) ウィンドウのスクリーンショット](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

次のものに対するコネクタが必要です。

- ユーザーが表現される各 Active Directory フォレスト。 この表現には、**foreignSecurityPrincipals** オブジェクトと **Contact** オブジェクトを含めることができます。
- Azure AD 内のコネクタ。

Azure AD へのコネクタがない場合は、「[MV 属性](#mv-attributes)」のセクションを参照して、Azure AD へのプロビジョニングの基準を確認してください。

**[Connectors]\(コネクタ\)** タブから、[コネクタ スペース オブジェクト](#connector-space-object-properties)に移動することもできます。 行を選択し、**[プロパティ]** をクリックします。

## <a name="next-steps"></a>次の手順
- [Azure AD Connect Sync](how-to-connect-sync-whatis.md) の詳細情報についてはこちらをご覧ください。
- [ハイブリッド ID](whatis-hybrid-identity.md) の詳細についてはこちらをご覧ください。
