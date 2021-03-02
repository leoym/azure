# Conteúdo da certificação AZ-900

## Este documento contém informações úteis para a certificação AZ-900. Apresentamos os tópicos baseados no conteúdo da prova.

## Listamos comandos, links de referência e códigos de exemplo.

###########################################################################################################
# Parte 1 - CRIANDO RECURSOS NA CLOUD
###########################################################################################################

###########################################################################################################
# Gerenciando os recursos na infraestrutura na Cloud Azure com AZ CLI
###########################################################################################################

1) Criando um Resource Group

	https://docs.microsoft.com/en-us/cli/azure/group?view=azure-cli-latest

	az group list
	
	az group create -l westus -n rg-teste
	az group delete --resource-group rg-teste

	az group create -l eastus -n rg-teste-dev
	az group delete --resource-group rg-teste-dev

2) Criando uma Rede Virtual Vnet

	https://docs.microsoft.com/en-us/cli/azure/network/vnet?view=azure-cli-latest#az_network_vnet_create

	az network vnet list
	
	az network vnet create -g rg-teste -n rede-teste
	az network vnet delete -g rg-teste -n rede-teste

	az network vnet create -g rg-teste-dev -n rede-teste-dev
	az network vnet delete -g rg-teste-dev -n rede-teste-dev
	
3) Criando grupo de segurança NSG

	https://docs.microsoft.com/en-us/cli/azure/network/nsg?view=azure-cli-latest

	az network nsg create -g rg-teste -n subnet-teste-01-nsg --tags super_secure no_80 no_22
	az network nsg delete -g rg-teste -n subnet-teste-01-nsg

	az network nsg delete -g rg-teste -n vm-teste-01NSG
	az network nsg delete -g rg-teste -n subnet-teste-01-nsg

	3.1) Criando um application Security Group 

	https://docs.microsoft.com/en-us/cli/azure/network/asg?view=azure-cli-latest

	az network asg create -g rg-teste -n Web --tags TesteWebApp, CostCenter=IT

	3.2) Criando regras de acesso - Rules

	https://docs.microsoft.com/en-us/cli/azure/network/nsg/rule?view=azure-cli-latest
	https://docs.microsoft.com/en-us/cli/azure/network/nsg/rule?view=azure-cli-latest#az_network_nsg_rule_create

	az network nsg rule create -g rg-teste --nsg-name subnet-teste-01-nsg -n  subnet-teste-01-nsg-rule  --priority 4096 \
    --source-address-prefixes '8' --source-port-ranges 80 \
    --destination-address-prefixes '*' --destination-port-ranges 80 8080 --access Deny \
    --protocol Tcp --description "Deny from specific IP address ranges on 80 and 8080."

	az network nsg rule create -g rg-teste --nsg-name  subnet-teste-01-nsg -n subnet-teste-01-nsg-rule-web \
    --priority 500 --source-address-prefixes Internet --destination-port-ranges 22 80 8080 \
    --destination-asgs Web --access Allow --protocol Tcp --description "Allow Internet to Web ASG on ports 22, 80,8080."

4) Criando Route Table

	https://docs.microsoft.com/en-us/cli/azure/network/route-table?view=azure-cli-latest

	az network route-table create -g rg-teste -n rt-table-teste-01

	az network route-table delete -g rg-teste -n rt-table-teste-01

5) Criando Route Table route

	https://docs.microsoft.com/en-us/cli/azure/network/route-table/route?view=azure-cli-latest

	az network route-table route create -g rg-teste --route-table-name rt-table-teste-01 -n rt-teste-01 \
    --next-hop-type VirtualAppliance --address-prefix 10.0.0.0/16  --next-hop-ip-address 10.0.100.4

6) Criando a subnet

	https://docs.microsoft.com/en-us/cli/azure/network/vnet/subnet?view=azure-cli-latest

	az network vnet subnet create -g rg-teste --vnet-name rede-teste -n subnet-teste-01 \
    --address-prefixes 10.0.1.0/24 --network-security-group subnet-teste-01-nsg --route-table rt-table-teste-01 

