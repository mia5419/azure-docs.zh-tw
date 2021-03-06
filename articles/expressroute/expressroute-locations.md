---
title: "ExpressRoute 位置 | Microsoft Docs"
description: "本文提供提供服務所在位置以及如何連線到 Azure 區域的詳細概觀。"
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/18/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: dcee5436a7bb377f37d6db2701c2e5df2e41944f
ms.openlocfilehash: e7a511efdf89d299ba309b9a812092411a55c08d


---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute 合作夥伴和對等互連位置
本文中的資料表會提供有關 ExpressRoute 連線提供者、ExpressRoute 地理涵蓋範圍、透過 ExpressRoute 支援的 Microsoft 雲端服務，以及 ExpressRoute 系統整合者 (SI) 的資訊。

## <a name="a-namepartnersaexpressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 連線提供者
所有的 Azure 區域和位置都支援 ExpressRoute。 以下地圖提供了 Azure 區域和 ExpressRoute 位置的清單。 ExpressRoute 位置是指 Microsoft 與數個服務提供者對等互連的位置。

![位置圖][0]

如果您至少與地緣政治區域內的一個 ExpressRoute 位置連線，您將有權存取地緣政治區域內所有區域中的 Azure 服務。

### <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a>地緣政治區域內 ExpressRoute 位置的 Azure 區域。
下表提供地緣政治區域內 ExpressRoute 位置的 Azure 區域對應。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- |
| **北美洲** |美國東部、美國西部、美國東部 2、美國西部 2、美國中部、美國中南部、美國中北部、美國中西部、加拿大中部、加拿大東部 |亞特蘭大、芝加哥、達拉斯、拉斯維加斯、洛杉磯、紐約、西雅圖、矽谷、華盛頓特區、蒙特婁+、魁北克市+、多倫多 |
| **南美洲** |巴西南部 |聖保羅 |
| **歐洲** |北歐、西歐、英國西部、英國南部 |阿姆斯特丹、都柏林、倫敦、紐波特 (威爾斯)、巴黎 |
| **亞洲** |東亞、東南亞 |香港特別行政區、新加坡 |
| **日本** |日本西部、日本東部 |大阪、東京 |
| **澳大利亞** |澳洲東南部、澳洲東部 |墨爾本、雪梨 |
| **印度** |印度西部、印度中部、印度南部 |辰內，孟買 |
| **南韓** | 韓國中部、韓國南部 | 釜山、首爾 |

### <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>國家雲端的區域和地理政治界限
下表提供國家雲端的區域和地理政治界限等資訊。

| **地緣政治區域** | **Azure 區域** | **ExpressRoute 位置** |
| --- | --- | --- | --- |
| **美國政府雲端** |美國愛荷華州政府、美國維吉尼亞州政府、美國國防部中部+、美國國防部東部+  |芝加哥、達拉斯、紐約、矽谷+、華盛頓特區 |
| **中國** |中國北部、中國東部 |北京、上海 |
| **德國** |德國中部、德國東部 |柏林、法蘭克福 |

標準 ExpressRoute SKU 不支援跨地緣政治區域的連線。 您必須啟用 ExpressRoute 進階附加元件，以支援全球連線。 不支援連線至國家雲端環境。 如果有需要的話，您可以聯絡您的連線提供者。

