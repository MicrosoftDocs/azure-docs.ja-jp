---
title: Azure Service Fabric サービスの開始時にスクリプトを実行する
description: Service Fabric サービス セットアップ エントリ ポイントのポリシーを構成し、サービスの開始時にスクリプトを実行する方法について説明します。
author: athinanthny
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: atsenthi
ms.openlocfilehash: a25f16f08ab8ae9564363f179d19d4b30c5315fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96012529"
---
# <a name="run-a-service-startup-script-as-a-local-user-or-system-account"></a>サービス スタートアップ スクリプトをローカル ユーザー アカウントまたはローカル システム アカウントとして実行する
Service Fabric サービス実行可能ファイルを開始する前に、構成またはセットアップ作業が必要になることがあります。  たとえば、環境変数の構成です。 サービスのサービス マニフェストでは、サービス実行可能ファイルが開始される前に実行するスクリプトを指定できます。 セットアップ実行可能ファイルの実行アカウントは、サービス セットアップ エントリ ポイントの RunAs ポリシーを構成することで変更できます。  個別のセットアップ エントリ ポイントを使用すると、高い権限を持つ構成を短時間実行できるため、サービス ホスト実行可能ファイルは、長時間にわたって高い権限で実行する必要はありません。

セットアップ エントリ ポイント ([サービス マニフェスト](service-fabric-application-and-service-manifests.md)の **SetupEntryPoint**) は特権エントリ ポイントで、既定では、他のエントリポイントの前に、Service Fabric と同じ資格情報で実行されます (通常は *NetworkService* アカウント)。 **EntryPoint** によって指定された実行可能ファイルは、通常は実行時間の長いサービス ホストです。 **EntryPoint** 実行可能ファイルは、**SetupEntryPoint** 実行可能ファイルが正常に終了した後に実行されます。 結果のプロセスは監視されて再起動され、終了またはクラッシュした場合に、**SetupEntryPoint** でもう一度開始されます。 

## <a name="configure-the-service-setup-entry-point"></a>サービス セットアップ エントリ ポイントを構成する
**SetupEntryPoint** サービスで *MySetup.bat* セットアップ スクリプトを指定する、ステートレス サービスの簡単なサービス マニフェストの例を次に示します。  **Arguments** は、実行時に引数をスクリプトに渡すときに使用されます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="MyStatelessServicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest.</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyStatelessServiceType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <Arguments>MyValue</Arguments>
        <WorkingFolder>Work</WorkingFolder>        
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyStatelessService.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```
## <a name="configure-the-policy-for-a-service-setup-entry-point"></a>エントリ ポイント セットアップ サービスのポリシーを構成する
既定では、サービス セットアップ エントリ ポイント実行可能ファイルは、Service Fabric と同じ資格情報で実行されます (通常は *NetworkService* アカウント)。  アプリケーション マニフェストでは、ローカル システム アカウントまたは管理者アカウントで、スタートアップ スクリプトを実行するセキュリティのアクセス許可を変更できます。

### <a name="configure-the-policy-by-using-a-local-system-account"></a>ローカル システム アカウントを使用してポリシーを構成する
次のアプリケーション マニフェストの例は、ユーザー管理者アカウント (SetupAdminUser) で実行されるようにサービス セットアップ エントリ ポイントを構成する方法を示しています。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
    <Users>
      <User Name="SetupAdminUser">
        <MemberOf>
          <SystemGroup Name="Administrators" />
        </MemberOf>
      </User>
    </Users>
  </Principals>
</ApplicationManifest>
```

最初に、SetupAdminUser などのユーザー名で **Principals** セクションを作成します。 SetupAdminUser ユーザー アカウントは、Administrators システム グループのメンバーです。

次に、**ServiceManifestImport** セクションで、このプリンシパルを **SetupEntryPoint** に適用するためのポリシーを構成します。 このポリシーでは、**MySetup.bat** ファイルは、(管理者特権を持つ) SetupAdminUser として実行する必要があることを Service Fabric に通知します。 メイン エントリ ポイントに対してポリシーを適用 "*しない*" ため、**MyServiceHost.exe** のコードは、システム **NetworkService** アカウントで実行されます。 これはすべてのサービス エントリ ポイントが RunAs で実行する既定のアカウントです。