7) Criando máquinas virtuais

	https://docs.microsoft.com/en-us/cli/azure/vm?view=azure-cli-latest
	
	# Linux
	az vm create -n vm-teste-01 -g rg-teste --image UbuntuLTS --generate-ssh-keys
	az vm create -n vm-teste-02 -g rg-teste --image UbuntuLTS

	az vm resize -g rg-teste -n vm-teste-01 --size Standard_B1s
	az vm resize -g rg-teste -n vm-teste-02 --size Standard_B1s
	
	az vm stop -g rg-teste -n vm-teste-01
	az vm stop -g rg-teste -n vm-teste-02 
	
	az vm deallocate -g rg-teste -n vm-teste-01 
	az vm deallocate -g rg-teste -n vm-teste-02 

	az vm delete -g rg-teste -n vm-teste-01 
	az vm delete -g rg-teste -n vm-teste-02 

    * Liberar e Acessar com ssh

	# Windows
	az group create -l eastus -n rg-teste-dev

	az vm create -n vm-win-dev-01 -g rg-teste-dev --image Win2012R2Datacenter
	az vm delete -n vm-win-dev-01 -g rg-teste-dev

	az vm create -n vm-win-dev-02 -g rg-teste-dev --public-ip-address "" --image Win2012R2Datacenter
	az vm delete -n vm-win-dev-02 -g rg-teste-dev

	az vm create -n vm-win-dev-01 -g rg-teste-dev --public-ip-address "" --image Win2012R2Datacenter
	az vm resize -n vm-win-dev-01 -g rg-teste-dev --size Standard_B1s
	az vm deallocate -g rg-teste-dev -n vm-win-dev-01
	az vm delete -g rg-teste-dev -n vm-win-dev-01

	* Criar usuario e Acessar RDP

###########################################################################################################
# Parte 2 - ADMINISTRANDO OS RECURSOS NA CLOUD
###########################################################################################################

1) Gerenciando placas de rede NICs

	https://docs.microsoft.com/en-us/cli/azure/network/nic?view=azure-cli-latest#az_network_nic_list

	az network nic list --query "[?dnsSettings.internalDomainNameSuffix=`{dnsSuffix}`]"
	az network nic delete --resource-group  rg-teste --name vm-teste-01VMNic
	az network nic delete --resource-group  rg-teste --name vm-teste-02VMNic
	az network nic delete --resource-group  rg-teste-dev --name vm-win-dev-01VMNic
	az network nic delete --resource-group  rg-teste- dev --name vm-win-dev-01VMNic

2) Gerenciando IPs

	https://docs.microsoft.com/en-us/cli/azure/network/public-ip?view=azure-cli-latest#az_network_public_ip_delete
	
	az network public-ip delete -g rg-teste -n vm-teste-01PublicIP
	az network public-ip delete -g rg-teste -n vm-teste-02PublicIP

3) Administrando Discos

	https://docs.microsoft.com/en-us/cli/azure/disk?view=azure-cli-latest

	az disk delete --name vm-teste-02_disk1_705abce2cf5a4b41830a99fee4342ab8 --resource-group rg-teste
	az disk delete --name vm-teste-01_disk1_1ed93649dedb4c2c986242ae5cd71a6b --resource-group rg-teste
	
	az disk delete --name vm-win-dev-01_disk1_35d2b9df647a4735ac28e6eccdbf668b --resource-group rg-teste-dev

4) Gerenciando usuários

	https://docs.microsoft.com/en-us/cli/azure/vm/user?view=azure-cli-latest

	az delete user

5) Grupos de gerenciamento

	https://docs.microsoft.com/en-us/cli/azure/account/management-group?view=azure-cli-latest#az_account_management_group_create

	az account management-group create --name producao
	az account management-group list

6) Administrando Subscriptions

	https://docs.microsoft.com/pt-br/cli/azure/manage-azure-subscriptions-azure-cli

	az account show --output table

	az account list --output table

	az group create --location westus --resource-group StorageRG-teste

	az storage account create --resource-group StorageRG-teste --name storage136teste --location westus --sku Standard_LRS

7) Implantando com Azure Resource manager

	https://docs.microsoft.com/pt-br/azure/azure-resource-manager/templates/overview
	https://docs.microsoft.com/pt-br/azure/azure-resource-manager/templates/deploy-cli
	https://docs.microsoft.com/pt-br/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell

{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": []
}

templateFile="template1.json"
az deployment group create \
  --name template1 \
  --resource-group ResourceGroupsTESTE \
  --template-file $templateFile

{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "{provide-unique-name}",
      "location": "eastus",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2",
      "properties": {
        "supportsHttpsTrafficOnly": true
      }
    }
  ]
}

templateFile2="template2.json"

az deployment group create \
  --name addstorage \
  --resource-group ResourceGroupsTESTE \
  --template-file $templateFile2

