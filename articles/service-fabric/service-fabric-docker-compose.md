---
title: Azure Service Fabric Docker Compose デプロイメント プレビュー
description: Azure Service Fabric では、Service Fabric を使用して既存のコンテナーの調整を容易にするため、Docker Compose 形式を受け入れます。 このサポートは現在プレビューの段階です。
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: f84dd0ecb7a4002182c8455bfd86354d794a6f7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84691290"
---
# <a name="docker-compose-deployment-support-in-azure-service-fabric-preview"></a>Azure Service Fabric での Docker Compose のデプロイメントのサポート (プレビュー)

Docker は、複数コンテナー アプリケーションの定義に [docker-compose.yml](https://docs.docker.com/compose) ファイルを使用します。 Docker を使用した Azure Service Fabric での既存のコンテナー アプリケーションの調整を理解しやすくするため、プラットフォームでネイティブに Docker Compose をデプロイするためのプレビュー版サポートが含められています。 Service Fabric は `docker-compose.yml` ファイルのバージョン 3 以降を受け入れることができます。 

このサポートはプレビュー段階であるため、Compose ディレクティブのサブセットのみがサポートされます。

このプレビューを使用するには、Azure Portal でバージョン 5.7 以降の Service Fabric ランタイムを使用し、対応する SDK も使用してクラスターを作成します。 

> [!NOTE]
> この機能はプレビュー段階であり、実稼働環境ではサポートされません。
> 次の例では、ランタイム バージョン 6.0 および SDK version 2.8 が使用されています。

## <a name="deploy-a-docker-compose-file-on-service-fabric"></a>Service Fabric に Docker Compose ファイルをデプロイする

以下のコマンドでは、他の Service Fabric アプリケーションのように監視および管理できる (`fabric:/TestContainerApp` という名前の) Service Fabric アプリケーションが作成されます。 正常性クエリに指定したアプリケーション名を使用することができます。
Service Fabric は、"DeploymentName"を Compose デプロイメントの識別子として認識します。

### <a name="use-powershell"></a>PowerShell の使用

PowerShell で次のコマンドを実行して、docker-compose.yml ファイルから Service Fabric Compose のデプロイを作成します。

```powershell
New-ServiceFabricComposeDeployment -DeploymentName TestContainerApp -Compose docker-compose.yml [-RegistryUserName <>] [-RegistryPassword <>] [-PasswordEncrypted]
```

`RegistryUserName` および `RegistryPassword` は、コンテナー レジストリのユーザー名とパスワードです。 デプロイが完成したら、次のコマンドを使用して、その状態を確認できます。

```powershell
Get-ServiceFabricComposeDeploymentStatus -DeploymentName TestContainerApp
```

PowerShell で Compose のデプロイを削除するには、次のコマンドを使用します。

```powershell
Remove-ServiceFabricComposeDeployment  -DeploymentName TestContainerApp
```

PowerShell を使用して Compose デプロイメントをアップグレードするには、次のコマンドを使用します。

```powershell
Start-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp -Compose docker-compose-v2.yml -Monitored -FailureAction Rollback
```

PowerShell を使用して Compose デプロイのアップグレードをロールバックするには、次のコマンドを使用します。

```powershell
Start-ServiceFabricComposeDeploymentRollback -DeploymentName TestContainerApp
```

アップグレードを承諾すると、次のコマンドを使用して、アップグレードの進行状況を追跡できます。

```powershell
Get-ServiceFabricComposeDeploymentUpgrade -DeploymentName TestContainerApp
```

### <a name="use-azure-service-fabric-cli-sfctl"></a>Azure Service Fabric CLI (sfctl) の使用

次の Service Fabric CLI コマンドを使用することもできます。

```shell
sfctl compose create --deployment-name TestContainerApp --file-path docker-compose.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [ --timeout ]
```

デプロイメントが作成されたら、次のコマンドを使用して、その状態を確認できます。

```shell
sfctl compose status --deployment-name TestContainerApp [ --timeout ]
```

Compose デプロイを削除するには、次のコマンドを使用します。

```shell
sfctl compose remove  --deployment-name TestContainerApp [ --timeout ]
```

Compose デプロイメントをアップグレードするには、次のコマンドを使用します。

```shell
sfctl compose upgrade --deployment-name TestContainerApp --file-path docker-compose-v2.yml [ [ --user --encrypted-pass ] | [ --user --has-pass ] ] [--upgrade-mode Monitored] [--failure-action Rollback] [ --timeout ]
```

Compose デプロイのアップグレードをロールバックするには、次のコマンドを使用します。

```shell
sfctl compose upgrade-rollback --deployment-name TestContainerApp [ --timeout ]
```

アップグレードを承諾すると、次のコマンドを使用して、アップグレードの進行状況を追跡できます。

```shell
sfctl compose upgrade-status --deployment-name TestContainerApp
```

## <a name="supported-compose-directives"></a>サポートされる Compose ディレクティブ

このプレビューでは、以下のプリミティブを含む、Compose バージョン 3 形式からの構成オプションのサブセットがサポートされます。

* サービス > デプロイ > レプリカ
* サービス > デプロイ > 位置 > 制約
* サービス > デプロイ > リソース > 制限
    * -cpu-shares
    * -memory
    * -memory-swap
* サービス > コマンド
* サービス > 環境
* サービス > ポート
* サービス > イメージ
* サービス > 分離 (Windows の場合のみ)
* サービス > ログ > ドライバー
* サービス > ログ > ドライバー > オプション
* ボリューム & デプロイ > ボリューム

[Service Fabric のリソース ガバナンス](service-fabric-resource-governance.md)の説明に従って、リソースの制限を適用するようにクラスターをセットアップします。 他のすべての Docker Compose ディレクティブは、このプレビューではサポートされていません。

### <a name="ports-section"></a>ポート セクション

Service Fabric サービス リスナーによって使用されるポート セクションでは、http または https プロトコルを指定します。 これにより、エンドポイント プロトコルがネーム サービスによって正常に公開され、リバース プロキシが要求の転送を許可されるようになります。
* セキュリティで保護されていない Service Fabric Compose サービスをルーティングするには、**/http** を指定します。 例 - **"80:80/http"**。
* セキュリティで保護された Service Fabric Compose サービスをルーティングするには、**/https** を指定します。 例 - **"443:443/https"**。

> [!NOTE]
> /http および /https ポート セクションの構文は、適切な Service Fabric リスナーの URL を登録する Service Fabric に固有です。  Docker Compose ファイルの構文がプログラムによって検証される場合、検証エラーが発生する可能性があります。

## <a name="servicednsname-computation"></a>ServiceDnsName 計算

Compose ファイルで指定したサービス名が完全修飾ドメイン名である (つまり、ドット "." が含まれている) 場合、Service Fabric によって登録される DNS 名は `<ServiceName>` (ドットを含む) となります。 そうでない場合は、アプリケーション名の各パス セグメントが、最上位レベルのドメイン ラベルになる最初のパス セグメントを持つ、サービス DNS 名でのドメイン ラベルになります。

たとえば、指定されたアプリケーション名が `fabric:/SampleApp/MyComposeApp` の場合、登録される DNS 名は `<ServiceName>.MyComposeApp.SampleApp` になります。

## <a name="compose-deployment-instance-definition-versus-service-fabric-app-model-type-definition"></a>Service Fabric アプリケーション モデル (型定義) と Compose デプロイメント (インスタンス定義) の違い

docker-compose.yml ファイルでは、プロパティと構成を含む、コンテナーのデプロイ可能なセットが記述されています。
たとえば、このファイルには環境変数やポートを含めることができます。 位置の制約、リソースの制限、および DNS 名などのデプロイ パラメーターも、docker-compose.yml ファイルで指定できます。

[Service Fabric アプリケーション モデル](service-fabric-application-model.md)は、サービスの種類とアプリケーションの種類を使います。ここでは、同じ種類の多くのアプリケーション インスタンスを持つことができます。 たとえば、顧客ごとに 1 つのアプリケーション インスタンスを使うことができます。 この種類ベースのモデルは、ランタイムに登録されている同じアプリケーションの種類の複数のバージョンをサポートします。

たとえば、顧客 A は AppTypeA の種類 1.0 でインスタンス化されたアプリケーションを持つことができ、顧客 B は同じ種類とバージョンでインスタンス化された別のアプリケーションを持つことができます。 アプリケーション マニフェストでアプリケーションの種類を定義し、アプリケーションの作成時にアプリケーション名とデプロイのパラメーターを指定します。

このモデルは柔軟性がありますが、種類がマニフェスト ファイルから暗黙的に指定されるより単純なインスタンス ベースのデプロイ モデルもサポートする予定です。 このモデルでは、各アプリケーションが独自の独立したマニフェストを取得します。 インスタンス ベースのデプロイ形式である docker-compose.yml のサポートを追加することで、この取り組みをプレビューしています。

## <a name="next-steps"></a>次のステップ

* [Service Fabric アプリケーション モデル](service-fabric-application-model.md)の学習
* [Service Fabric CLI の概要](service-fabric-cli.md)
