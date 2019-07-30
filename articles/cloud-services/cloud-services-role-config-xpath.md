---
title: Cloud Services ロール構成の XPath チート シート |Microsoft Docs
description: クラウド サービス ロール構成で設定を環境変数として公開するために使用できるさまざまな XPath 設定。
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: cd2bdc4fc4b2a135907851ca4d3034430618e0cd
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359008"
---
# <a name="expose-role-configuration-settings-as-an-environment-variable-with-xpath"></a>XPath で環境変数としてロール構成設定を公開する
クラウド サービス worker ロールまたは Web ロールのサービス定義ファイルで、ランタイム構成値を環境変数として公開できます。 次の XPath 値がサポートされています (これは API 値に対応します)。

これらの XPath 値は、 [Microsoft.WindowsAzure.ServiceRuntime](/previous-versions/azure/reference/ee773173(v=azure.100)) ライブラリ経由でも使用できます。 

## <a name="app-running-in-emulator"></a>エミュレーターで実行中のアプリ
アプリがエミュレーターで実行されていることを示します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@emulated" |
| コード |var x = RoleEnvironment.IsEmulated; |

## <a name="deployment-id"></a>デプロイ ID
インスタンスのデプロイ ID を取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/Deployment/@id" |
| コード |var deploymentId = RoleEnvironment.DeploymentId; |

## <a name="role-id"></a>ロール ID
インスタンスの現在のロール ID を取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@id" |
| コード |var id = RoleEnvironment.CurrentRoleInstance.Id; |

## <a name="update-domain"></a>ドメインの更新
インスタンスの更新ドメインを取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@updateDomain" |
| コード |var ud = RoleEnvironment.CurrentRoleInstance.UpdateDomain; |

## <a name="fault-domain"></a>障害ドメイン
インスタンスの障害ドメインを取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@faultDomain" |
| コード |var fd = RoleEnvironment.CurrentRoleInstance.FaultDomain; |

## <a name="role-name"></a>ロール名
インスタンスのロール名を取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/@roleName" |
| コード |var rname = RoleEnvironment.CurrentRoleInstance.Role.Name; |

## <a name="config-setting"></a>構成設定
指定した構成設定の値を取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/ConfigurationSettings/ConfigurationSetting[@name='Setting1']/@value" |
| コード |var setting = RoleEnvironment.GetConfigurationSettingValue("Setting1"); |

## <a name="local-storage-path"></a>ローカル ストレージ パス
インスタンスのローカル ストレージ パスを取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@path" |
| コード |var localResourcePath = RoleEnvironment.GetLocalResource("LocalStore1").RootPath; |

## <a name="local-storage-size"></a>ローカル ストレージ サイズ
インスタンスのローカル ストレージのサイズを取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='LocalStore1']/@sizeInMB" |
| コード |var localResourceSizeInMB = RoleEnvironment.GetLocalResource("LocalStore1").MaximumSizeInMegabytes; |

## <a name="endpoint-protocol"></a>エンドポイント プロトコル
インスタンスのエンドポイント プロトコルを取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@protocol" |
| コード |var prot = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].Protocol; |

## <a name="endpoint-ip"></a>エンドポイント IP
指定したエンドポイントの IP アドレスを取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@address" |
| コード |var address = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Address |

## <a name="endpoint-port"></a>エンドポイント ポート
インスタンスのエンドポイント ポートを取得します。

| Type | 例 |
| --- | --- |
| XPath |xpath="/RoleEnvironment/CurrentInstance/Endpoints/Endpoint[@name='Endpoint1']/@port" |
| コード |var port = RoleEnvironment.CurrentRoleInstance.InstanceEndpoints["Endpoint1"].IPEndpoint.Port; |

## <a name="example"></a>例
[@emulated xpath 値](#app-running-in-emulator)に設定された `TestIsEmulated` という環境変数でスタートアップ タスクを作成する worker ロールの例を次に示します。 

```xml
<WorkerRole name="Role1">
    <ConfigurationSettings>
      <Setting name="Setting1" />
    </ConfigurationSettings>
    <LocalResources>
      <LocalStorage name="LocalStore1" sizeInMB="1024"/>
    </LocalResources>
    <Endpoints>
      <InternalEndpoint name="Endpoint1" protocol="tcp" />
    </Endpoints>
    <Startup>
      <Task commandLine="example.cmd inputParm">
        <Environment>
          <Variable name="TestConstant" value="Constant"/>
          <Variable name="TestEmptyValue" value=""/>
          <Variable name="TestIsEmulated">
            <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
          </Variable>
          ...
        </Environment>
      </Task>
    </Startup>
    <Runtime>
      <Environment>
        <Variable name="TestConstant" value="Constant"/>
        <Variable name="TestEmptyValue" value=""/>
        <Variable name="TestIsEmulated">
          <RoleInstanceValue xpath="/RoleEnvironment/Deployment/@emulated"/>
        </Variable>
        ...
      </Environment>
    </Runtime>
    ...
</WorkerRole>
```

## <a name="next-steps"></a>次の手順
[ServiceConfiguration.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg) ファイルの詳細を理解します。

[ServicePackage.cspkg](cloud-services-model-and-package.md#servicepackagecspkg) パッケージを作成します。

ロールの [リモート デスクトップ](cloud-services-role-enable-remote-desktop-new-portal.md) を有効にします。

