## <a name="set-up-azure-cli-for-azure-dns"></a>Azure DNS 向け Azure CLI の設定

### <a name="before-you-begin"></a>開始する前に

構成を開始する前に、以下がそろっていることを確認します。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial/)にサインアップしてください。
* Windows、Linux、または MAC 用の最新バージョンの Azure CLI をインストールしてください。 詳しくは、「 [Azure CLI のインストール](../articles/cli-install-nodejs.md)」をご覧ください。

### <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン

コンソール ウィンドウを開き、資格情報を使用して認証を行います。 詳細については、「[Azure CLI から Azure へのログイン](../articles/xplat-cli-connect.md)」をご覧ください。

```azurecli
azure login
```

### <a name="switch-cli-mode"></a>CLI モードの切り替え

Azure DNS では、Azure リソース マネージャーを使用します。 Azure Resource Manager コマンドを使用するように CLI モードを切り替えます。

```azurecli
azure config mode arm
```

### <a name="select-the-subscription"></a>サブスクリプションの選択

アカウントのサブスクリプションを確認します。

```azurecli
azure account list
```

使用する Azure サブスクリプションを選択します。

```azurecli
azure account set "subscription name"
```

### <a name="create-a-resource-group"></a>リソース グループの作成

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 ただし、すべての DNS リソースはグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure DNS には影響しません。

既存のリソース グループを使用する場合は、この手順をスキップしてください。

```azurecli
azure group create -n myresourcegroup --location "West US"
```

### <a name="register-resource-provider"></a>リソース プロバイダーの登録

Azure DNS サービスは Microsoft.Network リソース プロバイダーによって管理されます。 Azure DNS を使用するには、このリソース プロバイダーを使用するように Azure サブスクリプションを登録する必要があります。 この操作は、サブスクリプションごとに&1; 回だけ実行します。

```azurecli
azure provider register --namespace Microsoft.Network
```

