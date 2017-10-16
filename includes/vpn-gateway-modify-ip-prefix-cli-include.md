### <a name="noconnection"></a>ローカル ネットワーク ゲートウェイ IP アドレスのプレフィックスを変更するには (ゲートウェイに接続していない場合)

ゲートウェイに接続していない場合に IP アドレスのプレフィックスを追加または削除するには、ローカル ネットワーク ゲートウェイの作成に使用するコマンド [az network local-gateway create](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_create) を使います。 このコマンドは、VPN デバイスのゲートウェイ IP アドレスの更新にも使用できます。 現在の設定を上書きするには、ローカル ネットワーク ゲートウェイの既存の名前を使用します。 別の名前を使用した場合には、既存のゲートウェイが上書きされずに、新しいローカル ネットワーク ゲートウェイが作成されます。

変更を加えるたびに、変更するプレフィックスだけでなく、プレフィックスの一覧全体を指定する必要があります。 今回は、保持する必要があるプレフィックスだけを指定します。 今回の場合、10.0.0.0/24 と 20.0.0.0/24 です。

```azurecli
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --connection-name TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

### <a name="withconnection"></a>ローカル ネットワーク ゲートウェイ IP アドレスのプレフィックスを変更するには (ゲートウェイに接続している場合)

ゲートウェイに接続している状態で IP アドレスのプレフィックスを追加または削除する場合には、[az network local-gateway update](https://docs.microsoft.com/cli/azure/network/local-gateway#az_network_local_gateway_update) を使ってプレフィックスを更新します。 これに伴い、VPN 接続のためにある程度のダウンタイムが発生します。 IP アドレスのプレフィックスを変更するときに、VPN ゲートウェイを削除する必要はありません。

変更を加えるたびに、変更するプレフィックスだけでなく、プレフィックスの一覧全体を指定する必要があります。 この例では、10.0.0.0/24 と 20.0.0.0/24 が既に存在します。 更新にあたっては、追加する 30.0.0.0/24 と 40.0.0.0/24 も含め、プレフィックスを 4 つともすべて指定します。

```azurecli
az network local-gateway update --local-address-prefixes 10.0.0.0/24 20.0.0.0/24 30.0.0.0/24 40.0.0.0/24 --name VNet1toSite2 --connection-name TestRG1
```