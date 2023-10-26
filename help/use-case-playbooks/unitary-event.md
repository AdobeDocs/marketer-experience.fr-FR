---
title: Événement unitaire
description: Cette page d’instructions permet de simuler le type de validation de parcours [!UICONTROL Événement unitaire].
exl-id: 314f967c-e10f-4832-bdba-901424dc2eeb
source-git-commit: 194667c26ed002be166ab91cc778594dc1f09238
workflow-type: tm+mt
source-wordcount: '889'
ht-degree: 100%

---

# Événement unitaire

## Étapes à suivre {#steps-to-follow}

>[!CONTEXTUALHELP]
>id="marketerexp_sampledata_unitaryevent"
>title="Comment faire ?"
>abstract="Veuillez suivre le lien pour plus de détails."

>[!IMPORTANT]
>
>Ces instructions peuvent changer d’un **[!UICONTROL playbook]** à l’autre. Veuillez toujours vous référer à la section des données d’exemple du **[!UICONTROL playbook]** concerné.

## Prérequis

* Utilisez le playbook pour créer les ressources des instances comme **[!UICONTROL Parcours]**, **[!UICONTROL Schémas]**, **[!UICONTROL Segments]**, **[!UICONTROL Messages]**, etc.

* Les ressources créées seront affichées sur la page `Bill Of Material`.

<!-- TODO: attached image needs to change once postman is removed from UI -->
![Page de nomenclature](../assets/bom-page.png)

>[!TIP]
>
>Si vous utilisez un terminal pour exécuter les cURL, vous pouvez définir des valeurs de variable auparavant, afin qu’il n’y ait pas besoin de remplacer ces valeurs dans des cURL individuelles.
>Par exemple, si vous définissez `ORG_ID=************@AdobeOrg`, le shell remplacera automatiquement chaque occurrence de `$ORG_ID` avec la valeur, afin que vous puissiez copier, coller et exécuter les cURL ci-dessous sans modification.
>
> Les variables suivantes sont utilisées tout au long de ce document :
>
> ACCESS_TOKEN
>
> API_KEY
>
> ORG_ID
>
> SANDBOX_NAME
>
> PROFILE_SCHEMA_REF
>
> PROFILE_DATASET_NAME
>
> PROFILE_DATASET_ID
>
> JOURNEY_ID
>
> PROFILE_BASE_CONNECTION_ID
>
> PROFILE_SOURCE_CONNECTION_ID
>
> PROFILE_TARGET_CONNECTION_ID
>
> PROFILE_INLET_URL
>
> CUSTOMER_MOBILE_NUMBER
>
> CUSTOMER_FIRST_NAME
>
> CUSTOMER_LAST_NAME
>
> EMAIL
>
> EVENT_SCHEMA_REF
>
> EVENT_DATASET_NAME
>
> EVENT_DATASET_ID
>
> EVENT_BASE_CONNECTION_ID
>
> EVENT_SOURCE_CONNECTION_ID
>
> EVENT_TARGET_CONNECTION_ID
>
> EVENT_INLET_URL
>
> TIMESTAMP
>
> UNIQUE_EVENT_ID

## Récupérer le jeton IMS

