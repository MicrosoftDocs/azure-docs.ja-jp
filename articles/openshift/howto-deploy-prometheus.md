---
title: Azure Red Hat OpenShift クラスターに Prometheus インスタンスをデプロイする
description: アプリケーションのメトリックを監視するために Azure Red Hat OpenShift クラスターに Prometheus インスタンスを作成します。
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, メトリック, red hat
ms.openlocfilehash: 974096e515401ab5e292dbad4c9beba7f1611a8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633446"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift クラスターにスタンドアロンの Prometheus インスタンスをデプロイする

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 は、2022 年 6 月 30 日に廃止されます。 新しい Azure Red Hat OpenShift 3.11 クラスターの作成のサポートは、2020 年 11 月 30 日まで継続されます。 廃止された後、残っている Azure Red Hat OpenShift 3.11 クラスターは、セキュリティの脆弱性を防ぐためにシャットダウンされます。
> 
> このガイドに従って、[Azure Red Hat OpenShift 4 クラスターを作成](tutorial-create-cluster.md)します。
> ご質問がある場合は、[お問い合わせください](mailto:arofeedback@microsoft.com)。

この記事では、Azure Red Hat OpenShift クラスター内でサービス検索を使用するスタンドアロンの Prometheus インスタンスを構成する方法について説明します。

> [!NOTE]
> Azure Red Hat OpenShift クラスターへの顧客管理者アクセスは必要ありません。

ターゲット設定:

- 1 つのプロジェクト (prometheus-project): Prometheus と Alertmanager が含まれます。
- 2 つのプロジェクト (app-project1 と app-project2): 監視対象のアプリケーションが含まれます。

一部の Prometheus 構成ファイルはローカルに準備します。 それらを格納するための新しいフォルダーを作成します。 後でシークレット トークンがクラスターに追加される場合、構成ファイルはシークレットとしてクラスター内に格納されます。

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>OC ツールを使ってクラスターにサインインする

1. Web ブラウザーを開き、クラスターの Web コンソールに移動します (https://openshift.*random-id*.*region*.azmosa.io)。
2. Azure の資格情報でサインインします。
3. 右上隅にあるユーザー名を選択し、 **[Copy Login Command]\(ログイン コマンドのコピー\)** を選択します。
4. 使用するターミナルにユーザー名を貼り付けます。

> [!NOTE]
> 正しいクラスターにサインインしているかどうかを確認するには、`oc whoami -c` コマンドを実行します。

## <a name="prepare-the-projects"></a>プロジェクトを準備する

プロジェクトを作成するには、以下のコマンドを実行します。
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> `-n` または `--namespace` パラメーターを使用するか、`oc project` コマンドを実行してアクティブなプロジェクトを選択することができます。

## <a name="prepare-the-prometheus-configuration-file"></a>Prometheus 構成ファイルを準備する
次の内容を入力して prometheus.yml ファイルを作成します。
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
次の構成を入力して、Prom というシークレットを作成します。
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

prometheus.yml ファイルは、基本的な Prometheus 構成ファイルです。 3 つのプロジェクト (prometheus-project、app-project1、app-project2) で、間隔が設定され、自動検出が構成されます。 前の構成ファイルでは、自動検出されるエンドポイントは、認証なしに HTTP 経由で取得されます。

エンドポイントの取得について詳しくは、[Prometheus scape config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config) に関するページをご覧ください。


## <a name="prepare-the-alertmanager-config-file"></a>Alertmanager の構成ファイルを準備する
次の内容を入力して alertmanager.yml ファイルを作成します。
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
次の構成を入力して、Prom-Alerts というシークレットを作成します。
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

Alertmanager.yml は、Alert Manager の構成ファイルです。

> [!NOTE]
> 前の 2 つの手順を検証するには、`oc get secret -n prometheus-project` コマンドを実行します。

## <a name="start-prometheus-and-alertmanager"></a>Prometheus と Alertmanager を起動する
[openshift/origin リポジトリ](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)に移動し、[prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) テンプレートをダウンロードします。 次の構成を入力して、テンプレートを prometheus-project に適用します。
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
prometheus-standalone.yaml ファイルは OpenShift テンプレートです。 これにより、Prometheus インスタンスとその前面の oauth-proxy および Alertmanager インスタンス (これも oauth-proxy で保護されます) が作成されます。 このテンプレートでは、prometheus-project 名前空間を "取得" できるすべてのユーザーを許可するように、oauth-proxy が構成されます (`-openshift-sar` フラグを参照)。

> [!NOTE]
> prom StatefulSet に DESIRED と CURRENT の数が等しいレプリカがあるかどうかを確認するには、`oc get statefulset -n prometheus-project` コマンドを実行します。 プロジェクト内のすべてのリソースを確認するには、`oc get all -n prometheus-project` コマンドを実行します。

## <a name="add-permissions-to-allow-service-discovery"></a>サービス検索を可能にするアクセス許可を追加する

次の内容を入力して prometheus-sdrole.yml ファイルを作成します。
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
サービス検索を可能にするすべてのプロジェクトにテンプレートを適用するには、次のコマンドを実行します。
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> Role と RoleBinding が正しく作成されたことを確認するには、`oc get role` コマンドと `oc get rolebinding` コマンドを実行します。

## <a name="optional-deploy-example-application"></a>省略可能:サンプル アプリケーションをデプロイする

すべては動作していますが、メトリック ソースがありません。 Prometheus の URL (https://prom-prometheus-project.apps.*random-id*.*region*.azmosa.io/) にアクセスします。 次のコマンドを使用して見つけることができます。

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> ホスト名の先頭に https:// プレフィックスを忘れずに追加してください。

**[Status]\(ステータス\) > [Service Discovery]\(サービス検出\)** ページでは、アクティブなターゲットが 0/0 と示されます。

/metrics エンドポイントで基本的な Python のメトリックを公開するサンプル アプリケーションをデプロイするには、次のコマンドを実行します。
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
デプロイ後 30 秒以内に、新しいアプリケーションが有効なターゲットとして [サービス検索] ページに表示されます。

詳しくは、 **[状態]**  >  **[ターゲット]** を選択してください。

> [!NOTE]
> 正常に取得されたすべてのターゲットについて、Prometheus によって up メトリックにデータ ポイントが追加されます。 左上隅にある **[Prometheus]** を選択し、式に「**up**」と入力して、 **[実行]** を選択します。

## <a name="next-steps"></a>次のステップ

カスタム Prometheus インストルメンテーションをアプリケーションに追加できます。 Prometheus メトリックの準備が簡略化される Prometheus クライアント ライブラリは、さまざまなプログラミング言語に対応します。

詳しくは、次の GitHub ライブラリをご覧ください。

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Go](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
