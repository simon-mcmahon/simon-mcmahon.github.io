---
title: "Creating this blog with Jekyll and Github Pages"
last_modified_at: 2022-01-14T16:20:02-05:00
categories:
  - blog
tags:
  - jekyll
  - blog
  - github pages
---

## Step 1: Archive your previous resume website hosted with github pages

Take a full screen screenshot of the web page in Firefox `Ctrl + Shift + s`. To show the website.

Change the name of the repository to [2018-jekyll-resume](https://github.com/simon-mcmahon/2018-jekyll-resume) to free up the named space as `{username}.github.io`.

Push a commit to update the README with the screenshot, a short description of its features, and the fact it is an archive.
Then mark the it as archived. 
`Settings -> Danger Zone ->  Archive this repository`.

## Step 2: Setup a local Jekyll development instance.

Github pages and indeed the template we are following are based on Jekyll.

### Install Jekyll on Linux

My daily driving operating system is `Linux Mint 20.3 Cinnamon` which is based on `Ubuntu 20.04.5 LTS` . So we can follow the installation documentation found [here](https://jekyllrb.com/docs/installation/ubuntu/) .

But long story short...
```shell
# Install some dependencies 
sudo apt-get install ruby-full build-essential zlib1g-dev
#Fresh linux mint 20.3 doesn't have git
sudo apt install git
# Add stuff to our users .bashrc file to store gems in our home folder
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc

# Reactivate the file in our local terminal session
source ~/.bashrc

# Install jekyll
gem install jekyll bundler
```

### Setup local git development in your local machine

#### Get a personal authentication token
Now we need to be able to actually make changes and push code to our repository. Github [banned password authentication](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/) in August 2021, so we must create a personal access token. Follow the instructions from github about that [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token). 

*Note that you will need to give it ```repo``` status access.* 

Copy the token **NOW** as you won't get another chance.
![Copy the text from your PAT](/assets/images/creating-resume-blog/personal_access_tokens.png)


## Step 3: Take the Jekyll template to replicate.

With many thanks to [mmistakes](https://github.com/mmistakes) we can use the customisable theme [Minimal Mistakes](https://github.com/mmistakes/minimal-mistakes) and the [Github pages starter site](https://github.com/mmistakes/mm-github-pages-starter) he has provided.

Download the repository at [mm-github-pages-starter](https://github.com/mmistakes/mm-github-pages-starter) by clicking `Code -> Download as Zip` as seen in the picture.

![download as zip](/assets/images/creating-resume-blog/download-as-zip.png)

### Make a new github pages repository

Ensure to call this repository `{username}.github.io`. Make sure to set it to private for now so we don't advertise a shabby looking website.

![make new repo](/assets/images/creating-resume-blog/new-git-repo.png)

### Setup the local git repo

Firstly, extract the zip file you downloaded to `simple-blog-bootstrap-main`. But to keep things simple for us, I am going to rename this to our repo's name.

```shell
mv ./mm-github-pages-starter-master ./simon-mcmahon.github.io
```
Then, initialise the git repo. And push an initial commit to github. This is where you will be asked to enter in your personal access token. Inside the folder, do:
```shell
git init
git add .
git commit -m "initial commit"
git remote add origin https://github.com/simon-mcmahon/simon-mcmahon.github.io.git
git branch -M main
git push -u origin main
```

![git push](/assets/images/creating-resume-blog/git-push.png)

### Emulate the site locally

Next we build the gem packages and serve the site locally
```shell
bundle install 
# Add --baseurl to emulate relative urls as in github pages
bundle exec jekyll serve --baseurl=""
```
Then go to [localhost:4000](http://localhost:4000) to see your website.

![bundle serve](/assets/images/creating-resume-blog/bundle-serve.png)

And you should see the default site layout.
![default website](/assets/images/creating-resume-blog/default-site-layout.png)


## Step 4: Customising the _config.yml file

Since we are using a starting template, not all options are present in our `_config.yml` file. Go to the [theme repo _config.yml](https://github.com/mmistakes/minimal-mistakes/blob/master/_config.yml) to find out all the options.

To start off with, the documentation recommends adding the version number to the remote theme plugin.
```yaml
# Add version number to this line
remote_theme: "mmistakes/minimal-mistakes@4.24.0" 

# Add this line for dark skin
minimal_mistakes_skin: "dark" # "air", "aqua", "contrast", "dark", "dirt", "neon", "mint", "plum", "sunrise"
```
Next add a file called `CNAME` with the contents `{your website url}.com` for github pages if you have a domain.
You will have to add a CNAME record with your DNS provider manually. I leave that as an exercise for the reader.

Finally, edit all the other personal details. Add your picture, a favicon in the root directory and commit and push your changes.

**WELL DONE:** Now we have our very own blog!
{: .notice--success}

 
