---
title: クラウド サービス (クラシック) モデルとパッケージの概要 | Microsoft Docs
description: Azure でのクラウド サービス モデル (.csdef、.cscfg) と パッケージ (.cspkg) について説明します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1cf8e966b80e005a0cb2cf7ea46f355e38cb0011
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741538"
---
# <a name="what-is-the-cloud-service-classic-model-and-how-do-i-package-it"></a>クラウド サービス (クラシック) モデルとそれをパッケージ化する方法

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

クラウド サービスは、サービス定義 *(.csdef)*、サービスの構成 *(.cscfg)*、サービス パッケージ *(.cspkg)* の 3 つのコンポーネントから作成されます。 **ServiceDefinition.csdef** ファイルと **ServiceConfig.cscfg** ファイルはどちらも XML をベースとし、クラウド サービスの構造と構成方法について記述したファイルであり、まとめてモデルと呼ばれています。 **ServicePackage.cspkg** は、**ServiceDefinition.csdef** とその他のものから生成される zip ファイルで、必要なすべてのバイナリベースの依存関係が含まれます。 Azure では、**ServicePackage.cspkg** と **ServiceConfig.cscfg** の両方からクラウド サービスが作成されます。

Azure でクラウド サービスが実行されている場合は、 **ServiceConfig.cscfg** ファイルを使用して再構成することはできますが、定義後は変更できません。

