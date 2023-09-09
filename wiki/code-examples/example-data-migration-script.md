# Example Data Migration Script

## Example Data Migration Script for Azure CosmosDB Using Bash script and \`jq\`

```bash
#!/bin/bash

# NOTE: To execute, ensure the script has the necessary permissions
# (chmod +x script_name.sh), jq is installed, and the Azure CLI is authenticated.

RESOURCE_GROUP_NAME="ResourceGroupName"
ACCOUNT_NAME="CosmosDBAccountName"
DATABASE_NAME="DatabaseName"
COLLECTION_NAME="CollectionName"

while IFS=, read -r manufacturer model color fuel type VIN registrationMark registrationDate mileage valuation features
do
    json=$(jq -n \
                --arg manufacturer "$manufacturer" \
                --arg model "$model" \
                --arg color "$color" \
                --arg fuel "$fuel" \
                --arg type "$type" \
                --arg VIN "$VIN" \
                --arg registrationMark "$registrationMark" \
                --arg registrationDate "$registrationDate" \
                --arg mileage "$mileage" \
                --arg valuation "$valuation" \
                --arg features "$features" \
                '{manufacturer: $manufacturer, model: $model, color: $color, fuel: $fuel, type: $type, VIN: $VIN, registrationMark: $registrationMark, registrationDate: $registrationDate, mileage: $mileage, valuation: $valuation, features: $features}')

    az cosmosdb sql container create-item \
        --resource-group $RESOURCE_GROUP_NAME \
        --account-name $ACCOUNT_NAME \
        --database-name $DATABASE_NAME \
        --container-name $COLLECTION_NAME \
        --value "$json"

done < cars.csv
```
