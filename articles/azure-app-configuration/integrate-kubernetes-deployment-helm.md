---
title: Helm を使用して Azure App Configuration を Kubernetes デプロイと統合する
description: Helm を使用した Kubernetes デプロイで動的構成を使用する方法を学習します。
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 4e38366ddcee07f38ca390acf9d580b8764c1c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99979829"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Helm を使用して Kubernetes デプロイと統合する

Helm を使用すると、Kubernetes で実行されるアプリケーションを定義、インストール、およびアップグレードすることができます。 Helm chart には、Kubernetes アプリケーションのインスタンスを作成するために必要な情報が含まれています。 構成は、chart の外部の *values.yaml* という名前のファイルに保存されます。 

リリース プロセス中、Helm により、アプリケーションを実行するために、chart が適切な構成とマージされます。 たとえば、*values.yaml* に定義された変数は、実行中のコンテナー内で環境変数として参照できます。 また、Helm では、データ ボリュームとしてマウントしたり、環境変数として公開したりできる Kubernetes シークレットの作成もサポートされています。

*values.yaml* に格納されている値は、Helm の実行時にコマンド ラインで追加の YAML ベースの構成ファイルを指定することでオーバーライドすることができます。 Azure App Configuration では、構成値の YAML ファイルへのエクスポートがサポートされています。 このエクスポート機能をお使いのデプロイに統合すると、App Configuration に保存されている構成値を Kubernetes アプリケーションで利用できるようになります。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Helm を使用してアプリケーションを Kubernetes にデプロイするときに、App Configuration の値を使用する。
> * App Configuration 内の Key Vault 参照に基づいて Kubernetes シークレットを作成する。

このチュートリアルでは、Helm を使用した Kubernetes の管理に関する基本的な知識があることを前提としています。 [Azure Kubernetes Service](../aks/kubernetes-helm.md) での Helm を使用したアプリケーションのインストールについて詳細を確認します。

## <a name="prerequisites"></a>前提条件

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- [Azure CLI](/cli/azure/install-azure-cli) (バージョン 2.4.0 以降) をインストールします
- [Helm](https://helm.sh/docs/intro/install/) (バージョン 2.14.0 以降) をインストールします
- Kubernetes クラスター。

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **[構成エクスプローラー]**  >  **[作成]** の順に選択して、次のキーと値のペアを追加します。

    | Key | 値 |
    |---|---|
    | settings.color | White |
    | settings.message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

## <a name="add-a-key-vault-reference-to-app-configuration"></a>App Configuration に Key Vault 参照を追加する
1. [Azure portal](https://portal.azure.com) にサインインし、**Password** という名前と **myPassword** という値を持つシークレットを [Key Vault](../key-vault/secrets/quick-create-portal.md#add-a-secret-to-key-vault) に追加します。 
2. 前のセクションで作成した App Configuration ストア インスタンスを選択します。

3. **[構成エクスプローラー]** を選択します。

4. **[+ 作成]** 、 **[キー コンテナー参照]** の順に選択し、次の値を指定します。
    - **[キー]** :**secrets.password** を選択します。
    - **ラベル**:この値は空白のままにしておきます。
    - **[サブスクリプション]** 、 **[リソース グループ]** 、 **[キー コンテナー]** : 前の手順で作成したキー コンテナー内の値に対応する値を入力します。
    - **[シークレット]** : 前のセクションで作成した、**Password** という名前のシークレットを選択します。

## <a name="create-helm-chart"></a>Helm chart を作成する ##
まず、次のコマンドを使用して、サンプルの Helm chart を作成します
```console
helm create mychart
```

Helm により、次に示す構造を持つ、mychart という名前の新しいディレクトリが作成されます。 

> [!TIP]
> この [chart ガイド](https://helm.sh/docs/chart_template_guide/getting_started/)に従って詳細を確認してください。

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

次に、*deployment.yaml* ファイルの **spec:template:spec:containers** セクションを更新します。 次のスニペットでは、2 つの環境変数をコンテナーに追加します。 これらの値は、デプロイ時に動的に設定します。

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

更新後の完全な *deployment.yaml* ファイルは次のようになります。

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

機密データを Kubernetes シークレットとして格納するには、templates フォルダー内に *secrets.yaml* ファイルを追加します。

> [!TIP]
> [Kubernetes シークレット](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)の使用方法の詳細を確認してください。

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password | b64enc }}
```

最後に、*values.yaml* ファイルを次の内容で更新して、*deployment.yaml* および *secrets.yaml* ファイルで参照される構成設定とシークレットの既定値をオプションで指定します。 実際の値は、App Configuration からプルされる構成によって上書きされます。

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Helm インストール内の App Configuration から構成を渡す ##
最初に、App Configuration から *myConfig.yaml* ファイルに構成をダウンロードします。 **settings.** で始まるキーのみをダウンロードするには、キー フィルターを使用します。 Key Vault 参照のキーを除外するのにキー フィルターが十分でない場合は、引数 **--skip-keyvault** を使用してそれらを除外することができます。 

> [!TIP]
> [export コマンド](/cli/azure/appconfig/kv#az-appconfig-kv-export)の詳細を確認してください。 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

次に、*mySecrets.yaml* という名前のファイルにシークレットをダウンロードします。 コマンドライン引数 **--resolve-keyvault** を使用した場合、Key Vault 内の実際の値を取得することによって Key Vault 参照が解決されます。 このコマンドは、対応するキー コンテナーに対するアクセス許可を持つ資格情報を使用して実行する必要があります。

> [!WARNING]
> このファイルには機密情報が含まれているため、取り扱いに注意し、不要になった場合はクリーンアップしてください。

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

helm upgrade の **-f** 引数を使用して、作成した 2 つの構成ファイルを渡します。 これにより、*values.yaml* で定義された構成値が、App Configuration からエクスポートされた値で上書きされます。

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

また、helm upgrade の **--set** 引数を使用して、リテラル キー値を渡すこともできます。 **--set** 引数の使用は、機密データがディスクに保持されないようにするうえで適切な方法です。 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

[Kubernetes ダッシュボード](../aks/kubernetes-dashboard.md)にアクセスして、構成とシークレットが正常に設定されたことを確認します。 App Configuration からの **color** と **message** の値がコンテナーの環境変数に入力されていることがわかります。

![クイック スタートのアプリ (ローカルで起動)](./media/kubernetes-dashboard-env-variables.png)

App Configuration で Key Vault 参照として格納された 1 つのシークレット (**password**) も Kubernetes シークレットに追加されています。 

![データ セクション内のパスワードを強調表示するスクリーンショット。](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Helm を使用した Kubernetes デプロイで使用するために Azure App Configuration データをエクスポートしました。 App Configuration の使用方法の詳細については、Azure CLI のサンプルに進んでください。

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig)