### <a name="configure-the-policy-by-using-local-system-accounts"></a>ローカル システム アカウントを使用してポリシーを構成する
多くの場合、管理者アカウントではなく、ローカル システム アカウントを使用して起動スクリプトを実行することが推奨されます。 管理者グループのメンバーとして RunAs ポリシーを実行すると、コンピューターでユーザー アクセス制御 (UAC) が既定で有効になっているため、通常はうまく動作しません。 このような場合、ローカル ユーザーを管理者グループに追加するのではなく、SetupEntryPoint を LocalSystem として実行することが推奨されます。 次の例では、LocalSystem として実行するように SetupEntryPoint を設定します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="MyStatelessService_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyStatelessServicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <Service Name="MyStatelessService" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="MyStatelessServiceType" InstanceCount="[MyStatelessService_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
  <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
```

> [!NOTE]
> Linux クラスターの場合、サービスまたはセットアップ エントリ ポイントを **ルート** として実行するには、**AccountType** を **LocalSystem** として指定します。

## <a name="run-a-script-from-the-setup-entry-point"></a>セットアップ エントリ ポイントからスクリプトを実行する
次は、スタートアップ スクリプトをプロジェクトに追加して、管理者特権で実行します。 

Visual Studio でサービス プロジェクトを右クリックし、*MySetup.bat* という名前の新しいファイルを追加します。

次に、サービス パッケージに *MySetup.bat* ファイルが含まれていることを確認します。 既定では、含まれていません。 ファイルを選択し、右クリックしてコンテキスト メニューを表示し、 **[プロパティ]** を選択します。 [プロパティ] ダイアログ ボックスで、 **[出力ディレクトリにコピー]** が **[新しい場合はコピーする]** に設定されていることを確認します。 次のスクリーンショットをご覧ください。

![SetupEntryPoint バッチ ファイルの Visual Studio CopyToOutput][image1]

ここで *MySetup.bat* ファイルを編集し、次のコマンドを追加して、システム環境変数を設定します。また、テキスト ファイルを出力します。

```
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable %*
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
```

次に、ソリューションをビルドして、開発用のローカル クラスターにデプロイします。 サービスが開始した後、[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) で示されるように、MySetup.bat ファイルが成功したことを 2 つの方法で確認できます。 PowerShell コマンド プロンプトを起動し、次を入力します。

```
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
```

サービスがデプロイされ、[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) で開始したノードの名前をメモします。 たとえば、Node 2 です。 次に、アプリケーション インスタンスの作業フォルダーに移動し、 **TestVariable** の値を示す out.txt ファイルを探します。 たとえば、このサービスが Node 2 にデプロイされた場合は、**MyApplicationType** の次のパスに移動します。

```
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
```

## <a name="run-powershell-commands-from-a-setup-entry-point"></a>セットアップ エントリ ポイント から PowerShell コマンドを実行する
**SetupEntryPoint** ポイントから PowerShell を実行するには、PowerShell ファイルを指し示すバッチ ファイルで **PowerShell.exe** を実行します。 最初に、PowerShell ファイルをサービス プロジェクトに追加します (**MySetup.ps1** など)。 このファイルがサービス パッケージにも含まれるように、 *[新しい場合はコピーする]* プロパティを忘れずに設定します。 次の例では、システム環境変数 **TestVariable** を設定する PowerShell ファイル MySetup.ps1 を開始するバッチ ファイルを示します。

PowerShell ファイルを開始するための MySetup.bat。

```
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
```

PowerShell ファイルで、システム環境変数を設定するために次を追加します。

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

> [!NOTE]
> 既定では、バッチ ファイルが実行されると、**work** と呼ばれるアプリケーション フォルダーでファイルを検索します。 この場合、MySetup.bat を実行するときに、アプリケーションの **コード パッケージ** フォルダーである同じフォルダー内に MySetup.ps1 ファイルを見つける必要があります。 このフォルダーを変更するには、次の作業フォルダーを設定します。
> 
> 

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
```

## <a name="debug-a-startup-script-locally-using-console-redirection"></a>コンソール リダイレクトを使用してローカルでスタートアップ スクリプトをデバッグする
場合によっては、デバッグ目的でセットアップ スクリプトの実行結果をコンソールに出力し、確認すると便利です。 サービス マニフェストのセットアップ エントリ ポイントでは、コンソール リダイレクト ポリシーを設定できます。これにより出力がファイルに書き込まれます。 ファイル出力はアプリケーションがデプロイおよび実行されるクラスター ノード上の **log** と呼ばれるアプリケーション フォルダーに書き込まれます。 

> [!WARNING]
> アプリケーションのフェールオーバーに影響する可能性があるため、運用環境でデプロイされたアプリケーションのコンソール リダイレクト ポリシーは決して使用しないでください。 これは、ローカル デプロイおよびデバッグの目的のため "*だけ*" に使用します。  
> 
> 

次のサービス マニフェストの例は、FileRetentionCount 値を使用したコンソール リダイレクトの設定を示しています。

```xml
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
```

ここで **Echo** コマンドを書き込むように、MySetup.ps1 ファイルを変更すると、これはデバッグのために出力ファイルに書き込みます。

```
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
```

> [!WARNING]
> 自分のスクリプトをデバッグしたら、すぐにこのコンソール リダイレクト ポリシーを削除してください。



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次のステップ
* [アプリケーションとサービスのセキュリティについて確認する](service-fabric-application-and-service-security.md)
* [アプリケーション モデルを理解する](service-fabric-application-model.md)
* [サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
