## <a name="using-azure-portal"></a>Azure Portal の使用
1. 再デプロイする VM を選択し、[設定] ブレードで [再デプロイ] をクリックします。 次の例に示すように、[再デプロイ] が含まれる **[サポート + トラブルシューティング]** セクションが表示されるまでスクロールします。
   
    ![Azure VM ブレード](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. [再デプロイ] をクリックして、操作を確定します。
   
    ![VM の再デプロイ ブレード](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. VM が再デプロイの準備に入ると、次の例に示すように VM の **[ステータス]** が *"更新中"* に変わります。
   
    ![VM 更新中](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. VM が新しい Azure ホスト上で起動すると、次の例に示すように VM の **ステータス** が *"開始中"* に変わります。
   
    ![VM 開始中](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. VM の起動プロセスが完了すると、**ステータス**は *"実行中"* に戻ります。これは、VM が適切に再デプロイされたことを示します。
   
    ![VM 実行中](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