1. Veuillez lire la documentation [Authentification et accès aux API Experience Platform](https://experienceleague.adobe.com/docs/experience-platform/landing/platform-apis/api-authentication.html?lang=fr) pour générer le jeton d’accès.

## Publier le parcours créé par le playbook

Il y a deux façons de publier le parcours. Vous pouvez choisir l’une ou l’autre :

1. **En utilisant l’interface utilisateur AJO** : cliquez sur le lien Parcours sur `Bill Of Material Page`. Cette action vous redirige vers la page Parcours qui permet de cliquer sur le bouton **[!UICONTROL Publier]** afin de publier le parcours.

   ![Objet de parcours](../assets/journey-object.png)

1. **Utilisation des cURL**

   1. Publiez le parcours. La réponse contiendra l’ID de traitement nécessaire à l’étape suivante pour récupérer le statut de publication du parcours.

      ```bash
      curl --location --request POST "https://journey-private.adobe.io/authoring/jobs/journeyVersions/$JOURNEY_ID/deploy" \
      --header "Accept: */*" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-api-key: $API_KEY" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "Content-Type: application/json" 
      ```

   1. La publication du parcours peut prendre un certain temps. Pour vérifier le statut, exécutez la cURL ci-dessous, jusqu’à ce que le `response.status` soit `SUCCESS`. Veuillez attendre 10 à 15 secondes si la publication du parcours prend du temps.

      ```bash
      curl --location "https://journey-private.adobe.io/authoring/jobs/$JOB_ID" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-api-key: $API_KEY" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "Content-Type: application/json"
      ```

## Ingérer le profil du client ou de la cliente

>[!TIP]
>
>Si votre fournisseur de messagerie prend en charge les e-mails Plus, vous pouvez réutiliser la même adresse e-mail en ajoutant `+<variable>` dans votre e-mail. Ainsi, `usertest@email.com` peut être utilisé comme `usertest+v1@email.com` ou `usertest+24jul@email.com`. Cette méthode permet d’avoir un nouveau profil à chaque fois, tout en utilisant le même identifiant e-mail.
>
>P.S : les e-mails Plus constituent une fonctionnalité configurable qui doit être prise en charge par le fournisseur de messagerie. Vérifiez si vous pouvez recevoir des e-mails sur ces adresses avant de les utiliser en test.

1. Les nouveaux utilisateurs ou les nouvelles utilisatrices doivent créer les éléments **[!DNL customer dataset]** et **[!DNL HTTP Streaming Inlet Connection]**.
1. Si vous avez déjà créé les éléments **[!DNL customer dataset]** et **[!DNL HTTP Streaming Inlet Connection]**, passez à l’étape `5`.
1. Créez un jeu de données de profil client en exécutant la cURL ci-dessous.

   ```bash
   curl --location "https://platform.adobe.io/data/foundation/catalog/dataSet" \
   --header "Authorization: Bearer $ACCESS_TOKEN" \
   --header "x-gw-ims-org-id: $ORG_ID" \
   --header "x-sandbox-name: $SANDBOX_NAME" \
   --header "x-api-key: $API_KEY" \
   --header "Content-Type: application/json" \
   --data '{
       "name": "'$PROFILE_DATASET_NAME'",
       "schemaRef": {
           "id": "'$PROFILE_SCHEMA_REF'",
           "contentType": "application/vnd.adobe.xed-full-notext+json; version=1"
       },
       "tags": {
           "unifiedProfile": [
           "enabled:true"
           ],
           "unifiedIdentity": [
           "enabled:true"
           ]
       },
       "fileDescription": {
           "persisted": true,
           "containerFormat": "parquet",
           "format": "parquet"
       }
   }'
   ```

   La réponse s’affiche au format suivant : `"@/dataSets/<PROFILE_DATASET_ID>"`.

1. Créez la **[!DNL HTTP Streaming Inlet Connection]** en suivant les étapes ci-après.
   1. Créez une connexion de base.

      ```bash
      curl --location "https://platform.adobe.io/data/foundation/flowservice/connections?Cache-Control=no-cache" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "x-api-key: $API_KEY" \
      --header "Content-Type: application/json" \
      --data '{
          "name": "AbandonedCartProduct_Base_ConnectionForCustomerProfile_1694458293",
          "description": "Marketer Playground Playbook-Validation Customer Profile Base Connection 1",
          "auth": {
              "specName": "Streaming Connection",
              "params": {
                  "dataType": "xdm"
              }
          },
          "connectionSpec": {
              "id": "bc7b00d6-623a-4dfc-9fdb-f1240aeadaeb",
              "version": "1.0"
          }
      }'
      ```

      Obtenez l’identifiant de connexion de base à partir de la réponse et utilisez-le à la place de `PROFILE_BASE_CONNECTION_ID` dans les cURL suivantes.

   1. Créez une connexion source.

      ```bash
      curl --location "https://platform.adobe.io/data/foundation/flowservice/sourceConnections" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "Content-Type: application/json" \
      --header "x-api-key: $API_KEY" \
      --data '{
          "name": "AbandonedCartProduct_Source_ConnectionForCustomerProfile_1694458318",
          "description": "Marketer Playground Playbook-Validation Customer Profile Source Connection 1",
          "baseConnectionId": "'$PROFILE_BASE_CONNECTION_ID'",
          "connectionSpec": {
              "id": "bc7b00d6-623a-4dfc-9fdb-f1240aeadaeb",
              "version": "1.0"
          }
      }'
      ```

      Obtenez l’identifiant de connexion source à partir de la réponse et utilisez-le à la place de `PROFILE_SOURCE_CONNECTION_ID`.

   1. Créez une connexion cible.

      ```bash
      curl --location "https://platform.adobe.io/data/foundation/flowservice/targetConnections" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "Content-Type: application/json" \
      --header "x-api-key: $API_KEY" \
      --data '{
          "name": "AbandonedCartProduct_Target_ConnectionForCustomerProfile_1694458407",
          "description": "Marketer Playground Playbook-Validation Customer Profile Target Connection 1",
          "data": {
              "format": "parquet_xdm",
              "schema": {
                  "version": "application/vnd.adobe.xed-full+json;version=1",
                  "id": "'$PROFILE_SCHEMA_REF'"
              },
              "properties": null
          },
          "connectionSpec": {
              "id": "c604ff05-7f1a-43c0-8e18-33bf874cb11c",
              "version": "1.0"
          },
          "params": {
              "dataSetId": "'$PROFILE_DATASET_ID'"
          }
      }'
      ```

      Obtenez l’identifiant de connexion cible à partir de la réponse et utilisez-le à la place de `PROFILE_TARGET_CONNECTION_ID`.

   1. Créez un flux de données.

      ```bash
      curl --location "https://platform.adobe.io/data/foundation/flowservice/flows" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "Content-Type: application/json" \
      --header "x-api-key: $API_KEY" \
      --data '{
          "name": "AbandonedCartProduct_Dataflow_ForCustomerCustomerProfile_1694460528",
          "description": "Marketer Playground Playbook-Validation Customer Profile Dataflow 1",
          "flowSpec": {
              "id": "d8a6f005-7eaf-4153-983e-e8574508b877",
              "version": "1.0"
          },
          "sourceConnectionIds": [
              "'$PROFILE_SOURCE_CONNECTION_ID'"
          ],
          "targetConnectionIds": [
              "'$PROFILE_TARGET_CONNECTION_ID'"
          ]
      }'
      ```

   1. Obtenez la connexion de base. Le résultat contiendra l’inletUrl requis pour envoyer les données de profil.

      ```bash
      curl --location "https://platform.adobe.io/data/foundation/flowservice/connections/$PROFILE_BASE_CONNECTION_ID" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "Content-Type: application/json" \
      --header "x-api-key: $API_KEY"
      ```

      Obtenez l’inletUrl à partir de la réponse et utilisez-le à la place de `PROFILE_INLET_URL`.

1. À cette étape, la personne doit disposer des valeurs de `PROFILE_DATASET_ID` et `PROFILE_INLET_URL`. Dans le cas contraire, reportez-vous à l’étape `3` ou `4` respectivement.
1. Pour ingérer un client ou une client, la personne doit remplacer `CUSTOMER_MOBILE_NUMBER`, `CUSTOMER_FIRST_NAME`, `CUSTOMER_LAST_NAME` et `EMAIL` dans les cURL ci-dessous.

   1. `CUSTOMER_MOBILE_NUMBER` est le numéro de téléphone mobile, par exemple `+1 000-000-0000`.
   1. `CUSTOMER_FIRST_NAME` est le prénom de la personne.
   1. `CUSTOMER_LAST_NAME` est le nom de la personne.
   1. `EMAIL` est l’adresse e-mail de la personne. Il est essentiel d’utiliser une adresse distincte afin de pouvoir ingérer un nouveau profil.

1. Exécutez enfin la cURL pour ingérer le profil client. Mettez à jour `body.xdmEntity.consents.marketing.preferred` sur `email`, `sms` ou `push` en fonction des canaux que vous avez l’intention de vérifier. Paramétrez également la valeur `val` correspondante sur `y`.

   ```bash
   curl --location "$PROFILE_INLET_URL?synchronousValidation=true" \
   --header 'Content-Type: application/json' \
   --data-raw '{
       "header": {
           "schemaRef": {
               "id": "'$PROFILE_SCHEMA_REF'",
               "contentType": "application/vnd.adobe.xed-full+json;version=1.0"
           },
           "imsOrgId": "'$ORG_ID'",
           "datasetId": "'$PROFILE_DATASET_ID'",
           "source": {
               "name": "Streaming dataflow - 1694460605"
           }
       },
       "body": {
           "xdmMeta": {
               "schemaRef": {
                   "id": "'$PROFILE_SCHEMA_REF'",
                   "contentType": "application/vnd.adobe.xed-full+json;version=1.0"
               }
           },
           "xdmEntity": {
           "consents": {
               "marketing": {
                   "preferred": "email",
                   "email": {
                       "val": "y"
                   },
                   "push": {
                       "val": "n"
                   },
                   "sms": {
                       "val": "n"
                   }
               }
           },
           "mobilePhone": {
               "number": "'$CUSTOMER_MOBILE_NUMBER'",
               "status": "active"
           },
           "person": {
               "name": {
               "firstName": "'$CUSTOMER_FIRST_NAME'",
               "lastName": "'$CUSTOMER_LAST_NAME'"
               }
           },
           "personalEmail": {
               "address": "'$EMAIL'"
           },
           "testProfile": false
           }
       }
   }'
   ```

## Ingérer un événement de déclenchement de parcours

1. Les nouveaux utilisateurs ou les nouvelles utilisatrices doivent créer les éléments **[!DNL event dataset]** et **[!DNL HTTP Streaming Inlet Connection for events]**.
1. Si vous avez déjà créé les éléments **[!DNL event dataset]** et **[!DNL HTTP Streaming Inlet Connection for events]**, passez à l’étape `5`.
1. Créez un jeu de données d’événement en exécutant la cURL ci-dessous.

   ```bash
   curl --location "https://platform.adobe.io/data/foundation/catalog/dataSet" \
   --header "Authorization: Bearer $ACCESS_TOKEN" \
   --header "x-gw-ims-org-id: $ORG_ID" \
   --header "x-sandbox-name: $SANDBOX_NAME" \
   --header "x-api-key: $API_KEY" \
   --header "Content-Type: application/json" \
   --data '{
       "name": "'$EVENT_DATASET_NAME'",
       "schemaRef": {
           "id": "'$EVENT_SCHEMA_REF'",
           "contentType": "application/vnd.adobe.xed-full-notext+json; version=1"
       },
       "tags": {
           "unifiedProfile": [
               "enabled:true"
           ],
           "unifiedIdentity": [
               "enabled:true"
           ]
       },
       "fileDescription": {
           "persisted": true,
           "containerFormat": "parquet",
           "format": "parquet"
       }
   }'
   ```

   La réponse s’affiche au format suivant : `"@/dataSets/<EVENT_DATASET_ID>"`.

1. Créez la **[!DNL HTTP Streaming Inlet Connection for events]** en suivant les étapes ci-après.
   <!-- TODO: Is the name unique? If so, we may need to generate and provide in variables.txt-->
   1. Créez une connexion de base.

      ```bash
      curl --location "https://platform.adobe.io/data/foundation/flowservice/connections?Cache-Control=no-cache" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "x-api-key: $API_KEY" \
      --header "Content-Type: application/json" \
      --data '{
          "name": "AbandonedCartProduct_Base_ConnectionForAEPDemoSchema_1694461448",
          "description": "Marketer Playground Playbook-Validation AEP Demo Schema Base Connection 1",
          "auth": {
              "specName": "Streaming Connection",
              "params": {
                  "dataType": "xdm"
              }
          },
          "connectionSpec": {
              "id": "bc7b00d6-623a-4dfc-9fdb-f1240aeadaeb",
              "version": "1.0"
          }
      }'
      ```

      Obtenez l’identifiant de connexion de base à partir de la réponse et utilisez-le à la place de `EVENT_BASE_CONNECTION_ID`.

   1. Créez une connexion source.

      ```bash
      curl --location "https://platform.adobe.io/data/foundation/flowservice/sourceConnections" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "x-api-key: $API_KEY" \
      --header "Content-Type: application/json" \
      --data '{
          "name": "AbandonedCartProduct_Source_ConnectionForAEPDemoSchema_1694461464",
          "description": "Marketer Playground Playbook-Validation AEP Demo Schema Source Connection 1",
          "baseConnectionId": "'$EVENT_BASE_CONNECTION_ID'",
          "connectionSpec": {
              "id": "bc7b00d6-623a-4dfc-9fdb-f1240aeadaeb",
              "version": "1.0"
          }
      }'
      ```

      Obtenez l’identifiant de connexion source à partir de la réponse et utilisez-le à la place de `EVENT_SOURCE_CONNECTION_ID`.

   1. Créez une connexion cible.

      ```bash
      curl --location "https://platform.adobe.io/data/foundation/flowservice/sourceConnections" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "x-api-key: $API_KEY" \
      --header "Content-Type: application/json" \
      --data '{
          "name": "AbandonedCartProduct_Target_ConnectionForAEPDemoSchema_1694802667",
          "description": "Marketer Playground Playbook-Validation AEP Demo Schema Target Connection 1",
          "data": {
              "format": "parquet_xdm",
              "schema": {
                  "version": "application/vnd.adobe.xed-full+json;version=1",
                  "id": "'$EVENT_SCHEMA_REF'"
              },
              "properties": null
          },
          "connectionSpec": {
              "id": "c604ff05-7f1a-43c0-8e18-33bf874cb11c",
              "version": "1.0"
          },
          "params": {
              "dataSetId": "'$EVENT_DATASET_ID'"
          }
      }'
      ```

      Obtenez l’identifiant de connexion cible à partir de la réponse et utilisez-le à la place de `EVENT_TARGET_CONNECTION_ID`.

   1. Créez un flux de données.

      ```bash
      curl --location "https://platform.adobe.io/data/foundation/flowservice/flows" \
      --header "Authorization: Bearer $ACCESS_TOKEN" \
      --header "x-gw-ims-org-id: $ORG_ID" \
      --header "x-sandbox-name: $SANDBOX_NAME" \
      --header "x-api-key: $API_KEY" \
      --header "Content-Type: application/json" \
      --data '{
          "name": "AbandonedCartProduct_Dataflow_ForCustomerAEPDemoSchema_1694461564",
          "description": "Marketer Playground Playbook-Validation AEP Demo Schema Dataflow 1",
          "flowSpec": {
              "id": "d8a6f005-7eaf-4153-983e-e8574508b877",
              "version": "1.0"
          },
          "sourceConnectionIds": [
              "'$EVENT_SOURCE_CONNECTION_ID'"
          ],
          "targetConnectionIds": [
              "'$EVENT_TARGET_CONNECTION_ID'"
          ]
      }'
      ```

   1. Obtenez la connexion de base. Le résultat contiendra l’inletUrl requis pour envoyer les données de profil.

   ```bash
   curl --location "https://platform.adobe.io/data/foundation/flowservice/connections/$EVENT_BASE_CONNECTION_ID" \
       --header "Authorization: Bearer $ACCESS_TOKEN" \
       --header "x-gw-ims-org-id: $ORG_ID" \
       --header "x-sandbox-name: $SANDBOX_NAME" \
       --header "x-api-key: $API_KEY" \
       --header "Content-Type: application/json" 
   ```

   Obtenez l’inletUrl à partir de la réponse et utilisez-le à la place de `EVENT_INLET_URL`.

1. À cette étape, la personne doit disposer des valeurs de `EVENT_DATASET_ID` et `EVENT_INLET_URL`. Dans le cas contraire, reportez-vous à l’étape `3` ou `4` respectivement.
1. Pour ingérer un événement, la personne doit modifier la variable temporelle `TIMESTAMP` dans le corps de la requête de la cURL ci-dessous.

   1. Remplacez `body.xdmEntity` par le contenu de l’événement téléchargé json.
   1. `TIMESTAMP` indique l’heure à laquelle l’événement s’est produit. Utilisez la date et l’heure dans le fuseau horaire UTC, par exemple `2023-09-05T23:57:00.071+00:00`.
   1. Définissez une valeur unique pour la variable `UNIQUE_EVENT_ID`.

   ```bash
   curl --location "$EVENT_INLET_URL?synchronousValidation=true" \
   --header 'Content-Type: application/json' \
   --data-raw '{
       "header": {
           "schemaRef": {
               "id": "'$EVENT_SCHEMA_REF'",
               "contentType": "application/vnd.adobe.xed-full+json;version=1.0"
           },
           "imsOrgId": "'$ORG_ID'",
           "datasetId": "'$EVENT_DATASET_ID'",
           "source": {
               "name": "Streaming dataflow - 8/31/2023 9:04:25 PM"
           }
       },
       "body": {
           "xdmMeta": {
               "schemaRef": {
                   "id": "'$EVENT_SCHEMA_REF'",
                   "contentType": "application/vnd.adobe.xed-full+json;version=1.0"
               }
           },
           "xdmEntity": {
               "endUserIDs": {
                   "_experience": {
                       "aaid": {
                           "id": "'$EMAIL'"
                       },
                       "emailid": {
                           "id": "'$EMAIL'"
                       }
                   }
               },
               "_experience": {
                   "analytics": {
                       "customDimensions": {
                           "eVars": {
                           "eVar235": "AC11147"
                           }
                       }
                   }
               },
               "_id": "'$UNIQUE_EVENT_ID'",
               "commerce": {
                   "productListAdds": {
                       "value": 11498
                   }
               },
               "eventType": "commerce.productListAdds",
               "productListItems": [
                   {
                       "_id": "ACS1620",
                       "SKU": "P1",
                       "_experience": {
                           "analytics": {
                           "customDimensions": {
                               "eVars": {
                                   "eVar1": "Pants"
                               }
                           }
                           }
                       },
                       "currencyCode": "USD",
                       "name": "Sample value",
                       "priceTotal": 30841.13,
                       "product": "https://ns.adobe.com/xdm/common/uri",
                       "productAddMethod": "Sample value",
                       "quantity": 1
                   },
                   {
                       "_id": "ACS1729",
                       "SKU": "P2",
                       "_experience": {
                           "analytics": {
                               "customDimensions": {
                                   "eVars": {
                                       "eVar1": "Galliano"
                                   }
                               }
                           }
                       },
                       "currencyCode": "USD",
                       "name": "Sample value",
                       "priceTotal": 20841.13,
                       "product": "https://ns.adobe.com/xdm/common/uri",
                       "productAddMethod": "Sample value",
                       "quantity": 2
                   }
               ],
               "timestamp": "'$TIMESTAMP'",
               "web": {
                   "webInteraction": {
                       "URL": "https://experienceleague.adobe.com/docs/experience-platform/edge/data-collection/collect-commerce-data.html?lang=en",
                       "name": "Sample value",
                       "region": "Sample value"
                   },
                   "webPageDetails": {
                       "URL": "https://experienceleague.adobe.com/docs/experience-platform/edge/data-collection/collect-commerce-data.html?lang=en",
                       "isErrorPage": false,
                       "isHomePage": false,
                       "name": "Sample value",
                       "pageViews": {
                           "id": "Sample value",
                           "value": 1
                       },
                       "server": "Sample value",
                       "siteSection": "Sample value",
                       "viewName": "Sample value"
                   },
                   "webReferrer": {
                   "URL": "Sample value",
                   "type": "internal"
                   }
               }
           }
       }
   }'
   ```

## Validation finale

Vous devez recevoir un message sur le canal préféré que vous avez choisi et qui est utilisé à l’étape **[!DNL Ingest the Customer Profile]**. `8`

* `SMS` si le canal préféré est `sms` sur `customer_country_code` et `customer_mobile_no`.
* `Email` si le canal préféré est `email` sur `email`.

Vous pouvez également cocher `Journey Report`. Pour cela, cliquez sur `Journey Object`, puis sur `Bill of Materials page`, ce qui vous redirigera vers `Journey Details page`.

Pour tout parcours publié, la personne doit pouvoir accéder à un bouton **[!UICONTROL Afficher le rapport]**
![Page Rapport du parcours](../assets/journey-report-page.png)


## Nettoyer

Ne laissez pas plusieurs instances de `Journey` s’exécuter simultanément. Arrêtez le parcours s’il n’est destiné qu’à la validation une fois que celle-ci est terminée.
