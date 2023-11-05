+++
title = "Automated Deployment with GitHub Actions and FTP"
date = 2023-11-05
updated = 2023-11-05
description = "Learn how to effortlessly set up an automated deployment system for your application using GitHub Actions and FTP file transfer, ideal for repositories hosted on GitHub with access to cPanel management tools."

[taxonomies]
tags = ["Deployment", "GitHub", "Automation"]
+++

Automating your deployment process can significantly simplify your workflow. This guide will walk you through the steps to establish an automated deployment system for your GitHub-hosted repositories, particularly when your hosting provider offers cPanel access.

## 1. Configuring an FTP Account on Your Hosting Provider's Website
To begin, it's crucial to create an FTP account on your hosting provider's website. Specify the directory as "public_html" since this is where you want to deploy your application. Depending on the framework or library you are using, you may need to deploy the contents of different folders to the "public_html" folder on the server.

For instance, if you are using Zola, your project will be built in the "public" folder, and you will need to deploy the contents of this folder to the server. We'll delve into this in more detail shortly.

## 2. Using GitHub Actions to Set Up the Deployment System
### 2.1. Adding Repository Secrets
Before setting up the deployment system, we need to add the necessary variables for FTP file uploads to the repository. Follow these steps:

- Go to your repository and click on "Settings."
- Under "Secrets and Variables" > "Actions," click on "New Repository Secret."
- Add three secrets: FTP_PASSWORD, FTP_SERVER, and FTP_USERNAME.

### 2.2. Configuring the Automated Action
In your chosen repository, navigate to "Actions" > "New Workflow" > "Set up a workflow yourself." This will open an interactive editor in your window. We recommend renaming your *.yml file to something like "deploy.yml" for clarity. Next, paste the following code into the editor:

```yml
name: Publish website to cPanel
on:
  push:
    branches:
    - main

jobs:
  FTP-Deploy-Action:
    name: FTP-Deploy-Action
    runs-on: ubuntu-latest
    steps:
    - name: Checkout code
      uses: actions/checkout@v2.1.0
      with:
        fetch-depth: 2
    - name: FTP-Deploy-Action
      uses: SamKirkland/FTP-Deploy-Action@4.3.0
      with:
        server: ${{ secrets.FTP_SERVER }}
        username: ${{ secrets.FTP_USERNAME }}
        password: ${{ secrets.FTP_PASSWORD }}
        protocol: ftp
        port: 21
        local-dir: public/
```

Please note the following:

1. This YAML file will take the contents of the output directory of your application and upload them to the server via FTP. For Zola, the project is built in the "public/" directory. However, for your application, this path might be different.
2. Depending on whether your main branch is named "main" or "master," you may need to adjust the "branches" section in the YAML file above.

## 3. Push to the Main Branch
Once you've set up the deployment system, all you need to do is push new changes to your main branch. If everything is configured correctly, the YAML file should trigger an action that uploads your application to the server hosting it, making your deployment process hassle-free and automated.
