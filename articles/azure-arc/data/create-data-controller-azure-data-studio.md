---
title: Azure Data Studio でデータ コントローラーを作成する
description: Azure Data Studio でデータ コントローラーを作成する
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: f2d44cc769e9673eeb75828126f806d2b2308a17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103573882"
---
# <a name="create-data-controller-in-azure-data-studio"></a>Azure Data Studio でデータ コントローラーを作成する

展開ウィザードとノートブックから Azure Data Studio を使用して、データ コントローラーを作成できます。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>前提条件

- Kubernetes クラスターにアクセスし、デプロイ先の Kubernetes クラスターを示すように kubeconfig ファイルを構成しておく必要があります。
- [クライアント ツールをインストールする](install-client-tools.md)必要があります。ツールには、**Azure Data Studio** と、**Azure Arc** と **[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]** という名前の Azure Data Studio 拡張機能が含まれます。
- Azure Data Studio で Azure にログインする必要があります。  これを行うには、「CTRL/Command + SHIFT + P」と入力してコマンド テキスト ウィンドウを開き、「**Azure**」と入力します。  **Azure:サインイン**」を検索して選びます。   パネルの右上にある [+] アイコンをクリックして、Azure アカウントを追加します。

## <a name="use-the-deployment-wizard-to-create-azure-arc-data-controller"></a>展開ウィザードを使用して Azure Arc データ コントローラーをデプロイする

展開ウィザードを使用して Azure Arc データ コントローラーを作成するには、次の手順に従います。

1. Azure Data Studio の左側にあるナビゲーションで、[接続] タブをクリックします。
2. [接続] パネルの上部にある **[...]** ボタンをクリックし、 **[新しい展開...]** を選択します。
3. 新しい展開ウィザードで、 **[Azure Arc データ コントローラー]** を選択し、下部にある **[選択]** ボタンをクリックします。
4. 前提条件のツールが使用可能であり、必要なバージョンを満たしていることを確認します。 **[次へ]** をクリックします。
5. 既定の kubeconfig ファイルを使用するか、別のファイルを選択します。  **[次へ]** をクリックします。
6. Kubernetes クラスター コンテキストを選択します。 **[次へ]** をクリックします。
7. ターゲットの Kubernetes クラスターに応じて、展開構成プロファイルを選択します。 **[次へ]** をクリックします。
8. Azure Red Hat OpenShift または Red Hat OpenShift コンテナー プラットフォームを使用している場合は、セキュリティ コンテキスト制約を適用します。 「[OpenShift で Azure Arc 対応データ サービスのセキュリティ コンテキスト制約を適用する](how-to-apply-security-context-constraint.md)」の手順に従います。

   >[!IMPORTANT]
   >Azure Red Hat OpenShift または Red Hat OpenShift コンテナー プラットフォームでは、データ コントローラーを作成する前にセキュリティ コンテキスト制約を適用する必要があります。

1. 目的のサブスクリプションとリソース グループを選択します。
1. Azure の場所を選択します。
   
   ここで選択した Azure の場所は、データ コントローラーと管理されるデータベース インスタンスに関する *メタデータ* が格納される Azure 内の場所です。 実際には、データ コントローラーとデータベース インスタンスは、その場所がどこにあるのかに関係なく、Kubernetes クラスターに作成されます。

10. 適切な接続モードを選択します。 詳細については、[接続モード](./connectivity.md)に関するページを参照してください。 **[次へ]** をクリックします。

    直接接続モードを選択した場合は、「[サービス プリンシパルの作成](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal)」で説明されているように、サービス プリンシパルの資格情報が必要です。

11. データ コントローラーの名前と、データ コントローラーが作成される名前空間の名前を入力します。

    データ コントローラーと名前空間の名前は、Kubernetes クラスターでカスタム リソースを作成するために使用されるため、[Kubernetes の名前付け規則](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names)に従っている必要があります。
    
    既に名前空間が存在する場合は、名前空間に他の Kubernetes オブジェクト (ポッドなど) がまだ含まれていなければ使用されます。名前空間が存在しない場合は、名前空間の作成が試みられます。  Kubernetes クラスターで名前空間を作成するには、Kubernetes クラスター管理者特権が必要です。  Kubernetes クラスター管理者特権を持っていない場合は、[Kubernetes ネイティブのツールを使用したデータ コントローラーの作成](./create-data-controller-using-kubernetes-native-tools.md)に関する記事の最初のいくつかの手順を実行するように、Kubernetes クラスター管理者に依頼してください。これらの手順は、このウィザードを完了する前に、Kubernetes 管理者が実行する必要があります。


12. データ コントローラーが展開されるストレージ クラスを選択します。 
13.  ユーザー名とパスワードを入力し、データ コントローラー管理者ユーザー アカウントのパスワードを確認します。 **[次へ]** をクリックします。

14. 展開構成を確認します。
15. **[展開]** をクリックして目的の構成を展開するか、または **[ノートブックへのスクリプト]** をクリックして展開手順を確認したり、ストレージ クラス名やサービスの種類などの必要な変更を行ったりします。 ノートブックの上部にある **[すべて実行]** をクリックします。

## <a name="monitoring-the-creation-status"></a>作成状態の監視

コントローラーの作成が完了するまでに数分かかります。 次のコマンドを使用して、別のターミナル ウィンドウで進行状況を監視できます。

> [!NOTE]
>  次のコマンド例では、"arc" という名前のデータ コントローラーと Kubernetes 名前空間が作成されていることを前提としています。  異なる名前の名前空間/データ コントローラーを使用している場合は、"arc" をその名前で置き換えることができます。

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

次のようなコマンドを実行して、特定のポッドの作成状態を確認することもできます。  これは特に、何らの問題をトラブルシューティングするのに役立ちます。

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>作成の問題のトラブルシューティング

作成で問題が発生した場合は、「[トラブルシューティング ガイド](troubleshoot-guide.md)」を参照してください。
