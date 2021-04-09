---
title: 既存の実行可能ファイルを Azure Service Fabric にデプロイする
description: 既存のアプリケーションを Service Fabric クラスターにデプロイできるようにゲスト実行可能ファイルとしてパッケージ化する方法について説明します。
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 72fde75e16341164106bb952d0bb66b83be744e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86259256"
---
# <a name="package-and-deploy-an-existing-executable-to-service-fabric"></a>既存の実行可能ファイルのパッケージ化と Service Fabric へのデプロイ

[ゲスト実行可能](service-fabric-guest-executables-introduction.md)ファイルを既存の実行可能ファイルとしてパッケージ化する際、Visual Studio プロジェクト テンプレートを使用するか、[アプリケーション パッケージを手動で作成する](#manually)かを選択できます。 Visual Studio を使用する場合、アプリケーション パッケージの構造とマニフェスト ファイルは新しいプロジェクト テンプレートによって作成されます。

> [!TIP]
> 既存の Windows 実行可能ファイルをパッケージ化してサービスにするには、Visual Studio または Yeoman (Linux の場合) を使用すると最も簡単に行うことができます。
>

## <a name="use-visual-studio-to-package-and-deploy-an-existing-executable"></a>Visual Studio を使用した既存の実行可能ファイルのパッケージ化とデプロイ

Visual Studio には、ゲスト実行可能ファイルを Service Fabric クラスターにデプロイするときに役立つ Service Fabric サービスのテンプレートが用意されています。

1. **[ファイル]** 、 >  **[新しいプロジェクト]** の順に選択し、Service Fabric アプリケーションを作成します。
2. サービス テンプレートとして **ゲスト実行可能ファイル** を選択します。
3. **[参照]** をクリックし、実行可能ファイルが含まれたフォルダーを選択します。残りのパラメーターを入力し、サービスを作成します。
   * *Code Package Behavior* - フォルダー内の内容をすべて Visual Studio プロジェクトにコピーするように設定できます。これは、実行可能ファイルが変更されない場合に便利です。 実行可能ファイルが変更されることが予想され、新しいビルドを動的に取得する機能が必要な場合は、フォルダーにリンクすることもできます。 Visual Studio でアプリケーション プロジェクトを作成するときは、リンクされたフォルダーを使用できます。 このフォルダーを使用すると、プロジェクト内からソースの場所にリンクされるため、ソースのリンク先でゲスト実行可能ファイルを更新できるようになります。 これらの更新はビルド時にアプリケーション パッケージの一部になります。
   * *Program* - サービスを開始するために実行する必要がある実行可能ファイルを指定します。
   * *Arguments* - 実行可能ファイルに渡す引数を指定します。 引数を含むパラメーターの一覧を指定することもできます。
   * *WorkingFolder* - 開始するプロセスの作業ディレクトリを指定します。 次の 3 つの値のいずれかを指定できます。
     * `CodeBase` は、作業ディレクトリをアプリケーション パッケージ内のコード ディレクトリ (前述のファイル構造内に見られる `Code` ディレクトリ) に設定するよう指定します。
     * `CodePackage` は、作業ディレクトリをアプリケーション パッケージのルート (前述のファイル構造内に見られる `GuestService1Pkg`) に設定するよう指定します。
     * `Work` は、work という名前のサブディレクトリにファイルを配置するよう指定します。
4. サービスに名前を付けて、 **[OK]** をクリックします。
5. サービスに通信用のエンドポイントが必要な場合は、ServiceManifest.xml ファイルに Protocol、Port、Type を追加できます。 (例: `<Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" UriScheme="http" PathSuffix="myapp/" Type="Input" />`)。
6. これで、ローカル クラスターに対してパッケージ化と発行の操作を使用できるようになりました。この操作を行うには、Visual Studio でソリューションをデバッグします。 準備ができたら、アプリケーションをリモート クラスターに発行するか、ソリューションをソース管理にチェックインすることができます。
7. Service Fabric Explorer で実行されているゲスト実行可能サービスを表示する方法については、「[実行中のアプリケーションの確認](#check-your-running-application)」を参照してください。

例のチュートリアルについては、[Visual Studio を使用した最初のゲスト実行可能アプリケーションの作成](quickstart-guest-app.md)に関するページを参照してください。

### <a name="packaging-multiple-executables-with-visual-studio"></a>Visual Studio で複数の実行可能ファイルをパッケージ化する

Visual Studio を使用して、複数のゲスト実行可能ファイルを含むアプリケーション パッケージを生成することができます。 最初のゲスト実行可能ファイルを追加した後は、アプリケーション プロジェクトを右クリックし、 **[追加] -> [新しい Service Fabric サービス]** を選択して、2 つ目のゲスト実行可能ファイル プロジェクトをソリューションに追加します。

> [!NOTE]
> Visual Studio プロジェクトでソースをリンクした場合、Visual Studio ソリューションをビルドすると、アプリケーション パッケージは、ソースの変更に応じて常に最新の状態になります。

## <a name="use-yeoman-to-package-and-deploy-an-existing-executable-on-linux"></a>Linux で Yeoman を使用した既存の実行可能ファイルのパッケージ化とデプロイ

Linux でゲスト実行可能ファイルを作成してデプロイする手順は、csharp または java アプリケーションをデプロイする場合と同じです。

1. ターミナルで、「`yo azuresfguest`」と入力します。
2. アプリケーションに名前を付けます。
3. サービスの名前を指定し、実行可能ファイルのパスや呼び出すときに渡す必要があるパラメーターなどの詳細を提供します。

Yeoman により、インストールおよびアンインストール スクリプトと共に、適切なアプリケーション ファイルとマニフェスト ファイルが含まれたアプリケーション パッケージが作成されます。

### <a name="packaging-multiple-executables-using-yeoman-on-linux"></a>Linux で Yeoman を使用して複数の実行可能ファイルをパッケージ化する

`yo` を使用して作成したアプリケーションにサービスを追加するには、次の手順を実行します。

1. ディレクトリを既存アプリケーションのルートに変更します。  たとえば、Yeoman で作成したアプリケーションが `MyApplication` の場合は、`cd ~/YeomanSamples/MyApplication` です。
2. `yo azuresfguest:AddService` を実行し、必要な詳細を指定します。

<a id="manually"></a>

## <a name="manually-package-and-deploy-an-existing-executable"></a>既存の実行可能ファイルの手動によるパッケージ化とデプロイ

ゲスト実行可能ファイルを手動でパッケージ化するプロセスは、次の一般的な手順に基づいています。

1. パッケージ ディレクトリ構造を作成します。
2. アプリケーションのコードと構成ファイルを追加します。
3. サービス マニフェスト ファイルを編集します。
4. アプリケーション マニフェスト ファイルを編集します。

### <a name="create-the-package-directory-structure"></a>パッケージ ディレクトリ構造を作成する

まず、「[Azure Service Fabric アプリのパッケージ化](./service-fabric-package-apps.md)」の説明に従ってディレクトリ構造を作成します。

### <a name="add-the-applications-code-and-configuration-files"></a>アプリケーションのコードと構成ファイルを追加する

ディレクトリ構造を作成したら、アプリケーションのコード ファイルと構成ファイルを、code ディレクトリおよび config ディレクトリに追加できます。 code ディレクトリまたは config ディレクトリに、追加のディレクトリまたはサブディレクトリを作成することもできます。

Service Fabric では、アプリケーション ルート ディレクトリの内容が `xcopy` されるので、2 つの上位ディレクトリ、コード、設定を作成する以外に使用する定義済みの構造はありません (必要に応じて別の名前を選択できます。 詳細については、次のセクションで説明します)。

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
<ServiceManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

#### <a name="optional-update-setupentrypoint"></a>省略可能:SetupEntrypoint の更新

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

サービス マニフェスト ファイルの `EntryPoint` 要素では、サービスの起動方法を指定します。

`ExeHost` 要素は、サービスの起動に使用する実行可能ファイル (および引数) を指定します。 必要に応じて、`IsExternalExecutable="true"` 属性を `ExeHost` に追加して、プログラムがコード パッケージに含まれない外部の実行可能ファイルであることを示すことができます。 たとえば、「 `<ExeHost IsExternalExecutable="true">` 」のように入力します。

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
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

```powershell

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

* `InstanceCount = "1"`. 1 つのサービス インスタンスのみがクラスターにデプロイされます。 サービスがデプロイされるノードは、Service Fabric スケジューラによって決まります。
* `InstanceCount ="-1"`. 1 つのサービス インスタンスが、Service Fabric クラスターのすべてのノードにデプロイされます。 結果として、1 つ (のみ) のサービス インスタンスがクラスターの各ノードに存在することになります。

クライアント アプリケーションはクラスター内のいずれかのノードに "接続" するだけでエンドポイントを使用できるため、フロントエンド アプリケーション (REST エンドポイントなど) には便利な構成です。 この構成は、Service Fabric クラスターのすべてのノードがロード バランサーに接続されている場合などにも使用できます。 クライアントのトラフィックは、クラスター内のすべてのノードで実行されているサービス間で配布できます。

## <a name="check-your-running-application"></a>実行中のアプリケーションの確認
Service Fabric エクスプローラーで、サービスが実行されているノードを特定します。 この例では、Node1 で実行されています。

![サービスが実行されているノード](./media/service-fabric-deploy-existing-app/nodeappinsfx.png)

ノードに移動してアプリケーションを参照した場合は、ディスク上の場所を含むノードの重要な情報が表示されます。

![ディスク上の場所](./media/service-fabric-deploy-existing-app/locationondisk2.png)

サーバー エクスプローラーを使用してディレクトリを参照した場合は、次のスクリーンショットに示すように、作業ディレクトリとサービスのログ フォルダーを確認できます。

![ログの場所](./media/service-fabric-deploy-existing-app/loglocation.png)

## <a name="next-steps"></a>次のステップ

この記事では、ゲスト実行可能ファイルをパッケージ化し、Service Fabric にデプロイする方法について説明しました。 関連する情報やタスクは、次の記事を参照してください。

* [ゲスト実行可能ファイルをパッケージ化してデプロイするためのサンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)。これには、パッケージ化ツールのプレリリース版のリンクが含まれています。
* [REST を使用してネーム サービス経由で通信する 2 つのゲスト実行可能ファイル (C# と nodejs) のサンプル](https://github.com/Azure-Samples/service-fabric-containers)
* [Visual Studio で最初の Service Fabric アプリケーションを作成する](service-fabric-tutorial-create-dotnet-app.md)
