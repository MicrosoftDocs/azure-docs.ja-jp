---
title: Azure Security Center への AWS アカウントの接続
description: Azure Security Center から AWS リソースを監視します
author: memildin
ms.author: memildin
ms.date: 01/24/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2ad3ab8c39d9f9a29e74bd139fb5ddd1e5faeff9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755162"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Azure Security Center への AWS アカウントの接続

通常、クラウド ワークロードは複数のクラウド プラットフォームにまたがるため、クラウド セキュリティサービスもそうである必要があります。

Azure Security Center は、Azure、アマゾン ウェブ サービス (AWS)、および Google Cloud Platform (GCP) のワークロードを保護します。

AWS アカウントを Security Center にオンボードすると、AWS Security Hub と Azure Security Center が統合されます。 そのため、Security Center は、この両方のクラウド環境全体を可視化および保護することで、以下を実現します。

- エージェントの自動プロビジョニング (Security Center は [Azure Arc](../azure-arc/servers/overview.md) を使用して、Log Analytics エージェントをお使いの AWS インスタンスにデプロイします)
- ポリシー管理
- 脆弱性の管理
- 埋め込まれたエンドポイント検出と応答 (EDR)
- セキュリティ構成ミスの検出
- Security Center の推奨事項と AWS Security Command Hub の検出結果を 1 つのビューに表示
- Security Center のセキュリティ スコアの計算への AWS リソースの組み込み
- AWS リソースの規制コンプライアンスの評価

次のスクリーンショットでは、Security Center の概要ダッシュボードに AWS アカウントが表示されていることがわかります。

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Security Center の概要ダッシュボードに一覧表示された 3 つの GCP プロジェクト" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|[Azure Defender for servers](defender-for-servers-introduction.md) が必要|
|必要なロールとアクセス許可:|関連する Azure サブスクリプションの **所有者**<br>所有者がサービス プリンシパルの詳細を提供する場合は、**共同作成者** も AWS アカウントに接続できます|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||



## <a name="connect-your-aws-account"></a>AWS アカウントを接続する

以下の手順に従って AWS クラウド コネクタを作成します。 

### <a name="step-1-set-up-aws-security-hub"></a>手順 1. AWS Security Hub を設定する:

1. 複数のリージョンのセキュリティに関する推奨事項を表示するには、関連するリージョンごとに次の手順を繰り返します。

    > [!IMPORTANT]
    > AWS マスター アカウントを使用している場合は、これらの手順を繰り返して、関連するすべてのリージョンで、マスター アカウントと、接続されたすべてのメンバー アカウントを構成します

    1. [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html) を有効にします。
    1. [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html) を有効にします。
    1. Security Hub にデータが流れていることを確認します。

        Security Hub を初めて有効にするときは、データが使用可能になるまで数時間かかることがあります。

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>手順 2. AWS で Security Center に対する認証を設定する

Security Center が AWS に対して認証を行えるようにするには、2 つの方法があります。

- **Security Center の IAM ロールを作成する** - 最も安全な推奨される方法です
- **Security Center の AWS ユーザー** - IAM が有効になっていない場合の安全性が低いオプション

#### <a name="create-an-iam-role-for-security-center"></a>Security Center の IAM ロールを作成する
1. アマゾン ウェブ サービス コンソールの **[セキュリティ、アイデンティティ、コンプライアンス]** の **[IAM]** を選択します。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS サービス":::

1. **[ロール]** 、 **[ロールの作成]** の順に選択します。
1. **[別の AWS アカウント]** を選択します。
1. 次の詳細を入力します。

    - **アカウント ID** - Security Center の AWS コネクタ ページに示されているように、Microsoft アカウント ID (**158177204117**) を入力します。
    - **外部 ID が必要** - 選択する必要があります
    - **外部 ID** - Security Center の AWS コネクタ ページに示されているように、サブスクリプション ID を入力します 

