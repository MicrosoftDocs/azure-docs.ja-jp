---
title: Microsoft Azure の Cloud Foundry に最初のアプリをデプロイする
description: Azure の Cloud Foundry にアプリケーションをデプロイします。
author: seanmck
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 06/14/2017
ms.author: seanmck
ms.openlocfilehash: 037ac972dca49484f7b8c0ad8eed6942c901b997
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562930"
---
# <a name="deploy-your-first-app-to-cloud-foundry-on-microsoft-azure"></a>Microsoft Azure の Cloud Foundry に最初のアプリをデプロイする

[Cloud Foundry](https://cloudfoundry.org) は、Microsoft Azure で使用できる一般的なオープン ソース プラットフォームです。 この記事では、Azure 環境にある Cloud Foundry にアプリケーションをデプロイして管理する方法について説明します。

## <a name="create-a-cloud-foundry-environment"></a>Cloud Foundry 環境を作成する

Azure に Cloud Foundry 環境を作成する方法はいくつかあります。

- Azure Marketplace の [Pivotal Cloud Foundry オファー][pcf-azuremarketplace]を使用して、PCF Operations Manager と Azure Service Broker が含まれる標準環境を作成する。 マーケットプレースのオファーをデプロイする[全手順][pcf-azuremarketplace-pivotaldocs]は、Pivotal のドキュメントで確認できます。
- [Pivotal Cloud Foundry を手動でデプロイする][pcf-custom]ことでカスタマイズされた環境を作成する。
- Cloud Foundry 環境のデプロイを調整する VM である [BOSH](https://bosh.io) ディレクターをセットアップすることで、[オープン ソースの Cloud Foundry パッケージを直接デプロイする][oss-cf-bosh]。

> [!IMPORTANT] 
> PCF を Azure Marketplace からデプロイする場合は、Pivotal Apps Manager にアクセスするために必要な SYSTEMDOMAINURL と管理者の資格情報をメモに取ります。両方ともマーケットプレースのデプロイ ガイドで説明されています。 それらはこのチュートリアルを完了するために必要です。 マーケットプレースのデプロイでは、SYSTEMDOMAINURL は `https://system.*ip-address*.cf.pcfazure.com` の形式をしています。

## <a name="connect-to-the-cloud-controller"></a>Cloud Controller に接続する

Cloud Controller はアプリケーションをデプロイおよび管理するための Cloud Foundry 環境のプライマリ エントリ ポイントです。 Cloud Controller のコア API (CCAPI) は REST API ですが、さまざまなツールを使用してアクセスできます。 ここでは、[Cloud Foundry CLI][cf-cli] を使用してやり取りします。 この CLI は Linux、macOS、または Windows にインストールできますが、インストールしなくても [Azure Cloud Shell][cloudshell-docs] にプリインストールされています。

ログインするには、マーケットプレースのデプロイから取得した SYSTEMDOMAINURL に `api` を付加します。 既定のデプロイでは自己署名証明書が使用されるため、それも `skip-ssl-validation` スイッチに含めてください。

```bash
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Cloud Controller にログインするよう求められます。 マーケットプレースのデプロイ手順から取得した管理者アカウントの資格情報を使用します。

Cloud Foundry には名前空間として "*org*" や "*space*" が用意されており、共有デプロイ内でチームや環境を分離します。 PCF のマーケットプレースのデプロイには、オートスケーリング サービスや Azure Service Broker などのベース コンポーネントが含まれるように作成された、既定の "*system*" org や一連の space が含まれています。 ここでは、"*system*" space を選択します。


## <a name="create-an-org-and-space"></a>org と space を作成する

「`cf apps`」と入力すると、system org 内の system space にデプロイされている一連のシステム アプリケーションが表示されます。 

"*system*" org はシステム アプリケーション用に予約することをお勧めします。このため、サンプル アプリケーションを配置する org と space を作成します。

```bash
cf create-org myorg
cf create-space dev -o myorg
```

次のターゲット コマンドを使用して、新しい org と space に切り替えます。

```bash
cf target -o testorg -s dev
```

これにより、アプリケーションをデプロイすると、新しい org と space に作成されます。 現在、新しい org や space にアプリがないことを確認するには、もう一度「`cf apps`」と入力します。

> [!NOTE] 
> org や space の詳細、およびそれらを Cloud Foundry のロールベースのアクセス制御 (Cloud Foundry RBAC) で使用する方法の詳細については、[Cloud Foundry のドキュメント][cf-orgs-spaces-docs]を参照してください。

## <a name="deploy-an-application"></a>アプリケーションをデプロイする

Hello Spring Cloud という名前の Cloud Foundry のサンプル アプリケーションを使用してみましょう。このアプリケーションは Java で記述されており、[Spring Framework](https://spring.io) と [Spring Boot](https://projects.spring.io/spring-boot/) をベースとしています。

### <a name="clone-the-hello-spring-cloud-repository"></a>Hello Spring Cloud リポジトリを複製する

Hello Spring Cloud サンプル アプリケーションは GitHub で入手できます。 お客様の環境に複製し、新しいディレクトリに変更します。

```bash
git clone https://github.com/cloudfoundry-samples/hello-spring-cloud
cd hello-spring-cloud
```

### <a name="build-the-application"></a>アプリケーションのビルド

[Apache Maven](https://maven.apache.org) を使用してアプリをビルドします。

```bash
mvn clean package
```

### <a name="deploy-the-application-with-cf-push"></a>cf push を使用してアプリケーションをデプロイする

ほとんどのアプリケーションは、`push` コマンドを使用して Cloud Foundry にデプロイできます。

```bash
cf push
```

アプリケーションを "*プッシュ*" すると、Cloud Foundry がアプリケーションの種類 (このケースでは Java アプリ) を検出し、その依存関係 (このケースでは Spring フレームワーク) を特定します。 その後、コードを実行するために必要なすべてが、*droplet* と呼ばれるスタンドアロンのコンテナー イメージにパッケージされます。 最後に、Cloud Foundry は環境内で使用できるマシンの 1 つにアプリケーションをスケジュールし、そのマシンへの URL を作成します。これはコマンドの出力から入手できます。

![cf push コマンドの出力][cf-push-output]

hello-spring-cloud アプリケーションを確認するには、作成された URL をお使いのブラウザーで開きます。

![Hello Spring Cloud の既定の UI][hello-spring-cloud-basic]

> [!NOTE] 
> `cf push` 中に何が起こっているかについて詳しくは、Cloud Foundry のドキュメントの「[How Applications Are Staged][cf-push-docs]」 (アプリケーションのステージング方法) をご覧ください。

## <a name="view-application-logs"></a>アプリケーションのログを確認する

Cloud Foundry CLI を使用して、名前でアプリケーションのログを確認できます。

```bash
cf logs hello-spring-cloud
```

既定では、ログ コマンドは "*tail*" を使用します。これにより、新しいログが書き込まれると表示されます。 新しいログが表示されているかを確認するには、ブラウザーで hello-spring-cloud アプリを最新の情報に更新します。

既に書き込まれているログを表示するには、`recent` スイッチを追加します。

```bash
cf logs --recent hello-spring-cloud
```

## <a name="scale-the-application"></a>アプリケーションのスケールを変更する

既定では、`cf push` はアプリケーションの単一のインスタンスのみを作成します。 高可用性を確保し、スケールアウトを有効にしてより高いスループットを実現するには、一般的にアプリケーションの 1 つ以上のインスタンスを実行します。 `scale` コマンドを使用して、既にデプロイされているアプリケーションを簡単にスケールアウトできます。

```bash
cf scale -i 2 hello-spring-cloud
```

アプリケーションで `cf app` コマンドを実行することは、Cloud Foundry がアプリケーションの別のインスタンスを作成していることを示します。 アプリケーションが起動すると、Cloud Foundry が自動的にトラフィックの負荷分散を開始します。


## <a name="next-steps"></a>次のステップ

- [Cloud Foundry のドキュメントを読む][cloudfoundry-docs]
- [Cloud Foundry 用の Azure DevOps Services プラグインをセットアップする][vsts-plugin]
- [Cloud Foundry 用の Microsoft Log Analytics Nozzle を構成する][loganalytics-nozzle]

<!-- LINKS -->

[pcf-azuremarketplace]: https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry
[pcf-custom]: https://docs.pivotal.io/pivotalcf/1-10/customizing/azure.html
[oss-cf-bosh]: https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs
[pcf-azuremarketplace-pivotaldocs]: https://docs.pivotal.io/pivotalcf/customizing/pcf_azure.html
[cf-cli]: https://github.com/cloudfoundry/cli
[cloudshell-docs]: ../cloud-shell/overview.md
[cf-orgs-spaces-docs]: https://docs.cloudfoundry.org/concepts/roles.html
[spring-boot]: https://projects.spring.io/spring-boot/
[spring-framework]: https://spring.io
[cf-push-docs]: https://docs.cloudfoundry.org/concepts/how-applications-are-staged.html
[cloudfoundry-docs]: https://docs.cloudfoundry.org
[vsts-plugin]: https://github.com/Microsoft/vsts-cloudfoundry
[loganalytics-nozzle]: https://github.com/Azure/oms-log-analytics-firehose-nozzle

<!-- IMAGES -->
[cf-push-output]: ./media/cloudfoundry-deploy-your-first-app/cf-push-output.png
[hello-spring-cloud-basic]: ./media/cloudfoundry-deploy-your-first-app/hello-spring-cloud-basic.png