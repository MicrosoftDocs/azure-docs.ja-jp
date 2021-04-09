---
title: Azure Red Hat OpenShift 4 クラスターで Red Hat プル シークレットを追加または更新する
description: 既存の4.x ARO クラスターで Red Hat プル シークレットを追加または更新する
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 05/21/2020
keywords: プル シークレット、aro、openshift、Red Hat
ms.openlocfilehash: 58c0eb2be3423783a69d005277ffe75aaf59415f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100633735"
---
# <a name="add-or-update-your-red-hat-pull-secret-on-an-azure-red-hat-openshift-4-cluster"></a>Azure Red Hat OpenShift 4 クラスターで Red Hat プル シークレットを追加または更新する

このガイドでは、既存の Azure Red Hat OpenShift (ARO) 4.x クラスターでの Red Hat プル シークレットの追加および更新について説明します。

クラスターを初めて作成する場合は、クラスターの作成時にプル シークレットを追加できます。 Red Hat プル シークレットを使用して ARO クラスターを作成する方法の詳細については、「[Azure Red Hat OpenShift 4 クラスターを作成する](tutorial-create-cluster.md#get-a-red-hat-pull-secret-optional)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

このガイドでは、既存の Azure Red Hat OpenShift 4 クラスターがあることを前提としています。 クラスターへの管理者アクセス権を持っていることを確認します。

## <a name="prepare-your-pull-secret"></a>プル シークレットを準備する
Red Hat プル シークレットを追加せずに ARO クラスターを作成しても、自動的にクラスターでプル シークレットが作成されます。 ただし、このプル シークレットは完全に設定されていません。

このセクションでは、Red Hat プル シークレットからの追加の値でプル シークレットを更新する手順について説明します。

1. 次のコマンドを実行して、`openshift-config` 名前空間で `pull-secret` という名前のシークレットをフェッチし、個別のファイルに保存します。 

    ```console
    oc get secrets pull-secret -n openshift-config -o template='{{index .data ".dockerconfigjson"}}' | base64 -d > pull-secret.json
    ```

    出力は次のようになります。 (実際のシークレット値は削除されていることに注意してください。)

    ```json
    {
        "auths": {
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

2. [Red Hat OpenShift クラスター マネージャー ポータル](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)に移動して、 **[Download pull secret]\(プル シークレットのダウンロード\)** を選択します。 Red Hat プル シークレットは、次のようになります。 (実際のシークレット値は削除されていることに注意してください。)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            }
        }
    }
    ```

3. Red Hat プル シークレットで見つかったエントリを追加して、クラスターから取得したプル シークレット ファイルを編集します。 

    > [!IMPORTANT]
    > Red Hat プル シークレットからの `cloud.openshift.com` エントリを含めると、クラスターで Red Hat への利用統計情報の送信が開始されます。 このセクションは、利用統計情報を送信する場合にのみ含めてください。 それ以外の場合は、次のセクションを省略してください。    
    > ```json
    > {
    >         "cloud.openshift.com": {
    >             "auth": "<my-crc-secret>",
    >             "email": "klamenzo@redhat.com"
    >         }
    > ```

    > [!CAUTION]
    > プル シークレットからの `arosvc.azurecr.io` エントリは削除したり、変更したりしないでください。 このセクションは、クラスターが正常に機能するために必要です。

    ```json
    "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
    ```

    最終ファイルは次のようになります。 (実際のシークレット値は削除されていることに注意してください。)

    ```json
    {
        "auths": {
            "cloud.openshift.com": {
                "auth": "<my-crc-secret>",
                "email": "klamenzo@redhat.com"
            },
            "quay.io": {
                "auth": "<my-quayio-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.connect.redhat.com": {
                "auth": "<my-registry.connect.redhat.com-secret>",
                "email": "klamenzo@redhat.com"
            },
            "registry.redhat.io": {
                "auth": "<my-registry.redhat.io-secret>",
                "email": "klamenzo@redhat.com"
            },
            "arosvc.azurecr.io": {
                "auth": "<my-aroscv.azurecr.io-secret>"
            }
        }
    }
    ```

4. 有効な JSON ファイルであることを確認してください。 JSON を検証するには、さまざまな方法があります。 次の例では、jq を使用します。

    ```json
    cat pull-secret.json | jq
    ```

    > [!NOTE]
    > ファイル内にエラーがある場合は、`parse error` が表示されます。

## <a name="add-your-pull-secret-to-your-cluster"></a>クラスターにプル シークレットを追加する

次のコマンドを実行して、プル シークレットを追加します。

> [!NOTE]
> このコマンドを実行すると、クラスター ノードが更新され、その都度 1 つずつ再起動されます。 

```console
oc set data secret/pull-secret -n openshift-config --from-file=.dockerconfigjson=./pull-secret.json
```

シークレットが設定されると、Red Hat 認定オペレーターを有効にする準備が整います。

### <a name="modify-the-configuration-files"></a>構成ファイルを変更します。

Red Hat オペレーターが有効になるように、次のオブジェクトを変更します。

最初に、サンプル オペレーター構成ファイルを変更します。 次に、次のコマンドを実行すると、構成ファイルを編集できます。

```
oc edit configs.samples.operator.openshift.io/cluster -o yaml
```

`spec.architectures.managementState` 値と `status.architecture.managementState` 値を `Removed` から `Managed` に変更します。 

次の YAML スニペットには、編集された YAML ファイルの関連セクションのみが表示されています。

```yaml
apiVersion: samples.operator.openshift.io/v1
kind: Config
metadata:
  
  ...
  
spec:
  architectures:
  - x86_64
  managementState: Managed
status:
  architectures:

  ...

  managementState: Managed
  version: 4.3.27
```

次に、次のコマンドを実行して、オペレーター ハブ構成ファイルを編集します。  

```console
oc edit operatorhub cluster -o yaml
```

有効にする任意のソースに対して、`Spec.Sources.Disabled` 値と `Status.Sources.Disabled` 値を `true` から `false` に変更します。

次の YAML スニペットには、編集された YAML ファイルの関連セクションのみが表示されています。

```yaml
Name:         cluster

...
                 dd3310b9-e520-4a85-98e5-8b4779ee0f61
Spec:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Disabled:  false
    Name:      redhat-operators
Status:
  Sources:
    Disabled:  false
    Name:      certified-operators
    Status:    Success
    Disabled:  false
    Name:      community-operators
    Status:    Success
    Disabled:  false
    Name:      redhat-operators
    Status:    Success
Events:        <none>
```

ファイルを保存して、編集を適用します。

## <a name="validate-that-your-secret-is-working"></a>シークレットが動作していることを確認する

プル シークレットを追加し、適切な構成ファイルを変更した後は、クラスターが更新されるまで数分かかることがあります。 クラスターが更新されたことを確認するには、次のコマンドを実行して、使用可能な認定オペレーターと Red Hat オペレーターのソースを表示します。

```console
$ oc get catalogsource -A
NAMESPACE               NAME                  DISPLAY               TYPE   PUBLISHER   AGE
openshift-marketplace   certified-operators   Certified Operators   grpc   Red Hat     10s
openshift-marketplace   community-operators   Community Operators   grpc   Red Hat     18h
openshift-marketplace   redhat-operators      Red Hat Operators     grpc   Red Hat     11s
```

認定オペレーターと Red Hat オペレーターが表示されない場合は、しばらく待ってから再度試してください。

プル シークレットが更新され、正常に動作していることを確認するには、OperatorHub を開き、Red Hat で確認されたオペレーターについて確認します。 たとえば、OpenShift Container Storage オペレーターが使用可能かどうかを確認し、インストールするためのアクセス許可を持っているかどうかを確認します。

## <a name="next-steps"></a>次のステップ
Red Hat プル シークレットの詳細については、「[イメージ プル シークレットの使用](https://docs.openshift.com/container-platform/4.6/openshift_images/managing_images/using-image-pull-secrets.html)」を参照してください。

Red Hat OpenShift 4 の詳細については、[Red Hat OpenShift Container Platform に関するドキュメント](https://docs.openshift.com/container-platform/4.6/welcome/index.html)を参照してください。
