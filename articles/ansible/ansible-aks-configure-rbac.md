---
title: チュートリアル - Ansible を使用して Azure Kubernetes Service (AKS) でロールベースのアクセス制御 (RBAC) ロールを構成する | Microsoft Docs
description: Ansible を使用して Azure Kubernetes Service (AKS) クラスターで RBAC を構成する方法について説明します
keywords: ansible, azure, devops, bash, cloudshell, プレイブック, aks, コンテナー, aks, kubernetes, azure active directory, rbac
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: eae23806ee1b4e2dac1d3410e32c3242e89d4be8
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67719825"
---
# <a name="tutorial-configure-role-based-access-control-rbac-roles-in-azure-kubernetes-service-aks-using-ansible"></a>チュートリアル:Ansible を使用して Azure Kubernetes Service (AKS) でロールベースのアクセス制御 (RBAC) ロールを構成する

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

ユーザー認証に [Azure Active Directory (AD)](/azure/active-directory/) を使うように AKS を構成できます。 構成後は、Azure AD 認証トークンを使用して AKS クラスターにサインインします。 ユーザーの ID またはディレクトリのグループ メンバーシップに基づく RBAC が可能です。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Azure AD 対応の AKS クラスターを作成する
> * クラスターで RBAC ロールを構成する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]
- **RedHat OpenShift ライブラリをインストールする** - `pip install openshift`

## <a name="configure-azure-ad-for-aks-authentication"></a>AKS 認証用に Azure AD を構成する

AKS 認証用に Azure AD を構成すると、2 つの Azure AD アプリケーションが構成されます。 この操作は、Azure テナント管理者が行う必要があります。 詳細については、[Azure Active Directory と AKS の統合](/azure/aks/aad-integration#create-the-server-application)に関するページをご覧ください。 

Azure テナント管理者に問い合わせて、次の値を取得します。

- サーバー アプリ シークレット
- サーバー アプリ ID
- クライアント アプリ ID 
- テナント ID

これらの値は、サンプルのプレイブックを実行するために必要です。  

## <a name="create-an-aks-cluster"></a>AKS クラスターの作成

このセクションでは、[Azure AD アプリケーション](#configure-azure-ad-for-aks-authentication)を使用して AKS を作成します。

以下に、サンプルのプレイブックを使用する際に考慮すべき重要な点をいくつか示します。

- このプレイブックは `~/.ssh/id_rsa.pub` から `ssh_key` を読み込みます。 これを変更する場合は、"ssh-rsa" (引用符は除く) で始まる単一行形式を使用してください。
- `client_id` および `client_secret` の値は、既定の資格情報ファイルである `~/.azure/credentials` から読み込まれます。 これらの値をサービス プリンシパルに設定するか、環境変数からこれらの値を読み込むことができます。

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

次のプレイブックを `aks-create.yml` という名前で保存します。

```yml
- name: Create resource group
  azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

- name: List supported kubernetes version from Azure
  azure_rm_aksversion_facts:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster with RBAC enabled
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      enable_rbac: yes
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      aad_profile:
          client_app_id: "{{ client_app_id }}"
          server_app_id: "{{ server_app_id }}"
          server_app_secret: "{{ server_app_secret }}"
          tenant_id: "{{ app_tenant_id }}"
  register: aks

- name: Save cluster user config
  copy:
      content: "{{ aks.kube_config }}"
      dest: "aks-{{ name }}-kubeconfig-user"

- name: Get admin config of AKS
  azure_rm_aks_facts:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      show_kubeconfig: admin
  register: aks

- name: Save the kubeconfig
  copy:
      content: "{{ aks.aks[0].kube_config }}"
      dest: "aks-{{ name }}-kubeconfig"
```

## <a name="get-the-azure-ad-object-id"></a>Azure AD オブジェクト ID を取得する

RBAC のバインドを作成するには、まず Azure AD オブジェクト ID を取得する必要があります。 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインします。

1. ページの上部にある検索フィールドに、`Azure Active Directory` と入力します。 

1. [`Enter`] をクリックします。

1. **[管理]** メニューで **[ユーザー]** を選択します。

1. [名前] フィールドで、お使いのアカウントを検索します。

1. **[名前]** 列で、お使いのアカウントへのリンクを選択します。

1. **[ID]** セクションで、 **[オブジェクト ID]** をコピーします。

    ![Azure AD オブジェクト ID をコピーします。](./media/ansible-aks-configure-rbac/ansible-aad-object-id.png)

## <a name="create-rbac-binding"></a>RBAC のバインドを作成する

このセクションでは、AKS でロール バインドまたはクラスター ロール バインドを作成します。 

次のプレイブックを `kube-role.yml` という名前で保存します。

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: <your-aad-account>
```

`&lt;your-aad-account>` プレースホルダーは、お使いの Azure AD テナント [オブジェクト ID](#get-the-azure-ad-object-id) に置き換えます。

新しいロールを AKS にデプロイする次のプレイブックを `aks-kube-deploy.yml` という名前で保存します。

```yml
- name: Apply role to AKS
  k8s:
      src: kube-role.yml
      kubeconfig: "aks-{{ name }}-kubeconfig"
```

## <a name="run-the-sample-playbook"></a>サンプル プレイブックを実行する

このセクションでは、この記事で作成するタスクを呼び出す完全なサンプル プレイブックを示します。 

次のプレイブックを `aks-rbac.yml` という名前で保存します。

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exist
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create AKS
       vars:
           client_app_id: <client id>
           server_app_id: <server id>
           server_app_secret: <server secret>
           app_tenant_id: <tenant id>
       include_tasks: aks-create.yml

     - name: Enable RBAC
       include_tasks: aks-kube-deploy.yml
```

`vars` セクションで、次のプレースホルダーを実際の Azure AD 情報に置き換えます。

- `<client id>`
- `<server id>`
- `<server secret>`
- `<tenant id>`

`ansible-playbook` コマンドを使用して完全なプレイブックを実行します。

```bash
ansible-playbook aks-rbac.yml
```

## <a name="verify-the-results"></a>結果を確認する

このセクションでは、kubectl を使用して、この記事で作成するノードを一覧表示します。

ターミナルのプロンプトで次のコマンドを入力します。

```bash
kubectl --kubeconfig aks-aksansibletest-kubeconfig-user get nodes
```

このコマンドを実行すると、認証ページに移動します。 Azure のアカウントを使用してサインインします。

認証されると、kubectl によって次の結果のようにノードが一覧表示されます。

```txt
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XXXXXXXX to authenticate.
NAME                       STATUS   ROLES   AGE   VERSION
aks-nodepool1-33413200-0   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-1   Ready    agent   49m   v1.12.6
aks-nodepool1-33413200-2   Ready    agent   49m   v1.12.6
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

この記事で作成したリソースが不要になったら、削除してください。 

`cleanup.yml` として次のコードを保存します。

```yml
---
- hosts: localhost
  vars:
      name: aksansibletest
      resource_group: aksansibletest
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
      - name: Remove kubeconfig
        file:
            state: absent
            path: "aks-{{ name }}-kubeconfig"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure 上の Ansible](/azure/ansible/)
