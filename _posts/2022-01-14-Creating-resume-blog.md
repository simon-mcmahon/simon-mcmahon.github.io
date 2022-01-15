

# Making a new resume website with a blog section.

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
```
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

### Setup local git development in your repo

#### Get a personal authentication token
Now we need to be able to actually make changes and push code to our repository. Github [banned password authentication](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/) in August 2021, so we must create a personal access token. Follow the instructions from github about that [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token). 

*Note that you will need to give it ```repo``` status access.* 

Copy the token **NOW** as you won't get another chance.
![Copy the text from your PAT](/assets/images/creating-resume-blog/creating-resume-blog/personal_access_tokens.png)


## Step 3: Take the Jekyll template to replicate.

With many thanks to [chadbaldwin](https://github.com/chadbaldwin) we can follow the guide he has posted on his blog [here](https://chadbaldwin.net/2021/03/14/how-to-build-a-sql-blog.html). For most of the steps.

Download the repository at [simple-blog-bootstrap](https://github.com/chadbaldwin/simple-blog-bootstrap) by clicking `Code -> Download as Zip` as seen in the picture.

![download as zip](/assets/images/creating-resume-blog/creating-resume-blog/download-as-zip.png)

### Make a new github pages repository

Ensure to call this repository `{username}.github.io`. Make sure to set it to private for now so we don't advertise a shabby looking website.

![make new repo](/assets/images/creating-resume-blog/creating-resume-blog/new-git-repo.png)

### Setup the local git repo

Firstly, extract the zip file you downloaded to `simple-blog-bootstrap-main`. But to keep things simple for us, I am going to rename this to our repo's name.

```
mv ./simple-blog-bootstrap-main ./simon-mcmahon.github.io
```
Then, initialise the git repo. And push an initial commit to github. This is where you will be asked to enter in your personal access token. Inside the folder, do:
```
git init
git add .
git commit -m "initial commit"
git remote add origin https://github.com/simon-mcmahon/simon-mcmahon.github.io.git
git branch -M main
git push -u origin main
```

![git push](/assets/images/creating-resume-blog/creating-resume-blog/git-push.png)

# Work in progress

Add in the forgotten stuff to the git ignore.
```
Gemfile
Gemfile.lock
.jekyll-metadata
.jekyll-cache
```
In order to get it to run locally, we need to do:

```
bundle init
```

Then add some stuff about needed gems to our Gemfile. From [here](https://jekyllrb.com/docs/ruby-101/#gemfile)

```
gem "jekyll"

gem "minima", "~> 2.5"

group :jekyll_plugins do
  gem "jekyll-feed"
  gem "jekyll-seo-tag"
  gem "jekyll-sitemap"
end
```
Then do `bundle install` and `bundle exec jekyll serve`.
Then look for it on port 4000.

![bundle serve](/assets/images/creating-resume-blog/creating-resume-blog/bundle-serve.png)

This still gives us a weird page `index of`. Something is wrong compared to github pages. Github pages builds simonmcmahon.com properly. But it doesn't display locally right.

![github serve](/assets/images/creating-resume-blog/creating-resume-blog/github-pages-blog.png)

![local serve](/assets/images/creating-resume-blog/creating-resume-blog/local-blog-archive.png)

......................

After much fiddling, the boilerplate for the Gemfile I needed came from the default Gemfile after doing `jekyll new test`.
Along with some stuff in the `_config.html` file.

```
# This will help ensure the proper Jekyll version is running.
# Happy Jekylling!
#gem "jekyll", "~> 4.2.1"
# This is the default theme for new Jekyll sites. You may change this to anything you like.
gem "minima", "~> 2.5"
# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
# uncomment the line below. To upgrade, run `bundle update github-pages`.
gem "github-pages", group: :jekyll_plugins


group :jekyll_plugins do
  gem "jekyll-feed"
  gem "jekyll-seo-tag"
  gem "jekyll-sitemap"
end
```

Use jekyll instead of markdown for code snippets:

Jekyll also offers powerful support for code snippets:


{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}


Or use https://highlightjs.org/ to use the github syntax for code highlighting

# TODO
add in more languages to highlightjs. 
https://highlightjs.org/download/
AND look at the _includes/head.html

