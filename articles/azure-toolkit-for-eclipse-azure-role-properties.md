---
title: "Azure ロールのプロパティ"
description: "Azure Toolkit for Eclipse を使用して、Azure ロールの設定を構成する方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 5c0ec412-5702-465a-8f47-87a8ce99a267
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: ff60ebaddd3a7888cee612f387bd0c50799496ac
ms.openlocfilehash: eb1f4c815618e866e683b3fe2e3adf93a151ff5a


---
# <a name="azure-role-properties"></a>Azure ロールのプロパティ
Azure ロールに対する各種構成は、Azure Toolkit for Eclipse 内で設定することができます。

## <a name="configuring-azure-role-properties"></a>Azure ロールのプロパティを構成する
Azure ロールのプロパティは、worker ロールのプロパティ ダイアログ ボックスで構成します。 Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで目的のロールのコンテキスト メニューを開き、**[Azure]** サブメニューを選択します  (Eclipse の Project Explorer でロールが表示されない場合は、Project Explorer で Azure プロジェクトを展開してください)。

![][ic789599]

このトピックでは、 **[Properties (プロパティ)]** ダイアログ ボックスで設定できるさまざまなプロパティについて説明します。 多くのプロパティは、新しい Azure デプロイ プロジェクトを作成すると自動的に設定されます。

Azure のロールには、次のプロパティ ページが用意されています。

