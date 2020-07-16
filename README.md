# DNS, HTTPS and Shared Load Balancers

In this little demo application we'll create a new application called `copilot-demos` which will use the 'myhappylittle.cloud'

## Step 1:

In this example, we'll start by creating an application and associating my domain name `myhappylittle.cloud` to it. This domain name is registered with Route53 in my account - but will work as long as there's a correctly configured hosted zone with that name in your account.

```sh
copilot app init copilot-demos --domain myhappylittle.cloud
```

![App Init](https://user-images.githubusercontent.com/828419/87612887-9b50b680-c6c0-11ea-8a31-d5e616f05a45.png)

## Step 2:

I'm going to skip the `copilot init` workflow and set up an environment manually just to show what steps are happening.

My domain name, `myhappylittle.cloud` is in one account, but I want my environment and services to be in a different account, so I'll pass in a profile for that account when I set up my production environment (the profile flag is optional).

```sh
copilot env init --name prod --profile prod-acc
```

![Env Init](https://user-images.githubusercontent.com/828419/87612884-9ab82000-c6c0-11ea-8dc3-a056bf2950b4.png)

## Step 3:

Now let's set up our services.  You can omit these flags and have the wizard walk you through setting up your service.

For service 1:
```sh
copilot svc init --name svc2 --svc-type "Load Balanced Web Service" --dockerfile ./svc2/Dockerfile
```

For service 2:
```sh
copilot svc init --name svc2 --svc-type "Load Balanced Web Service" --dockerfile ./svc2/Dockerfile
```

## Step 4:

Last step! Let's deploy these services (again, you can omit the flags).

For service 1:
```sh
copilot deploy --name svc1 --env prod
```

For service 2:
```sh
copilot deploy --name svc2 --env prod
```

![Deploy](https://user-images.githubusercontent.com/828419/87612877-97bd2f80-c6c0-11ea-89be-eade42d27daa.png)

## Tadah

Now you can access

https://svc1.prod.copilot-demos.myhappylittle.cloud/
https://svc2.prod.copilot-demos.myhappylittle.cloud/

Which are two separate services, behind the same App Load Balancer.

Copilot took care of the ACM Cert generation and validation, DNS Subdomain delegation across accounts and spinning up the services.


![Service 1](https://user-images.githubusercontent.com/828419/87613005-ea96e700-c6c0-11ea-9a81-c607ff918673.png)

![Service 2](https://user-images.githubusercontent.com/828419/87613008-ed91d780-c6c0-11ea-8136-f37f2067b45c.png)