## <a name="what-would-you-like-to-know-more-about"></a>詳細について
* [ServiceDefinition.csdef](#csdef) ファイルと [ServiceConfig.cscfg](#cscfg) ファイルの詳細について教えてください。
* それについては知っているので、構成可能な [例をいくつか](#next-steps) 紹介してください。
* [ServicePackage.cspkg](#cspkg)を作成する必要があります。
* Visual Studio を使用しているのですが...
  * [クラウド サービスを作成する][vs_create]
  * [既存のクラウド サービスを再構成する][vs_reconfigure]
  * [クラウド サービス プロジェクトをデプロイする][vs_deploy]
  * [クラウド サービス インスタンスにリモート デスクトップ接続する][remotedesktop]

<a name="csdef"></a>

## <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef
**ServiceDefinition.csdef** ファイルは、Azure が使用する設定を指定して、クラウド サービスを構成します。 [Azure サービス定義スキーマ (.csdef ファイル)](/previous-versions/azure/reference/ee758711(v=azure.100)) は、サービス定義ファイルに使用可能な形式を提供します。 次の例は、Web ロールと Worker ロールに定義できる設定を示しています。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition">
  <WebRole name="WebRole1" vmsize="Medium">
    <Sites>
      <Site name="Web">
        <Bindings>
          <Binding name="HttpIn" endpointName="HttpIn" />
        </Bindings>
      </Site>
    </Sites>
    <Endpoints>
      <InputEndpoint name="HttpIn" protocol="http" port="80" />
      <InternalEndpoint name="InternalHttpIn" protocol="http" />
    </Endpoints>
    <Certificates>
      <Certificate name="Certificate1" storeLocation="LocalMachine" storeName="My" />
    </Certificates>
    <Imports>
      <Import moduleName="Connect" />
      <Import moduleName="Diagnostics" />
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <LocalResources>
      <LocalStorage name="localStoreOne" sizeInMB="10" />
      <LocalStorage name="localStoreTwo" sizeInMB="10" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
      <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" />
    </Startup>
  </WebRole>

  <WorkerRole name="WorkerRole1">
    <ConfigurationSettings>
      <Setting name="DiagnosticsConnectionString" />
    </ConfigurationSettings>
    <Imports>
      <Import moduleName="RemoteAccess" />
      <Import moduleName="RemoteForwarder" />
    </Imports>
    <Endpoints>
      <InputEndpoint name="Endpoint1" protocol="tcp" port="10000" />
      <InternalEndpoint name="Endpoint2" protocol="tcp" />
    </Endpoints>
  </WorkerRole>
</ServiceDefinition>
```

ここで使用する XML スキーマの詳細については、[サービスの定義スキーマ](/previous-versions/azure/reference/ee758711(v=azure.100))に関するページをご覧ください。ここでは、いくつかの要素を簡単に説明します。

**サイト**  
には、IIS7 でホストされている Web サイトか Web アプリケーションの定義が含まれています。

**InputEndpoints**  
 には、クラウド サービスにアクセスするために使用されるエンドポイントの定義が含まれています。

**InternalEndpoints**  
 には、互いに通信するために、ロール インスタンスが使用されるエンドポイントの定義が含まれています。

**ConfigurationSettings**  
 には、特定のロールの機能に対する設定の定義が含まれています。

**証明書**  
には、ロールに必要な証明書の定義が含まれています。 上記のコード例は、Azure Connect の構成に使用される証明書を示します。

**LocalResources**  
 には、ローカル ストレージ リソースの定義が含まれています。 ローカル ストレージ リソースは、ロールのインスタンスが実行されている仮想マシンのファイル システム内の予約されたディレクトリです。

**インポートする**  
 には、インポートされたモジュールの定義が含まれています。 上記のコード例は、リモート デスクトップ接続と Azure Connect のモジュールを示します。

**Startup**  
には、ロールの開始時に実行されるタスクが含まれています。 タスクは、.cmd ファイルか実行可能ファイルで定義されます。

<a name="cscfg"></a>

## <a name="serviceconfigurationcscfg"></a>ServiceConfiguration.cscfg
クラウド サービスのための設定の構成は、 **ServiceConfiguration.cscfg** ファイルの値によって決定されます。 このファイル内の各ロールにデプロイするインスタンスの数を指定します。 サービス定義ファイルで定義した構成設定の値は、サービス構成ファイルに追加されます。 クラウド サービスに関連付けられている管理証明書のサムプリントは、ファイルにも追加されます。 [Azure サービス構成スキーマ (.cscfg ファイル)](/previous-versions/azure/reference/ee758710(v=azure.100)) サービス構成ファイルの使用可能な形式を提供します。

サービス構成ファイルは、アプリケーションに付属していませんが、個別のファイルとして Azure にアップロードされ、クラウド サービスの構成に使用されます。 クラウド サービスを再デプロイしなくても、新しいサービス構成ファイルをアップロードできます。 クラウド サービスの実行中にクラウド サービスの構成値を変更できます。 次の例は、Web ロールと Worker ロールに定義できる構成設定を示しています。

```xml
<?xml version="1.0"?>
<ServiceConfiguration serviceName="MyServiceName" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration">
  <Role name="WebRole1">
    <Instances count="2" />
    <ConfigurationSettings>
      <Setting name="SettingName" value="SettingValue" />
    </ConfigurationSettings>

    <Certificates>
      <Certificate name="CertificateName" thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
      <Certificate name="Microsoft.WindowsAzure.Plugins.RemoteAccess.PasswordEncryption"
         thumbprint="CertThumbprint" thumbprintAlgorithm="sha1" />
    </Certificates>
  </Role>
</ServiceConfiguration>
```

ここで使用する XML スキーマの詳細については、[サービスの構成スキーマ](/previous-versions/azure/reference/ee758710(v=azure.100))に関するページをご覧ください。ここでは、各要素について簡単に説明します。

**インスタンス**  
は、ロールの実行するインスタンスの数を設定します。 アップグレード中にクラウド サービスが利用できなくなるのを避けるために、Web に接続されたロールのインスタンスは複数デプロイすることをお勧めします。 複数のインスタンスをデプロイすることにより、サービスに 2 つ以上のロール インスタンスがデプロイされている場合、インターネットに接続されたロールの 99.95% の外部接続を保証する [Azure Compute サービス レベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/) ガイドラインに従っていることになります。

**ConfigurationSettings**  
 は、ロールの実行中のインスタンスの設定を構成します。 `<Setting>` 要素の名前は、サービス定義ファイルの設定の定義と一致する必要があります。

**証明書**  
は、サービスによって使用される証明書を構成します。 上記のコード例は、RemoteAccess モジュールの証明書を定義する方法を示します。 *サムプリント* 属性の値は、使用する証明書のサムプリントに設定する必要があります。

<p/>

> [!NOTE]
> 証明書のサムプリントを構成ファイルに追加するには、テキスト エディターを使用します。 また、Visual Studio でロールの **[プロパティ]** ページにある **[証明書]** タブで、値を追加することもできます。
> 
> 

## <a name="defining-ports-for-role-instances"></a>ロール インスタンスのポートを定義する
Azure では、Web ロールに 1 つのエントリ ポイントのみを使用できます。 これは、1 つの IP アドレスからのすべてのトラフィックが発生したことを意味します。 ホスト ヘッダーを構成することによって、Web サイトを構成してポートを共有し、正しい場所に要求を送信できます。 またアプリケーションを構成して、IP アドレス上の既知のポートをリッスンすることもできます。

次の例では、Web サイトと Web アプリケーションとの Web ロールの構成を示します。 Web サイトは、ポート 80 の既定のエントリ場所として構成され、Web アプリケーションは、"mail.mysite.cloudapp.net" という別のホスト ヘッダーから要求を受信するように構成されます。

```xml
<WebRole>
  <ConfigurationSettings>
    <Setting name="DiagnosticsConnectionString" />
  </ConfigurationSettings>
  <Endpoints>
    <InputEndpoint name="HttpIn" protocol="http" port="80" />
    <InputEndpoint name="Https" protocol="https" port="443" certificate="SSL"/>
    <InputEndpoint name="NetTcp" protocol="tcp" port="808" certificate="SSL"/>
  </Endpoints>
  <LocalResources>
    <LocalStorage name="Sites" cleanOnRoleRecycle="true" sizeInMB="100" />
  </LocalResources>
  <Site name="Mysite" packageDir="Sites\Mysite">
    <Bindings>
      <Binding name="http" endpointName="HttpIn" />
      <Binding name="https" endpointName="Https" />
      <Binding name="tcp" endpointName="NetTcp" />
    </Bindings>
  </Site>
  <Site name="MailSite" packageDir="MailSite">
    <Bindings>
      <Binding name="mail" endpointName="HttpIn" hostHeader="mail.mysite.cloudapp.net" />
    </Bindings>
    <VirtualDirectory name="artifacts" />
    <VirtualApplication name="storageproxy">
      <VirtualDirectory name="packages" packageDir="Sites\storageProxy\packages"/>
    </VirtualApplication>
  </Site>
</WebRole>
```


## <a name="changing-the-configuration-of-a-role"></a>ロールの構成を変更する
クラウド サービスは、Azure で実行中に、サービスをオフラインにせずに更新できます。 構成情報を変更するには、新しい構成ファイルをアップロードするか、構成ファイルを編集して、実行中のサービスに適用します。 次の変更がサービスの構成に適用されます。

* **構成設定の値を変更する**  
   構成設定を変更すると、ロール インスタンスはインスタンスのオンライン中に変更を適用するか、インスタンスを適切に再利用し、インスタンスのオフライン中に変更を適用するかを選択できます。
* **ロール インスタンスのサービス トポロジを変更する**  
  トポロジの変更は、インスタンスが削除される場合を除いて、実行中のインスタンスには影響ありません。 残りのすべてのインスタンスは、通常はリサイクルの必要はありません。ただし、トポロジの変更に応じてロール インスタンスをリサイクルできます。
* **証明書の拇印を変更する**  
   ロール インスタンスがオフラインのときのみ、証明書を更新できます。 ロール インスタンスがオンラインの間に、証明書が追加、削除、変更されると、Azure はインスタンスを正常にオフラインにし、証明書を更新し、変更の完了後にオンラインに戻します。

### <a name="handling-configuration-changes-with-service-runtime-events"></a>サービス ランタイム イベントを使用して構成変更を処理する
[Azure ランタイム ライブラリ](/previous-versions/azure/reference/mt419365(v=azure.100))には、ロールから Azure 環境と対話するクラスを提供する [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee741722(v=azure.100)) 名前空間が含まれています。 [RoleEnvironment](/previous-versions/azure/reference/ee773173(v=azure.100)) クラスは、構成の変更の前後に発生する次のイベントを定義します。

* **[Changing](/previous-versions/azure/reference/ee758134(v=azure.100)) イベント**  
  これは、指定したロール インスタンスに構成の変更が適用される前に、必要な場合にロール インスタンスを停止する機会を提供します。
* **[Changed](/previous-versions/azure/reference/ee758129(v=azure.100)) イベント**  
   指定したロール インスタンスに構成の変更が適用された後に発生します。

> [!NOTE]
> 証明書の変更は、常にロール インスタンスをオフラインにするため、RoleEnvironment.Changing や RoleEnvironment.Changed イベントは発生しません。
> 
> 

<a name="cspkg"></a>

## <a name="servicepackagecspkg"></a>ServicePackage.cspkg
> [!NOTE]
> デプロイできる最大パッケージ サイズは 600 MB です

Azure のクラウド サービスとしてアプリケーションをデプロイするには、まず適切な形式でアプリケーションをパッケージ化する必要があります。 **CSPack** コマンドライン ツール ( [Azure SDK](https://azure.microsoft.com/downloads/)にインストール済み) を使用して、Visual Studio に代わる方法として、パッケージ ファイルを作成できます。

**CSPack** はサービス定義ファイルとサービス構成ファイルの内容を使用して、パッケージの内容を定義します。 **CSPack** は、 [Azure ポータル](cloud-services-how-to-create-deploy-portal.md#create-and-deploy)を使用して Azure にアップロードできるアプリケーション パッケージ ファイル (.cspkg) を生成します。 既定では、パッケージの名前は `[ServiceDefinitionFileName].cspkg` ですが、**CSPack** の `/out` オプションを使用して別の名前を指定することもできます。

**CSPack** は次の場所にあります  
`C:\Program Files\Microsoft SDKs\Azure\.NET SDK\[sdk-version]\bin\`

> [!NOTE]
> CSPack.exe (windows 上) は、SDK にインストール済みの **Microsoft Azure コマンド プロンプト** ショートカットを実行して使用可能です。  
> 
> CSPack.exe プログラムを実行して、必要なすべてのスイッチとコマンドについてのドキュメントをご覧ください。
> 
> 

<p />

> [!TIP]
> **Microsoft Azure Compute Emulator** でクラウド サービスをローカルで実行し、**/copyonly** オプションを使用します。 このオプションは、アプリケーションのバイナリ ファイルをディレクトリ レイアウトにコピーします。コンピューティング エミュレーターでは、そのディレクトリ レイアウトからファイルを実行できます。
> 
> 

### <a name="example-command-to-package-a-cloud-service"></a>クラウド サービスをパッケージ化するコマンド例
次の例では、Web ロールの情報を含むアプリケーション パッケージを作成します。 このコマンドは、使用するサービス定義ファイル、バイナリ ファイルがあるディレクトリ、パッケージ ファイルの名前を指定します。

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /out:[OutputFileName]
```

アプリケーションに Web ロールと worker ロールの両方が含まれている場合は、次のコマンドを使用します。

```cmd
cspack [DirectoryName]\[ServiceDefinition]
       /out:[OutputFileName]
       /role:[RoleName];[RoleBinariesDirectory]
       /sites:[RoleName];[VirtualPath];[PhysicalPath]
       /role:[RoleName];[RoleBinariesDirectory];[RoleAssemblyName]
```

変数が定義されている場所は次のとおりです。

| 変数 | 値 |
| --- | --- |
| \[DirectoryName\] |Azure プロジェクトの .csdef ファイルを含むルート プロジェクト ディレクトリの下のサブディレクトリです。 |
| \[ServiceDefinition\] |サービス定義ファイルの名前。 既定では、このファイルの名前は ServiceDefinition.csdef です。 |
| \[OutputFileName\] |生成されたパッケージ ファイルの名前。 通常、これは、アプリケーションの名前に設定されます。 ファイル名が指定されていない場合、アプリケーション パッケージは、\[ApplicationName\].cspkg として作成されます。 |
| \[RoleName\] |サービス定義ファイルに定義されているロールの名前です。 |
| \[RoleBinariesDirectory] |ロールのバイナリ ファイルの場所です。 |
| \[VirtualPath\] |サービス定義ファイルの Sites セクションで定義されている各仮想パスの物理ディレクトリです。 |
| \[PhysicalPath\] |サービス定義ファイルの [サイト] ノードで定義されている各仮想パスの内容の物理ディレクトリです。 |
| \[RoleAssemblyName\] |ロールのバイナリ ファイルの名前です。 |

## <a name="next-steps"></a>次のステップ
クラウド サービス パッケージを作成しているのですが...

* [クラウド サービスのインスタンスのリモート デスクトップをセットアップする][remotedesktop]
* [クラウド サービス プロジェクトをデプロイする][deploy]

Visual Studio を使用しているのですが...

* [新しいクラウド サービスを作成する][vs_create]
* [既存のクラウド サービスを再構成する][vs_reconfigure]
* [クラウド サービス プロジェクトをデプロイする][vs_deploy]
* [クラウド サービスのインスタンスのリモート デスクトップをセットアップする][vs_remote]

[deploy]: cloud-services-how-to-create-deploy-portal.md
[remotedesktop]: cloud-services-role-enable-remote-desktop-new-portal.md
[vs_remote]: cloud-services-role-enable-remote-desktop-visual-studio.md
[vs_deploy]: /visualstudio/azure/vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio
[vs_reconfigure]: /visualstudio/azure/vs-azure-tools-configure-roles-for-cloud-service
[vs_create]: /visualstudio/azure/vs-azure-tools-azure-project-create
