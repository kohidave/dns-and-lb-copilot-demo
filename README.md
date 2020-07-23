# DNS, HTTPS, Shared Load Balancers

In this little demo application we'll create a new application called `copilot-demos` which will use the 'myhappylittle.cloud'

## Step 1:

In this example, we'll start by creating an application and associating my domain name `myhappylittle.cloud` to it. This domain name is registered with Route53 in my account - but will work as long as there's a correctly configured hosted zone with that name in your account.

```sh
copilot app init copilot-demos --domain myhappylittle.cloud
```

![App Init](https://user-images.githubusercontent.com/828419/87612887-9b50b680-c6c0-11ea-8a31-d5e616f05a45.png)

## Step 2:

Now we'll set up our first front-end service. To do this, we'll run the `copilot init` command and select a Load Balanced Web Service as our service type. Copilot will extract the exposed port from the Dockerfile and forward traffic from the load balancer there.

In this example, I'll set up my test environment in another account by using the `--profile` account. You don't have to do this, but this goes to show that Copilot can delegate DNS across accounts.

```sh
copilot init --profile prod-acc
```

![Init](https://user-images.githubusercontent.com/828419/88345819-aed0d280-ccfb-11ea-81df-a58f06fc20b9.png)

## Step 3:

Now let's set up our second front-end service. The process is the same. As with all Copilot commands, if you'd like to skip the wizard experience, you can pass in values as a flag.


Notice how this time, the setup is much faster. That's because the test environment already exists and doesn't need to be created. Instead, our second service will use the resources already provisioned in the test environment, including the Load Balancer.

```sh
copilot init --profile prod-acc
```

![Init](https://user-images.githubusercontent.com/828419/88346256-c65c8b00-ccfc-11ea-8172-d4f8a35dcfa1.png)


## Tadah

Now you can access

https://svc1.test.copilot-demos.myhappylittle.cloud/
https://svc2.test.copilot-demos.myhappylittle.cloud/

Which are two separate services, behind the same Application Load Balancer.

Copilot took care of the ACM Cert generation and validation, DNS Subdomain delegation across accounts and spinning up the services.


![Svc1](https://user-images.githubusercontent.com/828419/88346361-fdcb3780-ccfc-11ea-8c3d-45ec022ffddc.png)
![Svc2](https://user-images.githubusercontent.com/828419/88346370-fefc6480-ccfc-11ea-9343-23fb40946fc8.png)

# Setting up CI/CD

In this section, we'll set up a Continuous Delivery pipeline powered by AWS CodePipeline.

## Step 1:

In this example, we'll set up a pipeline that deploys both svc1 and svc2. You can also set up a single pipeline per service. We'll start off by running pipeline init and walking through the wizard. We'll need a GitHub [personal access token](https://github.com/settings/tokens/new) with `repo` and  `admin:repo_hook` permissions.

```sh
copilot pipeline init
```

## Step 2:

This will generate a `pipeline.yml` file in your copilot directory. Let's update that to trigger whenever we push to our `release` branch.

![Editing Pipeline](https://user-images.githubusercontent.com/828419/88346831-24d63900-ccfe-11ea-85e6-fbbcec2cf91a.png)

## Step 3:

Now, let's push this code to our release branch:

```sh
git add . && git commit -m "Adding Pipeline config" && git push origin mainline:release
```

And finally, deploy our pipeline:

```sh
copilot pipeline update
```

![Pipeline Status](https://user-images.githubusercontent.com/828419/88347136-e2612c00-ccfe-11ea-872b-c662e6f41a63.png)


Now, every time we push to our release branch, svc1 and svc2 will be deployed.