###########################################################################################################
# Parte 3 - GERENCIANDO SERVIÇOS NA CLOUD
###########################################################################################################

1) Criando APP Services

	https://docs.microsoft.com/en-us/cli/azure/appservice?view=azure-cli-latest

2) Criando Container Instances 

	https://docs.microsoft.com/pt-br/azure/container-instances/container-instances-quickstart-portal
	https://docs.microsoft.com/en-us/cli/azure/container?view=azure-cli-latest

3) Azure Ad

	https://docs.microsoft.com/pt-br/azure/active-directory/fundamentals/active-directory-whatis
	https://docs.microsoft.com/pt-br/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal

	Duru208299**

4) AZ WVD - Desktop Virtual

	https://docs.microsoft.com/pt-br/azure/virtual-desktop/overview?WT.mc_id=Portal-Microsoft_Azure_WVD

###########################################################################################################
# Parte 4 - CRIANDO BANCO DE DADOS NA CLOUD
###########################################################################################################

1) Cosmos DB

	# Criando a instância

	https://docs.microsoft.com/en-us/cli/azure/cosmosdb?view=azure-cli-latest

	az group create -l eastus -n teste-data	
	az cosmosdb create --name vcosmosdb --resource-group teste-data	
	az cosmosdb delete --name vcosmosdb --resource-group teste-data

	# Acessando dados

	https://docs.microsoft.com/pt-br/azure/cosmos-db/sql-api-nodejs-get-started

	https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started

	https://docs.microsoft.com/pt-br/azure/cosmos-db/create-sql-api-nodejs

2) SQL

	# Criando o server
	
	https://docs.microsoft.com/en-us/cli/azure/sql/server?view=azure-cli-latest#az_sql_server_create

	az sql server create -l eastus -g teste-data -n teste-data-server -u testeuser -p Duru208299**
	az sql server delete --resource-group teste-data --name teste-data-server
	
	# Criando o banco de dados

	https://docs.microsoft.com/en-us/cli/azure/sql/db?view=azure-cli-latest#az_sql_db_create

	https://docs.microsoft.com/pt-br/azure/azure-sql/database/scripts/create-and-configure-database-cli

	az sql db create -g teste-data -s teste-data-server -n teste-data-db --service-objective S0
	az sql db delete --resource-group teste-data --server teste-data-server  --name teste-data-db
	
	az sql db create --resource-group teste-data --server  teste-data-server --name teste-data-db-adv --sample-name AdventureWorksLT 
	az sql db delete --resource-group teste-data --server  teste-data-server --name teste-data-db-adv
	# --edition GeneralPurpose --family Gen4 --capacity 1 --zone-redundant false

	Server: teste-data-server.database.windows.net
	Name: teste-data-server
	db: teste-data-db / teste-data-db-adv
	User: testeuser
	Password: Duru208299**

	# Conectando no banco

	https://docs.microsoft.com/pt-br/azure/developer/javascript/how-to/integrate-database

	https://docs.microsoft.com/pt-br/azure/azure-sql/database/connect-query-nodejs?tabs=windows

	https://docs.microsoft.com/pt-br/azure/azure-sql/database/connect-query-nodejs?tabs=ubuntu

	git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git

3) MySQL

	https://docs.microsoft.com/en-us/cli/azure/mysql?view=azure-cli-latest
	https://docs.microsoft.com/en-us/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_create

	az mysql server create -l eastus -g teste-data -n teste-data-mysql -u testeuser -p Duru208299** --sku-name GP_Gen5_2
	az mysql server delete --resource-group teste-data --name teste-data-mysql

	az mysql db create -g teste-data -s teste-data-mysql -n teste-data-mysql
	az mysql db delete -g teste-data -s teste-data-mysql -n teste-data-mysql


	https://docs.microsoft.com/pt-br/azure/mysql/connect-nodejs

	Código

	mkdir az-mysql-db-nodejs
	cd az-mysql-db-nodej
	npm install --save mysql
	npm list

4) PostGres

	https://docs.microsoft.com/en-us/cli/azure/postgres?view=azure-cli-latest
	https://docs.microsoft.com/en-us/cli/azure/postgres/server?view=azure-cli-latest#az_postgres_server_create

	az postgres server create -l eastus -g teste-data -n teste-data-postgres -u testeuser -p Duru208299** --sku-name B_Gen5_1 # GP_Gen5_2
	az postgres server delete --resource-group teste-data --name teste-data-postgres

	az postgres db create -g teste-data -s teste-data-postgres -n teste-data-postgres	
	az postgres db delete -g teste-data -s teste-data-postgres -n teste-data-postgres

	https://docs.microsoft.com/pt-br/azure/postgresql/connect-nodejs

