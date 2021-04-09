---
title: Azure Cloud Services (クラシック) の定義WorkerRole スキーマ | Microsoft Docs
description: Azure の worker ロールは、一般化された開発に使用され、Web ロールのバックグラウンド処理を実行できます。 Azure worker ロールのスキーマについて説明します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0871527187a3d678cb2b94bd8dc342cf2abde1ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98743136"
---
# <a name="azure-cloud-services-classic-definition-workerrole-schema"></a>Azure Cloud Services (クラシック) の定義: WorkerRole スキーマ

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

Azure の worker ロールは、一般化された開発に役立ち、Web ロールのバックグラウンド処理を実行できるロールです。

サービス定義ファイルの既定の拡張子は .csdef です。

## <a name="basic-service-definition-schema-for-a-worker-role"></a>worker ロールの基本サービス定義スキーマ
worker ロールが含まれるサービス定義ファイルの基本形式は次のとおりです。

```xml
<ServiceDefinition …>
  <WorkerRole name="<worker-role-name>" vmsize="<worker-role-size>" enableNativeCodeExecution="[true|false]">
    <Certificates>
      <Certificate name="<certificate-name>" storeLocation="[CurrentUser|LocalMachine]" storeName="[My|Root|CA|Trust|Disallow|TrustedPeople|TrustedPublisher|AuthRoot|AddressBook|<custom-store>" />
    </Certificates>
    <ConfigurationSettings>
      <Setting name="<setting-name>" />
    </ConfigurationSettings>
    <Endpoints>
      <InputEndpoint name="<input-endpoint-name>" protocol="[http|https|tcp|udp]" localPort="<local-port-number>" port="<port-number>" certificate="<certificate-name>" loadBalancerProbe="<load-balancer-probe-name>" />
      <InternalEndpoint name="<internal-endpoint-name" protocol="[http|tcp|udp|any]" port="<port-number>">
         <FixedPort port="<port-number>"/>
         <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
      </InternalEndpoint>
     <InstanceInputEndpoint name="<instance-input-endpoint-name>" localPort="<port-number>" protocol="[udp|tcp]">
         <AllocatePublicPortFrom>
            <FixedPortRange min="<minimum-port-number>" max="<maximum-port-number>"/>
         </AllocatePublicPortFrom>
      </InstanceInputEndpoint>
    </Endpoints>
    <Imports>
      <Import moduleName="[RemoteAccess|RemoteForwarder|Diagnostics]"/>
    </Imports>
    <LocalResources>
      <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    </LocalResources>
    <LocalStorage name="<local-store-name>" cleanOnRoleRecycle="[true|false]" sizeInMB="<size-in-megabytes>" />
    <Runtime executionContext="[limited|elevated]">
      <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
      </Environment>
      <EntryPoint>
         <NetFxEntryPoint assemblyName="<name-of-assembly-containing-entrypoint>" targetFrameworkVersion="<.net-framework-version>"/>
         <ProgramEntryPoint commandLine="<application>" setReadyOnProcessStart="[true|false]"/>
      </EntryPoint>
    </Runtime>
    <Startup priority="<for-internal-use-only>">
      <Task commandLine="" executionContext="[limited|elevated]" taskType="[simple|foreground|background]">
        <Environment>
         <Variable name="<variable-name>" value="<variable-value>">
            <RoleInstanceValue xpath="<xpath-to-role-environment-settings>"/>
          </Variable>
        </Environment>
      </Task>
    </Startup>
    <Contents>
      <Content destination="<destination-folder-name>" >
        <SourceDirectory path="<local-source-directory>" />
      </Content>
    </Contents>
  </WorkerRole>
</ServiceDefinition>
```

## <a name="schema-elements"></a>スキーマ要素
サービス定義ファイルには、本トピックの以降のセクションで詳しく説明する以下の要素が含まれています。

