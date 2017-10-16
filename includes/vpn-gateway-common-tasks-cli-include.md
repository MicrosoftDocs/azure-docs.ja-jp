### <a name="to-view-local-network-gateways"></a>ローカル ネットワーク ゲートウェイを表示するには

ローカル ネットワーク ゲートウェイの一覧を表示するには、[az network local-gateway list](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_list) コマンドを使用します。

```azurecli
az network local-gateway list --resource-group TestRG1
```

[!INCLUDE [modify-prefix](vpn-gateway-modify-ip-prefix-cli-include.md)]

[!INCLUDE [modify-gateway-IP](vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

### <a name="to-verify-the-shared-key-values"></a>共有キーの値を確認するには

共有キーの値が VPN デバイスの構成に使用したものと同じ値であることを確認します。 異なる場合は、デバイスの値を使用して接続をもう一度実行するか、返された値でデバイスを更新します。 値は一致する必要があります。 共有キーを表示するには、[az network vpn-connection-list](https://docs.microsoft.com/cli/azure/network/vpn-connection#az_network_vpn_connection_list) を使用します。

```azurecli
az network vpn-connection shared-key show --connection-name VNet1toSite2 --resource-group TestRG1
```
### <a name="to-view-the-vpn-gateway-public-ip-address"></a>VPN ゲートウェイのパブリック IP アドレスを表示するには

仮想ネットワーク ゲートウェイのパブリック IP アドレスを探すときには、[az network public-ip list](https://docs.microsoft.com/cli/azure/network/public-ip#az_network_public_ip_list) コマンドを使用します。 読みやすいように、この例の出力には書式を設定し、パブリック IP の一覧が表形式で表示されるようにしてあります。

```azurecli
az network public-ip list --resource-group TestRG1 --output table
```