###########################################################################################################
# PARTE 5 - GERENCIANDO SOLUÇÕES NA CLOUD
###########################################################################################################
	
1) IoT Hub

	https://docs.microsoft.com/en-us/cli/azure/ext/azure-iot/iot?view=azure-cli-latest

Create an IoT Hub

	https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-using-cli
	https://azure.microsoft.com/pt-br/pricing/details/iot-hub/

List locations
	az account list-locations -o table

Create RG
	az group create --name rg-teste --location eastus

Create IoT Hub
	az iot hub create --name iot-hub-teste \
   --resource-group rg-teste --sku S1

Delete IoT 
	az iot hub delete --name iot-hub-teste  -\
	  -resource-group rg-teste

Delete RG
	az group delete --name rg-teste 

	https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide

	https://docs.microsoft.com/en-us/azure/iot-hub/iot-hub-create-through-portal

	https://docs.microsoft.com/en-us/azure/iot-hub/monitor-iot-hub

2) IoT Central

	https://docs.microsoft.com/en-us/cli/azure/iot?view=azure-cli-latest

	https://docs.microsoft.com/en-us/cli/azure/iot/central/app?view=azure-cli-latest#az_iot_central_app_create

	az iot central app create -n iot-central-teste -g rg-teste -s iot-app-teste -l unitedstates -p F1

3) Azure Sphere

	https://azure.microsoft.com/pt-br/services/azure-sphere/

	https://docs.microsoft.com/pt-br/azure-sphere/product-overview/what-is-azure-sphere

	https://azure.microsoft.com/pt-br/pricing/details/azure-sphere/

	https://docs.microsoft.com/en-us/azure-sphere/reference/overview

	https://docs.microsoft.com/pt-br/azure-sphere/reference/azsphere-device?tabs=cliv1

###########################################################################################################
# PARTE 6 - DATA ANALYTICS NA CLOUD
###########################################################################################################

1) Azure Synapse Analytics
	https://azure.microsoft.com/pt-br/services/synapse-analytics/#features
	https://docs.microsoft.com/pt-br/azure/synapse-analytics/quickstart-create-workspace-cli
	https://docs.microsoft.com/en-us/cli/azure/sql/dw?view=azure-cli-latest
	
	az group create --name $SynapseResourceGroup --location $Region
	
2) HDInsight - Enterprise-ready, managed cluster service for open-source analytics

	https://azure.microsoft.com/pt-br/services/hdinsight/

	https://docs.microsoft.com/en-us/cli/azure/hdinsight?view=azure-cli-latest
	https://docs.microsoft.com/pt-br/azure/hdinsight/hdinsight-administer-use-command-line
	https://docs.microsoft.com/en-us/cli/azure/hdinsight/application?view=azure-cli-latest#az_hdinsight_application_create

	az hdinsight application create -g MyResourceGroup -n MyApplication \
--cluster-name MyCluster \
--script-uri https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh \
--script-action-name MyScriptAction \
--script-parameters '"-version latest -port 20000"'
	
3) Azure Databricks

	https://azure.microsoft.com/pt-br/services/databricks/

	https://docs.microsoft.com/pt-br/azure/databricks/scenarios/what-is-azure-databricks

	https://azure.microsoft.com/en-us/pricing/details/databricks/

	https://azure.microsoft.com/pt-br/pricing/details/databricks/

	https://azure.microsoft.com/pt-br/pricing/details/databricks/

	az databricks workspace create --resource-group rg-teste --name databricks-teste --location eastus --sku trial
	az databricks workspace create --resource-group rg-teste --name databricks-teste --location eastus --sku standard

###########################################################################################################
# PARTE 7 - CRIANDO IA NA CLOUD
###########################################################################################################

