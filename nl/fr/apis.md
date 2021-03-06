---

copyright:
  years: 2017
lastupdated: "2018-11-10"

---

{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:tip: .tip}
{:download: .download}

# Référence d'API pour IBM Virtual Router Appliance
{: #api-reference-for-ibm-virtual-router-appliance}

L'API SoftLayer® Application Programming Interface (SLAPI) est l'interface de développement qui permet aux développeurs et aux administrateurs système d'interagir directement avec le système de back-end d'IBM© Cloud.

SLAPI alimente de nombreuses fonctions dans le portail client, ce qui signifie, en principe, que si une interaction est possible dans le portail client, elle peut également être exécutée dans l'API. Comme vous pouvez interagir à l'aide d'un programme avec toutes les parties de l'environnement du portail client dans l'API, vous pouvez utiliser l'API pour automatiser des tâches.

SLAPI est un système d'appel RPC (Remote Procedure Call). Chaque appel implique l'envoi de données vers un noeud final d'API et la réception de données structurées. Le format utilisé pour l'envoi et la réception de données avec l'API SLAPI dépend de l'implémentation choisie pour l'API. L'API SLAPI utilise actuellement SOAP, XML-RPC ou REST pour la transmission des données.

Comme les API SLAPI se réfèrent aux API IBM Virtual Router Appliance, voir les ressources suivantes dans SoftLayer Development Network (SLDN) :

- [SoftLayer_Network_Gateway API ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://sldn.softlayer.com/reference/services/SoftLayer_Network_Gateway){: new_window} 
- [SoftLayer_Network_Gateway_Member API ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://sldn.softlayer.com/reference/services/SoftLayer_Network_Gateway_Member){: new_window} 
- [SoftLayer_Network_Gateway_Vlan API ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://sldn.softlayer.com/reference/services/SoftLayer_Network_Gateway_Vlan){: new_window} 
- [SoftLayer_Network_Gateway_Status API ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://sldn.softlayer.com/reference/services/SoftLayer_Network_Gateway_Status){: new_window} 
- [SoftLayer API Python Client ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](http://softlayer-api-python-client.readthedocs.io/en/latest/api/client/){: new_window} 
- [Autres exemples d'API SoftLayer avec des langages différents ![Icône de lien externe](../../icons/launch-glyph.svg "Icône de lien externe")](https://softlayer.github.io/python/){: new_window} 


### Exemple - Commander un dispositif de passerelle

```
import SoftLayer
from pprint import pprint


def main():

    client = SoftLayer.create_client_from_env(username=<your_username>,
                                              api_key=<your_api_key>,
                                              endpoint_url="https://api.softlayer.com/xmlrpc/v3")

    productOrder = {
        'orderContainers': [
            {
              "complexType": ('SoftLayer_Container_Product_Order_Hardware' +
                                '_Server_Gateway_Appliance'),
                "prices": [
                    {"id", 205533},  # price for OS
                    {"id", 177619},  # price for Server
                    {"id", 17188},   # price for RAM
                    {"id", 63077},   # price for DISK
                    {"id", 52527},   # price for Network speed
                    {"id", 418},
                    {"id": 198769},
                    {"id": 75007},
                    {"id": 21},
                    {"id": 906},
                    {"id": 792},
                    {"id": 56},
                    {"id": 57},
                    {"id": 59},
                    {"id": 420}
                ]
                "hardware": [{
                    "hostname": your_gwname,
                    "domain": your_domain.com
                }],
                "location": "DAL09",
                "quantity": 1,
                "packageId": 174
            }
        ]
    }
    if bool(verify_order_only):
        order = client['Product_Order'].verifyOrder(productOrder)
    else:
        order = client['Product_Order'].placeOrder(productOrder)
    pprint(order)


if __name__ == '__main__':
    main()
```
{: codeblock}

### Exemple - Associer, router, contourner et dissocier des VLAN par rapport à un dispositif de passerelle

```
import SoftLayer
from pprint import pprint

class Gateway:
    def __init__(self, client, gateway_id):
        self.__gateway_id = gateway_id
        self.__client = client
        self.__inside_vlans = None

    def getNetworkVlanId(self, vlanNum):
        """
        obtains the VLAN Object (Vlan Object id) given the vlanNumber
        via SoftLayer API call
        """
        nwfilter = {'networkVlans': {'vlanNumber': {'operation': vlanNum}}}
        mask = 'mask[id]'
        vlan = self.__client['SoftLayer_Account'].getNetworkVlans(filter=nwfilter,
                                           mask=mask)
        if vlan:
            return vlan[0]['id']
        else:
            pprint("Vlan Object not present")
            return 0

    def getInsideVlans(self):
        """
        obtains the inside VLANs via SoftLayer API call
        """
        self.__inside_vlans = self.__client['Network_Gateway'].getInsideVlans(
                    id=self.__gateway_id,
                    mask='mask[id, networkVlanId, networkVlan.vlanNumber]')
        return self.__inside_vlans

    def __findVlanAssocId(self, vlanNum):
        for vlan in self.__inside_vlans:
            if int(vlan['networkVlan']['vlanNumber']) == int(vlanNum):
                return vlan['id']
        return None

    def associateVlan(self, vlanNum):
        vlanId = self.getNetworkVlanId(vlanNum)
        if vlanId:
            vlanObj = {"bypassFlag": "false",
                       "networkGatewayId": self.__gateway_id,
                       "networkVlanId": vlanId}
            obj = self.__client['Network_Gateway_Vlan'].createObject(vlanObj)
            return True
        else:
            pprint("Invalid Vlan")
            return False

    def disassociateVlan(self, vlanNum):
        vlanId = self.getNetworkVlanId(vlanNum)
        self.__client['Network_Gateway_Vlan'].deleteObject(id=vlanId)
        return True

    def bypassAllVlans(self):
        self.__client['Network_Gateway'].bypassAllVlans(id=self.__gateway_id)

    def routeAllVlans(self):
        self.__client['Network_Gateway'].unbypassAllVlans(id=self.__gateway_id)

    def bypassSingleVlan(self, vlan):
        vlanAssocId = self.__findVlanAssocId(vlan)
        if vlanAssocId:
            vlanAssocObj = [{'id': vlanAssocId}]
            self.__client['Network_Gateway'].bypassVlans(vlanAssocObj,
                                                         id=self.__gateway_id)
        else:
            pprint("Cannot bypass VLAN %s is not associated" % vlan)

    def routeSingleVlan(self, vlan):
        vlanAssocId = self.__findVlanAssocId(vlan)
        if vlanAssocId:
            vlanAssocObj = [{'id': vlanAssocId}]
            self.__client['Network_Gateway'].unbypassVlans(vlanAssocObj,
                                                           id=self.__gateway_id)
        else:
            pprint("Can't route thru VLAN %s is not associated" % vlan)
```
{: codeblock}

### Exemple d'annulation d'un dispositif de passerelle

```
import SoftLayer
from pprint import pprint


def getGatewayMembers(client, appliance_id):
    """
        Retrieves the gateway's member(s)
        args:
            client:
                SL client credentials
            appliance_id:
                ID of the gateway appliance to be reclaimed
        returns:
            gateway members
    """
    mask = 'mask[hardwareId]'
    return client['Network_Gateway'].getMembers(id=appliance_id, mask=mask)


def cancelGateway(client, appliance_id):
    """
        Cancels a standalone or HA pair gateway
        args:
            client:
                SL client credentials
            appliance_id:
                ID of the gateway appliance to be reclaimed
        returns:
            True if all members of the appliance are successfully reclaimed
            False otherwise
    """
    members = getGatewayMembers(client, appliance_id)

    for hardware in members:
        billingID = client['Hardware'].getBillingItem(id=hardware['hardwareId'])
        result = client['Billing_Item'].cancelItem(False,
                                                   False,
                                                   "No longer needed",
                                                   "", id=billingID['id'])


def main():

    client = SoftLayer.create_client_from_env(username=<your_username>,
                                              api_key=<your_api_key>,
                                              endpoint_url="https://api.softlayer.com/xmlrpc/v3")

    cancelGateway(client, your_gateway_id)

if __name__ == '__main__':
    main()
```
{: codeblock}
