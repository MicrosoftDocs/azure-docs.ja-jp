---
title: 方法 - Azure Spring Cloud でアクセス許可を使用する
description: この記事では、Azure Spring Cloud でアクセス許可のカスタム ロールを作成する方法について説明します。
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0c0b3bd81e0f73a7879382e28516378bd722bc17
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877554"
---
# <a name="how-to-use-permissions-in-azure-spring-cloud"></a>Azure Spring Cloud でアクセス許可を使用する方法
この記事では、Azure Spring Cloud リソースに対するアクセス許可を委任するカスタム ロールを作成する方法を示します。 カスタム ロールは、さまざまな標準アクセス許可を使用して [Azure 組み込みロール](../role-based-access-control/built-in-roles.md)を拡張するものです。

ここでは以下のカスタム ロールを実装します。

* **開発者ロール**: 
    * デプロイ
    * テスト
    * アプリを再起動する
    * Git リポジトリのアプリ構成に変更を加え、適用できる
    * ログ ストリームを取得できる
* **Ops - サイト信頼性エンジニアリング**: 
    * アプリを再起動する
    * ログ ストリームを取得する
    * アプリや構成に変更を加えることはできない
* **Azure Pipelines/Jenkins/Github アクション ロール**:
    * 作成、読み取り、更新、削除の操作を実行できる
    * サービス インスタンス内にある Azure Spring Cloud とアプリで、すべてを作成し、構成できる:Terraform または ARM テンプレートを使用した Azure Pipelines、Jenkins、または GitHub Actions

## <a name="define-developer-role"></a>開発者ロールを定義する

開発者ロールには、アプリを再起動してログ ストリームを表示するアクセス許可が含まれますが、アプリや構成に変更を加えることはできません。

### <a name="navigate-subscription-and-resource-group-access-control-iam"></a>サブスクリプションとリソース グループのアクセス制御 (IAM) を進める

以下の手順に従ってロールの定義を開始します。

1. Azure portal で、カスタム ロールを割り当て可能にするサブスクリプションとリソース グループを開きます。
2. **[アクセス制御 (IAM)]** を開きます。
3. **[+ 追加]** をクリックします。
4. **[カスタム ロールの追加]** をクリックします。
5. **[次へ]** をクリックします。

   ![カスタム ロールの作成](media/spring-cloud-permissions/create-custom-role.png)

6. **[アクセス許可の追加]** をクリックします。

   ![[アクセス許可の追加] の開始](media/spring-cloud-permissions/add-permissions.png)

