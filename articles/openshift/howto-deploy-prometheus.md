---
title: Azure Red Hat OpenShift クラスターにスタンドアロンの Prometheus をデプロイする | Microsoft Docs
description: アプリケーションのメトリックを監視するために Azure Red Hat OpenShift クラスターに Prometheus インスタンスを作成する方法を説明します。
author: Makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 06/17/2019
keywords: prometheus, aro, openshift, メトリック, red hat
ms.openlocfilehash: e66658151361edd43f61d398274c88c047928028
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343235"
---
# <a name="deploy-a-standalone-prometheus-in-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift クラスターにスタンドアロンの Prometheus をデプロイする

このガイドでは、Azure Red Hat OpenShift クラスターのサービス検出でスタンドアロンの Prometheus を構成する方法について説明します。  クラスターへの "顧客管理者" アクセスは必要ありません。

対象のセットアップは次のとおりです。

- 1 つのプロジェクト (prometheus-project): Prometheus と Alertmanager が含まれます
- 2 つのプロジェクト (app-project1 と app-project2): 監視対象のアプリケーションが含まれます

一部の Prometheus 構成ファイルはローカル環境に準備します。 それらを格納するための新しいフォルダーを作成します。
後でシークレット トークンが追加される場合、これらの構成ファイルはシークレットとしてクラスター内に格納されます。

## <a name="step-1-sign-in-to-the-cluster-using-the-oc-tool"></a>手順 1:`oc` ツールを使ってクラスターにサインインする
Web ブラウザーを使用して、クラスターの Web コンソールに移動します (https://openshift.*random-id*.*region*.azmosa.io)。
Azure の資格情報でサインインします。
右上のユーザー名をクリックし、[Copy Login Command]\(ログイン コマンドのコピー\) を選択します。 使用するターミナルにそれを貼り付けます。

正しいクラスターにサインインしているかどうかを、`oc whoami -c` コマンドで確認することができます。

## <a name="step-2-prepare-the-projects"></a>手順 2:プロジェクトを準備する

プロジェクトを作成すします。
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> `-n` または `--namespace` パラメーターを使用するか、`oc project` コマンドでアクティブなプロジェクトを選択することができます

## <a name="step-3-prepare-prometheus-config"></a>手順 3:Prometheus の構成を準備する
次の内容で prometheus.yml という名前のファイルを作成します。
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
構成で "prom" という名前のシークレットを作成します。
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

上記のファイルは、基本的な Prometheus 構成ファイルです。
3 つのプロジェクト (prometheus-project、app-project1、app-project2) で、間隔が設定され、自動検出が構成されます。
この例では、自動検出されるエンドポイントは、認証なしに HTTP 経由で取得されます。
エンドポイントの取得について詳しくは、 https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config をご覧ください。


## <a name="step-4-prepare-alertmanager-config"></a>手順 4:Alertmanager の構成を準備する
次の内容で alertmanager.yml という名前のファイルを作成します。
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
構成で "prom-alerts" という名前のシークレットを作成します。
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

上記のファイルは、Alert Manager の構成ファイルです。

> [!NOTE]
> 前の 2 つの手順は、`oc get secret -n prometheus-project` で検証できます

## <a name="step-5-start-prometheus-and-alertmanager"></a>手順 5:Prometheus と Alertmanager を起動する
[prometheus-standalone.yaml](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) テンプレートを [openshift/origin リポジトリ](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus)からダウンロードし、prometheus-project に適用します
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
prometheus-standalone.yaml ファイルは OpenShift テンプレートであり、Prometheus インスタンスとその前面の oauth-proxy および Alertmanager インスタンス (やはり oauth-proxy で保護されます) が作成されます。  このテンプレートでは、"prometheus-project" 名前空間を "取得" できるすべてのユーザーを許可するように、oauth-proxy が構成されます (`-openshift-sar` フラグを参照)。

> [!NOTE]
> "prom" StatefulSet に *DESIRED* と *CURRENT* の数が等しいレプリカがあるかどうかを、`oc get statefulset -n prometheus-project` コマンドで確認できます。
> また、`oc get all -n prometheus-project` でプロジェクト内のすべてのリソースを確認できます。

## <a name="step-6-add-permissions-to-allow-service-discovery"></a>手順 6:サービスの検出を可能にするアクセス許可を追加する
次の内容で prometheus-sdrole.yml を作成します。
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus Service Discovery Role
    description: |
      A role and rolebinding adding permissions required to perform Service Discovery in a given project.
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
サービスの検出を可能にするすべてのプロジェクトにテンプレートを適用します。
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
```
Prometheus がそれ自体からメトリックを収集できるようにする場合は、忘れずに prometheus-project でもアクセス許可を適用してください。

> [!NOTE]
> Role および RoleBinding が正しく作成されたかどうかは、それぞれ `oc get role` および `oc get rolebinding` コマンドで確認できます

## <a name="optional-deploy-example-application"></a>省略可能:サンプル アプリケーションをデプロイする
すべては動作していますが、メトリック ソースがありません。 Prometheus の URL (https://prom-prometheus-project.apps.*random-id*.*region*.azmosa.io/) に移動します。次のコマンドで見つけることができます。
```
oc get route prom -n prometheus-project
```
ホスト名の前に https:// を付けることを忘れないでください。

**[Status]\(ステータス\) > [Service Discovery]\(サービス検出\)** ページでは、アクティブなターゲットが 0/0 と示されます。

/metrics エンドポイントで基本的な Python のメトリックを公開するサンプル アプリケーションをデプロイするには、次のコマンドを実行します。
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
デプロイ後 30 秒以内に、新しいアプリケーションが有効なターゲットとして [Service Discovery]\(サービス検出\) ページに表示されます。 **[Status]\(ステータス\) > [Targets]\(ターゲット\)** ページでは詳細を取得することができます。

> [!NOTE]
> 正常に取得されたすべてのターゲットについて、Prometheus で "up" メトリックにデータポイントが追加されます。 左上隅にある **[Prometheus]** をクリックし、式に「up」と入力して、 **[Execute]\(実行\)** をクリックします。

## <a name="next-steps"></a>次の手順

カスタム Prometheus インストルメンテーションをアプリケーションに追加できます。

Prometheus メトリックの準備が簡略化される Prometheus クライアント ライブラリは、さまざまなプログラミング言語に対応します。

 - Java https://github.com/prometheus/client_java
 - Python https://github.com/prometheus/client_python
 - Go https://github.com/prometheus/client_golang
 - Ruby https://github.com/prometheus/client_ruby