* [仮想マシン プロパティ](#virtual_machine_properties)
* [Caching のプロパティ](#caching_properties)
* [証明書のプロパティ](#certificates_properties)
* [コンポーネントのプロパティ](#components_properties)
* [デバッグのプロパティ](#debugging_properties)
* [エンドポイントのプロパティ](#endpoints_properties)
* [環境変数のプロパティ](#environment_variables_properties)
* [負荷分散/セッション アフィニティ (別名、"スティッキー セッション") のプロパティ](#session_affinity_properties)
* [ローカル ストレージのプロパティ](#local_storage_properties)
* [サーバー構成のプロパティ](#server_configuration_properties)
* [SSL オフロードのプロパティ](#ssl_offloading_properties)

<a name="virtual_machine_properties"></a>

### <a name="virtual-machine-properties"></a>仮想マシン プロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Properties (プロパティ)]** の順にクリックします。次の図に示すように、仮想マシンのサイズとインスタンスの数を変更することができます。

![][ic719499]

> [!NOTE]
> Windows のみ: インスタンス数の値を 1 より大きく設定し、なおかつアプリケーション サーバーを構成した場合、この設定に関係なく、エミュレーターで実行できるロール インスタンスの数が 1 つに制限されます。 これは、同じコンピューター上で複数の異なるサーバー インスタンスを実行したときに (たとえば、すべてのサーバー インスタンスがポート 8080 にバインドしようとして発生する) ポート バインドの競合を回避するための動作です。 指定したインスタンス数の設定は保持されますが、その設定が有効になるのはクラウドにデプロイした場合のみです。
> 
> 

<a name="caching_properties"></a> 

### <a name="caching-properties"></a>Caching のプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Caching (キャッシュ)]** の順にクリックします。 このダイアログ ボックスでは、併置された名前付きの memcache 互換キャッシュを有効にして、Web アプリケーションを高速化できます。

![][ic719483]

**[Caching]** プロパティ ページでは、次のグローバル設定を指定できます。

* 併置型キャッシュを有効にするかどうか。
* キャッシュ サイズ (メモリの割合で指定)。
* アプリケーションをクラウド サービスとして実行するときにキャッシュの状態を保存するためのストレージ アカウント名 (キャッシュの状態を保存しない場合は [none])。 コンピューティング エミュレーターでアプリケーションを実行するときは、ストレージ アカウント名は使用されません。ストレージ アカウント名を **[(auto) (自動)]** (既定値) に設定した場合、**[Publish to Azure (Azure に発行)]** ダイアログ ボックスで選択したものと同じストレージ アカウントが自動的に使用されます。

> [!NOTE]
> **[(auto)]** 設定の場合、Eclipse ツールキットの発行ウィザードを使用してデプロイを発行した場合にのみ目的の効果が得られます。 [Azure 管理ポータル][Azure Management Portal]など外部のメカニズムを使用して手動で .cspkg ファイルを発行した場合、デプロイは正しく機能しません。
> 
> 

キャッシュのプロパティに関するダイアログ ボックスを次に示します。

![][ic719501]

* **[Name]:** 併置型キャッシュの名前。
* **[Port number]:** キャッシュに使用するポート番号。
* **[Expiration policy]:** キャッシュ内のキーの有効期限を指定する次のいずれかの値。
  * **[Absolute (絶対値)]**: **[Minutes to live (有効期間 (分))]** に指定された時間になるとキーが期限切れになります。
  * **[NeverExpires]:** キーには有効期限がありません。
  * **[SlidingWindow (スライド式)]**: **[Minutes to live (有効期間 (分))]** に指定された時間にわたってアクセスされない時間があると、キーが期限切れになります。有効期限の時間は、アクセスされるたびにリセットされます。
* **[Minutes to live]:** 有効期限ポリシーが適用される memcached キーの有効期限 (分単位)。
* **[High availability with replicated backups on different role instances]:** 有効にした場合、別のロール インスタンスにレプリケートされたバックアップを使用して高可用性が確保されます。 この機能を使用するには、デプロイに対して少なくとも&2; つのロール インスタンスが有効になっている必要があります。

新しいキャッシュを追加するには、**[Caching (キャッシュ)]** プロパティ ページで **[Add (追加)]** ボタンをクリックして **[Configure Named Cache (名前付きキャッシュの構成)]** ダイアログ ボックスを開きます。 前述のプロパティの値を指定してください。

名前付きキャッシュを変更するには、キャッシュを選択し、**[Caching (キャッシュ)]** プロパティ ページで **[Edit (編集)]** ボタンをクリックします。 キャッシュのプロパティを変更するためのダイアログ ボックスが開きます。 **[OK]** を押して、キャッシュの値を保存します。

キャッシュを削除するには、目的のキャッシュを選択し、**[Caching (キャッシュ)]** プロパティ ページで **[Remove (削除)]** ボタンをクリックします。確認のメッセージが表示されるので、削除してかまわなければ **[Yes (はい)]** をクリックしてください。

キャッシュの使用方法の詳細については、[併置型キャッシュの使用方法][How to Use Co-located Caching]に関するページを参照してください。

<a name="certificates_properties"></a> 

### <a name="certificates-properties"></a>証明書のプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Certificates (証明書)]** の順にクリックします。

![][ic710964]

このダイアログ ボックスでは、Eclipse プロジェクトで参照する証明書を追加または削除できます。 ここに表示された証明書は Java キーストア内に自動的には格納されないため、自動的に Java アプリケーション内で使用できるようにはなりません。 デプロイしたアプリケーションの実行環境となる仮想マシン上の Windows 証明書ストアにプリロードしておき、後で他の Windows ソフトウェアから使用することができるよう、これらの証明書は Azure に登録されるのみです。 現在、**[Certificates (証明書)]** ダイアログ ボックスからこのようにして参照された証明書を使用するツールキットの機能は [SSL オフロード][SSL Offloading]のみです。SSL オフロードで使用されるインターネット インフォメーション サービス (IIS) と Application Request Routing (ARR) では、適切な証明書が、この方法で利用できることが要求されるためです。

発行ウィザードを使用して Azure にプロジェクトをデプロイするとき、これらの証明書に対応する Personal Information Exchange (PFX) ファイルをパスワードと共に指定するように求められます。これにより、これらのファイルが以前に Azure サービスにアップロードされていない場合にのみ自動的に Azure サービスにアップロードされるようになります。

<a name="components_properties"></a> 

### <a name="components-properties"></a>コンポーネントのプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Components (コンポーネント)]** の順にクリックします。 このダイアログ ボックスで、ロールのコンポーネントを追加、変更、削除できるほか、コンポーネントが処理される順序を変更することができます。

![][ic719502]

Java アプリケーション プロジェクト、特殊ファイル、実行可能コマンド ライン ステートメントなど、デプロイで必要な依存関係は、この機能を使って Azure デプロイ プロジェクトに追加することができます。

各コンポーネントに対し、次の項目を指定できます。

* Azure デプロイ プロジェクトのビルド時にコンポーネントをインポートするときの処理ステップ。
* コンポーネントを Azure のクラウドにデプロイするときの処理ステップ。

> [!NOTE]
> コンポーネントのファイルまたはコマンド ラインを指定するときは、デプロイの発行先が Windows の仮想マシンであることに注意してください。つまり、独自に指定した処理ステップは、Windows ベースのオペレーティング システムに対して有効である必要があります。 
> 
> 

コンポーネントには、次のプロパティがあります。

* **Import:** プロジェクトのビルド時にコンポーネントをどのような方法でプロジェクトにインポートするか。 次のいずれかの値を指定できます。
  * **[copy (コピー)]**: 対象のコンポーネントは、**[From]** プロパティで指定されたローカル パスからロールの **approot** ディレクトリにコピーされます。
  * **[EAR]**: 対象のコンポーネントは Java エンタープライズ アーカイブ (EAR) です。**[From]** プロパティで指定されたローカル パスにあるエンタープライズ アプリケーション プロジェクトからインポートされます  (その場所にあるプロジェクトの種類に基づいてツールキットによって自動的に検出されます)。
  * **[JAR]**: 対象のコンポーネントは Java アーカイブ (JAR) です。**[From]** プロパティで指定されたローカル パスにある Java プロジェクトからインポートされます  (その場所にあるプロジェクトの種類に基づいてツールキットによって自動的に検出されます)。
  * **[none (なし)]:** コンポーネントをインポートするための処理は実行されません。 ロールの **approot** ディレクトリにコンポーネントが既に存在していることを前提とした処理を行う場合に選択します。また、**[Deploy (デプロイ)]** 方法が **exec** のときに **[As]** プロパティで指定された実行可能コマンド ライン ステートメントがコンポーネントである場合も、これを選択します。
  * **[WAR]**: 対象のコンポーネントは Java Web アプリケーション アーカイブ (WAR) です。**[From]** プロパティで指定されたローカル パスにある動的 Web プロジェクトからインポートされます  (その場所にあるプロジェクトの種類に基づいてツールキットによって自動的に検出されます)。
  * **[zip]**: 対象のコンポーネントは zip ファイルです。**[From]** プロパティで指定されたディレクトリまたはファイルを zip 圧縮してインポートされます。
* **From:** デプロイにインポートするアイテム (フォルダーまたはファイル) が格納されているローカル コンピューター上のパス。 このプロパティには Windows の環境変数を使用できます。 インポート可能なすべてのコンポーネントが、プロジェクトのビルド時にロールの **approot** ディレクトリにインポートされます。
  
    (コンピューティング エミュレーターにではなく) クラウドにデプロイするときは、ダウンロードによってコンポーネントをデプロイすることができます。 コンポーネントの追加については、以下の関連情報を参照してください。    
* **[As]**: ロールの **approot** ディレクトリにインポートする (最終的に Azure クラウドにデプロイされる) コンポーネントに付けるファイル名。 ローカル コンピューター上のファイルの名前と同じ名前を使用する場合は、このプロパティを空欄にしておいてください。 (**[Deploy (デプロイ)]** 方法が **exec** に設定されている実行可能なコンポーネントの場合は、任意の Windows コマンド ライン ステートメントを指定できます)。
  
  > [!IMPORTANT]
  > この値に空白文字を使用した場合、その扱いは、デプロイ方法によって異なります。 デプロイ方法が **exec**の場合、空白は、ファイル名の一部としてではなく、コマンド ライン引数の区切り記号として解釈されます。 その他のデプロイ方法ではいずれも、空白がファイル名の一部として解釈されます。
  > 
  > 
* **Deploy:** デプロイの開始時にコンポーネントに適用する処理の方法。 次のいずれかの値を指定できます。
  
  * **[copy (コピー)]:** 対象のコンポーネントは、**[To]** プロパティで指定されたデプロイ先のパスにコピーされます。
  * **[exec]:** 対象のコンポーネントは、デプロイの開始時に **[To]** プロパティで指定されたパスのコンテキストで実行される、実行可能な Windows コマンド ライン ステートメントです。
  * **none:** デプロイの開始時にコンポーネントにアクションは適用されません。
  * **[zip]**: 対象のコンポーネントは、**[To]** プロパティで指定されたデプロイ先のパスに解凍されます。 この方法は、**[Import (インポート)]** プロパティが **[zip]** の場合にのみ使用できます。
* **[To]:** 対象コンポーネントのデプロイ先となる仮想マシン上のパス。 このプロパティには Windows の環境変数を使用できます。**approot** に対する相対パスで指定します。

新しいコンポーネントを追加するには、**[Components (コンポーネント)]** プロパティ ページの **[Add (追加)]** ボタンをクリックして **[Azure Role Component (Azure ロール コンポーネント)]** ダイアログ ボックスを開きます。 前述のプロパティの値を指定してください。 

新しい WAR コンポーネントを追加する例を次に示します。

![][ic719503]

(コンピューティング エミュレーターにではなく) クラウドにデプロイするとき、ダウンロードによってコンポーネントをデプロイする場合は、**[When in cloud, instead of including in the package, deploy from (クラウドにデプロイする場合、パッケージに含める代わりに次の場所からデプロイする)]** をオンにしてください。 Azure ストレージ アカウントからダウンロードする場合は、**[Storage account (ストレージ アカウント)]** ボックスの一覧から目的のストレージ アカウントを選択します (**[Accounts (アカウント)]** リンクをクリックすると一覧内の項目を変更できます)。この操作によって **[URL]** ボックスに値の一部が入力されたら、URL の残りの部分を手動で入力します。 Azure Storage を使用しない場合は、**[Storage account (ストレージ アカウント)]** ボックスの一覧の **[(none) (なし)]** を選択し、**[URL]** ボックスにコンポーネントの URL を入力します。 次のいずれかの方法を指定します。

* **[copy (コピー)]**: ダウンロードしたコンポーネントは、**[To Directory (ターゲット ディレクトリ)]** で指定されたデプロイ先のパスにコピーされます。
* **[same (同じ)]**: **[Deploy from package (パッケージからデプロイ)]** と同じ方法が **[Deploy from download (ダウンロードからデプロイ)]** に使用されます。
* **[zip]:** ダウンロードしたコンポーネントは、**[To Directory (ターゲット ディレクトリ)]** で指定されたデプロイ先のパスに解凍されます。

コンポーネントに変更を加えるには、対象のコンポーネントを選択し、**[Components (コンポーネント)]** プロパティ ページで **[Edit (編集)]** ボタンをクリックします。 コンポーネントのプロパティを変更するためのダイアログ ボックスが開きます。 **[OK]** を押すと、コンポーネントの値が保存されます。

コンポーネントを削除するには、対象のコンポーネントを選択し、**[Components (コンポーネント)]** プロパティ ページで **[Remove (削除)]** ボタンをクリックします。確認のメッセージが表示されるので、削除してかまわなければ **[Yes (はい)]** をクリックしてください。

コンポーネントは、リストの先頭から順に処理されます。 順序を変更するには、**[Move Up (上へ移動 )]** ボタンと **[Move Down (下へ移動)]** ボタンを使用します。

> [!NOTE]
> コンポーネントは、サーバーの構成機能でも使われています。 それらのコンポーネントを削除したり編集したりするには、対応するサーバーの構成を削除する必要があります。 そのようなコンポーネントに変更を加えようとすると、この点を確認するメッセージが表示されます。
> 
> 

<a name="debugging_properties"></a> 

### <a name="debugging-properties"></a>デバッグのプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Debugging (デバッグ)]** の順にクリックします。 次の図に示すように、このダイアログ ボックスでリモート デバッグを有効または無効にしたり、デバッグ構成を作成したりできます。

![][ic719504]

デバッグの詳細については、「[Eclipse での Azure アプリケーションのデバッグ][Debugging Azure Applications in Eclipse]」を参照してください。

<a name="endpoints_properties"></a> 

### <a name="endpoints-properties"></a>エンドポイントのプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Endpoints (エンドポイント)]** の順にクリックします。 次の図に示すように、このダイアログ ボックスではエンドポイントを作成、編集、削除することができます。

![][ic719505]

エンドポイントを追加するには、**[Endpoints (エンドポイント)]** プロパティ ページの **[Add (追加)]** ボタンをクリックして **[Add Endpoint (エンドポイントの追加)]** ダイアログ ボックスを開きます。

![][ic710897]

エンドポイントの名前を入力し、種類 (**[Input (入力)]**、**[Internal (内部)]**、**[InstanceInput (インスタンス入力)]** のいずれか) を選択して、パブリック ポートとプライベート ポートを指定します。 **[OK]** を押して、新しいエンドポイント値を保存します。

エンドポイントの種類によっては、次のようにポート範囲を使用できます。

* 入力インスタンス エンドポイントの場合、パブリック ポートにポート範囲を指定できます (たとえば、**2000 ～&2010;**)。プライベート ポートには固定値を使用します。
* 内部エンドポイントの場合、パブリック ポートは使用されません。プライベート ポートに範囲を指定してください。空欄またはアスタリスクを指定して Azure で自動的にポートを設定することもできます。
* 入力エンドポイントの場合、パブリック ポートに指定できるのは固定値のみです。プライベート ポートには固定値を指定できるほか、空欄またはアスタリスクを指定して Azure で自動的にポートを設定することもできます。

範囲ではなく単一のポート番号を使用する場合は、範囲の終わりを示すテキスト ボックスは空欄にしてください。

ポートの設定を自動で行う場合に実行時に実際に使用されるポートを調べるには、Azure サービス ランタイム API ([com.microsoft.windowsazure.serviceruntime パッケージの概要][com.microsoft.windowsazure.serviceruntime package summary]を参照) をアプリケーションで使用してください。

複数インスタンス デプロイのデバッグにインスタンス入力エンドポイントを利用する方法については、[複数インスタンス デプロイでの特定のロール インスタンスのデバッグ][Debugging a specific role instance in a multi-instance deployment]に関するページを参照してください。

エンドポイントに変更を加えるには、対象のエンドポイントを選択し、**[Endpoints (エンドポイント)]** プロパティ ページの **[Edit (編集)]** ボタンをクリックします。 エンドポイントの名前、種類、パブリック ポート、プライベート ポートを変更するためのダイアログ ボックスが開きます。 **[OK]** を押して、変更したエンドポイントの値を保存します。

エンドポイントを削除するには、対象のエンドポイントを選択し、**[Endpoints (エンドポイント)]** プロパティ ページの **[Remove (削除)]** ボタンをクリックします。確認のメッセージが表示されるので、削除してかまわなければ **[Yes (はい)]** をクリックしてください。

ロールのユーザーによって有効にされた機能 (キャッシュ、リモート デバッグ、セッション アフィニティ、SSL オフロードなど) を適切に構成するために、特殊なエンドポイントがツールキットによって自動的に構成されることがあります。これらのエンドポイントは、ユーザー定義のエンドポイントと共に一覧に表示されます。 関連付けられている機能が有効になっている限り、これらの自動生成されたエンドポイントをユーザーが編集または削除することはできません。

<a name="environment_variables_properties"></a> 

### <a name="environment-variables-properties"></a>環境変数のプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Environment Variables (環境変数)]** の順にクリックします。 次の図に示すように、このダイアログ ボックスで環境変数を作成、変更、削除することができます。

![][ic719506]

スタートアップ スクリプトの中で指定された環境変数の値は、ロールの起動時に与えられます。

> [!NOTE]
> 環境変数を指定するときは、デプロイの発行先が Windows の仮想マシンであることに注意してください。つまり環境変数は、Windows ベースのオペレーティング システムに対して有効である必要があります。
> 
> 

ここで、ロールの起動時に値が与えられる環境変数の例を紹介します。**[Add (追加)]** ボタンをクリックして新しい環境変数を作成しましょう。 ここでは、**MyRoleVersion** という名前の環境変数を作成し、その値として **1.0** を割り当てています。

![][ic659268]

jsp コードでは、 `System.getenv` メソッドを使用してこの値を表示できます。

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

アプリケーションを実行すると、次の出力が得られます。

![][ic552233]

環境変数を変更するには、対象の環境変数を選択し、**[Environment Variables (環境変数)]** プロパティ ページで **[Edit (編集)]** ボタンをクリックします。 環境変数のプロパティを変更するためのダイアログ ボックスが開きます。 **[OK]** を押して環境変数の値を保存します。

環境変数を削除するには、対象の環境変数を選択し、**[Environment Variables (環境変数)]** プロパティ ページの **[Remove (削除)]** ボタンをクリックします。確認のメッセージが表示されるので、削除してかまわなければ **[Yes (はい)]** をクリックしてください。

ロールのユーザーによって有効にされた機能 (サーバー構成、リモート デバッグ、ローカル ストレージなど) を適切に構成するために、特殊な環境変数がツールキットによって自動的に構成されることがあります。これらの環境変数は、ユーザー定義の環境変数と共に一覧に表示されます。 関連付けられている機能が有効になっている限り、これらの自動生成された環境変数をユーザーが編集または削除することはできません。

<a name="session_affinity_properties"></a> 

### <a name="load-balancing--session-affinity-aka-sticky-sessions-properties"></a>負荷分散/セッション アフィニティ (別名、"スティッキー セッション") のプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Load Balancing (負荷分散)]** の順にクリックします。 次の図に示すように、このダイアログ ボックスでセッション アフィニティを有効または無効にすることができます。

![][ic719492]

関連情報については、[セッション アフィニティ][Session Affinity]に関するページを参照してください。 また、[SSL オフロード][SSL Offloading]に関するページで説明されているように、SSL オフロードのコンテキストでのこの機能の動作に注意してください。

<a name="local_storage_properties"></a> 

### <a name="local-storage-properties"></a>ローカル ストレージのプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Local Storage (ローカル ストレージ)]** の順にクリックします。 アプリケーションを実行している仮想マシンの一時ローカル ストレージは、このダイアログ ボックスで作成、変更、削除することができます。 次の図に示すように、ローカル ストレージのサイズとして特定の値を設定したり、ロールをリサイクルするときに内容を保持するかどうかを設定したりすることができます。

![][ic719508]

また、必要に応じて、ローカル ストレージに対応する環境変数を指定することもできます。

既定では、Azure にデプロイするすべてのものが、ロール インスタンスの **approot** フォルダーに配置 (および解凍) されます。 ほとんどの単純なデプロイは解凍した後もこのフォルダーに収まりますが、 **approot** ディレクトリに割り当てられる領域は限られ、適切に定義されません (妥当な経験則では 1 GB 未満)。 そのため、**approot** フォルダーに収まらない可能性がある大規模なデプロイに対して十分なディスク領域が Azure によって割り当てられるようにするには、**[Local Storage (ローカル ストレージ)]** ダイアログを使用してローカル ストレージ リソースを設定する必要があります。 これを簡単に行う方法については、「[大規模なデプロイ][Deploying Large Deployments]」を参照してください。

ストレージ リソースは、Eclipse ツールキットによってリソースに自動的に関連付けられた環境変数を使用して、スタートアップ スクリプト (たとえば、**startup.cmd**) から簡単に参照することができます (環境変数は、**[Local Storage (ローカル ストレージ)]** ダイアログ ボックスに示されます)。 環境変数には、構成したローカル リソースの完全なパスが、スタートアップ スクリプトの実行時に格納されます。 

ローカル ストレージ リソースに変更を加えるには、対象のローカル ストレージ リソースを選択し、**[Local Storage (ローカル ストレージ)]** プロパティ ページで **[Edit (編集)]** ボタンをクリックします。 ローカル ストレージ リソースのプロパティを変更するためのダイアログ ボックスが開きます。 **[OK]** を押して、ローカル ストレージ リソースの値を保存します。

ローカル ストレージ リソースを削除するには、目的のローカル ストレージ リソースを選択し、**[Local Storage (ローカル ストレージ)]** プロパティ ページで **[Remove (削除)]** ボタンをクリックします。確認のメッセージが表示されるので、削除してかまわなければ **[Yes (はい)]** をクリックしてください。

<a name="server_configuration_properties"></a> 

### <a name="server-configuration-properties"></a>サーバー構成のプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[Server Configuration (サーバー構成)]** の順にクリックします。 このダイアログ ボックスで、デプロイで使用される JDK や Java アプリケーション サーバーを追加、削除、変更できるほか、デプロイで使用されるアプリケーション (WAR、JAR、EAR ファイルなど) を追加したり削除したりすることができます。

### <a name="jdk-configuration"></a>JDK の構成
このダイアログ ボックスでは、デプロイに使用する JDK パッケージを指定できます。 Windows 上で Eclipse を使用している場合は、Azure エミュレーターで実行したときにローカルで使用する JDK パッケージを指定できるほか、そのローカル インストールを Azure にデプロイすることができます。 Windows 以外のオペレーティング システムでは、エミュレーター JDK の設定は適用されません。Windows との互換性がないため、ローカルにインストールされた JDK をデプロイすることはできません。 ただし、Azure にデプロイするサード パーティ製の JDK パッケージを選択したり、代替となるダウンロード場所から Windows と互換性のある独自の JDK パッケージを参照したりすることは、使用しているオペレーティング システムに関係なくいつでも行うことができます。

Windows 上の JDK を指定する方法の例を次に示します。

![][ic780647]

Windows 上で Eclipse を使用している場合は、コンピューティング エミュレーターで使用する JDK を指定できます。そのためには、**[Emulator deployment (エミュレーター デプロイ)]** セクションの **[Use the JDK from this file path for testing locally (ローカルなテスト用にこのファイル パスの JDK を使用する)]** チェック ボックスを必ずオンにしてください。 そのうえで JDK のローカル パスを指定します。必要な JDK が自動選択されない場合は、目的の JDK を参照画面で選択してください。 また、JDK を Azure クラウド サービスにデプロイすることもできます。そのためには、**[Cloud deployment (クラウド デプロイ)]** セクションの **[Deploy my local JDK (auto-upload to cloud storage) (ローカル JDK をデプロイする (クラウド ストレージに自動アップロードする))]** を選択します。

注: Windows 以外のオペレーティング システムでは、**[Emulator deployment (エミュレーター デプロイ)]** 設定と **[Deploy my local JDK (ローカル JDK をデプロイする)]** オプションは使用できません。 Windows 以外のサポート対象オペレーティング システム (Mac など) 上で JDK を指定する例を次に示します。

![][ic789643]

**[Cloud deployment]** セクションには、JDK パッケージのソースと種類に関して、使用しているオペレーティング システムに関係なく、次の&2; つのオプションがあります。

* **[Deploy a 3rd party JDK package available on Azure]** 
* **[Deploy from a custom download]** 

**[Deploy a 3rd party JDK package available from Azure]** オプションを使用する場合:

1. **[Deploy a 3rd party JDK package available from Azure]**チェック ボックスをオンにします。
2. ドロップダウン リストから、Azure で利用可能なサード パーティ製の JDK パッケージを選択します。
3. Windows を使用している場合、**[JDK]** タブは次のようになります。 ![][ic780648]
   Mac OS またはサポートされている他の非 Windows オペレーティング システムを使用している場合、[JDK] タブは次のようになります。 ![][ic789643]
4. **[OK]** をクリックして変更を保存します。
5. サード パーティ製の JDK パッケージ プロバイダーからライセンス契約に同意することを求められたら、ライセンス条項を確認します。 (条項に同意する場合) **[Yes (はい)]** をクリックして **[Accept license agreement (ライセンス条項に同意する)]** ダイアログを閉じます。
    **[Deploy a 3rd party JDK package available from Azure (Azure で使用できるサード パーティの JDK パッケージをデプロイする)]** ボックスの一覧に表示される項目を決定するロジックはカスタマイズすることができます。 項目をカスタマイズするには、**[JDK]** ダイアログ ボックスで、**[Customize (カスタマイズ)]** リンクをクリックします。 すると、**[JDK]** プロパティ ページが閉じ、**componentsets.xml** ファイルが Eclipse で開かれます。このファイルを、必要に応じて変更できます。 **componentsets.xml** に関するドキュメントは、**componentsets.xml** ファイル自体に含まれています。

**[Deploy a JDK from a custom download]** オプションを使用する場合:

1. JDK インストール ディレクトリの ZIP を作成します。このとき、ディレクトリの内容ではなくノード自体が ZIP 構造の子になるようにします。 後で必要になるのでこのディレクトリの名前を控えておきます。この JDK インストールは Windows 仮想マシンにデプロイされることに注意してください。
2. Azure ストレージ アカウントに ZIP を BLOB としてアップロードします。 Azure Storage への BLOB のアップロードには、外部のツールを使用できます。 プライベート BLOB を使用することをお勧めします。 ZIP コンテンツの BLOB の URL を書き留めます。
3. **[Deploy a JDK from a custom download]**の順にクリックします。
    Azure ストレージ アカウントからダウンロードする場合は、**[Storage account (ストレージ アカウント)]** ボックスの一覧から目的のストレージ アカウントを選択します (**[Accounts (アカウント)]** リンクをクリックすると一覧内の項目を変更できます)。この操作によって **[URL]** ボックスに値の一部が入力されたら、URL の残りの部分を手動で入力します。 Azure Storage を使用しない場合は、**[Storage account (ストレージ アカウント)]** ボックスの一覧の **[(none) (なし)]** を選択し、**[URL]** ボックスに JDK ダウンロードの URL を入力します。 Azure Storage を使用する場合、URL 内の BLOB 名は小文字で指定してください。
4. **[JAVA_HOME]** ボックスに適切なディレクトリ名が指定されていることを確認します。 既定では、ローカルでの使用のために選択した値と同じ JDK ディレクトリ名が参照されます。 ただし、(たとえば、別のバージョンを使用した結果) ZIP に含まれているディレクトリの名前が異なる場合は、それに応じて **[JAVA_HOME]** ボックスのディレクトリ名を更新します。それは、この設定が (コンピューティング エミュレーターではなく) クラウドで使用されるためです。
5. **[OK]** をクリックして変更を保存します。

これで終了です。 以上で完了です。ここでクラウド向けにビルドすると、パッケージ サイズが大幅に縮小されることがわかります。通常、ビルド プロセスに要する時間も短くなり、クラウドに発行するときのデプロイ自体の時間も短縮されます。 **[Deploy my local JDK (auto-upload to cloud storage) (ローカル JDK をデプロイする (クラウド ストレージに自動アップロードする))]** オプションまたは **[Deploy a JDK from a custom download (カスタム ダウンロードから JDK をデプロイする)]** オプションは、アプリケーションをクラウドにデプロイする場合にのみ有効になります。 これらのオプションは、コンピューティング エミュレーター環境には作用しません。コンピューティング エミュレーターにデプロイするときは、ローカルに置かれている方のコンポーネントが使用されます。 

### <a name="server-configuration"></a>サーバー構成
アプリケーション サーバーを指定する方法の例を次に示します。

![][ic796926]

**[Deploy a server of this type]** チェック ボックスがオンになっていることを確認して、使用するアプリケーション サーバーの種類を選択します。

クラウド デプロイに使用するサーバーを指定する場合、次のオプションを利用できます。

1. **[Deploy a 3rd party server available on Azure]** : 効率と単純さに優先順位が置かれ、サーバーのカスタム構成を必要としない開発とテストのシナリオで主に使用します。 また、これらのサーバーのいずれかを開始点として使用するものの、必要なサーバー カスタマイズ手順をデプロイのスタートアップ ロジックに含める場合も、このオプションを使用します。
2. **[Deploy from a custom download]** : このオプションは主に、クラウドで使うことを目的として準備、構成されたサーバーがある実稼働環境のシナリオで使用します。
3. **[Deploy my local server installation]** : 主に、ローカル サーバーのインストール環境に対し、必要なカスタム構成が済んでいる場合に使用します。 このオプションを選択する場合、下の **[Local server path]** ボックスにもローカル サーバーのパスを指定する必要があります。

**[Deploy a 3rd party server available on Azure]** オプションを使用する場合:

1. **[Deploy a 3rd party server available on Azure]**チェック ボックスをオンにします。
2. ドロップダウン メニューから、クラウド上のデプロイで使用する目的のサーバー ソフトウェアを選択します。 使用するサーバーの種類を既に指定してある場合、選択できるのは、そのサーバーの種類と同じファミリに属するクラウド サーバーに限定されます。 まだサーバーの種類を選択していない場合は、Azure で現在使用できる任意のサーバーから選択できます。サーバーの種類は自動的に選択されます。
3. **[OK]** をクリックして変更を保存します。

**[Deploy from a custom download]** オプションを使用する場合:

1. 前の手順に従ってサーバーの種類が既に選択されていることを確認します。 サーバーは選択したサーバーの種類と同じファミリに属している必要があるため、この選択で、カスタム ダウンロードからサーバーをデプロイする方法をプラグインに指示します。
2. **[Deploy from a custom download]**の順にクリックします。
    Azure ストレージ アカウントからダウンロードする場合は、**[Storage account (ストレージ アカウント)]** ボックスの一覧から目的のストレージ アカウントを選択します (**[Accounts (アカウント)]** リンクをクリックすると一覧内の項目を変更できます)。この操作によって **[URL]** ボックスに値の一部が入力されたら、サーバー ダウンロード ZIP に対する URL の残りの部分を手動で入力します (Azure Storage を使用する場合、URL 内の BLOB 名は小文字で表記する必要があります)。 Azure Storage を使用しない場合は、**[Storage account (ストレージ アカウント)]** ボックスの一覧の **[(none) (なし)]** を選択し、**[URL]** ボックスにサーバー ダウンロード ZIP の URL を入力します。 通常この ZIP に、アプリケーション サーバーのインストール ディレクトリを表す子フォルダーが格納されます。 たとえば、Apache Tomcat 7.0.35 の zip を使用している場合、zip 内には、**apache-tomcat-7.0.35** などのインストール ディレクトリを表す子フォルダーがあります。 
3. ホーム ディレクトリの環境変数の値を指定します。 この値は、ローカル アプリケーション サーバーがある場合に既定で使用される値になりますが、クラウドのアプリケーション サーバーがローカル アプリケーション サーバーと異なる場合は別の値を指定することができます。 ただし、クラウド アプリケーション サーバーは、前に選択したサーバーの種類と同じファミリに属している必要があります。
    今後、クラウド アプリケーション サーバーの zip を更新する場合は、ホーム ディレクトリ設定を手動で変更するか、ローカル設定に合わせて変更することができます (ローカル アプリケーション サーバーも変更した場合)。
4. **[OK]** をクリックして変更を保存します。

**[Server Configuration (サーバー構成)]** プロパティ ページの **[Server (サーバー)]** タブに項目を表示する際に適用される基になるロジックをカスタマイズすることができます。 これは、実際のニーズが既定値を超える場合や他のサーバーを追加する場合に必要となる高度な機能です。 ロジックをカスタマイズするには、**[Server (サーバー)]** ダイアログ ボックスで、**[Customize (カスタマイズ)]** リンクをクリックします。 すると、**[Server Configuration (サーバー構成)]** プロパティ ページが閉じ、**componentsets.xml** ファイルが Eclipse で開かれます。このファイルを必要に応じて変更して、サーバーの構成テンプレートを拡張できます。 **componentsets.xml** に関するドキュメントは、**componentsets.xml** ファイル自体に含まれています。

**[Deploy my local server (auto-upload to cloud storage)]** オプションを使用する場合:

1. **[Deploy my local server (auto-upload to cloud storage)]**チェックボックスをオンにします。
2. **[Storage account (ストレージ アカウント)]** ボックスの一覧の **[(auto) (自動)]** を選択します。 **[(auto) (自動)]** を指定すると、**[Publish to Azure (Azure に発行)]** ダイアログ ボックスでデプロイに対して選択したものと同じサーバーのストレージ アカウントが使用されます。
3. **[OK]** をクリックして変更を保存します。

次の条件のいずれかに該当する場合は、使用しているコンピューターの **[Local server path]** ボックスでサーバー インストール パスを選択します。

* デプロイをエミュレーターでテストする (Windows にのみ適用されます)。
* ローカルにインストールされているサーバーをクラウドにデプロイする。
* クラウドで独自のカスタム サーバー ダウンロードを使用する。この場合、上の **[Deploy my local server (auto-upload to cloud storage) (ローカル サーバーをデプロイする (クラウド ストレージに自動アップロードする))]** オプションも選択する必要があります。

これらの条件のいずれにも該当しない場合は、ローカル サーバーの設定を省略できます。

### <a name="applications-configuration"></a>アプリケーションの構成
アプリケーションを指定する方法の例を次に示します。

![][ic719512]

別のアプリケーションを追加するには、**[Add (追加)]** をクリックします。アプリケーションを削除するには、**[Remove (削除)]** をクリックします。 クラウドへのデプロイ時に、効率上の理由からアプリケーションのソースとしてダウンロードを使用する場合は、[コンポーネントのプロパティ](#components_properties)を使用して、URL、ストレージ アカウントなどの値を指定してください。 

April 2014 リリースより、アプリケーションは、デプロイ用に選択したのと同じ ( **eclipsedeploy** コンテナーの下の) ストレージ アカウントに自動的にアップロードされます。 デプロイのスタートアップ ロジックには、このストレージ アカウントから最初にそれらのアプリケーションをダウンロードする手順が含まれています。 これは、デプロイ内のアプリケーションをアップグレードする際にパッケージ全体を再ビルドして再デプロイする必要がないことを意味します。 つまり、手動で (たとえば、Azure ポータルを使用して) 新しいバージョンのアプリケーションをストレージ アカウントに直接アップロードして、ツールキットによってそこにアップロードされていた WAR ファイルを置き換える必要はありません。 Azure の管理ポータルまたはコマンド ライン ユーティリティを使用して、これらのすべてのロール インスタンスのリサイクルを開始するだけです (現在、Eclipse ツールキット内から直接リサイクルをトリガーすることはできません)。

### <a name="notes-about-server-configuration"></a>サーバーの構成に関する注意事項
**[Server configuration (サーバー構成)]** プロパティ ページで加えられた変更は、package.xml ファイルの `<component>` 要素に反映されます。

JDK またはアプリケーション サーバーに対して **[Automatically upload (自動アップロード)]** オプションまたは **[Deploy from download (デプロイからダウンロード)]** オプションを使用し、(コンピューティング エミュレーターではなく) クラウド用にビルドしていて、かつネットワークに接続されている場合、Ant ビルダーがダウンロードの可用性を検証したときに、次のようなメッセージがコンソールに出力されることがあります。

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

**[Deploy from download]** オプションを選択した場合、次の警告が表示されることがありますが、ビルドは続行されます。

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

この警告は、単にダウンロードの利用の可否が検証されていないことを示しています。 したがって、何かの理由でクラウドでのデプロイに失敗した場合は、この警告を受け取っていないか確認してください。

(たとえば、ダウンロードの検証が原因でビルドが不必要に遅くなっていると思われる場合に) ダウンロードの検証を無効にするには、package.xml の `<windowsazurepackage>` 要素の `verifydownloads` 属性を `false` に設定します。 

`<windowsazurepackage verifydownloads="false" ...>` 

**[Automatically upload]** オプションを選択した場合、アップロードが必要なときはいつでも、アップロードの進行状況を報告するビルド メッセージが 5 秒ごとにコンソール ウィンドウに表示されます。

<a name="ssl_offloading_properties"></a> 

### <a name="ssl-offloading-properties"></a>SSL オフロードのプロパティ
Eclipse の [Project Explorer (プロジェクト エクスプローラー)] ウィンドウで、ロールのコンテキスト メニューを開き、**[Azure]**、**[SSL Offloading (SSL オフロード)]** の順にクリックします。 

![][ic719481]

このダイアログ ボックスで SSL オフロードを有効にすると、Azure に対する Java のデプロイにおいてハイパー テキスト転送プロトコル セキュア (HTTPS) のサポートを簡単に有効にすることができます。Java アプリケーション サーバーで SSL を構成する必要はありません。 詳細については、[SSL オフロード][SSL Offloading]と [SSL オフロードの使用方法][How to Use SSL Offloading]に関するページを参照してください。

## <a name="see-also"></a>関連項目
[Azure Toolkit for Eclipse][Azure Toolkit for Eclipse]

[Azure Toolkit for Eclipse のインストール][Installing the Azure Toolkit for Eclipse]

[Azure 向け Hello World アプリケーションを Eclipse で作成する][Creating a Hello World Application for Azure in Eclipse]

[Azure プロジェクトのプロパティ][Azure Project Properties]

[Azure ストレージ アカウントの一覧][Azure Storage Account List]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター][Azure Java Developer Center]を参照してください。

<!-- URL List -->

[Azure Java Developer Center]: http://go.microsoft.com/fwlink/?LinkID=699547
[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Azure Project Properties]: http://go.microsoft.com/fwlink/?LinkID=699524
[Azure Storage Account List]: http://go.microsoft.com/fwlink/?LinkID=699528
[com.microsoft.windowsazure.serviceruntime package summary]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Creating a Hello World Application for Azure in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Debugging a specific role instance in a multi-instance deployment]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Debugging Azure Applications in Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Deploying Large Deployments]: http://go.microsoft.com/fwlink/?LinkID=699536
[How to Use Co-located Caching]: http://go.microsoft.com/fwlink/?LinkID=699542
[How to Use SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699545
[Installing the Azure Toolkit for Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Session Affinity]: http://go.microsoft.com/fwlink/?LinkID=699548
[SSL Offloading]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->



<!--HONumber=Jan17_HO1-->


