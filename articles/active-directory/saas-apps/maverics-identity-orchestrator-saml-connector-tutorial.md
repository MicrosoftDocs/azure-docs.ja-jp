---
title: チュートリアル:Azure Active Directory シングル サインオン (SSO) と Maverics Identity Orchestrator SAML Connector を統合する | Microsoft Docs
description: Azure Active Directory と Maverics Identity Orchestrator SAML Connector の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 19f6b0601afe9ad84f02c93d7f6e1ae3a71a06a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104585096"
---
# <a name="integrate-azure-ad-single-sign-on-with-maverics-identity-orchestrator-saml-connector"></a>Azure AD シングル サインオンと Maverics Identity Orchestrator SAML Connector を統合する

Strata の Maverics Identity Orchestrator には、認証とアクセス制御のために、オンプレミス アプリケーションを Azure Active Directory (Azure AD) と統合する簡単な方法が用意されています。 Maverics Orchestrator を使用すると、現在、ヘッダー、Cookie、およびその他の独自の認証方法に依存しているアプリの認証と承認を最新化できます。 Maverics Orchestrator のインスタンスは、オンプレミスまたはクラウドにデプロイできます。 

このハイブリッド アクセスのチュートリアルでは、レガシの Web アクセス管理製品によって現在保護されているオンプレミスの Web アプリケーションを移行し、認証とアクセス制御に Azure AD を使用する方法について説明します。 基本的な手順は次のとおりです。

1. Maverics Orchestrator をセットアップする
1. アプリケーションをプロキシする
1. エンタープライズ アプリケーションを Azure AD に登録する
1. Azure を使用して認証し、アプリケーションへのアクセスを承認する
1. シームレスなアプリケーション アクセスのためにヘッダーを追加する
1. 複数のアプリケーションを使用する

## <a name="prerequisites"></a>前提条件

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Maverics Identity Orchestrator SAML Connector での SSO が有効なサブスクリプション。 Maverics ソフトウェアを入手するには、[Strata の営業担当者](mailto:sales@strata.io)にお問い合わせください。
* ヘッダーベースの認証が使用されている少なくとも 1 つのアプリケーション。 この例では、 https://app.sonarsystems.com でホストされている Sonar という名前のアプリケーションと、 https://app.connectulum.com でホストされている Connectulum という名前のアプリケーションを使って作業します。
* Maverics Orchestrator をホストする Linux マシン
  * OS: RHEL 7.7 以降、CentOS 7 以降
  * ディスク: 10 GB 以上
  * メモリ: 4 GB 以上
  * ポート:22 (SSH/SCP)、443、7474
  * インストール/管理タスクでの root アクセス
  * Maverics Identity Orchestrator をホストしているサーバーから、保護されているアプリケーションへのネットワーク エグレス

## <a name="step-1-set-up-the-maverics-orchestrator"></a>手順 1: Maverics Orchestrator をセットアップする

### <a name="install-maverics"></a>Maverics をインストールする

1. 最新の Maverics RPM を入手します。 Maverics ソフトウェアのインストール先となるシステムにパッケージをコピーします。

1. `maverics.rpm` を実際のファイル名に置き換えて、Maverics パッケージをインストールします。

   `sudo rpm -Uvf maverics.rpm`

   Maverics をインストールすると、`systemd` でサービスとして実行されます。 サービスが実行されていることを確認するには、次のコマンドを実行します。

   `sudo systemctl status maverics`

1. Orchestrator を再起動してログに従うには、次のコマンドを実行します。

   `sudo service maverics restart; sudo journalctl --identifier=maverics -f`

Maverics をインストールすると、既定の `maverics.yaml` ファイルが `/etc/maverics` ディレクトリに作成されます。 構成を編集して `appgateways` と `connectors` を含める前の構成ファイルは、こちらに示すようになります。

```yaml
# © Strata Identity Inc. 2020. All Rights Reserved. Patents Pending.

version: 0.1
listenAddress: ":7474"
```

### <a name="configure-dns"></a>DNS を構成する

DNS によって、Orchestrator サーバーの IP を覚える必要がなくなります。

架空の Orchestrator IP である 12.34.56.78 を使用して、ブラウザー マシン (ノート PC) のホスト ファイルを編集します。 Linux ベースのオペレーティング システムでは、このファイルは `/etc/hosts` にあります。 Windows では、`C:\windows\system32\drivers\etc` にあります。

```
12.34.56.78 sonar.maverics.com
12.34.56.78 connectulum.maverics.com
```

DNS が想定どおりに構成されていることを確認するには、Orchestrator の状態エンドポイントに対して要求を行うことができます。 ブラウザーから、 http://sonar.maverics.com:7474/status を要求します。

### <a name="configure-tls"></a>TLS の構成

セキュリティを維持するには、セキュリティで保護されたチャネルを介して Orchestrator と通信することが重要です。 これを実現するには、`tls` セクションに証明書とキーのペアを追加します。

