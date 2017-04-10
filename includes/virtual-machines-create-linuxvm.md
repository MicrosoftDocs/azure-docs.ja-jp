
1. [Azure CLI 1.0 からの Azureへの接続](../articles/xplat-cli-connect.md)に関する記事で示されている手順を使用して、Azure サブスクリプションにサインインします。

2. 以下を使用して、クラシック デプロイ モードであることを確認します。

    ```azurecli
    azure config mode asm
    ```

3. 以下を使用して、利用可能なイメージからロードする Linux イメージを探します。

   ```azurecli   
    azure vm image list | grep "Linux"
    ```
   
    Windows コマンド プロンプト ウィンドウでは、grep ではなく **find** を使用します。
   
4. `azure vm create` を使用して、上記のリストの Linux イメージで新しい仮想マシンを作成します。 この手順では、クラウド サービスとストレージ アカウントが作成されます。 `-c` オプションを使用して、この VM を既存のクラウド サービスに接続することもできます。 `-e` オプションを使用して、Linux 仮想マシンにログインするための SSH エンドポイントを作成します。 次の例では、`West US` の場所にある `Ubuntu-14_04_4-LTS` イメージを使用して、`myVM` という名前の VM を作成し、ユーザー名 `ops` を追加します。
   
    ```azurecli
    azure vm create myVM \
        b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB \
        -g ops -p P@ssw0rd! -z "Small" -e -l "West US"
    ```

    出力は次の例のようになります。

    ```azurecli
    info:    Executing command vm create
    + Looking up image b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-14_04_4-LTS-amd64-server-20160516-en-us-30GB
    + Looking up cloud service
    info:    cloud service myVM not found.
    + Creating cloud service
    + Retrieving storage accounts
    + Creating VM
    info:    vm create command OK
    ```
   
   > [!NOTE]
   > Linux 仮想マシンの場合は、`vm create` で `-e` オプションを指定する必要があります。 仮想マシンを作成した後に SSH を有効にすることはできません。 SSH の詳細については、[Azure 上の Linux における SSH の使用方法](../articles/virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

5. `azure vm show` コマンドを使用して、VM の属性を確認できます。 次の例では、`myVM` という名前の VM の情報が一覧表示されます。

    ```azurecli   
    azure vm show myVM
    ```

6. 次のように、`azure vm start` コマンド使用して VM を起動します。

    ```azurecli
    azure vm start myVM
    ```

## <a name="next-steps"></a>次のステップ
これらすべての Azure CLI 1.0 仮想マシン コマンドについて詳しくは、[クラシック デプロイメント API での Azure CLI 1.0 コマンドの使用](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)に関する記事をご覧ください。

