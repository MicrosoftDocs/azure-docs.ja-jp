---
title: Azure での OpenShift Container Platform 3.11 のデプロイ後タスク
description: OpenShift Container Platform 3.11 クラスターがデプロイされた後の追加タスクについて説明します。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.custom: devx-track-ansible, devx-track-azurecli
ms.openlocfilehash: c3f9aaa15a697202aa76c563ed62bf37443d69ec
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669386"
---
# <a name="post-deployment-tasks"></a>デプロイ後タスク

OpenShift クラスターをデプロイした後に、追加の項目を構成することができます。 この記事には、次の内容が含まれます。

- Azure Active Directory (Azure AD) を使用してシングル サインオンを構成する方法
- OpenShift を監視する Azure Monitor ログを構成する方法
- メトリックとログを構成する方法
- Open Service Broker for Azure (OSBA) をインストールする方法

## <a name="configure-single-sign-on-by-using-azure-active-directory"></a>Azure Active Directory を使用してシングル サインオンを構成する

認証に Azure Active Directory を使用するには、まず Azure AD アプリの登録を作成する必要があります。 このプロセスには、アプリの登録の作成とアクセス許可の構成という 2 つの手順があります。

### <a name="create-an-app-registration"></a>アプリの登録を作成する

以下の手順では、Azure CLI を使用してアプリの登録を作成し、GUI (Portal) を使用してアクセス許可を設定します。 アプリの登録を作成するには、次の 5 つの情報が必要です。

