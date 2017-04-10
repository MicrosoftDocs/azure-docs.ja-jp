1. Azure [管理ポータル](http://manage.windowsazure.com)で、**[仮想マシン]** をクリックし、先ほど作成した仮想マシン (**testlinuxvm**) を選択します。
2. コマンド バーで、**[ディスクの接続]**、**[空のディスクの接続]** の順にクリックします。
   
    **[空のディスクの接続]** ダイアログ ボックスが表示されます。
3. **[仮想マシン名]**、**[ストレージの場所]**、**[ファイル名]** の値は既に定義されています。 必要なのは、ディスクのサイズを入力することだけです。 **[サイズ]** ボックスに「**5**」と入力します。
   
    ![[空のディスクの接続]][Image2]
   
    **注:** ディスクはすべて、.vhd ファイルから Azure ストレージに作成されます。 ストレージに追加する .vhd ファイルの名前は指定できますが、ディスクの名前は Azure によって自動的に生成されます。
4. チェック マークをクリックして、仮想マシンにデータ ディスクを接続します。
5. 仮想マシンの名前をクリックしてダッシュボードを表示します。こうすることでデータ ディスクが仮想マシンに正しく接続されたか確認することができます。 接続したディスクは、**[ディスク]** テーブルに表示されます。
   
    データ ディスクは接続しただけでは使用できません。使用するには、ログインしてセットアップを完了する必要があります。

## <a name="connect-to-the-virtual-machine-using-ssh-or-putty-and-complete-setup"></a>SSH または PuTTY を使用して仮想マシンに接続し、セットアップを完了する
仮想マシンにログオンし、ディスクのセットアップを完了して、ディスクにデータを格納できるようにします。

1. 仮想マシンをプロビジョニングしたら、SSH または PuTTY を使用して接続し、(前の手順に従って) **newuser** としてログインします。    
2. SSH または PuTTY のウィンドウで次のコマンドを入力し、アカウントのパスワードを入力します。
   
    `$ sudo grep SCSI /var/log/messages`
   
    表示されたメッセージで、最後に追加されたデータ ディスクの識別子を確認できます (この例では**sdc**)。
   
    ![GREP][Image4]
3. SSH または PuTTY のウィンドウで次のコマンドを入力し、ディスク **/dev/sdc**をパーティション分割します。
   
    `$ sudo fdisk /dev/sdc`
4. 「 **n** 」を入力して、新しいパーティションを作成します。
   
    ![FDISK][Image5]
5. 「**p**」を入力してパーティションをプライマリ パーティションに指定し、「**1**」を入力して最初のパーティションにします。次に Enter キーを押してシリンダーの既定値 (1) をそのまま使用します。
   
    ![FDISK][Image6]
6. 「 **p** 」を入力すると、パーティション分割されたディスクに関する詳細情報が表示されます。
   
    ![FDISK][Image7]
7. 「 **w** 」と入力すると、ディスクの設定が書き込まれます。
   
    ![FDISK][Image8]
8. **mkfs** コマンドを使用して、新しいディスクをフォーマットします。
   
    `$ sudo mkfs -t ext4 /dev/sdc1`
9. 次に、新しいファイル システムをマウントするために利用可能なディレクトリが必要です。 たとえば、次のコマンドを入力してドライブをマウントする新しいディレクトリを作成し、アカウントのパスワードを入力します。
   
    `sudo mkdir /datadrive`
10. 次のコマンドを入力してドライブをマウントします。
    
    `sudo mount /dev/sdc1 /datadrive`
    
    これで、データ ディスクを **/datadrive**として使用する準備ができました。
11. 新しいドライブを /etc/fstab に追加します。
    
    再起動後にドライブを自動的に再マウントするために、そのドライブを /etc/fstab ファイルに追加する必要があります。 また、ドライブを参照する際に、デバイス名 (つまり /dev/sdc1) だけではなく、UUID (汎用一意識別子) を /etc/fstab で使用することもお勧めします。 新しいドライブの UUID を確認するには、 **blkid** ユーティリティを次のように使用します。
    
        `sudo -i blkid`
    
    次のような出力が表示されます。
    
        `/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"`
        `/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"`
        `/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"`
    
    > [!NOTE]
    > blkid では、すべての場合に sudo アクセスが必要なわけではありませんが、sbin または /usr/sbin が `$PATH` にない場合、ディストリビューションによっては `sudo -i` を使用した方が簡単に実行できる場合があります。
    > 
    > 
    
    **注意事項:** /etc/fstab ファイルを不適切に編集すると、システムが起動できなくなる可能性があります。 編集方法がはっきりわからない場合は、このファイルを適切に編集する方法について、ディストリビューションのドキュメントを参照してください。 編集する前に、/etc/fstab ファイルのバックアップを作成することもお勧めします。
    
    テキスト エディターを使用して、/etc/fstab ファイルの最後の部分に新しいファイル システムに関する情報を入力します。  この例では、前の手順で作成した新しい **/dev/sdc1** デバイスに対して UUID 値を使用し、マウントポイントとして **/datadrive** を使用します。
    
        `UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2`
    
    追加のデータ ドライブやパーティションを作成する場合、それらも /etc/fstab に個別に入力する必要があります。
    
    これで、ファイル システムのマウントを解除してから再マウントするだけで、ファイル システムが正しくマウントされるかどうかをテストできます。つまり、 前の手順で作成したサンプルのマウント ポイント `/datadrive` を使用します。 
    
        `sudo umount /datadrive`
        `sudo mount /datadrive`
    
    2 番目のコマンドでエラーが発生した場合、/etc/fstab ファイルの構文が正しいかどうかを確認してください。

    >[AZURE.NOTE] この後、fstab を編集せずにデータ ディスクを削除すると VM は起動できません。 これが頻繁に発生する場合、大部分のディストリビューションでは `nofail` または `nobootwait` fstab オプションが提供されます。これによって、ディスクがなくてもシステムを起動することができます。 これらのパラメーターの詳細については、使用しているディストリビューションのドキュメントを参照してください。


[Image2]: ./media/attach-data-disk-centos-vm-in-portal/AttachDataDiskLinuxVM2.png
[Image4]: ./media/attach-data-disk-centos-vm-in-portal/GrepScsiMessages.png
[Image5]: ./media/attach-data-disk-centos-vm-in-portal/fdisk1.png
[Image6]: ./media/attach-data-disk-centos-vm-in-portal/fdisk2.png
[Image7]: ./media/attach-data-disk-centos-vm-in-portal/fdisk3.png
[Image8]: ./media/attach-data-disk-centos-vm-in-portal/fdisk4.png
[Image9]: ./media/attach-data-disk-centos-vm-in-portal/mkfs.png



<!--HONumber=Jan17_HO3-->


