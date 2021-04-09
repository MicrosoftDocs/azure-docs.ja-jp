---
title: Azure FarmBeats をインストールする
description: この記事では、Azure サブスクリプションに Azure FarmBeats をインストールする方法について説明します
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 5863dcc20fb13f1bb203c68ad168655371130601
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92674557"
---
# <a name="install-azure-farmbeats"></a>Azure FarmBeats をインストールする

この記事では、Azure サブスクリプションに Azure FarmBeats をインストールする方法について説明します。

Azure FarmBeats は、Azure Marketplace で入手できる企業間オファリングです。 複数のプロバイダーにまたがる農業データ セットを集約し、アクションにつながる分析情報を生成することができます。 これを Azure FarmBeats を使用して実行するには、合成したデータ セットに基づいて人工知能 (AI) または機械学習 (ML) モデルを構築します。 Azure FarmBeats の 2 つの主要なコンポーネントは次のとおりです。

- **データ ハブ**:さまざまなプロバイダーにまたがるさまざまな農業データ セットの集計、正規化、およびコンテキスト化を可能にする API レイヤー。

- **アクセラレータ**:データ ハブ上に構築された Web アプリケーション。 これを使うと、モデルの開発と視覚化をすぐに始められます。 アクセラレータでは、Azure FarmBeats API を使って、取り込まれたセンサー データのグラフとしての視覚化とモデル出力のマップとしての視覚化の例を示します。

## <a name="general-information"></a>一般情報

### <a name="components-installed"></a>インストールされるコンポーネント

Azure FarmBeats をインストールすると、次のリソースが Azure サブスクリプションにプロビジョニングされます。

| インストールされる Azure リソース  | Azure FarmBeats コンポーネント  |
|---------|---------|
| Application Insights   |      データ ハブとアクセラレータ      |
| App Service     |     データ ハブとアクセラレータ     |
| App Service プラン   | データ ハブとアクセラレータ  |
| API 接続    |  データ ハブ       |
| Azure Cache for Redis       | データ ハブ      |
| Azure Cosmos DB   |  データ ハブ       |
| Azure Data Factory V2       |     データ ハブとアクセラレータ      |
| Azure Batch アカウント    | データ ハブ   |
| Azure Key Vault |  データ ハブとアクセラレータ        |
| Azure Maps アカウント       |     アクセラレータ    |
| イベント ハブの名前空間    |     データ ハブ      |
| ロジック アプリ      |  データ ハブ       |
| ストレージ アカウント      |     データ ハブとアクセラレータ      |
| Time Series Insights     |    データ ハブ    |

### <a name="costs-incurred"></a>発生するコスト

