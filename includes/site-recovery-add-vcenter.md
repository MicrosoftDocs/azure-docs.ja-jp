* **[Add vCenter (vCenter の追加)]** で、vSphere ホストまたは vCenter サーバーのフレンドリ名を指定し、サーバーの IP アドレスまたは FQDN を指定します。 別のポートで要求をリッスンするように VMware サーバーが構成されている場合を除き、ポートは 443 のままにしておきます。 VMware vCenter または vSphere ESXi サーバーに接続するためのアカウントを選択します。 **[OK]**をクリックします。

    ![VMware](./media/site-recovery-add-vcenter/vmware-server.png)

   > [!NOTE]
   > vCenter サーバーまたはホスト サーバーに対する管理者特権のないアカウントを使用して VMware vCenter サーバーまたは VMware vSphere ホストを追加する場合、そのアカウントで、Datacenter、Datastore、Folder、Host、Network、Resource、Virtual Machine、vSphere Distributed Switch の各特権が有効になっていることを確認します。 さらに、VMware vCenter サーバーは Storage ビューの特権が有効になっている必要があります。


<!--HONumber=Feb17_HO2-->


