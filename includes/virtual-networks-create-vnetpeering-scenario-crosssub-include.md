## <a name="a-namex-subapeering-across-subscriptions"></a><a name="x-sub"></a>サブスクリプション間のピアリング
このシナリオでは、異なるサブスクリプションに存在する&2; つの VNet 間でピアリングを作成します。

![cross sub scenario](./media/virtual-networks-create-vnetpeering-scenario-crosssub-include/figure01.PNG)

VNet ピアリングにおける承認には、ロールベースのアクセス制御 (RBAC) が使用されています。 異なるサブスクリプション間のシナリオでは、まずピアリング リンクを作成するユーザーに、必要なアクセス許可を与える必要があります。

> [!NOTE]
> 同じユーザーが 2 つのサブスクリプションに対する権限を持っている場合、以下の手順 1. ～ 4. は省略してください。
> 
> 



<!--HONumber=Feb17_HO1-->


