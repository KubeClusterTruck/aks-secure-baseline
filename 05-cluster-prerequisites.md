# Deploy the AKS cluster prerequisites and shared services

Now that the [hub-spoke network is provisioned](./04-networking.md), the next step in the [AKS secure Baseline reference implementation](./) is deploying the AKS cluster prerequisites and shared Azure service instances.

Azure Front Door is a global service shared by all the deployed clusters. Azure Front Door always routes traffic to the fastest and available (healthy) backend. The Azure Application Gateway of each AKS Cluster will be each Azure Front Door backend.
We will reach two goals:

1. your traffic to your closest service backend
1. if some app fails, then the others instances works as backup.

## Steps

1. Create resource group in order to deploy Shared services

   ```bash
   az group create --name rg-bu0001a0042-shared-services --location centralus
   ```

1. Read the FQDN values will have each Azure Application Gateway, the public ip DNS name already deployed

   ```bash
   APPGW_FQDN_BU0001A0042_03=$(az deployment group show --resource-group rg-enterprise-networking-spokes -n spoke-BU0001A0042-03 --query properties.outputs.appGwFqdn.value -o tsv)
   APPGW_FQDN_BU0001A0042_04=$(az deployment group show --resource-group rg-enterprise-networking-spokes -n spoke-BU0001A0042-04 --query properties.outputs.appGwFqdn.value -o tsv)
   ```

1. Deploy Azure Front Door

   > :book: Each client of our application around the world will be served for the closet AKS Cluster, and in case of some failure in one of the instance, the user will be served for another.

   ```bash
   az deployment group create -g g-bu0001a0042-shared-services -f frontdoor-stamp.json -p backendNames="['${APPGW_FQDN_BU0001A0042_03}','${APPGW_FQDN_BU0001A0042_04}']"
   ```

1. Lorem Ipsum

   > :book: Lorem ipsum

   ```bash
   echo "foo bar"
   ```

### Next step

:arrow_forward: [Deploy the AKS cluster](./06-aks-cluster.md)