Orchestrator サーバーの自己署名証明書とキーを生成するには、`/etc/maverics` ディレクトリ内から次のコマンドを実行します。

`openssl req -new -newkey rsa:4096 -x509 -sha256 -days 365 -nodes -out maverics.crt -keyout maverics.key`

> [!NOTE]
> 運用環境では、ブラウザーでの警告を避けるために、既知の CA によって署名された証明書を使用できます。 信頼できる CA をお探しの場合は、[Let's Encrypt](https://letsencrypt.org/) は無料であり、お勧めできる選択肢です。

次に、Orchestrator 用の新しく生成された証明書とキーを使用します。 構成ファイルには、現在、こちらのコードが含まれています。

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key
```

TLS が想定どおりに構成されていることを確認するには、Maverics サービスを再起動し、状態エンドポイントに対して要求を行います。 ブラウザーから、 https://sonar.maverics.com/status を要求します。

## <a name="step-2-proxy-an-application"></a>手順 2: アプリケーションをプロキシする

次に、`appgateways` を使用して、Orchestrator で基本的なプロキシを構成します。 この手順により、Orchestrator に、保護されたアプリケーションへの必要な接続があることを検証できます。

構成ファイルには、現在、こちらのコードが含まれています。

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com
```

プロキシが想定どおりに動作していることを確認するには、Maverics サービスを再起動し、Maverics プロキシ経由でアプリケーションに対して要求を行います。 ブラウザーから、 https://sonar.maverics.com を要求します。 必要に応じて、`https://sonar.maverics.com/RESOURCE` などの特定のアプリケーション リソースへに対して要求を行うことができます。ここで、`RESOURCE` は、保護されたアップストリーム アプリの有効なアプリケーション リソースです。

## <a name="step-3-register-an-enterprise-application-in-azure-ad"></a>手順 3: エンタープライズ アプリケーションを Azure AD に登録する

次に、エンドユーザーの認証に使用される新しいエンタープライズ アプリケーションを Azure AD 内に作成します。

> [!NOTE]
> 条件付きアクセスなどの Azure AD の機能を使用する場合は、オンプレミスのアプリケーションごとにエンタープライズ アプリケーションを作成することが重要です。 これにより、アプリごとの条件付きアクセス、アプリごとのリスク評価、アプリごとの割り当てられたアクセス許可などが許可されます。 一般に、Azure AD のエンタープライズ アプリケーションは、Maverics の Azure コネクタにマップされます。

エンタープライズ アプリケーションを Azure AD に登録するには、次のようにします。

1. Azure AD テナントで、 **[エンタープライズ アプリケーション]** に移動し、 **[新しいアプリケーション]** を選択します。 Azure AD ギャラリーで、「**Maverics Identity Orchestrator SAML Connector**」を検索して選択します。

1. Maverics Identity Orchestrator SAML Connector の **[プロパティ]** ペインで、 **[ユーザーの割り当てが必要ですか?]** を **[いいえ]** に設定して、ディレクトリ内のすべてのユーザーがアプリケーションを使用できるようにします。

1. Maverics Identity Orchestrator SAML Connector の **[概要]** ペインで、 **[シングル サインオンを設定する]** を選択してから、 **[SAML]** を選択します。

1. Maverics Identity Orchestrator SAML Connector の **[SAML ベースのサインオン]** ペインで、 **[編集]** (鉛筆アイコン) ボタンを選択して **[基本的な SAML 構成]** を編集します。

   !["基本的な SAML 構成" 編集ボタンのスクリーンショット。](common/edit-urls.png)

1. **エンティティ ID** `https://sonar.maverics.com` を入力します。 エンティティ ID はテナント内のアプリ間で一意である必要があり、任意の値を指定できます。 この値は、次のセクションで Azure コネクタの `samlEntityID` フィールドを定義するときに使用します。

1. **応答 URL** `https://sonar.maverics.com/acs` を入力します。 この値は、次のセクションで Azure コネクタの `samlConsumerServiceURL` フィールドを定義するときに使用します。

1. **サインオン URL** `https://sonar.maverics.com/` を入力します。 このフィールドは Maverics では使用されませんが、Azure AD で、ユーザーが Azure AD のマイアプリ ポータルを介してアプリケーションにアクセスできるようにするために必要です。

1. **[保存]** を選択します。

1. **[SAML 署名証明書]** セクションで、 **[コピー]** ボタンを選択して **[アプリのフェデレーション メタデータ URL]** の値をコピーし、お使いのコンピューターに保存します。

   ![[SAML 署名証明書] コピー ボタンのスクリーンショット。](common/copy-metadataurl.png)

## <a name="step-4-authenticate-via-azure-and-authorize-access-to-the-application"></a>手順 4: Azure を使用して認証し、アプリケーションへのアクセスを承認する

次に、Maverics で Azure コネクタを構成して、使用するために作成したばかりのエンタープライズ アプリケーションを配置します。 この `connectors` 構成と `idps` ブロックを組み合わせることで、Orchestrator ではユーザーを認証できます。

構成ファイルには、現在、次のコードが含まれています。 `METADATA_URL` は、前の手順で取得したアプリのフェデレーション メタデータ URL の値に必ず置き換えてください。

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

認証が想定どおりに動作していることを確認するには、Maverics サービスを再起動し、Maverics プロキシ経由でアプリケーション リソースに対して要求を行います。 リソースにアクセスする前に、認証のために Azure にリダイレクトされます。

## <a name="step-5-add-headers-for-seamless-application-access"></a>手順 5: シームレスなアプリケーション アクセスのためにヘッダーを追加する

まだアップストリーム アプリケーションにヘッダーを送信しません。 Maverics プロキシを通過する要求に `headers` を追加して、アップストリーム アプリケーションでユーザーを識別できるようにしましょう。

構成ファイルには、現在、こちらのコードが含まれています。

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp

appgateways:
  - name: sonar
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com
```

認証が想定どおりに動作していることを確認するには、Maverics プロキシ経由でアプリケーション リソースに対して要求を行います。 これで、保護されたアプリケーションでは要求でヘッダーを受信するようになりました。 

アプリケーションで異なるヘッダーが想定されている場合は、ヘッダー キーを自由に編集してください。 SAML フローの一部として Azure AD から返されるすべての要求は、ヘッダーで使用できます。 たとえば、別のヘッダー `secondary_email: azureSonarApp.email` を含めることができます。ここで、`azureSonarApp` はコネクタ名で、`email` は Azure AD から返される要求です。 

## <a name="step-6-work-with-multiple-applications"></a>手順 6: 複数のアプリケーションを使用する

次に、別のホスト上にある複数のアプリケーションにプロキシするために必要なものを見てみましょう。 この手順を実行するには、別の App Gateway、Azure AD 内の別のエンタープライズ アプリケーション、および別のコネクタを構成します。

構成ファイルには、現在、こちらのコードが含まれています。

```yaml
version: 0.1
listenAddress: ":443"

tls:
  maverics:
    certFile: /etc/maverics/maverics.crt
    keyFile: /etc/maverics/maverics.key

idps:
  - name: azureSonarApp
  - name: azureConnectulumApp

appgateways:
  - name: sonar
    host: sonar.maverics.com
    location: /
    # Replace https://app.sonarsystems.com with the address of your protected application
    upstream: https://app.sonarsystems.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureSonarApp.authenticated}}", "true"]

    headers:
      email: azureSonarApp.name
      firstname: azureSonarApp.givenname
      lastname: azureSonarApp.surname

  - name: connectulum
    host: connectulum.maverics.com
    location: /
    # Replace https://app.connectulum.com with the address of your protected application
    upstream: https://app.connectulum.com

    policies:
      - resource: /
        allowIf:
          - equal: ["{{azureConnectulumApp.authenticated}}", "true"]

    headers:
      email: azureConnectulumApp.name
      firstname: azureConnectulumApp.givenname
      lastname: azureConnectulumApp.surname

connectors:
  - name: azureSonarApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://sonar.maverics.com/acs
    samlEntityID: https://sonar.maverics.com

  - name: azureConnectulumApp
    type: azure
    authType: saml
    # Replace METADATA_URL with the App Federation Metadata URL
    samlMetadataURL: METADATA_URL
    samlConsumerServiceURL: https://connectulum.maverics.com/acs
    samlEntityID: https://connectulum.maverics.com
```

このコードで、App Gateway の定義に `host` フィールドが追加されていることが分かります。 `host` フィールドを使用することで、Maverics Orchestrator では、トラフィックのプロキシ先のアップストリーム ホストを区別できます。

新しく追加された App Gateway が想定どおりに動作していることを確認するには、 https://connectulum.maverics.com に対して要求を行います。

## <a name="advanced-scenarios"></a>高度なシナリオ

### <a name="identity-migration"></a>ID の移行

有効期間が終了した Web アクセス管理ツールには我慢できないが、パスワードの一括リセットを行わずにユーザーを移行する方法がないとお考えですか。 Maverics Orchestrator では、`migrationgateways` を使用して ID の移行をサポートします。

### <a name="web-server-gateways"></a>Web サーバー ゲートウェイ

Maverics Orchestrator を使用してネットワークとプロキシのトラフィックをやり直したくないとお考えですか。 問題ありません。 Maverics Orchestrator では、Web サーバー ゲートウェイ (モジュール) と組み合わせることで、プロキシを使用せずに同じソリューションを提供できます。

## <a name="wrap-up"></a>まとめ

この時点で、Maverics Orchestrator をインストールし、Azure AD 内にエンタープライズ アプリケーションを作成して構成し、認証を要求しポリシーを適用しながら、保護されたアプリケーションにプロキシするように Orchestrator を構成しました。 Maverics Orchestrator を分散 ID 管理のユースケースに使用する方法の詳細については、[Strata にお問い合わせ](mailto:sales@strata.io)ください。

## <a name="next-steps"></a>次の手順

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)
- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)