## <a name="a-namelocationsaconnectivity-provider-locations"></a><a name="locations"></a>連線提供者位置
> [!div class="op_single_selector"]
> * [依提供者的位置](expressroute-locations.md#locations)
> * [依位置的提供者](expressroute-locations-providers.md#locations)
>
>

### <a name="production-azure"></a>生產 Azure
| **服務提供者** | **Microsoft Azure** | **Office 365 和 CRM Online** | **位置** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |支援 |支援 |墨爾本、雪梨 |
| **[Aryaka Networks](http://www.aryaka.com/)** |支援 |支援 |阿姆斯特丹、達拉斯、矽谷、新加坡、東京、華盛頓特區 |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |支援 |支援 |阿姆斯特丹、芝加哥、達拉斯、倫敦、矽谷、新加坡、雪梨、華盛頓特區 |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |支援 |支援 |多倫多 |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |支援 |支援 |阿姆斯特丹、香港、倫敦、矽谷、新加坡、雪梨、東京、華盛頓特區 |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |敬請期待 |敬請期待 |矽谷 |
| **China Telecom Global** |支援 |不支援 |香港 |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |支援 |支援 |達拉斯、蒙特婁、多倫多 |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |支援 |支援 |阿姆斯特丹、都柏林、倫敦、東京 |
| **Comcast** |支援 |支援 |芝加哥、矽谷、華盛頓特區 |
| **Console**| 支援 | 支援 |矽谷 |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |支援 |支援 |洛杉磯、紐約 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |支援 |支援 |阿姆斯特丹、亞特蘭大、芝加哥、達拉斯、香港、倫敦、洛杉磯、墨爾本、紐約、大阪、巴黎+、聖保羅、西雅圖、矽谷、新加坡、雪梨、東京、多倫多、華盛頓特區 |
| **euNetworks** |支援 |支援 |阿姆斯特丹 |
| **GÉANT** |支援 |支援 |阿姆斯特丹 |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |支援 |支援 |大阪、東京 |
| **[InterCloud](https://www.intercloud.com/)** |支援 |支援 |阿姆斯特丹、倫敦、新加坡、華盛頓特區 |
| **Internet Solutions - Cloud Connect** |支援 |支援 |阿姆斯特丹、倫敦 |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)** |支援 |支援 |阿姆斯特丹、倫敦、巴黎 |
| **Jisc** |支援 |支援 |倫敦 |
| **[KPN](http://www.kpn.com/cloudconnect)** | 支援 | 支援 | 阿姆斯特丹 | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |支援 |支援 |阿姆斯特丹、芝加哥、達拉斯、拉斯維加斯+、倫敦、西雅圖、矽谷、新加坡、華盛頓特區 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支援 |支援 |達拉斯、香港、拉斯維加斯、洛杉磯、墨爾本、紐約、西雅圖、新加坡、雪梨、多倫多、華盛頓特區 |
| **MTN** |支援 |支援 |倫敦 |
| **[新一代資料](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |支援 |支援 |Newport(Wales) |
| **NEXTDC** |支援 |支援 |墨爾本、雪梨 |
| **NTT Communications** |支援 |支援 |倫敦、洛杉磯、大阪、新加坡、東京、華盛頓特區 |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |支援 |支援 |阿姆斯特丹、香港、倫敦、矽谷、新加坡、雪梨、華盛頓特區 |
| **PCCW Global Limited** |支援 |支援 |香港 |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |支援 |支援 |辰內 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |支援 |支援 |新加坡 |
| **Softbank** |支援 |支援 |大阪、東京 |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |支援 |支援 |阿姆斯特丹、辰內、香港、倫敦、孟買、矽谷、新加坡、華盛頓特區 |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |支援 |支援 |阿姆斯特丹、都柏林、倫敦 |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |支援 |支援 |聖保羅 |
| **Telenor** |支援 |支援 |阿姆斯特丹、倫敦 |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |支援 |支援 |墨爾本、雪梨 |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |支援 |支援 |阿姆斯特丹、香港、倫敦、矽谷、新加坡、雪梨、東京、華盛頓特區 |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |支援 |不支援 |倫敦 |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |支援 |支援 |芝加哥、洛杉磯、紐約、矽谷、多倫多、華盛頓特區 |

 **+** 表示即將推出

### <a name="national-cloud-environment"></a>國家雲端環境

### <a name="us-government-cloud"></a>美國政府雲端
| **服務提供者** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |支援 |支援 |芝加哥、華盛頓特區 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |支援 |支援 |芝加哥、達拉斯、紐約、矽谷+、華盛頓特區 |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |支援 |支援 |芝加哥、紐約+、華盛頓特區 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支援 | 支援 | 達拉斯 |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |支援 |支援 |芝加哥、達拉斯、紐約、華盛頓特區 |

### <a name="china"></a>中國
| **服務提供者** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **China Telecom** |支援 |不支援 |北京、上海 |

若要深入了解，請參閱 [ExpressRoute (中國)](http://www.windowsazure.cn/home/features/expressroute/)。

### <a name="germany"></a>德國
| **服務提供者** | **Microsoft Azure** | **Office 365** | **位置** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |支援 |不支援 |柏林+、法蘭克福 |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |支援 |不支援 |法蘭克福 |
| **e-shelter** |支援 |不支援 |柏林 |
| **Interxion** |支援 |不支援 |法蘭克福 |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |支援  | 不支援 | 柏林+ |

## <a name="a-namenonpartnersaconnectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>透過未列出的服務提供者連線
如果上一節中未列出您的連線提供者，您仍然可以建立連線。

* 請洽詢您的連線提供者，以了解他們是否連線到上方表格中列出的任何 Exchange 提供者。 您可以檢查下列連結，以收集 Exchange 提供者所提供之服務的相關詳細資訊。 已有數個連線提供者連線到乙太網路 Exchange。
  * [Cologix](http://www.cologix.com/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/colocated-hybrid-cloud/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* 請您的連線提供者將您的網路延伸到選擇的對等互連位置。
  * 請確保您的連線提供者以高可用性的方式延伸您的連線，因此不會有單一失敗點。
* 排序一個 ExpressRoute 循環，將 Exchange 視為連線至 Microsoft 的連線提供者。
  * 依照 [建立 ExpressRoute 循環](expressroute-howto-circuit-classic.md) 中的步驟來設定連線。

| **連線提供者** | **Exchange** | **位置** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |新加坡 |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |東京 |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | 阿姆斯特丹 |
| **[指數 E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | 倫敦 |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | 倫敦、斯勞 |
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix |紐約、華盛頓特區 |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | 雪梨 |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | 華盛頓 |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | 阿姆斯特丹、法蘭克福 |  
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | 達拉斯、洛杉磯 |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | 法蘭克福 |  
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix |芝加哥、矽谷、華盛頓特區 |
| **[XO Communications](http://www.xo.com/)** |Equinix |矽谷 |
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | 馬德里 |


## <a name="expressroute-system-integrators"></a>ExpressRoute 系統整合者
根據您的網路規模，為符合您的需求而啟用私人連線可能有一定的難度。 您可以使用下表所列出的任何系統整合者來協助您開始使用 ExpressRoute。

| **系統整合者** | **Continent** |
| --- | --- |
| **[Avanade Inc.](http://www.avanade.com/)** | 亞洲、歐洲、北美洲、南美洲 |
| **[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)** | 歐洲 |
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | 北美洲 |
| **[IT Consultancy](http://itconsult.com.au/microsoft-expressroute)** | 澳大利亞 |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | 歐洲 (德國) |
| **[Nelite](http://nelite.com/)** | 歐洲 |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | 亞洲 |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 北美洲 |
| **[Project Leadership](http://www.projectleadership.net/azure)** | 北美洲 |


## <a name="next-steps"></a>後續步驟
* 如需有關 ExpressRoute 的詳細資訊，請參閱 [ExpressRoute 常見問題集](expressroute-faqs.md)。
* 請確定符合所有必要條件。 請參閱 [ExpressRoute 必要條件](expressroute-prerequisites.md)。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "位置圖"



<!--HONumber=Jan17_HO3-->