Azure FarmBeats のコストは、基になる Azure サービスのコストを集計したものです。 Azure サービスの料金情報は、[料金計算ツール](https://azure.microsoft.com/pricing/calculator)を使用して計算できます。 インストール全体の実際のコストは、使用量によって変わります。 2 つのコンポーネントの安定した状態のコストは次のとおりです。

- データ ハブ - 1 日あたり $10 未満
- アクセラレータ - 1 日あたり $2 未満

### <a name="regions-supported"></a>サポートされているリージョン

現時点で、Azure FarmBeats は次のリージョンのパブリック クラウド環境でサポートされています。

- オーストラリア東部
- 米国中部
- 米国東部
- 米国東部 2
- 米国西部
- 米国西部 2
- 北ヨーロッパ
- 西ヨーロッパ
- 東アジア
- 東南アジア

### <a name="time-taken"></a>所要時間

準備とインストールを含め、Azure FarmBeats の設定全体にかかる時間は 1 時間未満です。

## <a name="prerequisites"></a>前提条件

Azure FarmBeats の実際のインストールを開始する前に、次の手順を完了する必要があります。

### <a name="verify-permissions"></a>アクセス許可を確認する

Azure FarmBeats をインストールするには、Azure テナントで次のアクセス許可が必要です。

- テナント - AAD アプリ作成者
- サブスクリプション - 所有者
- FarmBeats がインストールされるリソース グループ - 所有者

[AAD アプリケーションの作成](#create-an-aad-application)の手順では、最初の 2 つのアクセス許可が必要です。 必要に応じて、AAD アプリケーションを作成するために、適切なアクセス許可を持つユーザーを用意します。

FarmBeats をマーケットプレースからインストールするユーザーは、FarmBeats がインストールされるリソース グループの所有者である必要があります。 サブスクリプションの所有者については、リソース グループの作成時にこの内容が自動的に発生します。 その他については、リソース グループを事前に作成し、リソース グループの所有者になるようにサブスクリプションの所有者に依頼してください。

Azure portal でアクセス許可を確認するには、[Azure ロール ベースのアクセス制御](../../role-based-access-control/check-access.md)に関する記事の手順に従います。

### <a name="decide-subscription-and-region"></a>サブスクリプションとリージョンを決定する

Azure FarmBeats をインストールする Azure サブスクリプション ID とリージョンが必要です。 「[サポートされているリージョン](#regions-supported)」セクションに一覧表示されているリージョンのいずれかを選択します。

**[Azure サブスクリプション ID]** と **[Azure リージョン]** をメモしておきます。

### <a name="create-an-aad-application"></a>AAD アプリケーションを作成する

Azure FarmBeats では、Azure Active Directory アプリケーションの作成と登録が必要です。 AAD 作成スクリプトを正常に実行するには、次のアクセス許可が必要です。

- テナント - AAD アプリ作成者
- サブスクリプション - 所有者

PowerShell 環境を使用して、Cloud Shell インスタンスで次の手順を実行します。 初めて使用するユーザーの場合、サブスクリプションを選択し、ストレージ アカウントを作成するように求められます。 指示に従って設定を完了します。

1. [AAD アプリ ジェネレーター スクリプト](https://aka.ms/FarmBeatsAADScript)をダウンロードします。

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. 既定では、ファイルはホーム ディレクトリにダウンロードされます。 そのディレクトリに移動します。

    ```azurepowershell-interactive
        cd
    ```

3. AAD スクリプトを実行します。

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. このスクリプトでは、次の 3 つの入力を求められます。

    - **FarmBeats Web サイト名**:これは FarmBeats Web アプリケーションの一意の URL プレフィックスです。 プレフィックスが既に取得されている場合、スクリプトはエラーを表示します。インストールされると、FarmBeats のデプロイは https://\<FarmBeats-website-name>.azurewebsites.net からアクセス可能になり、Swagger API の場所は https://\<FarmBeats-website-name>-api.azurewebsites.net になります。

    - **Azure ログイン ID**:FarmBeats の管理者として追加するユーザーに Azure ログイン ID を入力します。 その後、このユーザーは、FarmBeats Web アプリケーションへのアクセス権を他のユーザーに付与することができます。 ログイン ID は、通常、john.doe@domain.comの形式となります。 Azure UPN もサポートされています。

    - **サブスクリプション ID**:これは、Azure FarmBeats をインストールするサブスクリプションの ID です。

5. 実行時間は約 2 分です。この AAD スクリプトでは、画面上と、同じディレクトリ内の json ファイルに値が出力されます。 他のユーザーがスクリプトを実行していた場合は、その出力を共有するよう依頼してください。

### <a name="create-sentinel-account"></a>Sentinel アカウントを作成する

Azure FarmBeats の設定を使用すると、欧州宇宙機関の [Sentinel-2](https://scihub.copernicus.eu/) 衛星ミッションからファームの衛星画像を取得できます。 この設定を構成するには、Sentinel アカウントが必要です。

次の手順に従って、Sentinel で無料アカウントを作成します。

1. 公式の[サインアップ](https://aka.ms/SentinelRegistration) ページにアクセスします。
2. 必要な詳細 (名、姓、ユーザー名、パスワード、およびメール ID) を入力し、フォームを完成させます。
3. 登録されているメール ID に確認リンクが送信されます。 メールに記載されているリンクを選択し、検証を完了します。

登録プロセスが完了しました。 確認も完了したら、**Sentinel ユーザー名** と **Sentinel パスワード** をメモしておきます。

## <a name="install"></a>インストール

これで、FarmBeats をインストールする準備ができました。 次の手順に従って、インストールを開始します。

1. Azure portal にサインインします。 右上隅でお使いのアカウントを選択し、Azure FarmBeats をインストールする Azure AD テナントに切り替えます。

2. ポータルで Azure Marketplace にアクセスし、Marketplace で **Azure FarmBeats** を検索します。

3. Azure FarmBeats の概要が表示された新しいウィンドウが開きます。 **［作成］** を選択します

4. 新しいウィンドウが開きます。 Azure FarmBeats をインストールする適切なサブスクリプション、リソース グループ、および場所を選択して、サインアップ プロセスを完了します。

5. **[FarmBeats Service Alerts]\(FarmBeats のサービス アラート\)** セクションで、Azure FarmBeats に関するサービス アラートを受信するメール アドレスを指定します。 ページの下部にある **[次へ]** を選択して、 **[依存関係]** タブに移動します。

    ![[基本] タブ](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. [AAD スクリプト](#create-an-aad-application)の出力から、[AAD アプリケーション] セクションの入力に個々のエントリをコピーします。

7. [Sentinel アカウント] セクションに、[Sentinel アカウント](#create-sentinel-account)のユーザー名とパスワードを入力します。 **[次へ]** を選択して、 **[確認および作成]** タブに移動します。

    ![[依存関係] タブ](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. 入力した詳細が検証されたら、 **[OK]** を選択します。 利用規約ページが表示されます。 規約を確認し、 **[作成]** を選択してインストールを開始します。 インストールの進行状況を確認できるページにリダイレクトされます。

インストールが完了したら、インストールを確認し、インストール中に指定した Web サイト名 (https://\<FarmBeats-website-name>.azurewebsites.net) に移動することによって FarmBeats ポータルの使用を開始できます。 FarmBeats ユーザー インターフェイスと、ファームを作成するためのオプションが表示されます。

**データ ハブ** は、 https://\<FarmBeats-website-name>-api.azurewebsites.net/swagger にあります。 ここでは、さまざまな FarmBeats API オブジェクトを表示し、API に対して REST 操作を実行することができるようになります。

## <a name="upgrade"></a>アップグレード

FarmBeats を最新バージョンにアップグレードするには、PowerShell 環境を使用して、Cloud Shell インスタンスで次の手順を実行します。 ユーザーは、FarmBeats がインストールされているサブスクリプションの所有者である必要があります。

初めて使用するユーザーの場合、サブスクリプションを選択し、ストレージ アカウントを作成するように求められます。 指示に従って設定を完了します。

1. [アップグレード スクリプト](https://aka.ms/FarmBeatsUpgradeScript)をダウンロードします。

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. 既定では、ファイルはホーム ディレクトリにダウンロードされます。 そのディレクトリに移動します。

    ```azurepowershell-interactive
        cd
    ```

3. アップグレード スクリプトを実行します。

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

input.json ファイルへのパスは省略可能です。 指定しなかった場合、スクリプトによってすべての必要な入力が要求されます。 アップグレードは約 30 分で完了します。

## <a name="uninstall"></a>アンインストール

Azure FarmBeats データ ハブ または アクセラレータ をアンインストールするには、次の手順を実行します。

1. Azure portal にログインし、これらのコンポーネントがインストールされている **リソース グループを削除** します。

2. Azure Active Directory に移動し、Azure FarmBeats のインストールにリンクされている **Azure AD アプリケーション削除** します。

## <a name="next-steps"></a>次のステップ

ここでは、Azure サブスクリプションに Azure FarmBeats をインストールする方法を学習しました。 次は、Azure FarmBeats インスタンスに[ユーザーを追加する](manage-users-in-azure-farmbeats.md#manage-users)方法を確認します。