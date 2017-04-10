---
title: "Azure Service Fabric への既存の実行可能ファイルのデプロイ | Microsoft Docs"
description: "既存のアプリケーションを Service Fabric クラスターにデプロイできるようにゲスト実行可能ファイルとしてパッケージ化する方法のチュートリアル"
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: 
ms.assetid: d799c1c6-75eb-4b8a-9f94-bf4f3dadf4c3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/17/2016
ms.author: mfussell;mikhegn
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: e9c53dc601406961ee7aeca2e350ba14e691cb9b
ms.lasthandoff: 03/29/2017


---
# <a name="deploy-a-guest-executable-to-service-fabric"></a>Service Fabric へのゲスト実行可能ファイルのデプロイ
Node.js アプリケーション、Java アプリケーション、Azure Service Fabric のネイティブなアプリケーションなど、あらゆる種類のアプリケーションを実行できます。 このようなアプリケーションを Service Fabric ではゲスト実行可能ファイルと呼びます。
Service Fabric は、ゲスト実行可能ファイルをステートレス サービスと同様に扱います。 これにより、ゲスト実行可能ファイルは可用性などのメトリックに基づいてクラスター内のノードに配置されます。 この記事を読むと、Visual Studio またはコマンド ライン ユーティリティを使用して、ゲスト実行可能ファイルをパッケージ化し、Service Fabric クラスターにデプロイする方法を理解できます。

この記事では、ゲスト実行可能ファイルをパッケージ化し、Service Fabric にデプロイするための手順について説明します。  

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Service Fabric でゲスト実行可能ファイルを実行するメリット
Service Fabric クラスターでゲスト実行可能ファイルを実行するメリットはいくつかあります。

* 高可用性: アプリケーションを Service Fabric で実行すると、可用性が向上します。 Service Fabric によって、アプリケーションのインスタンスが常に稼動している状態が維持されます。
* 正常性の監視: Service Fabric の正常性監視機能により、アプリケーションが稼動しているかどうかが検出され、障害が発生した場合は診断情報が提供されます。   
* アプリケーション ライフサイクル管理: Service Fabric を使用すると、ダウンタイムなしでアップグレードを実行できるだけでなく、アップグレード中に正常性に問題が発生したことが報告された場合は前のバージョンに自動的にロールバックされます。    
* 密度: 各アプリケーションを専用のハードウェアで実行する必要がないクラスターで複数のアプリケーションを実行できます。

