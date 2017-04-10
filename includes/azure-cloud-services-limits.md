| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| [デプロイあたりの Web/worker ロール数](../articles/cloud-services/cloud-services-choose-me.md)<sup>1</sup> |25 |25 |
| [インスタンスの入力エンドポイント](http://msdn.microsoft.com/library/gg557552.aspx#InstanceInputEndpoint) 数 |25 |25 |
| [入力エンドポイント](http://msdn.microsoft.com/library/gg557552.aspx#InputEndpoint) 数 |25 |25 |
| [内部エンドポイント](http://msdn.microsoft.com/library/gg557552.aspx#InternalEndpoint) 数 |25 |25 |

<sup>1</sup>Web/worker ロールの Cloud Service には、それぞれ運用環境用とステージング環境用の 2 つのデプロイを作成することができます。 この制限は、ロールあたりのインスタンス数 (スケール) ではなく、別個のロール (構成) の数を指してることに注意してください。



<!--HONumber=Nov16_HO3-->


