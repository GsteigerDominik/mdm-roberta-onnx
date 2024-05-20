# roberta-onnx
az group create --name mdm-appservice --location switzerlandnorth
# Azure Web App Deployment
az appservice plan create --name mdm-roberta-onnx-gsteidom --resource-group mdm-appservice --sku F1 --is-linux

az webapp create --resource-group mdm-appservice --plan mdm-roberta-onnx-gsteidom --name mdm-roberta-onnx-gsteidom --deployment-container-image-name dominikgsteiger/mdm-roberta-onnx:latest

az webapp config appsettings set --resource-group mdm-appservice --name mdm-roberta-onnx-gsteidom --settings WEBSITES_PORT=5000
# ACA Deployment 
az group create --name mdm-aca --location westeurope
az containerapp env create --name mdm-roberta-onnx-gsteidom --resource-group mdm-aca --location westeurope
az containerapp create --name mdm-roberta-onnx-gsteidom --resource-group mdm-aca --environment mdm-roberta-onnx-gsteidom --image dominikgsteiger/mdm-roberta-onnx:latest --target-port 5000 --ingress external --query properties.configuration.ingress.fqdn --debug
az group create --name mdm-aca --location switzerlandnorth
az containerapp env create --name mdm-roberta-onnx-gsteidom --resource-group mdm-aca --location switzerlandnorth
az containerapp create --name mdm-roberta-onnx-gsteidom --resource-group mdm-aca --environment mdm-roberta-onnx-gsteidom --image dominikgsteiger/mdm-roberta-onnx:latest --target-port 5000 --ingress external --query properties.configuration.ingress.fqdn --debug
az containerapp show -name mdm-roberta-onnx-gsteidom --resource-group mdm-aca
# Retrieve logs from the container app
az containerapp logs show --name mdm-roberta-onnx-gsteidom --resource-group mdm-aca
az container logs --resource-group myResourceGroup --name mycontainer

# ACI Deployment 
az group create --location switzerlandnorth --name mdm-aci
az container create --resource-group mdm-aci --name mdm-roberta-onnx-gsteidom --image dominikgsteiger/mdm-roberta-onnx:latest  --dns-name-label mdm-roberta-onnx-gsteidom --ports 5000