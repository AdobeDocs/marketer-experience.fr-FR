---
title: Événement unitaire
description: Cette page d’instructions permet de simuler le type de validation de parcours [!UICONTROL Événement unitaire].
source-git-commit: 0a52611530513fc036ef56999fde36a32ca0f482
workflow-type: ht
source-wordcount: '749'
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

* Le logiciel Postman doit être installé.
* Utilisez le playbook pour créer les ressources des instances comme **[!UICONTROL Parcours]**, **[!UICONTROL Schémas]**, **[!UICONTROL Segments]**, **[!UICONTROL Messages]**, etc.

Les ressources créées seront affichées sur la page `Bill Of Material`.

![Page de nomenclature](../assets/bom-page.png)

## Préparer Postman avec la collection requise

1. Consultez l’application **[!UICONTROL Playbook de cas d’utilisation]**.
1. Cliquez sur la vignette **[!UICONTROL Playbook]** correspondante pour accéder à la page de détail **[!UICONTROL Playbook]**.
1. Consultez la page **[!UICONTROL Nomenclature]** afin d’y rechercher la section **[!UICONTROL Données d’exemple]**.
1. Téléchargez le `postman.json` en cliquant sur les boutons correspondants de l’interface utilisateur.
1. Importez `postman.json` dans le **[!DNL Postman Software]**.
1. Créez un environnement Postman dédié à cette validation (par exemple, `Adobe <PLAYBOOK_NAME>`).

## Récupérer le jeton IMS

>[!NOTE]
>
>Toutes les variables d’environnement respectent la casse. Veuillez donc toujours utiliser le nom exact de la variable.

