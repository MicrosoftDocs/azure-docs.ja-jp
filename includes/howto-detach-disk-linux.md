仮想マシン (VM) に接続されたデータ ディスクが不要になった場合、そのディスクは簡単に切断できます。 VM からディスクを切断しても、ディスクはストレージから削除されません。 再びディスク上の既存のデータを使用する場合は、同じ VM や別の仮想マシンに再接続できます。  

> [!NOTE]
> Azure の VM では、オペレーティング システム ディスク、ローカル一時ディスク、オプションのデータ ディスクなど、さまざまな種類のディスクが使用されます。 詳細については、[仮想マシン用のディスクと VHD](../articles/storage/storage-about-disks-and-vhds-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) に関するページを参照してください。 VM を削除しない限り、オペレーティング システム ディスクを切断することはできません。

## <a name="find-the-disk"></a>ディスクの特定
VM からディスクを切断するには、まず切断するディスクの識別子である LUN 番号を確認する必要があります。 そのためには、次の手順に従います。

1. Azure CLI を開いて、 [Azure サブスクリプションに接続](../articles/xplat-cli-connect.md)します。 Azure サービス管理モード (`azure config mode asm`) であることを確認します。
2. VM に接続されているディスクを確認します。 次の例では、`myVM` という名前の VM のディスクが一覧表示されます。

    ```azurecli
    azure vm disk list myVM
    ```

    出力は次の例のようになります。

    ```azurecli
    * Fetching disk images
    * Getting virtual machines
    * Getting VM disks
      data:    Lun  Size(GB)  Blob-Name                         OS
      data:    ---  --------  --------------------------------  -----
      data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
      data:    0    30        myDataDisk.vhd
      info:    vm disk list command OK
    ```

3. 切断するディスクの LUN ( **論理ユニット番号** ) を記録しておきます。

## <a name="remove-operating-system-references-to-the-disk"></a>ディスクへのオペレーティング システム参照の削除
Linux ゲストからディスクを切断する前に、ディスク上に使用されているパーティションがないことを確認します。 また、再起動後にオペレーティング システムがパーティションを再マウントしないようにします。 次の手順により、ディスクの[接続](../articles/virtual-machines/linux/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)時に作成された可能性がある構成が元に戻ります。

1. `lsscsi` コマンドを使用してディスク識別子を検出します。 `lsscsi` は、`yum install lsscsi` (Red Hat ベースのディストリビューション) または `apt-get install lsscsi` (Debian ベースのディストリビューション) のいずれかでインストールできます。 目的のディスク識別子を見つけるには、LUN 番号を使用します。 各行の組にある最後の数字が LUN です。 `lsscsi` の次の例では、LUN 0 が */dev/sdc* にマップされます。

    ```bash
    [1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
    [2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
    [3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
    [5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
    ```

2. `fdisk -l <disk>` を使用して、切断するディスクに関連付けられているパーティションを検出します。 次の例は、`/dev/sdc` の出力を示します。

    ```bash
    Disk /dev/sdc: 1098.4 GB, 1098437885952 bytes, 2145386496 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk label type: dos
    Disk identifier: 0x5a1d2a1a
    
        Device Boot      Start         End      Blocks   Id  System
    /dev/sdc1            2048  2145386495  1072692224   83  Linux
    ```

3. ディスクに対して表示されている各パーティションのマウントを解除します。 次の例では、`/dev/sdc1` のマウントを解除します。

    ```bash
    sudo umount /dev/sdc1
    ```

4. `blkid` コマンドを使用して、すべてのパーティションの UUID を検出します。 出力は次の例のようになります。

    ```bash
    /dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
    /dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
    /dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
    ```

5. 切断するディスクのすべてのパーティションに対する、デバイス パスまたは UUID のいずれかに関連付けられている **/etc/fstab** ファイルのエントリを削除します。  この例のエントリは次のようになります。

    ```sh  
   UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2
   ```

    または
   
   ```sh   
   /dev/sdc1   /datadrive   ext4   defaults   1   2
   ```

## <a name="detach-the-disk"></a>ディスクの切断
ディスクの LUN 番号を見つけて、オペレーティング システム参照を削除したら、切断する準備はできています。

1. `azure vm disk detach
   <virtual-machine-name> <LUN>` コマンドを実行して、選択したディスクを仮想マシンから切断します。 次の例では、`myVM` という名前の VM から LUN `0` を切断します。
   
    ```azurecli
    azure vm disk detach myVM 0
    ```

2. ディスクが切断されたかどうかは、もう一度 `azure vm disk list` を実行して確認できます。 次の例では、`myVM` という名前の VM をチェックします。
   
    ```azurecli
    azure vm disk list myVM
    ```

    出力は次の例のようになります。データ ディスクがもう接続されていないことがわかります。

    ```azurecli
    info:    Executing command vm disk list
   
   * Fetching disk images
   * Getting virtual machines
   * Getting VM disks
     data:    Lun  Size(GB)  Blob-Name                         OS
     data:    ---  --------  --------------------------------  -----
     data:         30        ubuntuVM-2645b8030676c8f8.vhd  Linux
     info:    vm disk list command OK
    ```

切断したディスクはストレージに残りますが、仮想マシンには接続されなくなっています。

