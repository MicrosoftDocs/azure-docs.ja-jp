---
title: "Generic LDAP コネクタ | Microsoft Docs"
description: "この記事では、Microsoft の Generic LDAP コネクタを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 984beeb0-4d91-4908-ad81-c19797c4891b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 9245a2ce63746f039a3015a5a0cda2ff05cf950e
ms.lasthandoff: 03/14/2017


---
# <a name="generic-ldap-connector-technical-reference"></a>Generic LDAP コネクタに関するテクニカル リファレンス
この記事では Generic LDAP コネクタについて説明します。 この記事は次の製品に適用されます。

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * 4.1.3671.0 以降の修正プログラム [KB3092178](https://support.microsoft.com/kb/3092178)を使用する必要があります。

MIM2016 と FIM2010R2 の場合、コネクタは [Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=717495)からダウンロードして入手できます。

IETF RFC を参照する際、このドキュメントでは形式 (RFC [RFC number]/[section in RFC document]) を使用します (RFC 4512/4.3 など)。
詳細については http://tools.ietf.org/html/rfc4500 を参照してください (4500 を正しい RFC 番号に置き換える必要があります)。

## <a name="overview-of-the-generic-ldap-connector"></a>Generic LDAP コネクタの概要
Generic LDAP コネクタを使用すると、同期サービスを LDAP v3 サーバーと統合することができます。

IETF RFC では、特定の操作やスキーマ要素 (たとえば、差分インポートを実行する上で必要なもの) は指定されていません。 これらの操作では、明示的に指定された LDAP ディレクトリのみがサポートされます。

包括的な観点から見た場合、コネクタの現在のリリースでサポートされている機能は次のとおりです。

| 機能 | サポート |
| --- | --- |
| 接続先のデータ ソース |コネクタはすべての LDAP v3 サーバー (RFC 4510 準拠) でサポートされています。 次のデータ ソースでテスト済みです。 <li>Microsoft Active Directory ライトウェイト ディレクトリ サービス (AD LDS)</li><li>Microsoft Active Directory グローバル カタログ (AD GC)</li><li>389 Directory Server</li><li>Apache Directory Server</li><li>IBM Tivoli DS</li><li>Isode Directory</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Open DJ</li><li>Open DS</li><li>Open LDAP (openldap.org)</li><li>Oracle (以前は Sun) Directory Server Enterprise Edition</li><li>RadiantOne Virtual Directory Server (VDS)</li><li>Sun One Directory Server</li>**ただし、次のディレクトリはサポートされていません。** <li>Microsoft Active Directory Domain Services (AD DS) (代わりに組み込みの Active Directory コネクタを使用)</li><li>Oracle Internet Directory (OID)</li> |
| シナリオ |<li>オブジェクトのライフサイクル管理</li><li>グループ管理</li><li>パスワード管理</li> |
| 操作 |すべての LDAP ディレクトリで次の操作がサポートされています。 <li>フル インポート</li><li>エクスポート</li>次の操作は特定のディレクトリでのみサポートされています。<li>差分インポート</li><li>パスワードの設定、パスワードの変更</li> |
| スキーマ |<li>スキーマは LDAP スキーマで検出されます (RFC3673 および RFC4512/4.2)</li><li>構造型クラス、aux クラス、および extensibleObject オブジェクト クラスをサポートします (RFC4512/4.3)</li> |

### <a name="delta-import-and-password-management-support"></a>差分インポートおよびパスワード管理のサポート
差分インポートおよびパスワード管理がサポートされているディレクトリは次のとおりです。

* Microsoft Active Directory ライトウェイト ディレクトリ サービス (AD LDS)
  * 差分インポートのすべての操作をサポート
  * パスワードの設定をサポート
* Microsoft Active Directory グローバル カタログ (AD GC)
  * 差分インポートのすべての操作をサポート
  * パスワードの設定をサポート
* 389 Directory Server
  * 差分インポートのすべての操作をサポート
  * パスワードの設定とパスワードの変更をサポート
* Apache Directory Server
  * このディレクトリには永続的な変更ログがないため差分インポートはサポートしていません。
  * パスワードの設定をサポート
* IBM Tivoli DS
  * 差分インポートのすべての操作をサポート
  * パスワードの設定とパスワードの変更をサポート
* Isode Directory
  * 差分インポートのすべての操作をサポート
  * パスワードの設定とパスワードの変更をサポート
* Novell eDirectory and NetIQ eDirectory
  * 差分インポートの追加、更新、および名前変更操作をサポート
  * 差分インポートの削除操作はサポートしていません。
  * パスワードの設定とパスワードの変更をサポート
* Open DJ
  * 差分インポートのすべての操作をサポート
  * パスワードの設定とパスワードの変更をサポート
* Open DS
  * 差分インポートのすべての操作をサポート
  * パスワードの設定とパスワードの変更をサポート
* Open LDAP (openldap.org)
  * 差分インポートのすべての操作をサポート
  * パスワードの設定をサポート
  * パスワードの変更はサポートしていません。
* Oracle (以前は Sun) Directory Server Enterprise Edition
  * 差分インポートのすべての操作をサポート
  * パスワードの設定とパスワードの変更をサポート
* RadiantOne Virtual Directory Server (VDS)
  * 7.1.1 以上のバージョンを使用する必要があります。
  * 差分インポートのすべての操作をサポート
  * パスワードの設定とパスワードの変更をサポート
* Sun One Directory Server
  * 差分インポートのすべての操作をサポート
  * パスワードの設定とパスワードの変更をサポート

### <a name="prerequisites"></a>前提条件
コネクタを使用する前に、次のものが同期サーバーにインストールされていることを確認してください。

* Microsoft .NET 4.5.2 Framework 以降

### <a name="detecting-the-ldap-server"></a>LDAP サーバーの検出
コネクタは、さまざまな手法に基づいて LDAP サーバーを検出し、識別します。 コネクタでは、ルート DSE、ベンダー名、バージョンを使用し、スキーマを調べて LDAP サーバーに存在している既知の一意のオブジェクトと属性を見つけます。 このデータは、見つかると、コネクタでの構成オプションの事前設定に使用されます。

### <a name="connected-data-source-permissions"></a>接続先のデータ ソースのアクセス権
接続されたディレクトリ内のオブジェクトに対してインポートおよびエクスポート操作を実行するには、それに必要なアクセス許可がコネクタのアカウントになければなりません。 コネクタは、エクスポートするためには書き込みアクセス許可を、インポートするためには読み取りアクセス許可を備えている必要があります。 アクセス許可は、ターゲット ディレクトリ自体の管理エクスペリエンス内で構成されます。

### <a name="ports-and-protocols"></a>ポートとプロトコル
コネクタでは構成で指定されたポート番号が使用されます。既定では、LDAP の場合は 389、LDAPS の場合は 636 が使用されます。

LDAPS では、SSL 3.0 または TLS を使用する必要があります。 SSL 2.0 はサポートされていないため、アクティブ化できません。

### <a name="required-controls-and-features"></a>必要なコントロールと機能
コネクタが正常に動作するためには、LDAP サーバー上で次の LDAP コントロール/機能を利用できる必要があります:   
`1.3.6.1.4.1.4203.1.5.3` True/False フィルター

True/False フィルターは、LDAP ディレクトリでサポートされていても、多くの場合は報告されません。**[Mandatory Features Not Found (必須機能が見つかりません)]** という状況下で **[Global Page (グローバル ページ)]** に表示される場合があります。　 True/False フィルターは、LDAP クエリで **OR** フィルターを作成する場合に使用します (たとえば、複数のオブジェクト型をインポートする場合)。 複数の種類のオブジェクトをインポートできる場合に、LDAP サーバーでこの機能がサポートされます。

使用するディレクトリの一意の識別子がアンカーである場合は、次の属性も使用できる必要があります (詳細については、「[アンカーの構成](#configure-anchors)」セクションを参照してください)。  
`1.3.6.1.4.1.4203.1.5.1` すべての操作属性

1 回のディレクトリ呼び出しに収まり切れない数のオブジェクトがディレクトリにある場合は、ページングの使用をお勧めします。 ページングが機能するには、次のオプションのいずれかが必要です。

**オプション 1:**   
`1.2.840.113556.1.4.319` pagedResultsControl

**オプション 2:**   
`2.16.840.1.113730.3.4.9` VLVControl  
`1.2.840.113556.1.4.473` SortControl

コネクタの構成で両方のオプションが有効になっている場合は、pagedResultsControl が使用されます。

`1.2.840.113556.1.4.417` ShowDeletedControl

ShowDeletedControl は USNChanged 差分インポート方法で使用できるだけであり、削除されたオブジェクトを確認できます。

コネクタは、サーバーに上記オプションがあるかどうか、検出を試みます。 オプションを検出できなかった場合、コネクタのプロパティ内のグローバル ページに警告が表示されます。 LDAP のすべてが、サポートしているコントロール/機能をすべて提示しているとは限らないため、この警告が表示されてもコネクタは問題なく動作する場合があります。

### <a name="delta-import"></a>差分インポート
差分インポートは、サポート ディレクトリが検出された場合にのみ使用できます。 現在、次の方法が使用されています。

* LDAP アクセスログ。 [http://www.openldap.org/doc/admin24/overlays.html#Access Logging](http://www.openldap.org/doc/admin24/overlays.html#Access Logging) を参照してください
* LDAP 変更ログ。 [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
* タイムスタンプ。 Novell/NetIQ eDirectory の場合、コネクタは最後の日付/時刻を使用して、作成されたオブジェクトと更新されたオブジェクトを取得します。 Novell/NetIQ eDirectory の場合、同等の方法を使用して、削除されたオブジェクトを取得することはできません。 タイムスタンプ オプションはまた、LDAP サーバー上で他にアクティブになっている差分インポート方法がない場合に使用することができます。 このオプションを使用して削除されたオブジェクトをインポートすることはできません。
* USNChanged。 [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>サポートされていません
次の LDAP 機能はサポートされていません。

* サーバー間の LDAP 参照 (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>新しいコネクタの作成
一般的な LDAP コネクタを作成するには、**[同期サービス]** で **[管理エージェント]** を選択し、**[作成]** を選択します。 **Generic LDAP (Microsoft)** コネクタを選択します。

![コネクタの作成](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>接続
[接続] ページで、ホスト、ポート、バインディングに関する情報を指定する必要があります。 選択したバインディングによっては、次のセクションに追加の情報が表示される場合があります。

![接続](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

* 接続タイムアウトの設定は、スキーマの検出時に行われるサーバーへの最初の接続でのみ使用されます。
* バインディングが匿名である場合は、ユーザー名/パスワードも証明書も使用されません。
* その他のバインディングの場合は、ユーザー名/パスワードに情報を入力するか、または証明書を選択します。
* Kerberos を使用して認証する場合は、ユーザーの領域/ドメインを指定します。

RFC4522 構文を使用してスキーマに属性を定義する場合は、 **[属性の別名]** テキスト ボックスを使用します。 スキーマの検出中にこれらの属性を検出することはできないため、コネクタがそれらの属性を識別できるようにサポートが必要です。 たとえば、バイナリ属性として userCertificate 属性を正しく識別するには、[Attribute Aliases (属性の別名)] ボックスに次を入力する必要があります。

`userCertificate;binary`

この構成がどのように表示されるかを次に示します。

![接続](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

**[操作属性をスキーマに含める]** チェックボックスを選択して、サーバーで作成された属性も含めます。 これには、オブジェクトの作成日時および最終更新日時などの属性が含まれます。

拡張可能なオブジェクト (RFC4512/4.3) が使用されている場合は **[Include extensible attributes in schema (拡張可能な属性をスキーマに含める)]** を選択します。このオプションを有効にすると、どの属性もすべてのオブジェクトで使用できるようになります。 このオプションを選択すると、スキーマの規模が非常に大きくなります。接続先のディレクトリがこの機能を使用していない限り、このオプションは非選択のままにしておくことをお勧めします。

### <a name="global-parameters"></a>グローバル パラメーター
[グローバル パラメーター] ページで、DN を差分変更ログおよびその他の LDAP 機能に構成します。 このページには、LDAP サーバーから提供された情報があらかじめ設定されています。

![接続](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

上部セクションには、サーバーの名前など、サーバーから提供された情報が表示されます。 コネクタでは、必須のコントロールがルート DSE に存在していることも確認されます。 該当するコントロールが一覧表示されていない場合は、警告が表示されます。 LDAP ディレクトリの中には、一部の機能しかルート DSE に一覧表示しないものがあります。このため、警告が存在する場合でも、コネクタは問題なく動作する場合があります。

**[Supported Controls (サポートされるコントロール)]** チェック ボックスでは、特定の操作の動作を制御します。

* ツリー削除を選択すると、1 回の LDAP 呼び出しで階層が削除されます。 ツリー削除を選択していない場合、コネクタは必要に応じて再帰削除を実行します。
* 結果のページングが選択されている場合、コネクタは実行の手順で指定されたサイズに従ってページング インポートを行います。
* VLVControl および SortControl は、pagedResultsControl に代わる、LDAP ディレクトリからのデータの読み取り方法です。
* 3 つのオプション (pagedResultsControl、VLVControl、SortControl) がいずれも選択されていない場合、コネクタは&1; 回の操作であらゆるオブジェクトをインポートします。この場合、オブジェクトが大規模なディレクトリであると、失敗する可能性があります。
* ShowDeletedControl は、差分インポート方法が USNChanged である場合にのみ使用されます。

変更ログ DN は差分変更ログで使用される名前付けコンテキストです (たとえば、 **cn=changelog**)。 差分インポートを実行するには、この値を指定する必要があります。

既定の変更ログ DN の一覧を次に示します。

| Directory | 差分変更ログ |
| --- | --- |
| Microsoft AD LDS および AD GC |自動的に検出されます。 USNChanged。 |
| Apache Directory Server |使用できません。 |
| Directory 389 |変更ログ。 使用する既定値: **cn=changelog** |
| IBM Tivoli DS |変更ログ。 使用する既定値: **cn=changelog** |
| Isode Directory |変更ログ。 使用する既定値: **cn=changelog** |
| Novell/NetIQ eDirectory |使用できません。 タイムスタンプ。 コネクタは、前回の更新の日時を使用して、追加されたレコードと更新されたレコードを取得します。 |
| Open DJ/DS |変更ログ。  使用する既定値: **cn=changelog** |
| Open LDAP |アクセス ログ。 使用する既定値: **cn=accesslog** |
| Oracle DSEE |変更ログ。 使用する既定値: **cn=changelog** |
| RadiantOne VDS |仮想ディレクトリ。 VDS に接続されているディレクトリに依存します。 |
| Sun One Directory Server |変更ログ。 使用する既定値: **cn=changelog** |

パスワード属性は、パスワード変更およびパスワード設定の操作でパスワードを設定する場合に、コネクタで使用する必要がある属性の名前です。
この値は既定で **userPassword** に設定されますが、特定の LDAP システムについては必要に応じて変更することができます。

追加のパーティションの一覧に加えて、自動的に検出されない名前空間を追加することもできます。 たとえばこの設定は、複数のサーバーが&1; つの論理クラスターを構成していて、すべて同時にインポートする必要がある場合などに使用することができます。 Active Directory では&1; つのフォレスト内に複数のドメインを保持することができますが、ドメインはすべて&1; つのスキーマを共有します。それと同じことを、このボックスに追加の名前空間を入力することでシミュレートできます。 それぞれの名前空間は、さまざまなサーバーからインポートすることができ、[Configure Partitions and Hierarchies (パーティションと階層の構成)] ページで追加の構成を行うことができます。 新しい行を取得するには Ctrl + Enter キーを使用します。

### <a name="configure-provisioning-hierarchy"></a>プロビジョニング階層の構成
このページは、プロビジョニングが必要なオブジェクトの種類 (organizationalUnit など) に DN コンポーネント (OU など) をマップする場合に使用します。

![Provisioning Hierarchy](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

プロビジョニング階層を構成することによって、必要に応じて自動的に構造体を作成するようコネクタを構成できます。 たとえば、名前空間 dc=contoso,dc=com が存在し、新しいオブジェクト cn=Joe, ou=Seattle, c=US, dc=contoso, dc=com がプロビジョニングされている場合、コネクタでは、US の type country と Seattle の organizationalUnit がまだディレクトリ内に存在していない場合、それらから成るオブジェクトを作成することができます。

### <a name="configure-partitions-and-hierarchies"></a>パーティションと階層の構成
パーティションおよび階層のページでは、インポートおよびエクスポートするオブジェクトを含んでいるすべての名前空間を選択します。

![パーティション](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

名前空間ごとに、[接続] 画面で指定した値を上書きする接続設定を構成することもできます。 これらの値が既定の空白値のままである場合は、[接続] 画面からの情報が使用されます。

コネクタでは、インポート元のコンテナーおよび OU と、エクスポート先のコンテナーおよび OU を選択することもできます。

検索を実行すると、検索はパーティション内のすべてのコンテナーで行われます。 多数のコンテナーがある場合、この動作はパフォーマンスの低下につながります。

>[!NOTE]
Generic LDAP コネクタに対する 2017 年 3 月の更新以降、検索範囲を選択したコンテナーのみに制限することができます。 これは、次の図に示すように、[Search only in selected containers (選択したコンテナーのみ検索)] チェックボックスをオンにすることで実行できます。

![選択したコンテナーのみ検索](./media/active-directory-aadconnectsync-connector-genericldap/partitions-only-selected-containers.png)

### <a name="configure-anchors"></a>アンカーの構成
このページには、あらかじめ構成された値が常に表示されるため、ページの内容を変更することはできません。 サーバーのベンダーが明らかになっている場合、このアンカーには変更できない属性 (オブジェクトの GUID など) が設定されている場合があります。 サーバーのベンダーが検出されていない場合、またはそれには変更できない属性がないことがわかっている場合、コネクタはアンカーとして dn (識別名) を使用します。

![アンカー](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)


使用する LDAP サーバーとアンカーの一覧を次に示します。

| Directory | アンカー属性 |
| --- | --- |
| Microsoft AD LDS および AD GC |objectGUID |
| 389 Directory Server |dn |
| Apache Directory |dn |
| IBM Tivoli DS |dn |
| Isode Directory |dn |
| Novell/NetIQ eDirectory |GUID |
| Open DJ/DS |dn |
| Open LDAP |dn |
| Oracle ODSEE |dn |
| RadiantOne VDS |dn |
| Sun One Directory Server |dn |

## <a name="other-notes"></a>他の注意事項
このセクションでは、このコネクタに固有の特徴、または他の理由で理解しておく必要のある特徴を示します。

### <a name="delta-import"></a>差分インポート
Open LDAP の差分ウォーターマークは、UTC 日付/時刻です。 このため、FIM 同期サービスと Open LDAP の間で時間を同期させる必要があります。 そうしないと、差分変更ログの一部のエントリが省略される場合があります。

Novell eDirectory の場合、差分インポートでオブジェクトの削除は検出されません。 このため、削除されたすべてのオブジェクトを検索するには、フル インポートを定期的に実行する必要があります。

日付/時刻に基づく差分変更ログを備えるディレクトリの場合は、フル インポートを定期的に実行することを強くお勧めします。 このプロセスを実行すると、LDAP サーバーとコネクタ スペースに現在存在するものとの相違点を同期エンジンで見つけることができます。

## <a name="troubleshooting"></a>トラブルシューティング
* コネクタのトラブルシューティングを行うためにログ記録を有効にする方法については、「 [How to Enable ETW Tracing for Connectors (コネクタの ETW トレースを有効にする方法)](http://go.microsoft.com/fwlink/?LinkId=335731)」を参照してください。

