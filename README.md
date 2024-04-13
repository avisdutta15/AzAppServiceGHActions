# AzAppServiceGHActions
A small PoC to showcase how to deploy .net application to Azure App Service using GitHub Actions.

    Prerequisits:
    1. You have already created a repository.
    2. The repository is cloned locally.
    3. A .NET Core WebAPI application is created, commited and pushed to the repo.

    Steps:
    1. Create an App Registration in Azure.
        a. Goto Microsoft Entra ID -> App Registrations -> (+ New Registration)
        b. Give the name as github-workflow-oidc-demo.
        c. Supported account types -> Single Tenant
        d. Register

    2. Create Federated Credentails
        a. Goto Microsoft Entra ID -> App Registrations -> Owned applications
        b. Select the app registration called - github-workflow-oidc-demo
        c. Manage -> Certificates and Secrets -> Federated credentials -> (+ Add credential)
        d. Select Github Actions deploying Azure Resources as the scenario.
        e. Fill in the details
            i.  Organization- Select your github organization. Goto your repo in Github. The url will be www.github.com/<Organization>/<Repository>. Copy the Organization from there.
            ii. Repository  - Copy the Repository from there.
            iii.Entity Type - Branch
            iv. Github Branch Name - main
            v.  Closely check the subject identifier. This value is used while establishing connection between Github Actions workflow and Microsoft Entra ID.
            vi. Credential details -> Name - github-workflow-oidc-demo-fc
        f. Click on Add.

    3. Role Assignment at subscription level.
        a. Goto Subscriptions -> Select the subscription -> Access Control (IAM)
        b. +Add (Add - role Assignment)
        c. Under Role -> Job function roles -> Reader
        d. Under Members -> Assign access to -> Select User, group, or service principal.
           Under Members -> Members -> + Select Members -> Type the app registration/service principal name i.e. github-workflow-oidc-demo
        e. Review + assign
        Note: The role assignment at subscription level is for test purpose only. We can remove it later only if 
              the role assignment is given to any resource under it. Else the github workflow will fail.
        
    4. Role Assignment at resource level i.e. WebApp level.
        a. Goto App Services -> Select the app service -> Access Control (IAM)
        b. +Add (Add - role Assignment)
        c. Under Role -> Privileged administrator roles -> Contributor
        d. Under Members -> Assign access to -> Select User, group, or service principal.
           Under Members -> Members -> + Select Members -> Type the app registration/service principal name i.e. github-workflow-oidc-demo
        e. Review + assign

    5. Create Github repository secrets 
        a. Copy the ClientId, TenantId and SubscriptionId from the github-workflow-oidc-demo overview page.
           SubscriptionId will be available under Subscriptions (not in App Registration page).
        b. Goto repository in github -> Settings
        c. Secrets and variables -> Actions -> Repository secrets -> New repository secret.
            AZURE_CLIENT_ID
            AZURE_TENANT_ID
            AZURE_SUBSCRIPTION_ID

    6. Create the github workflow and run it to deploy the app to the app service.