# ML

	1) Azure Machine Learning
	
	https://docs.microsoft.com/pt-br/azure/machine-learning/reference-azure-machine-learning-cli

	https://docs.microsoft.com/en-us/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest

	https://docs.microsoft.com/pt-br/azure/machine-learning/how-to-deploy-and-where?tabs=azcli

	2) Cognitive Services

	https://azure.microsoft.com/pt-br/services/cognitive-services/

	https://docs.microsoft.com/pt-br/azure/cognitive-services/cognitive-services-apis-create-account-cli?tabs=windows

	https://docs.microsoft.com/en-us/cli/azure/cognitiveservices?view=azure-cli-latest
	
	3) Azure Bot Service

	https://azure.microsoft.com/pt-br/services/bot-services/

	https://azure.microsoft.com/pt-br/pricing/details/bot-services/

	https://docs.microsoft.com/pt-br/azure/bot-service/bot-builder-deploy-az-cli?view=azure-bot-service-4.0&tabs=csharp

	https://docs.microsoft.com/en-us/cli/azure/bot?view=azure-cli-latest

###########################################################################################################
# PARTE 8 -CRIANDO SERVERLESS NA CLOUD
###########################################################################################################

# Serverless

	1) Azure Functions

	https://docs.microsoft.com/pt-br/azure/azure-functions/scripts/functions-cli-create-serverless
	https://docs.microsoft.com/pt-br/azure/azure-functions/scripts/functions-cli-create-serverless
	https://docs.microsoft.com/en-us/cli/azure/functionapp?view=azure-cli-latest
	https://docs.microsoft.com/pt-br/azure/azure-functions/create-first-function-cli-python?tabs=azure-cli%2Cbash%2Cbrowser

	20 Logic Apps

	https://docs.microsoft.com/pt-br/azure/logic-apps/quickstart-logic-apps-azure-cli

	https://docs.microsoft.com/pt-br/azure/logic-apps/sample-logic-apps-cli-script

###########################################################################################################
# PARTE 9 - DEVTOOLS NA CLOUD
###########################################################################################################

DevTools

	1) Azure DevOps

	https://azure.microsoft.com/pt-br/services/devops/

	2) GitHub

	https://github.com/

	3) GitHub Actions
	
	https://github.com/features/actions
	https://docs.github.com/pt/actions
	
	4) Azure DevTest Labs

	https://azure.microsoft.com/pt-br/services/devtest-lab/
	https://docs.microsoft.com/pt-br/azure/devtest-labs/devtest-lab-overview
	https://docs.microsoft.com/pt-br/azure/devtest-labs/devtest-lab-vmcli
	https://docs.microsoft.com/pt-br/azure/devtest-labs/scripts/create-verify-virtual-machine-in-lab-cli

###########################################################################################################
# PARTE 10 - GERENCIANDO NA CLOUD
###########################################################################################################

Management tools
		
	1) Azure Portal - Portal do Microsoft Azure - Compile, gerencie e monitore tudo, desde aplicativos Web simples a aplicativos em nuvem complexos em um único console unificado

		https://azure.microsoft.com/pt-br/features/azure-portal/

	2) Azure PowerShell - Azure PowerShell documentation - Azure PowerShell is a set of cmdlets for managing Azure resources directly from the PowerShell command line. Azure PowerShell is designed to make it easy to learn and get started with, but provides powerful features for automation. Written in .NET Standard, Azure PowerShell works with PowerShell 5.1 on Windows, and PowerShell 7.x and higher on all platforms.

		https://docs.microsoft.com/pt-br/powershell/azure/install-az-ps?view=azps-5.5.0
		https://docs.microsoft.com/pt-br/powershell/azure/?view=azps-5.5.0

	3) Azure CLI - What is Azure CLI - The Azure command-line interface (Azure CLI) is a set of commands used to create and manage Azure resources. The Azure CLI is available across Azure services and is designed to get you working quickly with Azure, with an emphasis on automation.

		https://docs.microsoft.com/pt-br/cli/azure/what-is-azure-cli

	4) Cloud Shell - Conecte-se ao Azure usando uma experiência de shell autenticada baseada em navegador, hospedada na nuvem e acessível de praticamente qualquer lugar.

		https://azure.microsoft.com/pt-br/features/cloud-shell/

	5) Azure Mobile App
	
		https://azure.microsoft.com/pt-br/services/app-service/mobile/#customers
		https://azure.microsoft.com/pt-br/services/app-service/mobile/#customers
		https://azure.microsoft.com/en-us/solutions/mobile/
		https://docs.microsoft.com/pt-br/previous-versions/azure/app-service-mobile/app-service-mobile-android-get-started

	6) Azure Advisor -	Otimize as implantações do Azure de forma fácil e rápida. O Assistente do Azure analisa as suas configurações e a telemetria de uso e oferece recomendações acionáveis e personalizadas para ajudar a otimizar os seus recursos do Azure para confiabilidade, segurança, excelência operacional, desempenho e custo.

		https://azure.microsoft.com/pt-br/services/advisor/
		https://docs.microsoft.com/pt-br/azure/advisor/advisor-overview

	7) Azure Resource Manager (ARM) templates - Com a mudança para a nuvem, muitas equipes adotaram métodos de desenvolvimento Agile. Essas equipes iteram rapidamente. Eles precisam implantar repetidamente suas soluções na nuvem e saber que sua infraestrutura está em um estado confiável. À medida que a infraestrutura se tornou parte do processo iterativo, a divisão entre operações e desenvolvimento desapareceu. As equipes precisam gerenciar a infraestrutura e o código do aplicativo por meio de um processo unificado.
	
		https://docs.microsoft.com/pt-br/azure/azure-resource-manager/templates/overview

	8) Azure Monitor - Obtenha uma visão completa de aplicativos, infraestruturas e redes

		https://azure.microsoft.com/pt-br/services/monitor/
		https://docs.microsoft.com/pt-br/azure/azure-monitor/overview
	
	9) Azure Service Health - Microsoft Azure Service Health is your personalized dashboard in the Azure portal for receiving notifications, guidance, and technical support when Azure service issues, updates, or planned maintenance affect your Azure resources.

		https://azure.microsoft.com/pt-br/resources/videos/azure-service-health-overview/

