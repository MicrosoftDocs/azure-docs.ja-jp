## <a name="quick-steps"></a>簡単な手順
この記事では、ポータルでサブスクリプションにログインし、Resource Manager デプロイメント モデルを使用して利用可能なイメージで仮想マシンを作成していることを前提としています。 仮想マシンの起動後、次の手順に従います。

1. ポータルで、仮想マシンを選択します。 DNS 名は空白です。 **[パブリック IP アドレス]** をクリックします。
   
   ![ポータルでパブリック IP リソースをクリック](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2. 目的の DNS 名ラベルを入力して、**[保存]** をクリックします。
   
   ![パブリック IP リソースの DNS 名ラベルを入力](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)
   
   これで、パブリック IP リソースのブレードでこの新しい DNS ラベルが表示されるようになりました。

3. パブリック IP のブレードを閉じ、ポータルの仮想マシンの概要のブレードに戻ります。 数秒後に設定がポータルに適用されます。 **[パブリック IP アドレス]** リソースの IP アドレスの横に DNS 名/FQDN が表示されることを確認します。
   
   ![新しい DNS ラベルが設定されていることを確認](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)



<!--HONumber=Nov16_HO3-->