1. **[次へ]** を選択します。
1. **[Attach permission policies]\(権限ポリシーをアタッチ\)** セクションで、次のポリシーを選択します。

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. 必要に応じて、タグを追加します。 ユーザーにタグを追加しても、接続には影響しません。
1. **[次へ]** を選択します。

1. ロールの一覧で、作成したロールを選択します

1. 後で使用できるように Amazon Resource Name (ARN) を保存します。 

#### <a name="create-an-aws-user-for-security-center"></a>Security Center の AWS ユーザーを作成する 
1. **[ユーザー]** タブを開き、 **[ユーザーの追加]** を選択します。
1. **[詳細]** ステップで Security Center のユーザー名を入力し、AWS アクセスの種類として **[プログラムによるアクセス]** が選択されていることを確認します。 
1. **[次のステップ: アクセス権限]** を選択します。
1. **[Attach existing policies directly]\(既存のポリシーを直接アタッチ\)** を選択し、次のポリシーを適用します。
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. **タグ** を選択します。 必要に応じて、タグを追加します。 ユーザーにタグを追加しても、接続には影響しません。
1. **[レビュー]** を選択します。
1. 後で使用するために、自動的に生成された **アクセス キー ID** と **シークレット アクセス キー** の CSV ファイルを保存します。
1. 概要を確認し、 **[ユーザーを作成]** をクリックします。


### <a name="step-3-configure-the-ssm-agent"></a>手順 3. SSM エージェントを構成する

お使いの AWS リソース全体でタスクを自動化するには、AWS Systems Manager が必要です。 お使いの EC2 インスタンスに SSM エージェントがない場合は、Amazon の関連する指示に従ってください。

