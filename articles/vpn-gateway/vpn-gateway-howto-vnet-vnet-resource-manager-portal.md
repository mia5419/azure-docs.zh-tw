---
title: "使用 Resource Manager 部署模型和 Azure 入口網站來連接 VNet | Microsoft Docs"
description: "使用 Resource Manager 和 Azure 入口網站建立 VNet 間的 VPN 閘道連接。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 28d81fe312195b9a9094e1ed066f5cba57c76933
ms.openlocfilehash: b85017913316a450fe19f1760abff6a86f933e2e


---
# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>使用 Azure 入口網站設定 VNet 對 VNet 連接
> [!div class="op_single_selector"]
> * [Resource Manager - Azure 入口網站](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [傳統 - 傳統入口網站](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

本文逐步引導您使用 VPN 閘道和 Azure 入口網站在 Resource Manager 部署模型中建立 VNet 間的連接。

當您使用 Azure 入口網站來連接虛擬網路時，VNet 必須在相同的訂用帳戶中。 如果您的虛擬網路在不同的訂用帳戶中，您還是可以使用 [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) 步驟連接它們。

![v2v 圖表](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>VNet 對 VNet 連線的部署模型和方法
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

下表顯示 VNet 對 VNet 組態目前可用的部署模型和方法。 當包含設定的文章推出時，我們會直接從此資料表連結至該文章。

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**VNet 對等互連**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>關於 VNet 對 VNet 連線
將虛擬網路連接至另一個虛擬網路 (VNet 對 VNet)，類似於將 VNet 連接至內部部署網站位置。 這兩種連線類型都使用 Azure VPN 閘道提供使用 IPsec/IKE 的安全通道。 您所連線的 Vnet 可位於不同區域或不同訂用帳戶。

您甚至可以將多網站組態與 VNet 對 VNet 通訊結合。 這可讓您建立使用內部虛擬網路連線結合跨單位連線的網路拓撲，如下圖所示：

![關於連線](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "About connections")

### <a name="why-connect-virtual-networks"></a>為什麼要連接虛擬網路？
針對下列原因，您可能希望連接虛擬網路：

* **跨區域的異地備援和異地目前狀態**
  
  * 您可以使用安全連線設定自己的異地複寫或同步處理，而不用查看網際網路對向端點。
  * 您可以使用 Azure 流量管理員和負載平衡器，利用異地備援跨多個 Azure 區域設定高度可用的工作負載。 其中一個重要的範例就是使用分散在多個 Azure 區域的可用性群組來設定 SQL Always On。
* **具有隔離或管理界限的區域性多層式應用程式**
  
  * 在相同區域中，您可以因為隔離或管理需求，設定將多層式應用程式與多個虛擬網路連線在一起。

如需 VNet 對 VNet 連線的詳細資訊，請參閱本文結尾處的 [VNet 對 VNet 常見問題集](#faq) 。

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>設定範例
練習這些步驟時，您可以使用範例組態值。 為了舉例說明，我們對每個 VNet 使用多個位址空間。 不過，VNet 對 VNet 組態不需要多個位址空間。

**TestVNet1 的值︰**

* VNet 名稱︰TestVNet1
* 位址空間︰10.11.0.0/16
  * 子網路名稱：FrontEnd
  * 子網路位址範圍：10.11.0.0/24
* 資源群組︰TestRG1
* 位置：美國東部
* 位址空間︰10.12.0.0/16
  * 子網路名稱：BackEnd
  * 子網路位址範圍︰10.12.0.0/24
* 閘道子網路名稱︰GatewaySubnet (這會在入口網站中自動填入)
  * 閘道子網路位址範圍︰10.11.255.0/27
* DNS 伺服器︰使用您的 DNS 伺服器的 IP 位址
* 虛擬網路閘道名稱︰TestVNet1GW
* 閘道類型：VPN
* VPN 類型：路由式
* SKU︰選取您想要使用的閘道 SKU
* 公用 IP 位址名稱︰TestVNet1GWIP
* 連接值︰
  * 名稱︰TestVNet1toTestVNet4
  * 共用金鑰︰您可以自行建立共用金鑰。 此範例中，我們將使用 abc123。 重點是當您建立 VNet 之間的連接時，此值必須符合。

**TestVNet4 的值︰**

* VNet 名稱︰TestVNet4
* 位址空間︰10.41.0.0/16
  * 子網路名稱：FrontEnd
  * 子網路位址範圍︰10.41.0.0/24
* 資源群組︰TestRG1
* 位置：美國西部
* 位址空間︰10.42.0.0/16
  * 子網路名稱：BackEnd
  * 子網路位址範圍︰10.42.0.0/24
* 閘道子網路名稱︰GatewaySubnet (這會在入口網站中自動填入)
  * 閘道子網路位址範圍︰10.41.255.0/27
* DNS 伺服器︰使用您的 DNS 伺服器的 IP 位址
* 虛擬網路閘道名稱︰TestVNet4GW
* 閘道類型：VPN
* VPN 類型：路由式
* SKU︰選取您想要使用的閘道 SKU
* 公用 IP 位址名稱︰TestVNet4GWIP
* 連接值︰
  * 名稱︰TestVNet4toTestVNet1
  * 共用金鑰︰您可以自行建立共用金鑰。 此範例中，我們將使用 abc123。 重點是當您建立 VNet 之間的連接時，此值必須符合。

## <a name="a-namecreatvneta1-create-and-configure-testvnet1"></a><a name="CreatVNet"></a>1.建立及設定 TestVNet1
如果您已經有 VNet，請驗證設定是否與您的 VPN 閘道設計相容。 請特別注意任何可能與其他網路重疊的子網路。 如果有重疊的子網路，您的連線便無法正常運作。 如果您的 VNet 已設定為正確的設定，即可開始執行 [指定 DNS 伺服器](#dns) 一節中的步驟。

### <a name="to-create-a-virtual-network"></a>建立虛擬網路
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-create-subnets"></a><a name="subnets"></a>2.新增其他位址空間和建立子網路
建立 VNet 之後，您可以新增其他位址空間和建立子網路。

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namegatewaysubneta3-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>3.建立閘道子網路
將虛擬網路連接到閘道之前，您必須先建立虛擬網路要連接的閘道子網路。 可能的話，最好使用 /28 或 /27 的 CIDR 區塊建立閘道子網路，以便提供足以容納未來其他組態需求的 IP 位址。

如果您要練習建立此組態，請在建立閘道子網路時參考這些[範例值](#values) 。

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>建立閘道子網路
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namednsservera4-specify-a-dns-server-optional"></a><a name="DNSServer"></a>4.指定 DNS 伺服器 (選擇性)
如果您想要對部署至 VNet 的虛擬機器進行名稱解析，則應該指定 DNS 伺服器。

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5.建立虛擬網路閘道
此步驟將帶您建立 VNet 的虛擬網路閘道。 此步驟可能需要 45 分鐘的時間才能完成。 如果您要練習建立此組態，您可以參考[範例設定](#values)。

### <a name="to-create-a-virtual-network-gateway"></a>建立虛擬網路閘道
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namecreatetestvnet4a6-create-and-configure-testvnet4"></a><a name="CreateTestVNet4"></a>6.建立及設定 TestVNet4
設定 TestVNet1 之後，請重複先前步驟來建立 TestVNet4，並換成 TestVNet4 的值。 您不需要等到 TestVNet1 的虛擬網路閘道建立完成後才設定 TestVNet4。 如果您使用自己的值，請確定位址空間沒有與任何您想要連接的 VNet 重疊。

## <a name="a-nametestvnet1connectiona7-configure-the-testvnet1-connection"></a><a name="TestVNet1Connection"></a>7.設定 TestVNet1 連接
當 TestVNet1 和 TestVNet4 的虛擬網路閘道完成後，您可以建立虛擬網路閘道連接。 本節中，您將建立從 VNet1 到 VNet4 的連接。

1. 在 [所有資源] 中，瀏覽至 VNet 的虛擬網路閘道。 例如，**TestVNet1GW**。 按一下 [TestVNet1GW] 以開啟 [虛擬網路閘道] 刀鋒視窗。
   
    ![連接刀鋒視窗](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Connections blade")
2. 按一下 [+新增] 以開啟 [新增連接] 刀鋒視窗。
3. 在 [新增連接] 刀鋒視窗的 [名稱] 欄位中，輸入連接名稱。 例如，**TestVNet1toTestVNet4**。
   
    ![連接名稱](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Connection name")
4. 在 [連線類型] 中， 從下拉式清單選取 [VNet 對 VNet]。
5. 因為您是從指定的虛擬網路閘道建立此連接，將會自動填入 [第一個虛擬網路閘道] 欄位值。
6. [第二個虛擬網路閘道] 欄位是您想要建立連接的 VNet 的虛擬網路閘道。 按一下 [選擇另一個虛擬網路閘道]，以開啟 [選擇虛擬網路閘道] 刀鋒視窗。
   
    ![新增連接](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Add a connection")
7. 檢視此刀鋒視窗上列出的虛擬網路閘道。 請注意，只會列出您的訂用帳戶中的虛擬網路閘道。 如果想要連接的虛擬網路閘道不在您的訂用帳戶中，請參閱 [PowerShell 文章](vpn-gateway-vnet-vnet-rm-ps.md)。 
8. 按一下您想要連接虛擬網路閘道。
9. 在 [共用金鑰] 欄位中，輸入連接的共用金鑰。 您可以產生此金鑰，或自行建立此金鑰。 在網站間連接中，您用於內部部署裝置與虛擬網路閘道連接的金鑰完全相同。 此處的概念類似，差別在於是連接到另一個虛擬網路閘道，而不是連接到 VPN 裝置。
   
    ![共用金鑰](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Shared key")
10. 按一下刀鋒視窗底部的 [確定] 以儲存變更。

## <a name="a-nametestvnet4connectiona8-configure-the-testvnet4-connection"></a><a name="TestVNet4Connection"></a>8.設定 TestVNet4 連接
接下來，建立從 TestVNet4 至 TestVNet1 的連接。 像就您建立從 TestVNet1 至 TestVNet4 的連接一樣，使用相同的方法。 請確定您使用相同的共用金鑰。

## <a name="a-nameverifyconnectiona9-verify-your-connection"></a><a name="VerifyConnection"></a>9.確認您的連接
確認連接。 對每個虛擬網路閘道，執行下列作業︰

1. 找出虛擬網路閘道的刀鋒視窗。 例如，**TestVNet4GW**。 
2. 在虛擬網路閘道刀鋒視窗中，按一下 [連接]，以檢視虛擬網路閘道的連接刀鋒視窗。

檢視連接並確認狀態。 一旦建立連接，您會看到 [狀態] 值為 [成功] 和 [已連接]。

![Succeeded](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Succeeded")

您可以分別按兩下每個連接，以檢視該連接的相關資訊。

![基本資訊](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="a-namefaqavnet-to-vnet-faq"></a><a name="faq"></a>VNet 對 VNet 常見問題集
檢視常見問題集詳細資料以取得 VNet 對 VNet 連線的其他資訊。

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>後續步驟
一旦完成您的連接，就可以將虛擬機器加入您的虛擬網路。 如需詳細資訊，請參閱 [虛擬機器文件](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) 。



<!--HONumber=Nov16_HO4-->


