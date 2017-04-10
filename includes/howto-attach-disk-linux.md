
ディスクの詳細については、[仮想マシン用のディスクと VHD](../articles/storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) に関するページを参照してください。

<a id="attachempty"></a>

## <a name="attach-an-empty-disk"></a>Attach an empty disk
1. Azure CLI 1.0 を開いて、[Azure サブスクリプションに接続](../articles/xplat-cli-connect.md)します。 Azure サービス管理モード (`azure config mode asm`) であることを確認します。
2. 次の例に示すように「`azure vm disk attach-new`」と入力し、新しいディスクを作成して接続します。 *myVM* は Linux 仮想マシンの名前で置き換え、ディスクのサイズを GB 単位で指定します (この例では *100 GB*)。

    ```azurecli
    azure vm disk attach-new myVM 100
    ```

3. データ ディスクが作成されて接続されると、次の例に示すように `azure vm disk list <virtual-machine-name>` の出力に表示されます。
   
    ```azurecli
    azure vm disk list TestVM
    ```

    出力は次の例のようになります。

    ```bash
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        myVM-2645b8030676c8f8.vhd  Linux
     data:    0    100       myVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

<a id="attachexisting"></a>

## <a name="attach-an-existing-disk"></a>既存のディスクの接続
既存のディスクを接続する場合は、ストレージ アカウントで利用できる .vhd を持っている必要があります。

1. Azure CLI 1.0 を開いて、[Azure サブスクリプションに接続](../articles/xplat-cli-connect.md)します。 Azure サービス管理モード (`azure config mode asm`) であることを確認します。
2. 接続する VHD が Azure サブスクリプションに既にアップロードされているかどうかを確認します。
   
    ```azurecli
    azure vm disk list
    ```

    出力は次の例のようになります。

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
     data:    Name                                          OS
     data:    --------------------------------------------  -----
     data:    myTestVhd                                     Linux
     data:    TestVM-ubuntuVMasm-0-201508060029150744  Linux
     data:    TestVM-ubuntuVMasm-0-201508060040530369
     info:    vm disk list command OK
    ```

3. 使用するディスクが見つからない場合は、`azure vm disk create` または `azure vm disk upload` を使用してローカル VHD をサブスクリプションにアップロードできます。 `disk create` の例は、次のようになります。
   
    ```azurecli
    azure vm disk create myVhd .\TempDisk\test.VHD -l "East US" -o Linux
    ```

    出力は次の例のようになります。

    ```azurecli
    info:    Executing command vm disk create
    + Retrieving storage accounts
    info:    VHD size : 10 GB
    info:    Uploading 10485760.5 KB
    Requested:100.0% Completed:100.0% Running:   0 Time:   25s Speed:    82 KB/s
    info:    Finishing computing MD5 hash, 16% is complete.
    info:    https://mystorageaccount.blob.core.windows.net/disks/myVHD.vhd was
    uploaded successfully
    info:    vm disk create command OK
    ```
   
   `azure vm disk upload` を使用して、特定のストレージ アカウントに VHD をアップロードすることもできます。 Azure 仮想マシン データ ディスクの管理用コマンドの詳細については、[こちら](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)を参照してください。

4. ここで、必要な VHD を仮想マシンに接続します。
   
    ```azurecli
    azure vm disk attach myVM myVhd
    ```
   
   *myVM* は仮想マシンの名前に、*myVHD* は目的の VHD に置き換えてください。

5. ディスクが仮想マシンに接続されたことを確認するには、 `azure vm disk list <virtual-machine-name>`を使用します。
   
    ```azurecli
    azure vm disk list myVM
    ```

    出力は次の例のようになります。

    ```azurecli
     info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        TestVM-2645b8030676c8f8.vhd  Linux
     data:    1    10        test.VHD
     data:    0    100        TestVM-76f7ee1ef0f6dddc.vhd
     info:    vm disk list command OK
    ```

> [!NOTE]
> データ ディスクを追加した後、仮想マシンがストレージとしてそのディスクを使用できるように、仮想マシンにログオンしてディスクを初期化する必要があります (ディスクを初期化する方法の詳細については次の手順を参照)。
> 
> 