- [Windows インスタンスでの SSM エージェントのインストールと構成](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Amazon EC2 Linux インスタンスでの SSM エージェントのインストールと構成](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>手順 4. Azure Arc の前提条件を満たす
1. 適切な [Azure リソース プロバイダー](../azure-arc/servers/agent-overview.md#register-azure-resource-providers)が登録されていることを確認します。
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. 大規模なオンボーディング用にサービス プリンシパルを作成します。 「[大規模なオンボーディング用にサービス プリンシパルを作成する](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)」で説明されているように、オンボードに使用するサブスクリプションの **所有者** として、Azure Arc オンボード用サービス プリンシパルを作成します。


### <a name="step-5-connect-aws-to-security-center"></a>手順 5. AWS を Security Center に接続する

1. Security Center のメニューから、 **[マルチ クラウド コネクタ]** を選択します。
1. **[Add AWS account]\(AWS アカウントの追加\)** を選択します。
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Security Center のマルチ クラウド コネクタ ページの [Add AWS account]\(AWS アカウントの追加\) ボタン":::
1. **[AWS authentication]\(AWS 認証\)** タブでオプションを構成します。
    1. コネクタの **表示名** を入力します。
    1. サブスクリプションが正しいことを確認します。 これは、コネクタと AWS Security Hub の推奨事項が追加されるサブスクリプションです。
    1. 「[手順 2. AWS で Security Center に対する認証を設定する](#step-2-set-up-authentication-for-security-center-in-aws)」で選択した認証オプションに応じて、次の操作を行います。
        - **[Assume Role]\(ロールを想定\)** を選択し、「[Security Center の IAM ロールを作成する](#create-an-iam-role-for-security-center)」から ARN を貼り付けます。
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Azure portal で AWS 接続ウィザードの関連フィールドに ARN ファイルを貼り付ける":::

            OR

        - **資格情報** を選択し、「[Security Center の AWS ユーザーを作成する](#create-an-aws-user-for-security-center)」で保存した .csv ファイルから **アクセス キー** と **秘密鍵** を貼り付けます。
1. **[次へ]** を選択します。
1. **[Azure Arc Configuration]\(Azure Arc 構成\)** タブでオプションを構成します。

    接続されている AWS アカウントの EC2 インスタンスが Security Center によって検出され、SSM を使用して Azure Arc にオンボードされます。 

    > [!TIP]
    > サポートされるオペレーティング システムの一覧については、FAQ の「[自身の EC2 インスタンスではどのオペレーティングシステムがサポートされていますか?](#what-operating-systems-for-my-ec2-instances-are-supported)」を参照してください。

    1. 選択したサブスクリプションで、検出された AWS EC2 がオンボードされる **リソース グループ** と **Azure リージョン** を選択します。
    1. こちらの「[大規模なオンボーディング用にサービス プリンシパルを作成する](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)」で説明されているように、Azure Arc の **サービス プリンシパル ID** と **サービス プリンシパルのクライアント シークレット** を入力します
    1. マシンがプロキシ サーバー経由でインターネットに接続している場合は、プロキシ サーバーの IP アドレスか、マシンがプロキシ サーバーとの通信に使用する名前とポート番号を指定します。 ```http://<proxyURL>:<proxyport>``` の形式で値を入力します。
    1. **[Review + create]\(レビュー + 作成\)** を選択します。

        概要情報をレビューする

        タグ セクションには、Azure で簡単に認識できるように、オンボード EC2 ごとに自動的に作成されるすべての Azure タグと、そのタグに関連する独自の詳細情報が記載されています。 

        Azure タグの詳細については、「[タグを使用して Azure リソースと管理階層を整理する](../azure-resource-manager/management/tag-resources.md)」を参照してください。

### <a name="step-6-confirmation"></a>手順 6. 確認

コネクタが正常に作成され、AWS Security Hub が正しく構成されたら、次のようになります。

- Security Center は、環境をスキャンして AWS EC2 インスタンスを探し、見つかったら Azure Arc にそのインスタンスをオンボードし、Log Analytics エージェントのインストールを可能にするほか、脅威の防止とセキュリティに関する推奨事項を提供します。 
- ASC サービスは、新しい AWS EC2 インスタンスを 6 時間ごとにスキャンして探し、構成に従ってそれらオンボードします。
- Security Center の規制コンプライアンス ダッシュボードに AWS CIS 標準が表示されます。
- Security Hub ポリシーが有効になっている場合、オンボードが完了してから 5 分から 10 分後、推奨事項が Security Center ポータルと規制コンプライアンス ダッシュボードに表示されます。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Security Center の推奨事項ページの AWS リソースと推奨事項":::



## <a name="monitoring-your-aws-resources"></a>AWS リソースの監視

上記のように、Azure Security Center のセキュリティに関する推奨事項ページには、Azure リソースおよび GCP リソースと共に AWS リソースが表示され、真のマルチクラウド ビューが実現します。

リソースのアクティブな推奨事項を、リソースの種類別にすべて表示するには、Security Center の資産インベントリ ページを使用し、関心のある AWS リソースの種類にフィルターを適用します。

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="AWS オプションを示す資産インベントリ ページのリソースの種類のフィルター"::: 


## <a name="aws-in-security-center-faq"></a>Security Center の AWS に関する FAQ

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>自身の EC2 インスタンスのではどのオペレーティングシステムがサポートされていますか?

AWS マシン向け Azure Arc への自動オンボードのためにサポートされている OS

- Ubuntu 16.04 - SSM エージェントは既定でプレインストールされています
- Ubuntu 18.04 - SSM エージェントは既定でプレインストールされています
- Windows Server - SSM エージェントは既定でプレインストールされています
- CentOS Linux 7 - SSM は手動でインストールするか、個別にオンボードする必要があります
- SUSE Linux Enterprise Server (SLES) 15 (x64) - SSM は手動でインストールするか、個別にオンボードする必要があります
- Red Hat Enterprise Linux (RHEL) 7 (x64) - SSM は手動でインストールするか、個別にオンボードする必要があります


## <a name="next-steps"></a>次の手順

AWS アカウントの接続は、Azure Security Center で利用できるマルチクラウド エクスペリエンスの一部です。 関連情報については、次のページを参照してください。

- [Azure Security Center への GCP アカウントの接続](quickstart-onboard-gcp.md)
