---
title: Application Gateway を使用して HTTP または HTTPS 経由で AKS サービスを公開する
description: この記事では、Application Gateway を使用して HTTP または HTTPS 経由で AKS サービスを公開する方法について説明します。
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 3b816ddc0eccf8c406cfed37d6bfc594e27d3629
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85850349"
---
# <a name="expose-an-aks-service-over-http-or-https-using-application-gateway"></a>Application Gateway を使用して HTTP または HTTPS 経由で AKS サービスを公開する 

これらのチュートリアルでは、HTTP または HTTPS 経由で [Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) を通して Kubernetes サービスの例を公開するための [Kubernetes イングレス リソース](https://kubernetes.io/docs/concepts/services-networking/ingress/)の使用方法について説明します。

## <a name="prerequisites"></a>前提条件

- `ingress-azure` Helm Chart がインストールされている。
  - [**グリーンフィールド デプロイ**](ingress-controller-install-new.md): 最初から開始する場合は、次のインストール手順を参照してください。Application Gateway を利用して AKS クラスターをデプロイし、AKS クラスター上に Application Gateway のイングレス コントローラーをインストールするおおまかな手順を示しています。
  - [**ブラウンフィールド デプロイ**](ingress-controller-install-existing.md): 既存の AKS クラスターと Application Gateway がある場合は、次の手順を参照して、AKS クラスター上に Application Gateway イングレス コントローラーをインストールしてください。
- このアプリケーション上で HTTPS を使用する場合は、x509 証明書とその秘密キーが必要になります。

## <a name="deploy-guestbook-application"></a>`guestbook` アプリケーションのデプロイ

ゲストブック アプリケーションは、Web UI フロントエンド、バックエンド、Redis データベースから構成される正規の Kubernetes アプリケーションです。 `guestbook` では既定で、名前が `frontend` のサービスを介して、ポート `80` 上にアプリケーションを公開します。 Kubernetes イングレス リソースがない場合、AKS クラスターの外部からサービスにアクセスすることはできません。 アプリケーションを使用し、イングレス リソースを設定して、HTTP および HTTPS 経由でアプリケーションにアクセスします。

以下の手順に従って、ゲストブック アプリケーションをデプロイします。

1. `guestbook-all-in-one.yaml` を[こちら](https://raw.githubusercontent.com/kubernetes/examples/master/guestbook/all-in-one/guestbook-all-in-one.yaml)からダウンロードします
1. 以下を実行して、ご自身の AKS クラスターに `guestbook-all-in-one.yaml` をデプロイします

  ```bash
  kubectl apply -f guestbook-all-in-one.yaml
  ```

これで、`guestbook` アプリケーションがデプロイされました。

## <a name="expose-services-over-http"></a>HTTP 経由でサービスを公開する

ゲストブック アプリケーションを公開するために、次のイングレス リソースを使用します。

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: guestbook
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: frontend
          servicePort: 80
```

このイングレスでは、`guestbook-all-in-one` デプロイの `frontend` サービスが Application Gateway の既定のバックエンドとして公開されます。

上記のイングレス リソースを `ing-guestbook.yaml` として保存します。

1. 次を実行して、`ing-guestbook.yaml` をデプロイします。

    ```bash
    kubectl apply -f ing-guestbook.yaml
    ```

1. イングレス コントローラーのログ上でデプロイの状態を確認します。

これで、`guestbook` アプリケーションが使用可能になりました。 Application Gateway のパブリック アドレスにアクセスすると、このことを確認できます。

## <a name="expose-services-over-https"></a>HTTPS 経由でサービスを公開する

### <a name="without-specified-hostname"></a>ホスト名を指定しない

ホスト名を指定しない場合、そのアプリケーション ゲートウェイを参照するすべてのホスト名において、ゲストブック サービスが使用可能です。

1. イングレスをデプロイする前に、kubernetes シークレットを作成して、証明書と秘密キーをホストする必要があります。 次を実行して、Kubernetes シークレットを作成できます

    ```bash
    kubectl create secret tls <guestbook-secret-name> --key <path-to-key> --cert <path-to-cert>
    ```

1. 次のイングレスを定義します。 イングレス内で、`secretName` セクションにシークレットの名前を指定します。

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - secretName: <guestbook-secret-name>
      rules:
      - http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

    > [!NOTE] 
    > 上記のイングレス リソース内の `<guestbook-secret-name>` を、お使いのシークレットの名前に置き換えます。 ファイル名 `ing-guestbook-tls.yaml` に上記のイングレス リソースを保存します。

1. 次を実行して、ing-guestbook-tls.yaml をデプロイします

    ```bash
    kubectl apply -f ing-guestbook-tls.yaml
    ```

1. イングレス コントローラーのログ上でデプロイの状態を確認します。

これで、HTTP および HTTPS の両方で `guestbook` アプリケーションが使用可能になります。

### <a name="with-specified-hostname"></a>ホスト名が指定されている

TLS の構成とサービスを多重化するために、イングレス上でホスト名を指定することもできます。
ホスト名を指定すると、指定したホスト上のみでゲストブック サービスを使用できます。

1. 次のイングレスを定義します。
    イングレス内で、`secretName` セクションにシークレットの名前を指定し、それに従って `hosts` セクション内のホスト名を置き換えます。

    ```yaml
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: guestbook
      annotations:
        kubernetes.io/ingress.class: azure/application-gateway
    spec:
      tls:
        - hosts:
          - <guestbook.contoso.com>
          secretName: <guestbook-secret-name>
      rules:
      - host: <guestbook.contoso.com>
        http:
          paths:
          - backend:
              serviceName: frontend
              servicePort: 80
    ```

1. 次を実行して、`ing-guestbook-tls-sni.yaml` をデプロイします

    ```bash
    kubectl apply -f ing-guestbook-tls-sni.yaml
    ```

1. イングレス コントローラーのログ上でデプロイの状態を確認します。

これで、`guestbook` アプリケーションは、指定されたホスト上 (この例では `<guestbook.contoso.com>`) のみで、HTTP および HTTPS の両方で使用できるようになります。

## <a name="integrate-with-other-services"></a>他のサービスとの統合

次のイングレスでは、このイングレスに追加のパスを付加して、それらのパスを他のサービスにリダイレクトできます。

```yaml
apiVersion: extensions/v1beta1
  kind: Ingress
  metadata:
    name: guestbook
    annotations:
      kubernetes.io/ingress.class: azure/application-gateway
  spec:
    rules:
    - http:
        paths:
        - path: </other/*>
          backend:
            serviceName: <other-service>
            servicePort: 80
        - backend:
            serviceName: frontend
            servicePort: 80
```
