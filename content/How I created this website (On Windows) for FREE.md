## Requirements 
1. [Obsidian](https://obsidian.md/)
2. [Node.js](https://nodejs.org/en)
3. [GitHub](https://github.com/)
4. [Cloudflare](https://www.cloudflare.com/)
5. [Visual Studio Code](https://code.visualstudio.com/download)

Download [Obsidian](https://obsidian.md/), [Node.js](https://nodejs.org/en) and [Visual Studio Code](https://code.visualstudio.com/download) and login (if you already created one account) or register as new on [GitHub](https://github.com/) and [Cloudflare](https://www.cloudflare.com/).

## Create a folder in the system 
For ease of explanation we would say it as `EX-Portal`(the name of the folder we created). Enter into the folder and now copy the path of the folder `EX-Portal`. Let the path of the folder be `D:\EX-Portal`. Open Windows PowerShell and paste the below commands one by one.

Note : Replace the `D:\EX-Portal` with your file path/location.

```
cd D:\EX-Portal
```

```
git clone https://github.com/jackyzha0/quartz.git
```

```
cd quartz
```

```
npm i
```

```
npx quartz create
```
After Entering the above command you will see an option menu for creating and setting up quartz.
Where you have to select the 1<sup>st</sup> option for both the configurations.

Don't close the PowerShell, we'll gonna need it for further use.

## Some editing in the code

Go to Visual Studio Code and hit the search icon. Search for `Quartz 4.0`. You will see a file named `quartz.config.ts`. Open the file. In the 6<sup> th </sup> line of the code, where it says `pageTitle: "🪴Quartz 4.0",`. Replace that with `pageTitle: "Anything that you want to be at the top of the site",`.
Save it.

## Content

Open Obsidian and then click on `Open folder as vault`. 

[Learn some basics on Obsidian from here](https://help.obsidian.md/Editing+and+formatting/Basic+formatting+syntax)

After getting some knowledge on how to use the Obsidian its time to create something for the website. Now get in to the folder named `content`. The path for the folder is `quartz\content`. Whatever you want to get displayed goes to the `content` folder. If you create a note outside the `content` it will not work at all.

## Build and Preview Site

Open the Windows PowerShell right where you left. Now to preview what you have built just paste the below command in PowerShell.

```
npx quartz build --serve
```
This will create a local web server for your website. Now copy the URL that the the above command outputs, which look like `http://localhost:8080/`. Then open a Web Browser of your own choice and then paste the URL and hit `Enter`.
You will see a preview of your website on the screen. If it worked, Congratulations 🎊. You are good to proceed further.
The problem here is that the site is set locally and it cannot be seen anywhere else rather then on your Desktop. 

## Uploading to GitHub

Now open GitHub on your Favorite Browser and proceed to `create a Repository`. Name the Repository and set it as `public`(don't do anything apart from this). Hit `Enter` or `Create`.
Now after creating a Repository you will see a `https:` link in the top portion of the screen. Just copy that. 

Now in PowerShell paste the below commands one by one.

```
git remote -v
```

Replace the `REMOTE-URL` with the `https:` link copied earlier 
```
git remote set-url origin REMOTE-URL
```

The output of this is not an error:
```
git remote add upstream https://github.com/jackyzha0/quartz.git
```

For initial push
```
npx quartz sync --no-pull
```

# Hosting

1. Open Cloudflare in your browser
2. Select `Workers and Pages`
3. Select `Create application`
4. Select `Pages`
5. Select `Connect to Git`
6. Select your GitHub Account and the Repository that you newly created(If your repository is not shown, configure repository access for the [Cloudflare Pages](https://github.com/settings/installations/46922011) app on GitHub)
7. **Set up builds and deployments** section

|Configuration option|Value|
|---|---|
|Production branch|`v4`|
|Framework preset|`None`|
|Build command|`npx quartz build`|
|Build output directory|`public`|

8. Select `Save and Deploy`
9. Now in front of `Domains:` you see your website link

Now open Visual Studio Code and 
1. open **quartz** folder
2. open **.github** folder
3. open **workflows** folder
4. create a file named **deploy.yml**
5. Write the below code in the file
```
name: Deploy Quartz site to GitHub Pages
 
on:
  push:
    branches:
      - v4
 
permissions:
  contents: read
  pages: write
  id-token: write
 
concurrency:
  group: "pages"
  cancel-in-progress: false
 
jobs:
  build:
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0 # Fetch all history for git info
      - uses: actions/setup-node@v3
        with:
          node-version: 18.14
      - name: Install Dependencies
        run: npm ci
      - name: Build Quartz
        run: npx quartz build
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: public
 
  deploy:
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
```

6. Now go to **GitHub** 
7. Open the newly created Repository 
8. Select **Setting**
9. Select **Pages**
10. Under **Source** select **GitHub Actions**
11. Now go to Windows PowerShell and paste the below command
```
npx quartz sync
```

This deploys your site to `<github-username>.github.io/<repository-name>`. You can search this in your browser to get the site.

# Note

To preview site in local web server

```
npx quartz build --serve
```

To push and commit the changes to the website

```
npx quartz sync
```

# Reference
[Official Quartz Documentation](https://quartz.jzhao.xyz/)