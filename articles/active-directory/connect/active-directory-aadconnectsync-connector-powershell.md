---
title: "PowerShell コネクタ | Microsoft Docs"
description: "この記事では、Microsoft の Windows PowerShell コネクタを構成する方法について説明します。"
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6dba8e34-a874-4ff0-90bc-bd2b0a4199b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: ab8c601d862868018fdffb4cd49e8b26acb878c9
ms.openlocfilehash: 65e5e6938ce67b6ba9751e38d23715f3512c4b93


---
# <a name="windows-powershell-connector-technical-reference"></a>Windows PowerShell コネクタに関するテクニカル リファレンス
この記事では、Windows PowerShell コネクタについて説明します。 この記事は次の製品に適用されます。

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * 4.1.3671.0 以降の修正プログラム [KB3092178](https://support.microsoft.com/kb/3092178)を使用する必要があります。

MIM2016 と FIM2010R2 の場合、コネクタは [Microsoft ダウンロード センター](http://go.microsoft.com/fwlink/?LinkId=717495)からダウンロードして入手できます。

## <a name="overview-of-the-powershell-connector"></a>PowerShell コネクタの概要
PowerShell コネクタを利用すれば、Windows PowerShell ベースの API を提供する外部システムと同期サービスを統合できます。 このコネクタは、呼び出しベースの ECMA2 (Extensible Connectivity Management Agent 2) フレームワークと Windows PowerShell の機能の橋渡しとなります。 ECMA フレームワークの詳細については、「 [Extensible Connectivity 2.2 Management Agent リファレンス](https://msdn.microsoft.com/library/windows/desktop/hh859557.aspx)」を参照してください。

### <a name="prerequisites"></a>前提条件
コネクタを使用する前に、次のものが同期サーバーにインストールされていることを確認してください。

* Microsoft .NET 4.5.2 Framework 以降
* Windows PowerShell 2.0、3.0、4.0

同期サービスのサーバーの実行ポリシーで、Windows PowerShell スクリプトの実行をコネクタに許可するように構成する必要があります。 コネクタが実行するスクリプトにデジタル署名がない場合を除き、コマンド   
`Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

## <a name="create-a-new-connector"></a>新しいコネクタの作成
同期サービスで Windows PowerShell コネクタを作成するには、同期サービスで要求される手順を実行する一連の Windows PowerShell スクリプトを提供する必要があります。 接続先のデータ ソースや必要とする機能により、実装の必要なスクリプトが異なります。 このセクションでは、実装可能なスクリプトとそれらが必要となる状況について説明します。

Windows PowerShell コネクタは、同期サービスのデータベース内に各スクリプトを保存するように設計されています。 ファイル システムに保存されているスクリプトを実行することができますが、各スクリプトの本文をコネクタの構成に直接挿入する方が簡単です。

PowerShell コネクタを作成するには、**[同期サービス]** で **[管理エージェント]** を選択し、**[作成]** を選択します。 **PowerShell (Microsoft)** コネクタを選択します。

![コネクタの作成](./media/active-directory-aadconnectsync-connector-powershell/createconnector.png)

### <a name="connectivity"></a>接続
リモート システムに接続するための構成パラメーターを指定します。 これらの値は同期サービスによって安全に保存され、コネクタが実行されるときに Windows PowerShell スクリプトで利用できます。

![接続](./media/active-directory-aadconnectsync-connector-powershell/connectivity.png)

次の接続パラメーターを構成できます。

**接続**

| パラメーター | 既定値 | 目的 |
| --- | --- | --- |
| サーバー |<Blank> |コネクタの接続先のサーバーの名前です。 |
| ドメイン |<Blank> |コネクタの実行時に使用するために保存する資格情報のドメインです。 |
| User |<Blank> |コネクタの実行時に使用するために保存する資格情報のユーザー名です。 |
| パスワード |<Blank> |コネクタの実行時に使用するために保存する資格情報のパスワードです。 |
| コネクタ アカウントの偽装 |False |True の場合、同期サービスは指定された資格情報のコンテキストで Windows PowerShell スクリプトを実行します。 可能であれば、各スクリプトに渡される **$Credentials** パラメーターを偽装の代わりに使用することが推奨されます。 このオプションを使用するために必要な追加のアクセス許可の詳細については、「 [偽装のための追加構成](#additional-configuration-for-impersonation)」を参照してください。 |
| 偽装時のユーザー プロファイルの読み込み |False |偽装中に、コネクタの資格情報のユーザー プロファイルの読み込むように Windows に指示します。 偽装されるユーザーにローミング プロファイルがある場合、コネクタはローミング プロファイルを読み込みません。 このパラメーターを使用するために必要な追加のアクセス許可の詳細については、「 [偽装のための追加構成](#additional-configuration-for-impersonation)」を参照してください。 |
| 偽装時のログオンの種類 |なし |偽装中のログオンの種類 詳細については、[dwLogonType][dw] 関連の文書をご覧ください。 |
| 署名済みスクリプトのみ |False |True の場合、Windows PowerShell コネクタは、各スクリプトに有効なデジタル署名があることを検証します。 False の場合、同期サービス サーバーの Windows PowerShell 実行ポリシーを「RemoteSigned」または「Unrestricted」にします。 |

**共通モジュール**  
このコネクタでは、共有 Windows PowerShell モジュールを構成に保存できます。 コネクタがスクリプトを実行するとき、各スクリプトでインポートできるように、Windows PowerShell モジュールがファイル システムに抽出されます。

インポート、エクスポート、パスワード同期のスクリプトについては、共通モジュールはコネクタの MAData フォルダーに抽出されます。 スキーマ、検証、階層、パーティションの探索スクリプトについては、共通モジュールは %TEMP% フォルダーに抽出されます。 いずれの場合も、抽出された共通モジュール スクリプトには共通モジュール スクリプト名設定に基づいて名前が付けられます。

MAData フォルダーから「FIMPowerShellConnectorModule.psm1」という名前のモジュールを読み込むには、次のステートメントを使用します。 `Import-Module (Join-Path -Path [Microsoft.MetadirectoryServices.MAUtils]::MAFolder -ChildPath "FIMPowerShellConnectorModule.psm1")`

%TEMP% フォルダーから「FIMPowerShellConnectorModule.psm1」という名前のモジュールを読み込むには、次のステートメントを使用します。 `Import-Module (Join-Path -Path $env:TEMP -ChildPath "FIMPowerShellConnectorModule.psm1")`

**パラメーター検証**  
検証スクリプトは、管理者が指定したコネクタ構成パラメーターが有効であることを確認するために使用できるオプションの Windows PowerShell スクリプトです。 サーバー、接続資格情報、接続パラメーターの検証が、検証スクリプトの一般的な使用方法です。 検証スクリプトは、次のタブとダイアログの変更後に呼び出されます。

* 接続
* グローバル パラメーター
* パーティション構成

検証スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameterPage |[ConfigParameterPage][cpp] |検証要求をトリガーした [構成] タブまたはダイアログ。 |
| ConfigParameters |[KeyedCollection][keyk] [string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |

検証スクリプトは、1 つの ParameterValidationResult オブジェクトをパイプラインに返します。

**スキーマ検出**  
スキーマ検出スクリプトは必須です。 このスクリプトは、属性フロー ルールの構成時に同期サービスで使用されるオブジェクトの種類、属性、属性制約を返します。 スキーマ検出スクリプトは、コネクタの作成中に実行され、コネクタのスキーマにデータを入力します。 これは、Synchronization Service Manager のスキーマの更新アクションによっても使用されます。

スキーマ検出スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk] [string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |

このスクリプトは&1; つの [Schema][schema] オブジェクトをパイプラインに返す必要があります。 Schema オブジェクトは、オブジェクトの種類 (ユーザー、グループなど) を表す [SchemaType][schemaT] オブジェクトで構成されます。 SchemaType オブジェクトには、特定の種類の属性 (名、姓、郵便住所など) を表す一連の [SchemaAttribute][schemaA] オブジェクトが含まれています。

**追加パラメーター**  
標準的な構成設定に加え、コネクタのインスタンスに固有のカスタム構成設定を追加で定義できます。 追加パラメーターはコネクタ、パーティション、実行ステップのレベルで指定し、関連 Windows PowerShell スクリプトからアクセスできます。 カスタム構成設定は同期サービス データベースに平文で保存するか、暗号化して保存できます。 同期サービスは、必要に応じて、安全な構成設定を自動的に暗号化し、復号化します。

カスタム構成設定を指定するには、コンマ (,) で各パラメーターの名前を区切ります。

スクリプトからカスタム構成設定にアクセスするには、アンダースコア (\_) とパラメーターの範囲 (Global、Partition、RunStep) をサフィックスとして名前に付ける必要があります。 たとえば、Global FileName パラメーターにアクセスするには、このコード スニペットを使用します。 `$ConfigurationParameters["FileName_Global"].Value`

### <a name="capabilities"></a>機能
管理エージェント デザイナーの [機能] タブでコネクタの動作と機能を定義します。 コネクタの作成後、このタブの選択を変更することはできません。 次の表は、機能の設定をまとめたものです。

![機能](./media/active-directory-aadconnectsync-connector-powershell/capabilities.png)

| 機能 | Description |
| --- | --- |
| [識別名の様式][dnstyle] |コネクタで識別名を利用できるかどうかと、利用できる場合、その様式を示します。 |
| [エクスポートの種類][exportT] |エクスポート スクリプトに表示されるオブジェクトの種類を決定します。 <li>AttributeReplace – 属性が変更されたとき、複数値属性の値のフル セットを含めます。</li><li>AttributeUpdate – 属性が変更されたとき、複数値属性の差分のみを含めます。</li><li>MultivaluedReferenceAttributeUpdate - 非参照複数値属性のフル セットと複数値参照属性の差のみを含めます。</li><li>ObjectReplace – 属性が変更されたとき、オブジェクトのすべての属性を含めます</li> |
| [データの正規化][DataNorm] |スクリプトに提供する前にアンカー属性を正規化するように同期サービスに指示します。 |
| [オブジェクト確認][oconf] |同期サービスの保留中のインポートの動作を構成します。 <li>Normal – エクスポートされたあらゆる変更をインポートで確認することを要求する既定の動作</li><li>NoDeleteConfirmation – オブジェクトが削除されるとき、保留中のインポートは生成されません。</li><li>NoAddAndDeleteConfirmation – オブジェクトが作成または削除されるとき、保留中のインポートは生成されません。</li> |
| アンカーとして DN を使用する |識別名の様式が LDAP に設定されている場合、コネクタ スペースのアンカー属性も識別名になります。 |
| 複数のコネクタの同時操作 |選択した場合、複数の Windows PowerShell コネクタを同時に実行できます。 |
| パーティション |選択した場合、コネクタは複数のパーティションとパーティション検出に対応します。 |
| 階層 |選択した場合、コネクタは LDAP 様式の階層構造に対応します。 |
| インポートの有効化 |選択した場合、コネクタはインポート スクリプトでデータをインポートします。 |
| 差分インポートの有効化 |選択した場合、コネクタはインポート スクリプトから差分を要求できます。 |
| エクスポートの有効化 |選択した場合、コネクタはエクスポート スクリプトでデータをエクスポートします。 |
| 完全エクスポートの有効化 |選択した場合、エクスポート スクリプトはコネクタ スペース全体のエクスポートに対応します。 このオプションを使用するには、エクスポート有効化も選択する必要があります。 |
| 最初のエクスポート パスに参照値なし |選択した場合、参照属性は&2; 番目のエクスポート パスでエクスポートされます。 |
| オブジェクトの名前変更の有効化 |選択した場合、識別名を変更できます。 |
| 置換として削除/追加 |選択した場合、削除/追加操作が&1; つの置換としてエクスポートされます。 |
| パスワード操作の有効化 |選択した場合、パスワード同期スクリプトを利用できます。 |
| 最初のパスのエクスポート パスワードの有効化 |選択した場合、プロビジョニング中に設定されたパスワードがオブジェクトの作成時にエクスポートされます。 |

### <a name="global-parameters"></a>グローバル パラメーター
管理エージェント デザイナーの [Global Parameters (グローバル パラメーター)] タブでは、コネクタによって実行される Windows PowerShell スクリプトを構成できます。 また、[接続] タブで定義されているカスタム構成設定のグローバル値を構成できます。

**パーティション検出**  
パーティションは、1 つの共有スキーマ内の別個の名前空間です。 たとえば、Active Directory では、各ドメインは&1; つのフォレスト内のパーティションになります。 パーティションは、インポート操作とエクスポート操作を論理的にグループ化します。 インポートとエクスポートにはコンテキストとしてパーティションがあり、すべての操作はそのコンテキストで実行されます。 パーティションは LDAP の階層を表します。 パーティションの識別名は、返されたすべてのオブジェクトがパーティションの範囲内にあることを検証するためにインポートで使用されます。 パーティションの識別名は、メタバースからコネクタ スペースにプロビジョニングし、エクスポート中にオブジェクトを関連付けるパーティションを決定する際にも使用されます。

パーティション検出スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |

このスクリプトは&1; つの [Partition][part] オブジェクトか Partition オブジェクトの List[T] をパイプラインに返します。

**階層検出**  
階層検出スクリプトは、識別名様式の機能が LDAP の場合にのみ使用されます。 このスクリプトは、インポート操作とエクスポート操作の対象となる/ならない一連のコンテナーを閲覧、選択できるようにするために使用されます。 このスクリプトは、スクリプトに指定されたルート ノードの直接的な子となるノードの一覧のみを提供します。

階層検出スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |
| ParentNode |[HierarchyNode][hn] |階層のルート ノードであり、この下にある直接の子をスクリプトが返します。 |

スクリプトは&1; つの子 HierarchyNode オブジェクトか子 HierarchyNode オブジェクトの List[T] を返します。

#### <a name="import"></a>インポート
インポート操作をサポートするコネクタは、3 つのスクリプトを実装する必要があります。

**インポート開始**  
インポート開始スクリプトは、インポート実行手順の最初に実行されます。 この手順の間、ソース システムに接続し、接続したシステムからデータをインポートする前の準備手順を実行できます。

インポート開始スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |インポート実行の種類 (差分または完全)、パーティション、階層、透かし、予想ページ サイズをスクリプトに通知します。 |
| 型 |[Schema][schema] |インポートするコネクタ スペースのスキーマ。 |

このスクリプトは&1; つの [OpenImportConnectionResults][oicres] オブジェクトをパイプラインに返します (たとえば `Write-Output (New-Object Microsoft.MetadirectoryServices.OpenImportConnectionResults)`)。

**データのインポート**  
データのインポート スクリプトはコネクタによって呼び出され、インポートするデータがなくなったらそのことを示します。 Windows PowerShell コネクタのページ サイズは 9,999 オブジェクトです。 スクリプトがインポートするオブジェクトを 10,000 個以上返す場合、ページングを利用する必要があります。 コネクタは、透かしを保存するためのカスタム データ プロパティを公開します。透かしを保存すると、データのインポート スクリプトが呼び出されるたびに、スクリプトが前回中断した部分からオブジェクトのインポートが再開されます。

データのインポート スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |
| GetImportEntriesRunStep |[ImportRunStep][irs] |ページングされたインポートと差分インポートで使用される透かし (CustomData) を保持します。 |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |インポート実行の種類 (差分または完全)、パーティション、階層、透かし、予想ページ サイズをスクリプトに通知します。 |
| 型 |[Schema][schema] |インポートするコネクタ スペースのスキーマ。 |

データのインポート スクリプトは List[[CSEntryChange][csec]] オブジェクトをパイプラインに書き込みます。 このコレクションは、インポートされる各オブジェクトを表す CSEntryChange 属性で構成されます。 フル インポート実行の間、このコレクションには、すべてのオブジェクトの全属性を持つ CSEntryChange オブジェクトのフル セットが与えられます。 差分インポートの間、CSEntryChange オブジェクトには、インポートする各オブジェクトの属性レベル差分か変更された完全なオブジェクト (置換モード) が含まれます。

**インポート終了**  
インポート実行の終わりに、インポート終了スクリプトが実行されます。 このスクリプトによって、必要なクリーンアップ作業が実行されます (システムへの接続の終了、エラーへの対応など)。

インポート終了スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |
| OpenImportConnectionRunStep |[OpenImportConnectionRunStep][oicrs] |インポート実行の種類 (差分または完全)、パーティション、階層、透かし、予想ページ サイズをスクリプトに通知します。 |
| CloseImportConnectionRunStep |[CloseImportConnectionRunStep][cecrs] |インポートが終了した理由をスクリプトに通知します。 |

このスクリプトは&1; つの [CloseImportConnectionResults][cicres] オブジェクトをパイプラインに返します (たとえば `Write-Output (New-Object Microsoft.MetadirectoryServices.CloseImportConnectionResults)`)。

#### <a name="export"></a>エクスポート
コネクタのインポート アーキテクチャと同じです。エクスポートをサポートするコネクタは&3; つのスクリプトを実装する必要があります。

**エクスポート開始**  
エクスポート開始スクリプトは、エクスポート実行手順の最初に実行されます。 この手順の間、ソース システムに接続し、接続したシステムにデータをエクスポートする前の準備手順を実行できます。

エクスポート開始スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |エクスポート実行の種類 (差分または完全)、パーティション、階層、予想ページ サイズをスクリプトに通知します。 |
| 型 |[Schema][schema] |エクスポートするコネクタ スペースのスキーマ。 |

スクリプトはパイプラインに出力を返しません。

**データのエクスポート**  
同期サービスは、保留中のエクスポートをすべて処理するために必要な回数だけデータのエクスポート スクリプトを呼び出します。 コネクタ スペースにコネクタのページ サイズを超える保留中のエクスポートがある場合、データのエクスポート スクリプトは複数回呼び出されます。同じオブジェクトに対して複数回呼び出されることもあります。

データのエクスポート スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |
| CSEntries |IList[CSEntryChange][csec] |すべてのコネクタ スペース オブジェクトとこのパスで処理する保留中エクスポートを一覧表示します。 |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |エクスポート実行の種類 (差分または完全)、パーティション、階層、予想ページ サイズをスクリプトに通知します。 |
| 型 |[Schema][schema] |エクスポートするコネクタ スペースのスキーマ。 |

データのエクスポート スクリプトは [PutExportEntriesResults][peeres] オブジェクトをパイプラインに返します。 このオブジェクトには、エラーまたはアンカー属性の変更が発生しない限り、エクスポートされたコネクタごとに結果情報を含める必要はありません。 たとえば、PutExportEntriesResults オブジェクトをパイプラインに返すには、 `Write-Output (New-Object Microsoft.MetadirectoryServices.PutExportEntriesResults)`

**エクスポート終了**  
エクスポート実行の終わりに、エクスポート終了スクリプトが実行されます。 このスクリプトによって、必要なクリーンアップ作業が実行されます (システムへの接続の終了、エラーへの対応など)。

エクスポート終了スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |
| OpenExportConnectionRunStep |[OpenExportConnectionRunStep][oecrs] |エクスポート実行の種類 (差分または完全)、パーティション、階層、予想ページ サイズをスクリプトに通知します。 |
| CloseExportConnectionRunStep |[CloseExportConnectionRunStep][cecrs] |エクスポートが終了した理由をスクリプトに通知します。 |

スクリプトはパイプラインに出力を返しません。

#### <a name="password-synchronization"></a>パスワードの同期
Windows PowerShell コネクタをパスワードの変更/リセットのターゲットとして利用できます。

パスワード スクリプトは、コネクタから次のパラメーターを受け取ります。

| Name | データ型 | Description |
| --- | --- | --- |
| ConfigParameters |[KeyedCollection][keyk][string, [ConfigParameter][cp]] |コネクタの構成パラメーターのテーブル。 |
| 資格情報 |[PSCredential][pscred] |[接続] タブで管理者が入力した資格情報が含まれます。 |
| Partition |[パーティション][part] |CSEntry が入るディレクトリ パーティション。 |
| CSEntry |[CSEntry][cse] |パスワードの変更またはリセットを受け取ったオブジェクトのコネクタ スペース エントリ。 |
| OperationType |String |操作がリセット (**SetPassword**) または変更 (**ChangePassword**) であることを示します。 |
| PasswordOptions |[PasswordOptions][pwdopt] |目的のパスワードのリセット動作を指定するフラグ。 このパラメーターは、OperationType が **SetPassword**の場合にのみ利用できます。 |
| OldPassword |文字列 |パスワード変更のためにオブジェクトの古いパスワードが入力されます。 このパラメーターは、OperationType が **ChangePassword**の場合にのみ利用できます。 |
| NewPassword |文字列 |スクリプトで設定するオブジェクトの新しいパスワードが入力されます。 |

パスワード スクリプトは、Windows PowerShell パイプラインに結果を返しません。 パスワード スクリプトでエラーが発生した場合、スクリプトは次のいずれかの例外をスローし、同期サービスに問題を通知します。

* [PasswordPolicyViolationException][pwdex1] – 接続したシステムのパスワード ポリシーをパスワードが満たさない場合にスローされます。
* [PasswordIllFormedException][pwdex2] – 接続したシステムでパスワードが受け取られない場合にスローされます。
* [PasswordExtension][pwdex3] – パスワード スクリプトのその他すべてのエラーに対してスローされます。

## <a name="sample-connectors"></a>サンプル コネクタ
利用できるサンプル コネクタの完全な概要については、[Windows PowerShell コネクタのサンプル コネクタ コレクション][samp]に関するページを参照してください。

## <a name="other-notes"></a>他の注意事項
### <a name="additional-configuration-for-impersonation"></a>偽装のための追加構成
偽装されるユーザーに同期サービス サーバーで次のアクセス許可を付与します。

次のレジストリ キーの読み取りアクセス:

* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Software\Microsoft\PowerShell
* HKEY_USERS\\[SynchronizationServiceServiceAccountSID]\Environment

同期サービスのサービス アカウントのセキュリティ ID (SID) を決定するには、次の PowerShell コマンドを実行します。

```
$account = New-Object System.Security.Principal.NTAccount "<domain>\<username>"
$account.Translate([System.Security.Principal.SecurityIdentifier]).Value
```

次のファイル システム フォルダーの読み取りアクセス:

* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\Extensions
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\ExtensionsCache
* %ProgramFiles%\Microsoft Forefront Identity Manager\2010\Synchronization Service\MaData\\{ConnectorName}

{ConnectorName} プレースホルダーは Windows PowerShell コネクタの名前に置き換えます。

## <a name="troubleshooting"></a>トラブルシューティング
* コネクタのトラブルシューティングを行うためにログ記録を有効にする方法については、「 [How to Enable ETW Tracing for Connectors (コネクタの ETW トレースを有効にする方法)](http://go.microsoft.com/fwlink/?LinkId=335731)」を参照してください。

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[cpp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameterpage.aspx
[keyk]: https://msdn.microsoft.com/library/ms132438.aspx
[cp]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.configparameter.aspx
[pscred]: https://msdn.microsoft.com/library/system.management.automation.pscredential.aspx
[schema]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schema.aspx
[schemaT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schematype.aspx
[schemaA]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.schemaattribute.aspx
[dnstyle]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.madistinguishednamestyle.aspx
[exportT]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maexporttype.aspx
[DataNorm]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.manormalizations.aspx
[oconf]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.maobjectconfirmation.aspx
[dw]: https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx
[part]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.partition.aspx
[hn]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.hierarchynode.aspx
[oicrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionrunstep.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[oicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openimportconnectionresults.aspx
[cecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeexportconnectionrunstep.aspx
[cicres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.closeimportconnectionresults.aspx
[oecrs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.openexportconnectionrunstep.aspx
[irs]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.importrunstep.aspx
[cse]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentry.aspx
[csec]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.csentrychange.aspx
[peeres]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.putexportentriesresults.aspx
[pwdopt]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordoptions.aspx
[pwdex1]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordpolicyviolationexception.aspx
[pwdex2]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordillformedexception.aspx
[pwdex3]: https://msdn.microsoft.com/library/windows/desktop/microsoft.metadirectoryservices.passwordextensionexception.aspx
[samp]: http://go.microsoft.com/fwlink/?LinkId=394291



<!--HONumber=Feb17_HO1-->


