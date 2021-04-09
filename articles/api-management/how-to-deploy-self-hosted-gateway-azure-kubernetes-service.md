---
title: Azure Kubernetes Service にセルフホステッド ゲートウェイをデプロイする | Microsoft Docs
description: Azure API Management のセルフホステッド ゲートウェイ コンポーネントを Azure Kubernetes Service にデプロイする方法について説明します
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "87015223"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Azure Kubernetes Service にデプロイする

この記事では、Azure API Management のセルフホステッド ゲートウェイ コンポーネントを [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) にデプロイする手順について説明します。 セルフホステッド ゲートウェイを Kubernetes クラスターにデプロイする場合は、この[ドキュメント](how-to-deploy-self-hosted-gateway-kubernetes.md)を参照してください。

## <a name="prerequisites"></a>前提条件

- [Azure API Management インスタンスを作成する](get-started-create-service-instance.md)
- [Azure Kubernetes クラスターを作成する](../aks/kubernetes-walkthrough-portal.md)
- [API Management インスタンスにゲートウェイ リソースをプロビジョニングします](api-management-howto-provision-self-hosted-gateway.md)。

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>AKS にセルフホステッド ゲートウェイをデプロイする

1. **[Deployment and infrastructure]\(デプロイとインフラストラクチャ\)** から **[ゲートウェイ]** を選択します。
2. デプロイするセルフホステッド ゲートウェイ リソースを選択します。
3. **[Deployment]/(デプロイ/)** を選択します。
4. 既定の **[有効期限]** および **[秘密鍵]** の値を使用して、 **[トークン]** テキスト ボックスの新しいトークンが自動生成されたことに注意してください。 必要に応じていずれかまたは両方を調整し、 **[生成]** を選択して新しいトークンを作成します。
5. **[デプロイ スクリプト]** で **[Kubernetes]** が選択されていることを確認します。
6. **[Deployment]/(デプロイ/)** の横にある **<gateway-name>.yml** ファイルのリンクを選択してファイルをダウンロードします。
7. 必要に応じて、yml ファイルのポート マッピングとコンテナー名を調整します。
8. シナリオによっては、[サービスの種類](../aks/concepts-network.md#services)の変更が必要になる場合があります。 既定値は `NodePort` です。
9. **[Deploy]/(デプロイ/)** テキストボックスの右端にある **[コピー]** アイコンを選択して、`kubectl` コマンドをクリップボードに保存します。
10. コマンドをターミナル (またはコマンド) ウィンドウに貼り付けます。 コマンドでは、ダウンロードした環境ファイルが現在のディレクトリに存在する必要があることに注意してください。
```console
    kubectl apply -f <gateway-name>.yaml
```
11. コマンドを実行します。 このコマンドは AKS クラスターに対して、Microsoft Container Registry からダウンロードしたセルフホステッド ゲートウェイのイメージを使用してコンテナーを実行し、HTTP (8080) ポートと HTTPS (443) ポートを公開するようにコンテナーを構成するように指示します。
12. 次のコマンドを実行して、ゲートウェイ ポッドが実行中であることを確認します。 使用するポッド名は異なることに注意してください。
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. 次のコマンドを実行して、ゲートウェイ サービスが実行中であることを確認します。 サービス名と IP アドレスが異なることに注意してください。
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Azure portal に戻り、デプロイしたばかりのゲートウェイ ノードが正常な状態を報告していることを確認します。

> [!TIP]
> <code>kubectl logs <gateway-pod-name></code> コマンドを使用して、セルフホステッド ゲートウェイ ログのスナップショットを表示します。

## <a name="next-steps"></a>次のステップ

* セルフホステッド ゲートウェイの詳細については、[Azure API Management のセルフホステッド ゲートウェイの概要](self-hosted-gateway-overview.md)に関する記事を参照してください
* [Azure Kubernetes Service](../aks/intro-kubernetes.md) の詳細を見る
* [クラウドでログを構成して保持する方法](how-to-configure-cloud-metrics-logs.md)について学習する
* * [ローカルでログを構成して永続化する方法](how-to-configure-local-metrics-logs.md)について学習する