[WorkerRole](#WorkerRole)

[ConfigurationSettings](#ConfigurationSettings)

[設定](#Setting)

[LocalResources](#LocalResources)

[LocalStorage](#LocalStorage)

[エンドポイント](#Endpoints)

[InputEndpoint](#InputEndpoint)

[InternalEndpoint](#InternalEndpoint)

[InstanceInputEndpoint](#InstanceInputEndpoint)

[AllocatePublicPortFrom](#AllocatePublicPortFrom)

[FixedPort](#FixedPort)

[FixedPortRange](#FixedPortRange)

[証明書](#Certificates)

[[MSSQLSERVER のプロトコルのプロパティ]](#Certificate)

[インポートする](#Imports)

[[インポート]](#Import)

[Runtime](#Runtime)

[環境](#Environment)

[EntryPoint](#EntryPoint)

[NetFxEntryPoint](#NetFxEntryPoint)

[ProgramEntryPoint](#ProgramEntryPoint)

[Variable](#Variable)

[RoleInstanceValue](#RoleInstanceValue)

[Startup](#Startup)

[タスク](#Task)

[Contents](#Contents)

[コンテンツ](#Content)

[SourceDirectory](#SourceDirectory)

##  <a name="workerrole"></a><a name="WorkerRole"></a> WorkerRole
`WorkerRole` 要素は、一般化された開発に役立ち、Web ロールのバックグラウンド処理を実行できるロールを記述します。 サービスには、0 個以上の worker ロールを含めることができます。

以下の表に、`WorkerRole` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|name|string|必須。 worker ロールの名前です。 ロールの名前は一意である必要があります。|
|enableNativeCodeExecution|boolean|任意。 既定値は `true` であり、ネイティブ コード実行および完全な信頼は既定で有効になっています。 worker ロールでネイティブ コード実行を無効化し、代わりに Azure 部分信頼を使用するには、この属性を `false` に設定します。|
|vmsize|string|省略可能。 このロールに割り当てる仮想マシンのサイズを変更するには、この値を設定します。 既定値は `Small` です。 指定できる仮想マシンのサイズと属性の一覧については、[Cloud Services の仮想マシンのサイズ](cloud-services-sizes-specs.md)に関するページをご覧ください。|

##  <a name="configurationsettings"></a><a name="ConfigurationSettings"></a> ConfigurationSettings
`ConfigurationSettings` 要素では、worker ロールの構成設定のコレクションを指定します。 この要素は、`Setting` 要素の親です。

##  <a name="setting"></a><a name="Setting"></a> 設定
`Setting` 要素では、ロールのインスタンスの構成設定を指定する名前と値のペアを指定します。

以下の表に、`Setting` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|name|string|必須。 構成設定の一意の名前です。|

ロールの構成設定とは、サービス定義ファイルで宣言され、サービス構成ファイルで設定されている名前と値のペアのことです。

##  <a name="localresources"></a><a name="LocalResources"></a> LocalResources
`LocalResources` 要素では、worker ロールのローカル ストレージ リソースのコレクションを指定します。 この要素は、`LocalStorage` 要素の親です。

##  <a name="localstorage"></a><a name="LocalStorage"></a> LocalStorage
`LocalStorage` 要素では、実行時にサービス用のファイル システム領域を提供するローカル ストレージ リソースを指定します。 ロールでは、0 個以上のローカル ストレージ リソースを定義することができます。

> [!NOTE]
>  `LocalStorage` 要素は、以前のバージョンの Azure SDK との互換性をサポートするために、`WorkerRole` 要素の子として表示される場合があります。

以下の表に、`LocalStorage` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|name|string|必須。 ローカル ストアの一意名です。|
|cleanOnRoleRecycle|boolean|任意。 ロールの再起動時にローカル ストアをクリーンアップするかどうかを指定します。 既定値は `true` です。|
|sizeInMb|INT|省略可能。 ローカル ストアに割り当てるストレージ領域の容量 (MB 単位) です。 指定しない場合、既定の 100 MB のストレージ領域が割り当てられます。 割り当て可能なストレージ領域の最小量は 1 MB です。<br /><br /> ローカル リソースの最大サイズは、仮想マシンのサイズによって決まります。 詳しくは、[Microsoft Azure Cloud Services の仮想マシンのサイズ](cloud-services-sizes-specs.md)に関するページをご覧ください。|

ローカル ストレージ リソースに割り当てられるディレクトリの名前は、name 属性で指定した値になります。

##  <a name="endpoints"></a><a name="Endpoints"></a> Endpoints
`Endpoints` 要素では、ロールの入力 (外部) エンドポイント、内部エンドポイント、インスタンス入力エンドポイントのコレクションを指定します。 この要素は、`InputEndpoint`、`InternalEndpoint`、`InstanceInputEndpoint` の各要素の親です。

入力エンドポイントと内部エンドポイントは別々に割り当てられます。 サービスには入力エンドポイント、内部エンドポイント、インスタンス入力エンドポイントを合計で 25 個設定でき、それらのエンドポイントをサービス内で許可されている 25 個のロールに割り当てることができます。 たとえばロールが 5 個ある場合、入力エンドポイントを 1 ロールにつき 5 個、または 1 つのロールに 25 個割り当てることができます。また、入力エンドポイントを 25 個のロールに 1 つずつ割り当てることもできます。

> [!NOTE]
>  デプロイするロールには、それぞれ 1 つあたり 1 つのインスタンスが必要になります。 既定では、1 つのサブスクリプションでプロビジョニングできるコア数は 20 個に制限されているため、1 つのロールにプロビジョニングできるインスタンスの上限は 20 個になります。 既定のプロビジョニングで提供されるよりも多くのインスタンスが必要な場合、[請求、サブスクリプション管理、クォータのサポート](https://azure.microsoft.com/support/options/)に関するページで、クォータの増加方法の詳細をご覧ください。

##  <a name="inputendpoint"></a><a name="InputEndpoint"></a> InputEndpoint
`InputEndpoint` 要素では、worker ロールの外部エンドポイントを指定します。

HTTP、HTTPS、UDP、TCP の各エンドポイントを組み合わせて複数のエンドポイントを定義できます。 入力エンドポイントに対しては任意のポート番号を指定できますが、サービス内の各ロールに対して指定するポート番号は一意である必要があります。 たとえば、1 つのロールで HTTP 用にポート 80、HTTPS 用にポート 443 を使用するように指定した場合、2 つ目のロールでは HTTP 用にポート 8080、HTTPS 用にポート 8043 を使用するように指定します。

以下の表に、`InputEndpoint` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|name|string|必須。 外部エンドポイントの一意名です。|
|protocol|string|必須。 外部エンドポイントのトランスポート プロトコルです。 worker ロールで使用可能な値は、`HTTP`、`HTTPS`、`UDP`、および `TCP` です。|
|port|INT|必須。 外部エンドポイントのポートです。 任意のポート番号を指定できますが、サービス内の各ロールに対して指定するポート番号は一意である必要があります。<br /><br /> 使用できる値の範囲は 1 以上 65535 以下です (Azure SDK バージョン 1.7 以上)。|
|証明書 (certificate)|string|HTTPS エンドポイントの場合は必須。 `Certificate` 要素で定義される証明書の名前です。|
|localPort|INT|省略可能。 エンドポイントでの内部接続に使用するポートを指定します。 `localPort` 属性では、エンドポイントの外部ポートをロールの内部ポートにマッピングします。 これは、外部に公開しているポートとは異なるポート上にある内部コンポーネントとロールが通信する必要のあるシナリオで役立ちます。<br /><br /> 指定しない場合、`localPort` の値は `port` 属性と同じになります。 検出可能な未割り当てポートをランタイム API で自動的に割り当てるには、`localPort` の値を "*" に設定します。<br /><br /> 使用できる値の範囲は 1 以上 65535 以下です (Azure SDK バージョン 1.7 以上)。<br /><br /> `localPort` 属性は、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。|
|ignoreRoleInstanceStatus|boolean|任意。 この属性の値を `true` に設定すると、サービスの状態が無視され、エンドポイントはロード バランサーにより削除されなくなります。 サービスのビジー状態のインスタンスをデバッグする場合、この値を `true` に設定すると便利です。 既定値は `false` です。 **注:** ロールが準備完了状態ではなくなっても、エンドポイントは引き続きトラフィックを受信できます。|
|loadBalancerProbe|string|省略可能。 入力エンドポイントに関連付けるロード バランサー プローブの名前です。 詳しくは、[LoadBalancerProbe スキーマ](schema-csdef-loadbalancerprobe.md)に関するページをご覧ください。|

##  <a name="internalendpoint"></a><a name="InternalEndpoint"></a> InternalEndpoint
`InternalEndpoint` 要素では、worker ロールの内部エンドポイントを指定します。 内部エンドポイントを使用できるのはサービス内で実行されている他のロール インスタンスのみであり、サービス外部のクライアントでは使用できません。 worker ロールは、HTTP、UDP、または TCP 内部エンドポイントを、最大で 5 つまで持つことができます。

以下の表に、`InternalEndpoint` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|name|string|必須。 内部エンドポイントの一意名です。|
|protocol|string|必須。 内部エンドポイントのトランスポート プロトコルです。 指定できる値は、`HTTP`、`TCP`、`UDP`、および `ANY` です。<br /><br /> 値を `ANY` にすると、あらゆるプロトコルおよびポートが許可されます。|
|port|INT|省略可能。 エンドポイントでの内部負荷分散接続に使用するポートを指定します。 負荷分散エンドポイントではポートを 2 つ使用します。 パブリック IP アドレス用のポートと、プライベート IP アドレス用のポートです。 通常、これらは同じものを設定しますが、別々のポートを使用することもできます。<br /><br /> 使用できる値の範囲は 1 以上 65535 以下です (Azure SDK バージョン 1.7 以上)。<br /><br /> `Port` 属性は、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。|

##  <a name="instanceinputendpoint"></a><a name="InstanceInputEndpoint"></a> InstanceInputEndpoint
`InstanceInputEndpoint` 要素では、worker ロールのインスタンス入力エンドポイントを指定します。 インスタンス入力エンドポイントは、ロード バランサーでのポート フォワーディングにより特定のロール インスタンスに関連付けられます。 各インスタンス入力エンドポイントは、使用可能なポートの範囲で特定のポートにマッピングされます。 この要素は、`AllocatePublicPortFrom` 要素の親です。

`InstanceInputEndpoint` 要素は、Azure SDK バージョン 1.7 以上を使用している場合のみ利用可能です。

以下の表に、`InstanceInputEndpoint` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|name|string|必須。 エンドポイントの一意名です。|
|localPort|INT|必須。 ロード バランサーから転送された受信トラフィックを受信するために、すべてのロール インスタンスがリッスンする内部ポートを指定します。 指定できる値は 1 以上 65535 以下です。|
|protocol|string|必須。 内部エンドポイントのトランスポート プロトコルです。 指定できる値は `udp` または `tcp` です。 HTTP/HTTPS ベースのトラフィックの場合は `tcp` を使用します。|

##  <a name="allocatepublicportfrom"></a><a name="AllocatePublicPortFrom"></a> AllocatePublicPortFrom
`AllocatePublicPortFrom` 要素では、外部の顧客が各インスタンス入力エンドポイントへのアクセスに使用できるパブリック ポートの範囲を指定します。 パブリック (VIP) ポート番号の割り当ては指定した範囲で行われ、テナントのデプロイおよび更新時に個々のロール インスタンス エンドポイントに割り当てられます。 この要素は、`FixedPortRange` 要素の親です。

`AllocatePublicPortFrom` 要素は、Azure SDK バージョン 1.7 以上を使用している場合のみ利用可能です。

##  <a name="fixedport"></a><a name="FixedPort"></a> FixedPort
`FixedPort` 要素では、内部エンドポイントで負荷分散接続を有効にする、エンドポイントのポートを指定します。

`FixedPort` 要素は、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。

以下の表に、`FixedPort` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|port|INT|必須。 内部エンドポイントのポートです。 この属性の効果は、`FixedPortRange` の min と max を同じポートに設定した場合と同じです。<br /><br /> 使用できる値の範囲は 1 以上 65535 以下です (Azure SDK バージョン 1.7 以上)。|

##  <a name="fixedportrange"></a><a name="FixedPortRange"></a> FixedPortRange
`FixedPortRange` 要素では、内部エンドポイントまたはインスタンス入力エンドポイントに割り当てるポートの範囲を指定し、それらのエンドポイントの負荷分散接続に使用するポートを設定します。

> [!NOTE]
>  `FixedPortRange` 要素の動作は、この要素を含む要素によって異なります。 `FixedPortRange` 要素が `InternalEndpoint` 要素内に含まれる場合、ロールが実行される仮想マシンすべてに対して、ロード バランサー上にある min 属性から max 属性までの範囲のすべてのポートが開かれます。 `FixedPortRange` 要素が `InstanceInputEndpoint` 要素内に含まれる場合、ロールが実行される各仮想マシン上で、min 属性から max 属性までの範囲に含まれるポートが 1 つだけ開かれます。

`FixedPortRange` 要素は、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。

以下の表に、`FixedPortRange` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|分|INT|必須。 範囲の最小ポートです。 使用できる値の範囲は 1 以上 65535 以下です (Azure SDK バージョン 1.7 以上)。|
|最大|string|必須。 範囲の最大ポートです。 使用できる値の範囲は 1 以上 65535 以下です (Azure SDK バージョン 1.7 以上)。|

##  <a name="certificates"></a><a name="Certificates"></a> 証明書
`Certificates` 要素では、worker ロールの証明書のコレクションを指定します。 この要素は、`Certificate` 要素の親です。 ロールには、任意の数の証明書を関連付けることができます。 Certificates 要素の使用方法について詳しくは、[証明書を使用したサービス定義ファイルの変更](cloud-services-configure-ssl-certificate-portal.md#step-2-modify-the-service-definition-and-configuration-files)に関するページをご覧ください。

##  <a name="certificate"></a><a name="Certificate"></a> Certificate
`Certificate` 要素では、worker ロールに関連付ける証明書を指定します。

以下の表に、`Certificate` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|name|string|必須。 この証明書の名前です。証明書を HTTPS `InputEndpoint` 要素に関連付けた場合に、その証明書を参照するために使用します。|
|storeLocation|string|必須。 この証明書を見つけることのできるローカル マシン上の証明書ストアの場所です。 設定可能な値は `CurrentUser` および `LocalMachine` です。|
|storeName|string|必須。 この証明書が存在するローカル マシン上の証明書ストアの名前です。 使用できる値は、組み込みのストア名 `My`、`Root`、`CA`、`Trust`、`Disallowed`、`TrustedPeople`、`TrustedPublisher`、`AuthRoot`、`AddressBook`、および任意のカスタム ストア名です。 カスタム ストア名を指定した場合、そのストアが自動で作成されます。|
|permissionLevel|string|省略可能。 ロール プロセスに付与するアクセス許可を指定します。 管理者特権プロセスのみが秘密キーへアクセスできるようにするには、`elevated` 権限を指定します。 `limitedOrElevated` 権限では、すべてのロール プロセスが秘密キーにアクセスできます。 指定できる値は `limitedOrElevated` または `elevated` です。 既定値は `limitedOrElevated` です。|

##  <a name="imports"></a><a name="Imports"></a> Imports
`Imports` 要素では、ゲスト オペレーティング システムにコンポーネントを追加する worker ロールのインポート モジュールのコレクションを指定します。 この要素は、`Import` 要素の親です。 この要素は省略可能であり、ロールに設定できる Runtime ブロックは 1 つのみです。

`Imports` 要素は、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。

##  <a name="import"></a><a name="Import"></a> Import
`Import` 要素では、ゲスト オペレーティング システムに追加するモジュールを指定します。

`Import` 要素は、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。

以下の表に、`Import` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|moduleName|string|必須。 インポートするモジュールの名前です。 有効なインポート モジュールは次のとおりです。<br /><br /> -   RemoteAccess<br />-   RemoteForwarder<br />-   Diagnostics<br /><br /> RemoteAccess モジュールと RemoteForwarder モジュールを使用すると、ロール インスタンスをリモート デスクトップ接続用に構成できます。 詳しくは、[リモート デスクトップ接続の有効化](cloud-services-role-enable-remote-desktop-new-portal.md)に関するページをご覧ください。<br /><br /> Diagnostics モジュールを使用すると、ロール インスタンスの診断データを収集できます。|

##  <a name="runtime"></a><a name="Runtime"></a> Runtime
`Runtime` 要素では、Azure ホスト プロセスのランタイム環境を制御する、worker ロールの環境変数設定のコレクションを指定します。 この要素は、`Environment` 要素の親です。 この要素は省略可能であり、ロールに設定できる Runtime ブロックは 1 つのみです。

`Runtime` 要素は、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。

以下の表に、`Runtime` 要素の属性を示します。

| 属性 | Type | [説明] |
| --------- | ---- | ----------- |
|executionContext|string|省略可能。 ロール プロセスを起動するコンテキストを指定します。 既定のコンテキストは `limited` です。<br /><br /> -   `limited` – プロセスは管理者権限を使用せずに起動されます。<br />-   `elevated` – プロセスは管理者権限を使用して起動されます。|

##  <a name="environment"></a><a name="Environment"></a> Environment
`Environment` 要素では、worker ロールの環境変数設定のコレクションを指定します。 この要素は、`Variable` 要素の親です。 ロールには、任意の数の環境変数セットを設定できます。

##  <a name="variable"></a><a name="Variable"></a> Variable
`Variable` 要素では、ゲスト オペレーティングに設定する環境変数を指定します。

`Variable` 要素は、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。

以下の表に、`Variable` 要素の属性を示します。

| 属性 | Type | 説明 |
| --------- | ---- | ----------- |
|name|string|必須。 設定する環境変数の名前です。|
|value|string|省略可能。 環境変数に対して設定する値です。 value 属性か `RoleInstanceValue` 要素のいずれかを指定する必要があります。|

##  <a name="roleinstancevalue"></a><a name="RoleInstanceValue"></a> RoleInstanceValue
`RoleInstanceValue` 要素では、変数の値の取得先となる xPath を指定します。

以下の表に、`RoleInstanceValue` 要素の属性を示します。

| 属性 | Type | [説明] |
| --------- | ---- | ----------- |
|xpath|string|省略可能。 インスタンスのデプロイ設定の保存先のパスです。 詳しくは、[XPath を使用した構成変数](cloud-services-role-config-xpath.md)に関するページをご覧ください。<br /><br /> value 属性か `RoleInstanceValue` 要素のいずれかを指定する必要があります。|

##  <a name="entrypoint"></a><a name="EntryPoint"></a> EntryPoint
`EntryPoint` 要素では、ロールのエントリ ポイントを指定します。 この要素は、`NetFxEntryPoint` 要素の親です。 これらの要素により、既定の WaWorkerHost.exe 以外のアプリケーションをロールのエントリ ポイントとして機能させるように指定できます。

`EntryPoint` 要素は、Azure SDK バージョン 1.5 以上を使用している場合のみ利用可能です。

##  <a name="netfxentrypoint"></a><a name="NetFxEntryPoint"></a> NetFxEntryPoint
`NetFxEntryPoint` 要素では、ロールで実行するプログラムを指定します。

> [!NOTE]
>  `NetFxEntryPoint` 要素は、Azure SDK バージョン 1.5 以上を使用している場合のみ利用可能です。

以下の表に、`NetFxEntryPoint` 要素の属性を示します。

| 属性 | Type | [説明] |
| --------- | ---- | ----------- |
|assemblyName|string|必須。 エントリ ポイントが含まれるアセンブリのパスとファイル名です。 パスは、フォルダー **\\%ROLEROOT%\Approot** に対して相対的に指定します (`commandLine` で **\\%ROLEROOT%\Approot** を指定しないでください。これは仮定のものです)。 **%ROLEROOT%** は Azure で保持される環境変数であり、ロールのルート フォルダーの場所を表します。 **\\%ROLEROOT%\Approot** フォルダーは、ロールのアプリケーション フォルダーとなります。|
|targetFrameworkVersion|string|必須。 アセンブリがビルドされた .NET Framework のバージョンです。 たとえば、「 `targetFrameworkVersion="v4.0"` 」のように入力します。|

##  <a name="programentrypoint"></a><a name="ProgramEntryPoint"></a> ProgramEntryPoint
`ProgramEntryPoint` 要素では、ロールで実行するプログラムを指定します。 `ProgramEntryPoint` 要素では、.NET アセンブリに基づかないプログラムのエントリ ポイントを指定できます。

> [!NOTE]
>  `ProgramEntryPoint` 要素は、Azure SDK バージョン 1.5 以上を使用している場合のみ利用可能です。

以下の表に、`ProgramEntryPoint` 要素の属性を示します。

| 属性 | Type | [説明] |
| --------- | ---- | ----------- |
|commandLine|string|必須。 実行するプログラムのパス、ファイル名、およびコマンド ライン引数です。 パスは **\%ROLEROOT%\Approot** フォルダーに対して相対的です。commandLine では **%ROLEROOT%\Approot** までが既定値であるため、この部分は指定しないでください。 **%ROLEROOT%** は Azure で保持される環境変数であり、ロールのルート フォルダーの場所を表します。 **%ROLEROOT%\Approot** フォルダーは、ロールのアプリケーション フォルダーを表します。<br /><br /> プログラムが終了するとロールはリサイクルされるので、通常、起動して有限のタスクを実行するだけのプログラムではなく、実行を継続するようにプログラムを設定します。|
|setReadyOnProcessStart|boolean|必須。 コマンド ライン プログラムの開始通知をロール インスタンスが待機するかどうかを指定します。 現在、この値は `true` に設定する必要があります。 値を `false` に設定することは、将来の使用に予約されています。|

##  <a name="startup"></a><a name="Startup"></a> Startup
`Startup` 要素では、ロールの起動時に実行されるタスクのコレクションを指定します。 この要素は、`Variable` 要素の親にすることができます。 ロール スタートアップ タスクの使用について詳しくは、[スタートアップ タスクの構成方法](cloud-services-startup-tasks.md)に関するページをご覧ください。 この要素は省略可能であり、ロールに設定できる Startup ブロックは 1 つのみです。

以下の表に、`Startup` 要素の属性を示します。

| 属性 | Type | [説明] |
| --------- | ---- | ----------- |
|priority|INT|内部使用専用です。|

##  <a name="task"></a><a name="Task"></a> Task
`Task` 要素では、ロールの起動時に実行されるスタートアップ タスクを指定します。 スタートアップ タスクを使用することで、ソフトウェア コンポーネントのインストールや他のアプリケーションの実行など、ロールの実行準備を整えるタスクを実行できます。 タスクは、`Startup` 要素ブロック内に記載された順番で実行されます。

`Task` 要素は、Azure SDK バージョン 1.3 以上を使用している場合のみ利用可能です。

以下の表に、`Task` 要素の属性を示します。

| 属性 | Type | [説明] |
| --------- | ---- | ----------- |
|commandLine|string|必須。 CMD ファイルなど、実行するコマンドが含まれるスクリプトです。 スタートアップ コマンドとバッチ ファイルは ANSI 形式で保存する必要があります。 ファイルの先頭にバイト順マーカーが設定されているファイル形式は、正常に処理されません。|
|executionContext|string|スクリプトが実行されるコンテキストを指定します。<br /><br /> -   `limited` (既定) – プロセスをホストするロールと同じ権限で実行します。<br />-   `elevated` – 管理者特権で実行します。|
|taskType|string|コマンドの実行動作を指定します。<br /><br /> -   `simple` (既定) – システムは、他のタスクが起動されるまでこのタスクの終了を待機します。<br />-   `background` – システムはタスクの終了を待機しません。<br />-   `foreground` – すべてのフォアグラウンド タスクが終了するまでロールが再起動されない点を除き、background と同様です。|

##  <a name="contents"></a><a name="Contents"></a> 目次
`Contents` 要素では、worker ロールのコンテンツのコレクションを指定します。 この要素は、`Content` 要素の親です。

`Contents` 要素は、Azure SDK バージョン 1.5 以上を使用している場合のみ利用可能です。

##  <a name="content"></a><a name="Content"></a> コンテンツ
`Content` 要素では、Azure 仮想マシンにコピーするコンテンツのソースの場所、およびそのコンテンツのコピー先のパスを定義します。

`Content` 要素は、Azure SDK バージョン 1.5 以上を使用している場合のみ利用可能です。

以下の表に、`Content` 要素の属性を示します。

| 属性 | Type | [説明] |
| --------- | ---- | ----------- |
|destination|string|必須。 コンテンツを配置する Azure 仮想マシン上の場所です。 この場所は、フォルダー **%ROLEROOT%\Approot** に対して相対的です。|

この要素は、`SourceDirectory` 要素の親要素です。

##  <a name="sourcedirectory"></a><a name="SourceDirectory"></a> SourceDirectory
`SourceDirectory` 要素では、コンテンツのコピー元となるローカル ディレクトリを定義します。 この要素を使用して、Azure 仮想マシンにコピーするローカルのコンテンツを指定します。

`SourceDirectory` 要素は、Azure SDK バージョン 1.5 以上を使用している場合のみ利用可能です。

以下の表に、`SourceDirectory` 要素の属性を示します。

| 属性 | Type | [説明] |
| --------- | ---- | ----------- |
|path|string|必須。 Azure 仮想マシンにコピーするコンテンツが含まれるローカル ディレクトリの相対パスまたは絶対パスです。 ディレクトリ パスでは環境変数の展開がサポートされます。|

## <a name="see-also"></a>参照
[Cloud Service (クラシック) 定義スキーマ](schema-csdef-file.md)



