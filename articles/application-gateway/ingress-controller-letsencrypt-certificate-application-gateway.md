---
title: Application Gateway に LetsEncrypt.org 証明書を使用する
description: この記事では、LetsEncrypt.org から証明書を取得し、AKS クラスター用の Application Gateway 上でそれを使用する方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: df8722e8160538daa1535711092790dbb2405097
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "84807030"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>AKS クラスター用の Application Gateway 上の LetsEncrypt.org に証明書を使用する

このセクションでは、[LetsEncrypt.org](https://letsencrypt.org/) を利用し、ドメインの TLS/SSL 証明書を自動的に取得するように AKS を構成します。 証明書は Application Gateway にインストールされ、AKS クラスターの SSL/TLS の終了が実行されます。 ここで説明する設定には、[cert-manager](https://github.com/jetstack/cert-manager) Kubernetes アドオンを使用します。これにより、証明書の作成と管理が自動化されます。

既存の AKS クラスターに [cert-manager](https://docs.cert-manager.io) をインストールするには、次の手順を実行します。

1. Helm Chart

    次のスクリプトを実行して、`cert-manager` Helm Chart をインストールします。 リセットすると、以下のようになります。

    - AKS に新しい `cert-manager` 名前空間が作成される
    - 次の CRDs を作成します: Certificate、Challenge、ClusterIssuer、Issuer、Order
    - cert-manager チャートが ([docs.cert-manager.io](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps) から) インストールされる

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. ClusterIssuer リソース

    `ClusterIssuer` リソースを作成します。 `cert-manager` では、署名された証明書を取得する `Lets Encrypt` 証明機関を表す必要があります。

    名前空間のない `ClusterIssuer` リソースを使用すると、cert-manager からは複数の名前空間から使用できる証明書が発行されます。 `Let’s Encrypt` では、特定のドメイン名を制御していることを確認し、証明書を発行するために、ACME プロトコルが使用されます。 `ClusterIssuer` プロパティの構成の詳細については、[こちら](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html)を参照してください。 `ClusterIssuer` から `cert-manager` に対して、`Lets Encrypt` ステージング環境を使用してテスト用の証明書 (ブラウザー/クライアントの信頼ストアに存在しないルート証明書) を発行するように指示されます。

    以下の YAML の既定のチャレンジの種類は `http01` です。 その他のチャレンジについては、[letsencrypt.org の「Challenge Types (チャレンジの種類)」](https://letsencrypt.org/docs/challenge-types/)を参照してください。

    > [!IMPORTANT] 
    > 以下の YAML の `<YOUR.EMAIL@ADDRESS>` を更新してください

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. アプリをデプロイする

    Lets Encrypt Certificate と共に Application Gateway を使用して `guestbook` アプリケーションを公開するイングレス リソースを作成します。

    Application Gateway に DNS 名を持つパブリック フロントエンド IP 構成を用意します (既定の `azure.com`ドメインを使用するか、`Azure DNS Zone` サービスをプロビジョニングし、独自のカスタム ドメインを割り当てます)。
    注釈 `certmanager.k8s.io/cluster-issuer: letsencrypt-staging` に注目してください。これは、タグ付きイングレス リソースを処理するように cert-manager に指示するものです。

    > [!IMPORTANT] 
    > 以下の YAML の `<PLACEHOLDERS.COM>` は、独自のドメイン (または 'kh-aks-ingress.westeurope.cloudapp.azure.com' などの Application Gateway のもの) に更新してください

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    数秒後に、自動的に発行された **ステージング** `Lets Encrypt` 証明書を使用して、Application Gateway HTTPS URL を介して `guestbook` サービスにアクセスできるようになります。
    ブラウザーから、無効な証明機関について警告される場合があります。 ステージング証明書は `CN=Fake LE Intermediate X1` から発行されます。 これは、システムが想定どおりに機能し、運用環境の証明書を準備できたことを示します。

4. 運用証明書

    ステージング証明書が正常に設定されたら、運用 ACME サーバーに切り替えることができます。
    1. イングレス リソースのステージング注釈を `certmanager.k8s.io/cluster-issuer: letsencrypt-prod` に置き換えます。
    1. 前の手順で作成した既存のステージング `ClusterIssuer` を削除し、新しいものを作成するには、上の ClusterIssuer YAML の ACME サーバーを `https://acme-v02.api.letsencrypt.org/directory` に置き換えます

5. 証明書の有効期限と更新

    `Lets Encrypt` の証明書の有効期限が切れる前に更新すると、`cert-manager` によって、Kubernetes シークレット ストア内の証明書が自動的に更新されます。 その時点で、Application Gateway Ingress Controller により、Application Gateway の構成に使用されているイングレス リソースの参照には更新されたシークレットが適用されます。