1. Veuillez lire la documentation [Authentification et accès aux API Experience Platform](https://experienceleague.adobe.com/docs/experience-platform/landing/platform-apis/api-authentication.html?lang=fr) pour générer le jeton d’accès.
1. Stockez la valeur du jeton d’accès dans des variables d’environnement nommées `ACCESS_TOKEN`.
1. Stockez d’autres valeurs liées à l’authentification, comme `API_KEY`, `IMS_ORG` et `SANDBOX_NAME`, dans des variables d’environnement.

>[!IMPORTANT]
>
>Avant d’exécuter une API à partir de Postman, assurez-vous que toutes les variables d’environnement requises ont été ajoutées.

## Publier le parcours créé par le playbook

Il y a deux façons de publier le parcours. Vous pouvez choisir l’une ou l’autre :

1. **En utilisant l’interface utilisateur AJO** : cliquez sur le lien Parcours sur `Bill Of Material Page`. Cette action vous redirige vers la page Parcours qui permet de cliquer sur le bouton **[!UICONTROL Publier]** afin de publier le parcours.

   ![Objet de parcours](../assets/journey-object.png)

1. **En utilisant l’API Postman**

   1. Déclenchez la requête **[!DNL Publish Journey]** depuis **[!DNL Journey Publish]** > **[!DNL Queue journey publish job]**.
   1. La publication du parcours peut prendre un certain temps. Pour vérifier le statut, exécutez l’API de vérification du statut de publication du parcours, jusqu’à ce que le `response.status` soit `SUCCESS`. Veuillez attendre 10 à 15 secondes si la publication du parcours prend du temps.

   >[!NOTE]
   >
   >Toutes les variables d’environnement respectent la casse. Veuillez donc toujours utiliser le nom exact de la variable.

## Ingérer le profil du client ou de la cliente

>[!TIP]
>
>Vous pouvez réutiliser la même adresse e-mail en ajoutant `+<variable>` à votre e-mail. Par exemple, `usertest@email.com` peut être réutilisé comme `usertest+v1@email.com` ou `usertest+24jul@email.com`. Cette méthode permet d’avoir un nouveau profil à chaque fois, tout en utilisant le même identifiant e-mail.

1. Les nouveaux utilisateurs ou les nouvelles utilisatrices doivent créer les éléments **[!DNL customer dataset]** et **[!DNL HTTP Streaming Inlet Connection]**.
1. Si vous avez déjà créé les éléments **[!DNL customer dataset]** et **[!DNL HTTP Streaming Inlet Connection]**, passez à l’étape `5`.
1. Déclenchez **[!DNL Customer Profile Ingestion]** > **[!DNL Create Customer Profile InletId]** > **[!DNL Create Dataset]** pour créer **[!DNL customer dataset]**. Vous stockerez ainsi un élément `CustomerProfile_dataset_id` dans les variables d’environnement Postman.
1. Créez **[!DNL HTTP Streaming Inlet Connection]**. Pour cela utilisez les API Postman sous **[!DNL Customer Profile Ingestion > Create Customer Profile InletId]**.

   1. `CustomerProfile_dataset_id` doit être disponible dans les variables d’environnement Postman. Si tel n’est pas le cas, reportez-vous à l’étape `3`.
   1. Déclenchez **[!DNL `CREATE Base Connection`]** pour [!DNL create base connection].
   1. Déclenchez **[!DNL `CREATE Source Connection`]** pour [!DNL create source connection].
   1. Déclenchez **[!DNL `CREATE Target Connection`]** pour [!DNL create target connection].
   1. Déclenchez **[!DNL `CREATE Dataflow`]** pour [!DNL create dataflow].
   1. Déclenchez **[!DNL `GET Base Connection`]**. Cette action stocke automatiquement `CustomerProfile_inlet_id` dans les variables d’environnement Postman.

1. A cette étape, `CustomerProfile_dataset_id` et `CustomerProfile_inlet_id` doivent se trouver dans les variables d’environnement Postman. Si tel n’est pas le cas, veuillez vous référer à l’étape `3` ou `4` respectivement.
1. Pour ingérer le client ou la cliente, la personne doit stocker `customer_country_code`, `customer_mobile_no`, `customer_first_name`, `customer_last_name` et `email` dans les variables d’environnement Postman.

   1. `customer_country_code` est l’indicatif téléphonique mobile, par exemple `91` ou `1`.
   1. `customer_mobile_no` est le numéro de téléphone mobile, par exemple `9987654321`.
   1. `customer_first_name` est le prénom de la personne.
   1. `customer_last_name` est le nom de la personne.
   1. `email` est l’adresse e-mail de la personne. Il est essentiel d’utiliser une adresse distincte afin de pouvoir ingérer un nouveau profil.

1. Mettez à jour la requête Postman **[!DNL Customer Ingestion]** > **[!DNL Customer Streaming Ingestion]** pour modifier le canal préféré du client ou de la cliente. Par défaut, [!DNL `email`] est configuré dans la requête.

   ```js
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
   }
   ```

1. Changez le canal préféré en `sms` ou `push` et modifiez les valeurs respectives des canaux en `y` et `n`, par exemple.

   ```js
   "consents": {
       "marketing": {
           "preferred": "sms",
           "email": {
               "val": "n"
           },
           "push": {
               "val": "n"
           },
           "sms": {
               "val": "y"
           }
       }
   }
   ```

1. Enfin, déclenchez **[!DNL `Customer Profile Ingestion > Customer Profile Streaming Ingestion`]** pour ingérer le profil du client ou de la cliente.

## Événement d’ingestion

1. Les nouveaux utilisateurs ou les nouvelles utilisatrices doivent créer les éléments **[!DNL event dataset]** et **[!DNL HTTP Streaming Inlet Connection for events]**.
1. Si vous avez déjà créé les éléments **[!DNL event dataset]** et **[!DNL HTTP Streaming Inlet Connection for events]**, passez à l’étape `5`.
1. Déclenchez **[!DNL `Schemas Data Ingestion > AEP Demo Schema Ingestion > Create AEP Demo Schema InletId > Create Dataset`]** pour créer **[!DNL event dataset]**, afin de stocker un élément `AEPDemoSchema_dataset_id` dans les variables d’environnement Postman.
1. Créez **[!DNL HTTP Streaming Inlet Connection for events]**. Utilisez les API Postman sous **[!DNL Schemas Data Ingestion]** > **[!DNL AEP Demo Schema Ingestion]** > **[!DNL Create AEP Demo Schema InletId]**.

   1. `AEPDemoSchema_dataset_id` doit être disponible dans les variables d’environnement Postman. Si tel n’est pas le cas, reportez-vous à l’étape `3`.
   1. Déclenchez **[!DNL `CREATE Base Connection`]** pour [!DNL create base connection].
   1. Déclenchez **[!DNL `CREATE Source Connection`]** pour [!DNL create source connection].
   1. Déclenchez **[!DNL `CREATE Target Connection`]** pour [!DNL create target connection].
   1. Déclenchez **[!DNL `CREATE Dataflow`]** pour [!DNL create dataflow].
   1. Déclenchez **[!DNL `GET Base Connection`]**. Cette action stocke automatiquement `AEPDemoSchema_inlet_id` dans les variables d’environnement Postman.

1. À cette étape, `AEPDemoSchema_dataset_id` et `AEPDemoSchema_inlet_id` doivent faire parti des variables d’environnement Postman. Si tel n’est pas le cas, veuillez vous référer à l’étape `3` ou `4` respectivement.
1. Pour ingérer un événement, la personne doit modifier la variable temporelle `timestamp` dans le corps de la requête de **[!DNL Schemas Data Ingestion]** > **[!DNL AEP Demo Schema Ingestion]** > **[!DNL AEP Demo Schema Streaming Ingestion]** dans Postman.

   1. `timestamp` est l’heure à laquelle l’événement s’est produit. Utilisez l’heure actuelle, par exemple `2023-07-21T16:37:52+05:30`. Ajustez le fuseau horaire en fonction de vos besoins.

1. Déclenchez **[!DNL Schemas Data Ingestion > AEP Demo Schema Ingestion > AEP Demo Schema Streaming Ingestion]** pour ingérer l’événement, de sorte que le parcours puisse être déclenché.

## Validation finale

Vous devez recevoir un message sur le canal préféré que vous avez choisi et qui est utilisé à l’étape **[!DNL Ingest the Customer Profile]**. `8`

* `SMS` si le canal préféré est `sms` sur `customer_country_code` et `customer_mobile_no`.
* `Email` si le canal préféré est `email` sur `email`.

Vous pouvez également cocher `Journey Report`. Pour cela, cliquez sur `Journey Object`, puis sur `Bill of Materials page`, ce qui vous redirigera vers `Journey Details page`.

Pour tout parcours publié, la personne doit pouvoir accéder à un bouton **[!UICONTROL Afficher le rapport]**
![Page Rapport du parcours](../assets/journey-report-page.png)


## Nettoyer

Ne laissez pas plusieurs instances de `Journey` s’exécuter simultanément. Arrêtez le parcours s’il n’est destiné qu’à la validation une fois que celle-ci est terminée.

