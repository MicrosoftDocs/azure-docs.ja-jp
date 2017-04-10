## <a name="highly-available-solutions-with-azure-traffic-manager"></a>Azure Traffic Manager での高可用性ソリューション
Azure Traffic Manager を単独で使用してワークロードの高可用性の要件を満たすことができるのか、または他の DNS ソリューションやプロセスとトラフィック マネージャーを組み合わせる必要があるかどうかを確認する必要があります。 ニーズに応じて、以下のサービスを使用できます。

* **Traffic Manager 単独**。 ワークロードにとって 99.99% の稼働時間で十分であれば、Traffic Manager を単独で使用できます。 Traffic Manager サービスで障害が発生した場合、Traffic Manager サービスが再び確立されるまでユーザーはワークロードにアクセスできません。
* **Azure Traffic Manager と別のトラフィック マネージャー ソリューションを併用する**。 Traffic Manager サービスで障害が発生した場合、別のトラフィック マネージャー サービスを参照するように CNAME レコードを変更できます。 ワークロードへのアクセスは引き続き使用可能であり、ワークロードをホストしているすべての場所に分散されます。 これは最も高価なソリューションですが、より高度な SLA が必要なワークロードでは要求される場合があります。



<!--HONumber=Jan17_HO3-->