## <a name="samples"></a>サンプル
* [ゲスト実行可能ファイルをパッケージ化してデプロイするためのサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication)
* [REST を使用してネーム サービス経由で通信する 2 つのゲスト実行可能ファイル (C# と nodejs) のサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>アプリケーション マニフェスト ファイルとサービス マニフェスト ファイルの概要
ゲスト実行可能ファイルのデプロイの一環として、[アプリケーション モデル](service-fabric-application-model.md)に関するページに目を通し、Service Fabric のパッケージ化とデプロイのモデルについて理解しておくと役立ちます。 Service Fabric パッケージ化モデルでは、アプリケーション マニフェストとサービス マニフェストの 2 つの XML ファイルを使用します。 ApplicationManifest.xml ファイルと ServiceManifest.xml ファイルのスキーマ定義が、Service Fabric SDK と共に *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd* にインストールされています。

* **アプリケーション マニフェスト**: アプリケーション マニフェストは、アプリケーションについて記述するために使用されます。 このファイルには、アプリケーションを構成する複数のサービスとその他のパラメーター (インスタンス数など) が列挙されています。パラメーターは、個々のサービスや複数のサービスをデプロイする方法を定義するために使用されます。
  
  Service Fabric では、デプロイとアップグレードを行う際に 1 つのアプリケーションをユニットとして扱います。 アプリケーションは 1 つのユニットとしてアップグレードすることができ、エラー (およびロールバック) が発生した場合も 1 つのユニットとして管理されます。 Service Fabric は、アップグレード プロセスが正常完了した場合だけでなく、失敗した場合でも、アプリケーションが不明または不安定な状態にならないことを保証します。
* **サービス マニフェスト** : サービス マニフェストは、サービスのコンポーネントを記述するために使用されます。 このファイルには、サービスの名前と種類、そのコード、構成などの情報が記載されています。 また、サービスのデプロイ後の構成に使用できるその他のパラメーターも含まれます。

## <a name="application-package-file-structure"></a>アプリケーション パッケージ ファイルの構造
Service Fabric にアプリケーションをデプロイするには、事前に定義されたディレクトリ構造にアプリケーションが従っている必要があります。 この構造の例を次に示します。

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

ApplicationPackageRoot には、アプリケーションを定義する ApplicationManifest.xm ファイルが含まれています。 アプリケーションに含まれる各サービスのサブディレクトリには、サービスに必要なすべてのアーティファクトが含まれています。 これらのサブディレクトリは、ServiceManifest.xml と一般的に次のディレクトリです。

* *Code*。 このディレクトリにはサービス コードが含まれています。
* *Config*:settings.xml ファイル (および、必要に応じてその他のファイル) が含まれています。 実行時にサービスがこのファイルにアクセスし、特定の構成設定を取得します。
* *Data*:追加のローカル データが格納されている追加ディレクトリ。 サービスでこのデータが必要になる場合があります。 データは、一時的なデータのみを格納します。 フェールオーバー中など、サービスの再配置が必要な場合、Service Fabric ではこのデータ ディレクトリには変更がコピー/レプリケートされません。

> [!NOTE]
> `config` ディレクトリと `data` ディレクトリは、必要がなければ作成する必要はありません。
> 
> 

## <a name="package-an-existing-executable"></a>既存の実行可能ファイルのパッケージ化
ゲスト実行可能ファイルをパッケージ化する際、Visual Studio プロジェクト テンプレートを使用するか、[アプリケーション パッケージを手動で作成する](#manually)かを選択できます。 Visual Studio を使用する場合、アプリケーション パッケージの構造とマニフェスト ファイルは新しいプロジェクト テンプレートによって作成されます。

> [!TIP]
> 既存の Windows 実行可能ファイルをパッケージ化してサービスにする場合は、Visual Studio を使用すると最も簡単に行うことができます。
> 
> 

## <a name="use-visual-studio-to-package-an-existing-executable"></a>Visual Studio を使用した既存の実行可能ファイルのパッケージ化
Visual Studio には、ゲスト実行可能ファイルを Service Fabric クラスターにデプロイするときに役立つ Service Fabric サービスのテンプレートが用意されています。

1. **[ファイル]**、 > **[新しいプロジェクト]** の順に選択し、Service Fabric アプリケーションを作成します。
2. サービス テンプレートとして**ゲスト実行可能ファイル**を選択します。
3. **[参照]** をクリックし、実行可能ファイルが含まれたフォルダーを選択します。残りのパラメーターを入力し、サービスを作成します。
   * *Code Package Behavior* - フォルダー内の内容をすべて Visual Studio プロジェクトにコピーするように設定できます。これは、実行可能ファイルが変更されない場合に便利です。 実行可能ファイルが変更されることが予想され、新しいビルドを動的に取得する機能が必要な場合は、フォルダーにリンクすることもできます。 Visual Studio でアプリケーション プロジェクトを作成する際に、リンクされたフォルダーを使用できます。 このフォルダーを使用すると、プロジェクト内からソースの場所にリンクされるため、ソースのリンク先でゲスト実行可能ファイルを更新できるようになります。 これらの更新はビルド時にアプリケーション パッケージの一部になります。
   * *Program* - サービスを開始するために実行する必要がある実行可能ファイルを指定します。
   * *Arguments* - 実行可能ファイルに渡す引数を指定します。 引数を含むパラメーターの一覧を指定することもできます。
   * *WorkingFolder* - 開始するプロセスの作業ディレクトリを指定します。 次の 3 つの値のいずれかを指定できます。
     * `CodeBase` は、作業ディレクトリをアプリケーション パッケージ内のコード ディレクトリ (前述のファイル構造内に見られる `Code` ディレクトリ) に設定するよう指定します。
     * `CodePackage` は、作業ディレクトリをアプリケーション パッケージのルート (前述のファイル構造内に見られる `GuestService1Pkg`) に設定するよう指定します。
     * `Work` は、work という名前のサブディレクトリにファイルを配置するよう指定します。
4. サービスに名前を付けて、**[OK]** をクリックします。
5. サービスに通信用のエンドポイントが必要な場合は、ServiceManifest.xml ファイルに Protocol、Port、Type を追加できます。 (例: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`)。
6. これで、ローカル クラスターに対してパッケージ化と発行の操作を使用できるようになりました。この操作を行うには、Visual Studio でソリューションをデバッグします。 準備ができたら、アプリケーションをリモート クラスターに発行するか、ソリューションをソース管理にチェックインすることができます。
7. Service Fabric Explorer で実行されているゲスト実行可能サービスを表示する方法については、この記事の末尾をご覧ください。

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>既存の実行可能ファイルの手動によるパッケージ化とデプロイ
ゲスト実行可能ファイルを手動でパッケージ化するプロセスは、次の一般的な手順に基づいています。

1. パッケージ ディレクトリ構造を作成します。
2. アプリケーションのコードと構成ファイルを追加します。
3. サービス マニフェスト ファイルを編集します。
4. アプリケーション マニフェスト ファイルを編集します。

<!--
>[AZURE.NOTE] We do provide a packaging tool that allows you to create the ApplicationPackage automatically. The tool is currently in preview. You can download it from [here](http://aka.ms/servicefabricpacktool).
-->

### <a name="create-the-package-directory-structure"></a>パッケージ ディレクトリ構造を作成する
最初に前のセクション「アプリケーション パッケージ ファイルの構造」の説明に従ってディレクトリ構造を作成することができます。

### <a name="add-the-applications-code-and-configuration-files"></a>アプリケーションのコードと構成ファイルを追加する
ディレクトリ構造を作成したら、アプリケーションのコード ファイルと構成ファイルを、code ディレクトリおよび config ディレクトリに追加できます。 code ディレクトリまたは config ディレクトリに、追加のディレクトリまたはサブディレクトリを作成することもできます。

Service Fabric では、アプリケーション ルート ディレクトリの内容が `xcopy` されるので、2 つの上位ディレクトリ、コード、設定を作成する以外に使用する定義済みの構造はありません  (必要に応じて別の名前を選択できます。 詳細については、次のセクションで説明します)。

> [!NOTE]
> アプリケーションに必要なすべてのファイルと依存関係を必ず含めるようにしてください。 Service Fabric では、アプリケーションのサービスがデプロイされるクラスター内のすべてのノードに、アプリケーション パッケージの内容がコピーされます。 そのため、パッケージには、アプリケーションを実行するのに必要なすべてのコードが含まれている必要があります。 依存関係がインストール済みであることを前提としないでください。
> 
> 

### <a name="edit-the-service-manifest-file"></a>サービス マニフェスト ファイルを編集する
次の手順では、サービス マニフェスト ファイルを編集して、次の情報を含めます。

* サービスの種類の名前。 これは、Service Fabric がサービスを識別するために使用する ID です。
* アプリケーションの起動に使用するコマンド (ExeHost)。
* アプリケーションをセットアップするために実行する必要のあるスクリプト (SetupEntrypoint)。

`ServiceManifest.xml` ファイルの例を次に示します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

次のセクションでは、更新する必要のあるファイルの別の部分を見てみましょう。

#### <a name="update-servicetypes"></a>ServiceTypes の更新
```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

* `ServiceTypeName`には、任意の名前を選択できます。 この値は、サービスを識別するために `ApplicationManifest.xml` ファイルで使用されます。
* `UseImplicitHost="true"` を指定します。 サービスは自己完結型アプリに基づいているため、プロセスとして起動し、正常性を監視する処理のみが必要であることを、この属性で Service Fabric に伝えます。

#### <a name="update-codepackage"></a>CodePackage の更新
CodePackage 要素には、サービスのコードの場所 (およびバージョン) を指定します。

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

`Name` 要素は、サービスのコードが含まれるアプリケーション パッケージ内のディレクトリ名を指定するために使用されます。 `CodePackage` にも `version` 属性があります。 この属性を使用すると、コードのバージョンを指定できます。また、Service Fabric のアプリケーション ライフサイクル管理インフラストラクチャを使用してサービスのコードをアップグレードするためにも使用できます。

#### <a name="optional-update-setupentrypoint"></a>省略可能: SetupEntrypoint の更新
```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
SetupEntryPoint 要素を使用して、サービスのコードが起動される前に実行する必要がある実行可能ファイルまたはバッチ ファイルを指定します。 これはオプションの手順なので、必要な初期化が存在しない場合は追加する必要はありません。 SetupEntryPoint は、サービスを再起動するたびに実行されます。

SetupEntryPoint は 1 つしかないため、アプリケーションのセットアップに複数のスクリプトが必要な場合は、セットアップ スクリプトを 1 つのバッチ ファイルにグループ化する必要があります。 SetupEntryPoint は、あらゆる種類のファイル (実行可能ファイル、バッチ ファイル、PowerShell コマンドレット) を実行できます。 詳細については、 [SetupEntryPoint の構成](service-fabric-application-runas-security.md)に関するページをご覧ください。

上の例では、SetupEntryPoint は code ディレクトリの `scripts` サブディレクトリにある `LaunchConfig.cmd` という名前のバッチ ファイルを実行します (WorkingFolder 要素が CodeBase に設定されている場合)。

#### <a name="update-entrypoint"></a>EntryPoint の更新
```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

サービス マニフェスト ファイルの `EntryPoint` 要素では、サービスの起動方法を指定します。 `ExeHost` 要素は、サービスの起動に使用する実行可能ファイル (および引数) を指定します。

* `Program` には、サービスを開始する必要がある実行可能ファイルの名前を指定します。
* `Arguments` には、実行可能ファイルに渡す引数を指定します。 引数を含むパラメーターの一覧を指定することもできます。
* `WorkingFolder` には、開始するプロセスの作業ディレクトリを指定します。 次の 3 つの値のいずれかを指定できます。
  * `CodeBase` は、作業ディレクトリをアプリケーション パッケージ内のコード ディレクトリ (前述のファイル構造内の `Code` ディレクトリ) に設定するよう指定します。
  * `CodePackage` は、作業ディレクトリをアプリケーション パッケージのルート (前述のファイル構造内の `GuestService1Pkg`) に設定するよう指定します。
    * `Work` は、work という名前のサブディレクトリにファイルを配置するよう指定します。

WorkingFolder は、アプリケーション スクリプトと初期化スクリプトのいずれかが相対パスを使用できるように、正しい作業ディレクトリを設定するのに便利です。

#### <a name="update-endpoints-and-register-with-naming-service-for-communication"></a>エンドポイントの更新と、通信のための Naming Service の登録
```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
上の例では、 `Endpoint` 要素に、アプリケーションがリッスンできるエンドポイントを指定しています。 この例の場合、Node.js アプリケーションはポート 3000 で HTTP をリッスンします。

また、他のサービスがこのサービスのエンドポイント アドレスを解決できるように、このエンドポイントを Naming Service に発行するよう Service Fabric に依頼できます。 これにより、サービス間、つまりゲスト実行可能ファイル間で通信できるようになります。
発行されるエンドポイント アドレスの形式は、 `UriScheme://IPAddressOrFQDN:Port/PathSuffix`です。 `UriScheme` と `PathSuffix` はオプションの属性です。 `IPAddressOrFQDN` は、この実行可能ファイルが配置されているノードの IP アドレスまたは完全修飾ドメイン名で、自動的に算定されます。

次の例では、サービスがデプロイされると、サービス インスタンス用に発行されたエンドポイントが Service Fabric Explorer に `http://10.1.4.92:3000/myapp/` のように表示されます。 または、ローカル コンピューターの場合は `http://localhost:3000/myapp/` と表示されます。

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000"  UriScheme="http" PathSuffix="myapp/" Type="Input" />
</Endpoints>
```
サービス間の通信で、これらのアドレスを [リバース プロキシ](service-fabric-reverseproxy.md) と共に使用することができます。

### <a name="edit-the-application-manifest-file"></a>アプリケーション マニフェスト ファイルを編集する
`Servicemanifest.xml` ファイルを構成したら、適切なサービスの種類と名前が使用されるように `ApplicationManifest.xml` ファイルを少し変更する必要があります。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

#### <a name="servicemanifestimport"></a>ServiceManifestImport
`ServiceManifestImport` 要素では、アプリに追加する 1 つ以上のサービスを指定できます。 サービスは、`ServiceManifest.xml` ファイルが配置されているディレクトリの名前を指定する `ServiceManifestName` で参照されます。

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

## <a name="set-up-logging"></a>ログを設定する
ゲスト実行可能ファイルについて、アプリケーションと構成のスクリプトにエラーがあるかどうかを確認するコンソールのログを表示できると便利です。
コンソールのリダイレクトは、`ServiceManifest.xml` ファイルで `ConsoleRedirection` 要素を使用して構成できます。

> [!WARNING]
> アプリケーションのフェールオーバーに影響する可能性があるため、運用環境でデプロイされたアプリケーションのコンソール リダイレクト ポリシーは決して使用しないでください。 これは、ローカル デプロイおよびデバッグの目的のため "*だけ*" に使用します。  
> 
> 

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

`ConsoleRedirection` を使用してコンソール出力 (stdout と stderr の両方) を作業ディレクトリにリダイレクトすることができます。 これは、Service Fabric クラスター内のアプリケーションのセットアップまたは実行中にエラーがないことを確認する機能を提供します。

`FileRetentionCount` によって、作業ディレクトリ内に保存されているファイルの数を判断できます。 たとえば、値が 5 の場合は、これまでの 5 回の実行のログ ファイルが作業ディレクトリに格納されていることを意味します。

`FileMaxSizeInKb` では、ログ ファイルの最大サイズを指定します。

ログ ファイルは、サービスの作業ディレクトリのいずれかに保存されます。 ファイルの場所を特定するには、Service Fabric Explorer を使用して、サービスが実行されているノードと使用されている作業ディレクトリを確認します。 この方法については、この記事の後半で説明します。

## <a name="deployment"></a>デプロイ
最後のステップは、[アプリケーションのデプロイ](service-fabric-deploy-remove-applications.md)です。 次の PowerShell スクリプトは、ローカル デプロイ用クラスターにアプリケーションをデプロイし、新しい Service Fabric サービスを開始する方法を示しています。

```PowerShell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MultipleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```

>[!TIP]
> パッケージが大規模な場合や、パッケージに多くのファイルが含まれる場合は、[パッケージを圧縮](service-fabric-package-apps.md#compress-a-package)してからイメージ ストアにコピーします。 詳細については、[こちら](service-fabric-deploy-remove-applications.md#upload-the-application-package)をご覧ください。
>

Service Fabric サービスは、さまざまな "構成" にデプロイできます。 たとえば、単一または複数のインスタンスとしてデプロイしたり、Service Fabric クラスターの各ノードに 1 つのサービス インスタンスが配置されるようにデプロイしたりできます。

`New-ServiceFabricService` コマンドレットの `InstanceCount` パラメーターは、Service Fabric クラスターで開始するサービス インスタンスの数を指定するために使用します。 `InstanceCount` 値は、デプロイするアプリケーションの種類に応じて設定できます。 よく使用されるシナリオは 2 つあります。

* `InstanceCount = "1"` 1 つのサービス インスタンスのみがクラスターにデプロイされます。 サービスがデプロイされるノードは、Service Fabric スケジューラによって決まります。
* `InstanceCount ="-1"` 1 つのサービス インスタンスが、Service Fabric クラスターのすべてのノードにデプロイされます。 結果として、1 つ (のみ) のサービス インスタンスがクラスターの各ノードに存在することになります。

クライアント アプリケーションはクラスター内のいずれかのノードに "接続" するだけでエンドポイントを使用できるため、フロントエンド アプリケーション (REST エンドポイントなど) には便利な構成です。 この構成は、Service Fabric クラスターのすべてのノードがロード バランサーに接続されている場合などにも使用できます。 クライアントのトラフィックは、クラスター内のすべてのノードで実行されているサービス間で配布できます。

## <a name="check-your-running-application"></a>実行中のアプリケーションの確認
Service Fabric エクスプローラーで、サービスが実行されているノードを特定します。 この例では、Node1 で実行されています。

![サービスが実行されているノード](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

ノードに移動してアプリケーションを参照した場合は、ディスク上の場所を含むノードの重要な情報が表示されます。

![ディスク上の場所](./media/service-fabric-deploy-existing-app/locationondisk2.png)

サーバー エクスプローラーを使用してディレクトリを参照した場合は、次のスクリーンショットに示すように、作業ディレクトリとサービスのログ フォルダーを確認できます。

![ログの場所](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="creating-a-guest-executable-using-yeoman-for-service-fabric-on-linux"></a>Linux で Service Fabric の Yeoman を使用したゲスト実行可能ファイルの作成

Linux でゲスト実行可能ファイルを作成してデプロイする手順は、csharp または java アプリケーションをデプロイする場合と同じです。 

1. ターミナルで、「`yo azuresfguest`」と入力します。
2. アプリケーションに名前を付けます。
3. 最初のサービスの種類を選択し、名前を付けます。 ゲスト実行可能ファイルの**ゲスト バイナリ**を選択し (コンテナーの場合は**ゲスト コンテナー**を選択)、実行可能ファイルのパスや呼び出す必要があるパラメーターなどの詳細を指定します。

Yeoman により、インストールおよびアンインストール スクリプトと共に、適切なアプリケーション ファイルとマニフェスト ファイルが含まれたアプリケーション パッケージが作成されます。

## <a name="next-steps"></a>次のステップ
この記事では、ゲスト実行可能ファイルをパッケージ化し、Service Fabric にデプロイする方法について説明しました。 関連する情報やタスクは、次の記事を参照してください。

* [ゲスト実行可能ファイルをパッケージ化してデプロイするためのサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/GuestExe/SimpleApplication)。これには、パッケージ化ツールのプレリリース版のリンクが含まれています。
* [REST を使用してネーム サービス経由で通信する 2 つのゲスト実行可能ファイル (C# と nodejs) のサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-containers)
* [複数のゲスト実行可能ファイルのデプロイ](service-fabric-deploy-multiple-apps.md)
* [Visual Studio で最初の Service Fabric アプリケーションを作成する](service-fabric-create-your-first-application-in-visual-studio.md)


