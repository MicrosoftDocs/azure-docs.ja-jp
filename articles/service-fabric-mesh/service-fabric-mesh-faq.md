---
title: Azure Service Fabric Mesh に関する一般的な質問
description: Azure Service Fabric Mesh に関してよく寄せられる質問と答えについて説明します。
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 8e53ab0ae4cc463bea8a6a8cb6d339f94fdcac6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626038"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Service Fabric Mesh に関してよく寄せられる質問

> [!IMPORTANT]
> Azure Service Fabric Mesh のプレビューは廃止されました。 Service Fabric Mesh API を介した新しいデプロイは許可されなくなります。 既存のデプロイのサポートは、2021 年 4 月 28 日まで継続されます。
> 
> 詳細については、[Azure Service Fabric Mesh のプレビューの廃止](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)に関する記事を参照してください。

Azure Service Fabric Mesh は、仮想マシン、ストレージ、ネットワークを管理することなく開発者がマイクロサービス アプリケーションをデプロイできるフル マネージド サービスです。 この記事ではよく寄せられる質問に回答します。

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>問題を報告したり、質問したりするにはどうすればよいですか。

[service-fabric-mesh-preview GitHub リポジトリ](https://aka.ms/sfmeshissues)で質問したり、Microsoft エンジニアから回答を得たり、問題を報告したりできます。

## <a name="quota-and-cost"></a>クォータとコスト

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>プレビューに参加すると、どのくらいの費用がかかりますか?

現時点では、Mesh プレビューにアプリケーションやコンテナーをデプロイしても料金は発生しません。 請求の有効化については 5 月の更新プログラムをご確認ください。 しかし、頻繁にテストするのでなければ、デプロイしたリソースを削除し、実行状態のまま残さないことをお勧めします。

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>コア数と RAM にはクォータ制限がありますか?

はい。 サブスクリプション別のクォータは次のようになっています:

- アプリケーションの数:5
- アプリケーションあたりのコア数:12
- アプリケーションあたりの合計 RAM:48 GB
- ネットワークとイングレス エンドポイント:5
- アタッチできる Azure ボリューム:10
- サービス レプリカの数:3
- デプロイ可能なコンテナーは最大 4 コアと 16 GB RAM に制限されています。
- コンテナーにコアを少しずつ割り当てることができます。増分は 0.5 コアであり、最大 6 コアまで割り当て可能です。

### <a name="how-long-can-i-leave-my-application-deployed"></a>アプリケーションをデプロイしたままにできる期間はどれくらいですか?

現在、アプリケーションの有効期間は 2 日間に制限されています。 これは、プレビューに割り当てられる空きコアを最大限に利用するためです。 その結果、特定のデプロイを 48 時間だけ継続して実行することができます。その後は、シャットダウンされます。

そのようになった場合は、Azure CLI で `az mesh app show` コマンドを実行することで、システムがシャットダウンしたことを確認できます。 `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` が返されるかどうかをチェックします 

次に例を示します。 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

リソース グループを削除するには、`az group delete <nameOfResourceGroup>` コマンドを使用します。

## <a name="deployments"></a>デプロイメント

### <a name="what-container-images-are-supported"></a>どのコンテナー イメージがサポートされますか?

Windows Fall Creators Update (バージョン 1709) のコンピューターで開発を行っている場合は、Windows バージョン 1709 の Docker イメージのみを使用できます。

Windows 10 April 2018 Update (バージョン 1803) のコンピューターで開発を行っている場合は、Windows バージョン 1709 または Windows バージョン 1803 の Docker イメージを使用できます。

サービスをデプロイするために、次のコンテナー OS イメージを使用できます:
- Windows - windowsservercore と nanoserver
    - Windows Server 1709
    - Windows Server 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - 既知の制限事項はありません

> [!NOTE]
> Mesh の Visual Studio ツールでは、Windows Server 2019 および 1809 のコンテナーへのデプロイがまだサポートされていません。

### <a name="what-types-of-applications-can-i-deploy"></a>どのような種類のアプリケーションをデプロイできますか? 

アプリケーション リソースに対する制限に適合するコンテナーで稼働するものであればなんでもデプロイできます (クォータの詳細については上記を参照してください)。 ユーザーが不正なワークロードの実行またはシステムの不正利用 (マイニングなど) に Mesh を使用していることが検出された場合、マイクロソフトは当該ユーザーのデプロイを停止し、当該ユーザーがサービスを実行できないようにサブスクリプションをブロックする権限を有します。 特定のワークロードの実行についてご質問がある場合は、お問い合わせください。 

## <a name="developer-experience-issues"></a>開発者エクスペリエンスの問題

### <a name="dns-resolution-from-a-container-doesnt-work"></a>コンテナーからの DNS 解決が機能しない

コンテナーから Service Fabric DNS サービスへの送信 DNS クエリが、特定の状況で失敗することがあります。 これは調査中です。 対処方法:

- Windows Fall Creators Update (バージョン 1709) 以降をベース コンテナー イメージとして使用します。
- サービス名だけでは機能しない場合は、完全修飾名を試してください:ServiceName.ApplicationName。
- サービスの Docker ファイルで、`EXPOSE <port>` を追加します。port はサービスを公開しているポートです。 次に例を示します。

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>Service Fabric 開発クラスターの場合と Mesh の場合で DNS が同じように動作しない

ローカル開発クラスターと Azure Mesh では異なる方法でサービスを参照することが必要な場合があります。

ローカル開発クラスターでは `{serviceName}.{applicationName}` を使用します。 Azure Service Fabric Mesh では、`{servicename}` を使用します。 

Azure Mesh では現在のところ、アプリケーション全体の DNS 解決に対応していません。

Windows 10 で Service Fabric 開発クラスターを実行するときに発生するその他の DNS 問題については、[Windows コンテナーのデバッグ](../service-fabric/service-fabric-how-to-debug-windows-containers.md)および[既知の DNS の問題](../service-fabric/service-fabric-dnsservice.md#known-issues)に関するページを参照してください。

### <a name="networking"></a>ネットワーク

ローカル コンピューターでアプリを使用または実行中に ServiceFabric ネットワーク NAT が消失する場合があります。 これが発生したかどうかを診断するには、コマンド プロンプトから

`docker network ls` を実行し、`servicefabric_nat` が表示されているかどうか確認します。  されていない場合、次のコマンドを実行します: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

これにより、アプリが既にローカルでデプロイされていて異常な状態にある場合でも、問題が解決します。

### <a name="issues-running-multiple-apps"></a>複数のアプリの実行に関する問題

CPU の可用性と制限がすべてのアプリケーションにわたって固定されている状況が発生する可能性があります。 対処方法:
- 5 ノードのクラスターを作成します。
- デプロイされているアプリ全体で、サービスでの CPU 使用率を減らします。 たとえば、サービスの service.yaml ファイルで `cpu: 1.0` を `cpu: 0.5` に変更します

1 ノードのクラスターには複数のアプリケーションをデプロイできません。 対処方法:
- 複数のアプリをローカル クラスターにデプロイするときは、5 ノードのクラスターを使用します。
- 現在テストしていないアプリを削除します。

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>Windows コンテナーに対する VS ツールのサポートの制限

Visual Studio のツールでは、現在、ベースの OS バージョン Windows Server 1709 および 1803 での Windows コンテナーのデプロイのみがサポートされます。 

## <a name="feature-gaps-and-other-known-issues"></a>機能のギャップとその他の既知の問題

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>アプリケーションをデプロイしたところ、それに関連付けられているネットワーク リソースに IP アドレスがない

IP アドレスがすぐに利用可能にならない既知の問題があります。 数分後にネットワーク リソースのステータスを見て、関連付けられている IP アドレスが表示されるか確認してください。

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>アプリケーションが正しいネットワーク/ボリューム リソースにアクセスできない

アプリケーション モデル内では、関連付けられているリソースにネットワークやリソースがアクセスできるように、完全リソース ID を使用します。 クイック スタート サンプルの例を次に示します:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>スケールアウトすると、実行中のものを含めたすべてのコンテナーが影響を受ける

これはバグであり、修正プログラムが実装されています。

## <a name="next-steps"></a>次のステップ

Service Fabric Mesh の詳細については、[概要](service-fabric-mesh-overview.md)ページをお読みください。