###########################################################################################################
# PARTE 11 - REDES E SEGURANÇA NA CLOUD
###########################################################################################################

Segurança - General security and network security features

	1) Azure Security Center

	A Central de Segurança do Azure é um sistema de gerenciamento de segurança de infraestrutura unificado que fortalece a postura de segurança de seus data centers e fornece proteção avançada contra ameaças em suas cargas de trabalho híbridas locais e na nuvem, estejam elas no Azure ou não.

	https://docs.microsoft.com/pt-br/azure/security-center/security-center-introduction

	2) Key Vault

	Azure Key Vault é um serviço de nuvem para armazenar e acessar segredos com segurança. Um segredo é qualquer coisa que você deseja controlar rigidamente o acesso ao, como chaves de API, senhas, certificados ou chaves de criptografia. Key Vault serviço dá suporte a dois tipos de contêineres: cofres e pools HSM gerenciados. Os cofres dão suporte ao armazenamento de chaves, segredos e certificados com respaldo de software e HSM. Os pools HSM gerenciados dão suporte apenas a chaves apoiadas por HSM.

	https://docs.microsoft.com/pt-br/azure/key-vault/general/basic-concepts#:~:text=Azure%20Key%20Vault%20%C3%A9%20um,e%20acessar%20segredos%20com%20seguran%C3%A7a.&text=A%20secret%20is%20anything%20that,cofres%20e%20pools%20HSM%20gerenciados.

	3) Azure Sentinel

	https://azure.microsoft.com/pt-br/services/azure-sentinel/
	
	4) Azure Dedicated Hosts
	Azure Dedicated Host is a service that provides physical servers - able to host one or more virtual machines - dedicated to one Azure subscription. Dedicated hosts are the same physical servers used in our data centers, provided as a resource. You can provision dedicated hosts within a region, availability zone, and fault domain. Then, you can place VMs directly into your provisioned hosts, in whatever configuration best meets your needs.

	https://docs.microsoft.com/en-us/azure/virtual-machines/dedicated-hosts
	
	5) Network Security Groups (NSG)

		https://azure.microsoft.com/pt-br/blog/network-security-groups/

		https://docs.microsoft.com/pt-br/azure/virtual-network/network-security-groups-overview
	
	6) Azure Firewall

		https://docs.microsoft.com/pt-br/azure/firewall/overview
		https://azure.microsoft.com/pt-br/services/azure-firewall/

	7) Azure DDoS protection
	
		https://docs.microsoft.com/pt-br/azure/ddos-protection/ddos-protection-overview

###########################################################################################################
# PARTE 12 - ADMINISTRANDO NA CLOUD
###########################################################################################################

	1) Azure Active Directory	
	
		https://azure.microsoft.com/pt-br/services/active-directory/

	2) Azure Policy
	
		https://docs.microsoft.com/pt-br/azure/governance/policy/overview

	3) Azure Blueprints

		https://azure.microsoft.com/pt-br/services/blueprints/

		https://docs.microsoft.com/pt-br/azure/governance/blueprints/overview