### <a name="search-for-azure-spring-cloud-permissions"></a>Azure Spring Cloud のアクセス許可を検索する:
7. 検索ボックスで、*Microsoft.app* を検索します。
*[Microsoft Azure Spring Cloud]* を選択します。

   ![[Azure Spring Cloud] を選択します](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. 開発者ロールのアクセス許可を選択します。

**[Microsoft.AppPlatform/Spring]** から、以下を選択します。
* 書き込み:Azure Spring Cloud サービス インスタンスを作成または更新する
* 読み取り:Azure Spring Cloud サービス インスタンスを取得する
* その他:Azure Spring Cloud サービス インスタンスのテスト キーを一覧表示する

**[Microsoft.AppPlatform/Spring/apps]** から、以下を選択します。
* 読み取り:Microsoft Azure Spring Cloud アプリケーションを読み取る
* その他:Microsoft Azure Spring Cloud アプリケーション リソースのアップロード URL を取得する

**[Microsoft.AppPlatform/Spring/apps/bindings]** から、以下を選択します。
* 読み取り:Microsoft Azure Spring Cloud アプリケーション バインドを読み取る

**[Microsoft.AppPlatform/Spring/apps/deployments]** から、以下を選択します。
* 書き込み:Microsoft Azure Spring Cloud アプリケーション デプロイを書き込む
* 読み取り:Microsoft Azure Spring Cloud アプリケーション デプロイを読み取る
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを起動する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを停止する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを再起動する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイのログ ファイル URL を取得する

**[Microsoft.AppPlatform/Spring/apps/domains]** から、以下を選択します。
* 読み取り:Microsoft Azure Spring Cloud アプリケーションのカスタム ドメインを読み取る

**[Microsoft.AppPlatform/Spring/certificates]** から、以下を選択します。
* 読み取り:Microsoft Azure Spring Cloud 証明書を読み取る

**[Microsoft.AppPlatform/locations/operationResults/Spring]** から、以下を選択します。
* 読み取り:操作結果を読み取る

**[Microsoft.AppPlatform/locations/operationStatus/operationId]** から、以下を選択します。
* 読み取り:操作状態を読み取る

    [ ![開発者のアクセス許可を作成する](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. **[追加]** をクリックします。

10. アクセス許可を確認します。

11. **[確認と作成]** をクリックします。

## <a name="define-devops-engineer-role"></a>DevOps エンジニア ロールを定義する
この手順では、Azure Spring Cloud アプリをデプロイ、テスト、再起動するためのアクセス許可を持つロールを定義します。

1. サブスクリプション、リソース グループと進む手順を繰り返して、[アクセス制御 (IAM)] にアクセスします。
2. DevOps エンジニア ロールのアクセス許可を選択します。

**[Microsoft.AppPlatform/Spring]** から、以下を選択します。
* 書き込み:Azure Spring Cloud サービス インスタンスを作成または更新する
* 削除:Azure Spring Cloud サービス インスタンスを削除する
* 読み取り:Azure Spring Cloud サービス インスタンスを取得する
* その他:Azure Spring Cloud サービス インスタンスのテスト エンドポイントを有効にする
* その他:Azure Spring Cloud サービス インスタンスのテスト エンドポイントを無効にする
* その他:Azure Spring Cloud サービス インスタンスのテスト キーを一覧表示する
* その他:Azure Spring Cloud サービス インスタンスのテスト キーを再生成する

**[Microsoft.AppPlatform/Spring/apps]** から、以下を選択します。
* 書き込み:Microsoft Azure Spring Cloud アプリケーションを書き込む
* 削除:Microsoft Azure Spring Cloud アプリケーションを削除する
* 読み取り:Microsoft Azure Spring Cloud アプリケーションを読み取る
* その他:Microsoft Azure Spring Cloud アプリケーション リソースのアップロード URL を取得する
* その他:Microsoft Azure Spring Cloud アプリケーションのカスタム ドメインを検証する

**[Microsoft.AppPlatform/Spring/apps/bindings]** から、以下を選択します。
* 書き込み:Microsoft Azure Spring Cloud アプリケーション バインドを書き込む
* 削除:Microsoft Azure Spring Cloud アプリケーション バインドを削除する
* 読み取り:Microsoft Azure Spring Cloud アプリケーション バインドを読み取る

**[Microsoft.AppPlatform/Spring/apps/deployments]** から、以下を選択します。
* 書き込み:Microsoft Azure Spring Cloud アプリケーション デプロイを書き込む
* 削除:Microsoft Azure Spring Cloud アプリケーション デプロイを削除する
* 読み取り:Microsoft Azure Spring Cloud アプリケーション デプロイを読み取る
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを起動する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを停止する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを再起動する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイのログ ファイル URL を取得する

**[Microsoft.AppPlatform/Spring/apps/deployments/skus]** から、以下を選択します。
* 読み取り:アプリケーション デプロイの利用可能な SKU を一覧表示する

**[Microsoft.AppPlatform/locations]** から、以下を選択します。
* その他:名前を使用できるかどうかを調べる

[Microsoft.AppPlatform/locations/operationResults/Spring] から、以下を選択します。読み取り:操作結果を読み取る

**[Microsoft.AppPlatform/locations/operationStatus/operationId]** から、以下を選択します。
* 読み取り:操作状態を読み取る

**[Microsoft.AppPlatform/skus]** から、以下を選択します。
* 読み取り:利用可能な SKU を一覧表示する

   [ ![Dev/Op のアクセス許可](media/spring-cloud-permissions/dev-ops-permissions.png) ](media/spring-cloud-permissions/dev-ops-permissions.png#lightbox)

3. **[追加]** をクリックします。

4. アクセス許可を確認します。

5. **[確認と作成]** をクリックします。

## <a name="define-ops---site-reliability-engineering-role"></a>Ops - サイト信頼性エンジニアリング ロールを定義する
この手順では、Azure Spring Cloud アプリをデプロイ、テスト、再起動するためのアクセス許可を持つロールを定義します。

1. サブスクリプション、リソース グループと進む手順を繰り返して、[アクセス制御 (IAM)] にアクセスします。

2. Ops - サイト信頼性エンジニアリング ロールのアクセス許可を選択します。

**[Microsoft.AppPlatform/Spring]** から、以下を選択します。
* 読み取り:Azure Spring Cloud サービス インスタンスを取得する
* その他:Azure Spring Cloud サービス インスタンスのテスト キーを一覧表示する

**[Microsoft.AppPlatform/Spring/apps]** から、以下を選択します。
* 読み取り:Microsoft Azure Spring Cloud アプリケーションを読み取る

**[Microsoft.AppPlatform/apps/deployments]** から、以下を選択します。
* 読み取り:Microsoft Azure Spring Cloud アプリケーション デプロイを読み取る
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを起動する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを停止する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを再起動する

**[Microsoft.AppPlatform/locations/operationResults/Spring]** から、以下を選択します。
* 読み取り:操作結果を読み取る

**[Microsoft.AppPlatform/locations/operationStatus/operationId]** から、以下を選択します。
* 読み取り:操作状態を読み取る

   [ ![Ops/SRE のアクセス許可](media/spring-cloud-permissions/ops-sre-permissions.png) ](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. **[追加]** をクリックします。

4. アクセス許可を確認します。

5. **[確認と作成]** をクリックします。

## <a name="define-azure-pipelinesprovisioning-role"></a>Azure Pipelines/プロビジョニング ロールを定義する
この Jenkins/Github アクション ロールを使用すると、サービス インスタンス内にある Azure Spring Cloud とアプリで、すべてを作成し、構成することができます。 このロールは、コードをリリースまたはデプロイするためのものです。

1. サブスクリプション、リソース グループと進む手順を繰り返して、[アクセス制御 (IAM)] にアクセスします。

2. **[アクセス許可]** オプションを開きます。

3. Azure Pipelines/プロビジョニング ロールのアクセス許可を選択します。
  
**[Microsoft.AppPlatform/Spring]** から、以下を選択します。
* 書き込み:Azure Spring Cloud サービス インスタンスを作成または更新する
* 削除:Azure Spring Cloud サービス インスタンスを削除する
* 読み取り:Azure Spring Cloud サービス インスタンスを取得する
* その他:Azure Spring Cloud サービス インスタンスのテスト エンドポイントを有効にする
* その他:Azure Spring Cloud サービス インスタンスのテスト エンドポイントを無効にする
* その他:Azure Spring Cloud サービス インスタンスのテスト キーを一覧表示する
* その他:Azure Spring Cloud サービス インスタンスのテスト キーを再生成する

**[Microsoft.AppPlatform/Spring/apps]** から、以下を選択します。
* 書き込み:Microsoft Azure Spring Cloud アプリケーションを書き込む
* 削除:Microsoft Azure Spring Cloud アプリケーションを削除する
* 読み取り:Microsoft Azure Spring Cloud アプリケーションを読み取る
* その他:Microsoft Azure Spring Cloud アプリケーション リソースのアップロード URL を取得する
* その他:Microsoft Azure Spring Cloud アプリケーションのカスタム ドメインを検証する

**[Microsoft.AppPlatform/Spring/apps/bindings]** から、以下を選択します。
* 書き込み:Microsoft Azure Spring Cloud アプリケーション バインドを書き込む
* 削除:Microsoft Azure Spring Cloud アプリケーション バインドを削除する
* 読み取り:Microsoft Azure Spring Cloud アプリケーション バインドを読み取る

**[Microsoft.AppPlatform/Spring/apps/deployments]** から、以下を選択します。
* 書き込み:Microsoft Azure Spring Cloud アプリケーション デプロイを書き込む
* 削除:Microsoft Azure Spring Cloud アプリケーション デプロイを削除する
* 読み取り:Microsoft Azure Spring Cloud アプリケーション デプロイを読み取る
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを起動する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを停止する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイを再起動する
* その他:Microsoft Azure Spring Cloud アプリケーション デプロイのログ ファイル URL を取得する

**[Microsoft.AppPlatform/skus]** から、以下を選択します。
* 読み取り:利用可能な SKU を一覧表示する

**[Microsoft.AppPlatform/locations]** から、以下を選択します。
* その他:名前を使用できるかどうかを調べる

**[Microsoft.AppPlatform/locations/operationResults/Spring]** から、以下を選択します。
* 読み取り:操作結果を読み取る

**[Microsoft.AppPlatform/locations/operationStatus/operationId]** から、以下を選択します。
* 読み取り:操作状態を読み取る

**[Microsoft.AppPlatform/skus]** から、以下を選択します。
* 読み取り:利用可能な SKU を一覧表示する

   [ ![Pipelines のアクセス許可](media/spring-cloud-permissions/pipelines-permissions-box.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. **[追加]** をクリックします。

5. アクセス許可を確認します。

6. **[確認と作成]** をクリックします。


## <a name="see-also"></a>関連項目
* [Azure portal を使用して Azure カスタム ロールを作成または更新する](../role-based-access-control/custom-roles-portal.md)

カスタム アクセス許可を定義する 3 つの方法の詳細については、以下を参照してください。
* [ロールを複製する](../role-based-access-control/custom-roles-portal.md#clone-a-role)
* [最初から行う](../role-based-access-control/custom-roles-portal.md#start-from-scratch)
* [JSON から始める](../role-based-access-control/custom-roles-portal.md#start-from-json)