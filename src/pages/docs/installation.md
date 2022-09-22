---
title: Installing Gimlet on a local cluster
description: How to install Gimlet on k3s / k3d or Rancher / Docker Desktop or Minikube or kind
---

On this page you can learn how to install Gimlet on k3s / k3d or Rancher / Docker Desktop or Minikube or kind.

---

You can install Gimlet with an installer. The installer initiates a gitops environment and puts Gimlet into its gitops repository. This way Gimlet itself is managed by gitops.

You will access the dashboard with a kubectl port-forward command after installation, later you can decide to [move the installation to a real domain and HTTPS](/docs/installation#moving-the-installation-to-a-real-domain-and-https).

## Prerequisites

- A [https://github.com](https://github.com) personal or organization account.
- A Kubernetes cluster running on your laptop. Gimlet is tested with:
    - k3s / k3d
    - Rancher / Docker Desktop
    - Minikube
    - kind

## Starting the installer with a oneliner

The following oneliner kickstarts the Gimlet installer.

```bash
curl -L -s https://get.gimlet.io | bash -s trial
```

## Creating a Github Application

First, the installer creates a Github Application to manage all Gimlet access with it.

![Creating a Github application](https://images.tango.us/public/screenshot_e090274e-ea94-4621-b6f0-b7a770d6815b.png?crop=focalpoint&fit=crop&fp-x=0.4999&fp-y=0.2487&fp-z=3.1368&w=1200&mark-w=0.2&mark-pad=0&mark64=aHR0cHM6Ly9pbWFnZXMudGFuZ28udXMvc3RhdGljL21hZGUtd2l0aC10YW5nby13YXRlcm1hcmsucG5n&ar=3840%3A1960)

Install the newly created Github Application and select what repositories Gimlet should have access to. It is most convenient to allow all repositories. If you prefer a smaller set of repositories, select a few application repositories you want deploy with Gimlet and remember to extend the repository list if you want to roll out Gimlet for new apps.

![Step 3 screenshot](https://images.tango.us/public/edited_image_3c1732d7-a923-47d7-bea8-fc69190d5e57.png?crop=focalpoint&fit=crop&fp-x=0.4969&fp-y=0.5822&fp-z=2.2336&w=1200&mark-w=0.2&mark-pad=0&mark64=aHR0cHM6Ly9pbWFnZXMudGFuZ28udXMvc3RhdGljL21hZGUtd2l0aC10YW5nby13YXRlcm1hcmsucG5n&ar=3840%3A1960)

As a final step, you also need to authorize the newly created application. Keep in mind that you are granting this access to your own Github Application. You are not granting any access to any third-party nor the Gimlet creators.

![Step 4 screenshot](https://images.tango.us/public/screenshot_395e38bc-d9a5-4ef0-8cd9-7a3aeb23d8aa.png?crop=focalpoint&fit=crop&fp-x=0.4947&fp-y=0.5477&fp-z=2.2108&w=1200&mark-w=0.2&mark-pad=0&mark64=aHR0cHM6Ly9pbWFnZXMudGFuZ28udXMvc3RhdGljL21hZGUtd2l0aC10YW5nby13YXRlcm1hcmsucG5n&ar=3840%3A1960)

## Bootstrapping the gitops environment

You have to provide information about the environment you are creating in this step.

You need to name the environment and set the approach to structure gitops resources. By default, Gimlet uses two gitops repositories per environment [by convention](/concepts/gitops-conventions), one for infrastructure components, one for application deployments.

![Step 2 screenshot](https://images.tango.us/public/edited_image_374cd8b6-0385-48db-b8d1-e7ac7c4246da.png?crop=focalpoint&fit=crop&fp-x=0.5000&fp-y=0.5000&fp-z=1.0000&w=1200&mark-w=0.2&mark-pad=0&mark64=aHR0cHM6Ly9pbWFnZXMudGFuZ28udXMvc3RhdGljL21hZGUtd2l0aC10YW5nby13YXRlcm1hcmsucG5n&ar=2176%3A662)

Then, at the bottom of the screen, you need to select what components to install.

Component settings are prefilled with values, in general you should not change them, unless you are instructed otherwise. Some common scenarios are listed bellow.

#### k3s/k3d or Rancher Desktop

Enable the *K3s / K3d / Rancher* component.

If you use the built in Traefik ingress controller, make sure the Nginx component is turned off.

#### Docker Desktop

Docker Desktop doesn't require special handling, therefor leave everything at default.

#### Minikube

TODO

#### kind

TODO

## Kickstarting the gitops automation

![Step 2 screenshot](https://images.tango.us/public/screenshot_5bb0d866-ba00-46d7-ba0e-130b3d9693d3.png?crop=focalpoint&fit=crop&fp-x=0.4950&fp-y=0.3286&fp-z=1.8696&w=1200&mark-w=0.2&mark-pad=0&mark64=aHR0cHM6Ly9pbWFnZXMudGFuZ28udXMvc3RhdGljL21hZGUtd2l0aC10YW5nby13YXRlcm1hcmsucG5n&ar=3840%3A1960)

Once the boorstrapping is done, you have two new git repositories to store manifests in.

Follow the instructions to kickstart the automation: add deploy keys, and run *kubectl* commands.

Once you did all those, close the browser tab, and return to the terminal to finalize the install. The installer script is going to check the deployed applications.

TODO what you need to see.

## Accessing Gimlet

TODO: lock in the trial domain in get.sh

You were accessing the installer on the [http://gimlet.trial:9000](http://gimlet.trial:9000) address. Now that the installer is stopped, you will reuse this address to access the Gimlet dashboard.

The `/etc/hosts` file entry already exists, you only need to forward the ingress controller to your local port 9000 to make Gimlet accessible on [http://gimlet.trial:9000](http://gimlet.trial:9000).

#### k3s/k3d or Rancher Desktop

```
kubectl port-forward -n kube-system svc/traefik 9000:80
```

#### Docker Desktop

```
kubectl port-forward -n infrastructure svc/ingress-nginx-controller 9000:80
```

#### Minikube

#### kind

## Third party services to access Gimlet

Gimlet integrates to many third party tools.

To fully experience Gimlet, you have to make the Gimlet API available for third party services. On local Kubernetes clusters you can use *ngrok* to make that happen.

### Exposing the Gimletd API for CI artifact shippers

[CI artifact shippers](/docs/deploy-your-first-app-to-kubernetes#integrate-ci-with-gimlet) take two parameters: Gimletd's API location and an API token. Local installations can be exposed by the following ngrok command, then you can use the ngrok URL in the `GIMLET_SERVER` secret in CI.

```
ngrok http --host-header=gimletd.trial 127.0.0.1:9000
```

### Exposing the Gimlet Dashboard API for Github commit webhooks

The Gimlet Dashboard uses Github webhooks to make realtime git commit information available. Without them, you have to manually refresh the Gimlet Dashboard in the browser for new commits and their statuses to show up.

The following ngrok command exposes the Gimlet Dashboard API in ngrok, and you can use the ngrok URL in the Github webhook configuration.

Please note that with a free ngrok account, you can only run a single ngrok session at a time. It is better to use the one free ngrok session for the CI shipper integration.

```
ngrok http --host-header=gimlet.trial 127.0.0.1:9000
```

## Where to go next

Congratulations, now you have a fully functioning Gimlet dashboard locally available for evaluation.

To install it on a public facing cluster, check out the other installation methods.