- 表示名: アプリの登録名 (例: OCPAzureAD)
- ホーム ページ: OpenShift コンソール URL (例: `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- 識別子 URI: OpenShift コンソール URL (例: `https://masterdns343khhde.westus.cloudapp.azure.com/console`)
- 応答 URL: マスター パブリック URL とアプリの登録名 (例: `https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD`)
- パスワード: セキュリティで保護されたパスワード (強力なパスワードを使用する)

次の例では、上記の情報を使用してアプリの登録を作成します。

```azurecli
az ad app create --display-name OCPAzureAD --homepage https://masterdns343khhde.westus.cloudapp.azure.com/console --reply-urls https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/hwocpadint --identifier-uris https://masterdns343khhde.westus.cloudapp.azure.com/console --password {Strong Password}
```

コマンドが成功すると、次のような JSON が出力されます。

```json
{
  "appId": "12345678-ca3c-427b-9a04-ab12345cd678",
  "appPermissions": null,
  "availableToOtherTenants": false,
  "displayName": "OCPAzureAD",
  "homepage": "https://masterdns343khhde.westus.cloudapp.azure.com/console",
  "identifierUris": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/console"
  ],
  "objectId": "62cd74c9-42bb-4b9f-b2b5-b6ee88991c80",
  "objectType": "Application",
  "replyUrls": [
    "https://masterdns343khhde.westus.cloudapp.azure.com/oauth2callback/OCPAzureAD"
  ]
}
```

後の手順のために、コマンドから返された appId プロパティを書き留めます。

Azure portal で次の操作を行います。

1. **[Azure Active Directory]**  >  **[アプリの登録]** の順に選択します。
2. アプリの登録を検索します (例: OCPAzureAD)。
3. 結果のアプリの登録をクリックします。
4. **[設定]** の **[必要なアクセス許可]** を選択します
5. **[必要なアクセス許可]** の **[追加]** を選択します。

   ![アプリケーションの登録](media/openshift-post-deployment/app-registration.png)

6. [手順 1: API の選択]、**[Windows Azure Active Directory (Microsoft.Azure.ActiveDirectory)]** の順にクリックします。 下部にある **[選択]** をクリックします。

   ![[アプリの登録] の [API の選択]](media/openshift-post-deployment/app-registration-select-api.png)

7. [手順 2: アクセス許可の選択] で、**[委任されたアクセス許可]** の **[サインインとユーザー プロファイルの読み取り]** を選択し、**[選択]** をクリックします。

   ![[アプリの登録] のアクセス](media/openshift-post-deployment/app-registration-access.png)

8. **[完了]** を選択します。

### <a name="configure-openshift-for-azure-ad-authentication"></a>Azure AD 認証用に OpenShift を構成する

認証プロバイダーとして Azure AD を使用するように OpenShift を構成するには、すべてのマスター ノードで /etc/origin/master/master-config.yaml ファイルを編集する必要があります。

テナント ID を確認するには、次の CLI コマンドを使用します。

```azurecli
az account show
```

yaml ファイルで、次の行を探します。

```yaml
oauthConfig:
  assetPublicURL: https://masterdns343khhde.westus.cloudapp.azure.com/console/
  grantConfig:
    method: auto
  identityProviders:
  - challenge: true
    login: true
    mappingMethod: claim
    name: htpasswd_auth
    provider:
      apiVersion: v1
      file: /etc/origin/master/htpasswd
      kind: HTPasswdPasswordIdentityProvider
```

これらの行の真上に次の行を挿入します。

```yaml
  - name: <App Registration Name>
    challenge: false
    login: true
    mappingMethod: claim
    provider:
      apiVersion: v1
      kind: OpenIDIdentityProvider
      clientID: <appId>
      clientSecret: <Strong Password>
      claims:
        id:
        - sub
        preferredUsername:
        - unique_name
        name:
        - name
        email:
        - email
      urls:
        authorize: https://login.microsoftonline.com/<tenant Id>/oauth2/authorize
        token: https://login.microsoftonline.com/<tenant Id>/oauth2/token
```

identityProviders の下にテキストを正確に揃えて配置します。 テナント ID を確認するには、次の CLI コマンドを使用します。```az account show```

すべてのマネージャーで OpenShift マスター サービスを再起動します。

```bash
sudo /usr/local/bin/master-restart api
sudo /usr/local/bin/master-restart controllers
```

OpenShift Console に、認証のオプションが 2 つ表示されるようになります (htpasswd_auth と [アプリの登録])。

## <a name="monitor-openshift-with-azure-monitor-logs"></a>Azure Monitor ログを使用して OpenShift を監視する

OpenShift に Log Analytics エージェントを追加するには 3 つの方法があります。
- OpenShift の各ノードに直接 Log Analytics エージェント for Linux をインストールする
- OpenShift の各ノードで Azure Monitor VM 拡張機能を有効にする
- Log Analytics エージェントを OpenShift デーモン セットとしてインストールする

詳細については、[手順](../../azure-monitor/containers/containers.md#configure-a-log-analytics-agent-for-red-hat-openshift)全体を参照してください。

## <a name="configure-metrics-and-logging"></a>メトリックとログを構成する

OpenShift Container Platform および OKD 用の Azure Resource Manager テンプレートは、ブランチに基づいて、インストールの一部としてメトリックとログ記録を有効にするための入力パラメーターを提供します。

OpenShift Container Platform Marketplace オファーでは、クラスターのインストール時のメトリックとログ記録を有効にすることもできます。

クラスターのインストール時にメトリック/ログ記録を有効にしなかった場合でも、後から簡単に有効にできます。

### <a name="azure-cloud-provider-in-use"></a>使用中の Azure クラウド プロバイダー

デプロイ時に指定された資格情報を使用し、(使用しているテンプレートとブランチに応じて) 踏み台ノードまたは最初のマスター ノードに SSH を実行します。 次のコマンドを実行します。

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True \
-e openshift_metrics_cassandra_storage_type=dynamic

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True \
-e openshift_logging_es_pvc_dynamic=true
```

### <a name="azure-cloud-provider-not-in-use"></a>使用中ではない Azure クラウド プロバイダー

```bash
ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-metrics/config.yml \
-e openshift_metrics_install_metrics=True

ansible-playbook /usr/share/ansible/openshift-ansible/playbooks/openshift-logging/config.yml \
-e openshift_logging_install_logging=True
```

## <a name="install-open-service-broker-for-azure-osba"></a>Open Service Broker for Azure (OSBA) をインストールする

Open Service Broker for Azure (OSBA) を使用して、OpenShift から Azure Cloud Services を直接プロビジョニングすることができます。 OSBA は、Azure 用の Open Service Broker API の実装です。 Open Service Broker API は、クラウド ネイティブ アプリケーションがロックインなしでクラウド サービスの管理に使用できるクラウド プロバイダー用の共通言語を定義した仕様です。

OpenShift に OSBA をインストールするには、こちらの手順に従います: https://github.com/Azure/open-service-broker-azure#openshift-project-template。 
> [!NOTE]
> OpenShift プロジェクト テンプレートのセクションの手順のみを実行し、インストールのセクション全体は実行しないでください。

## <a name="next-steps"></a>次のステップ

- [OpenShift Container Platform の概要](https://docs.openshift.com)
