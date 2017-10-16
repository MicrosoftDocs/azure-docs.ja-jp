Azure Redis Cache のインスタンスに接続するには、キャッシュ クライアントにキャッシュのホスト名、ポート、およびキーが必要です。 一部のクライアントは、若干異なる名前で、これらの項目を参照できます。 この情報は、Azure Portal で取得するか、Azure CLI などのコマンドライン ツールを使用して取得することができます。

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Azure Portal を使用して、ホスト名、ポート、およびアクセス キーを取得する
Azure Portal を使用してホスト名、ポート、およびアクセス キーを取得するには、[Azure Portal](https://portal.azure.com) でキャッシュを[参照](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings)し、**[リソース]** メニューで **[アクセス キー]** および **[プロパティ]** をクリックします。 

![Redis cache settings](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Azure CLI を使用して、ホスト名、ポート、およびアクセス キーを取得する
Azure CLI 2.0 を使用してホスト名とポートを取得するには [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) を呼び出し、キーを取得するには [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) を呼び出します。 次のスクリプトでは、これらの 2 つのコマンドを呼び出し、コンソールにホスト名、ポート、およびキーを表示します。

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

このスクリプトの詳細については、「[Get the hostname, ports, and keys for Azure Redis Cache (Azure Redis Cache のホスト名、ポート、およびキーを取得する)](../articles/redis-cache/scripts/cache-keys-ports.md)」を参照してください。 Azure CLI 2.0 の詳細については、「[Install Azure CLI 2.0 (Azure CLI 2.0 のインストール)](https://docs.microsoft.com/cli/azure/install-azure-cli)」と「[Get started with Azure CLI 2.0 (Azure CLI 2.0 の使用開始)](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)」を参照してください。
