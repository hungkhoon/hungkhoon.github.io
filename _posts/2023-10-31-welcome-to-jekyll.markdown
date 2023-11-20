---
layout: post
title:  "Getting Started in Jekyll"
date:   2023-10-31 09:12:00 +0800
categories: general
author: "HK"
---

Main reference: 
* [Giraffe Academy's YouTube tutorial](https://www.youtube.com/playlist?list=PLLAZ4kZ9dFpOPV5C5Ay0pHaa0RJFhcmcB){:target="_blank"}


## A. Jeckyll

### Jekycll Commands

* To create a new site called hungkhoon.github.io

  `jekyll new hungkhoon.github.io`

* To open the website for the first time:

  `bundle exec jekyll serve`

* To open the website:

  `jekyll serve`

* To open the website (showing with draft blogs):

  `jekyll serve --draft`

### Directory structure in Jekyll
* Your Jekyll project has the following folder:
  - `_drafts`: folder to put the drafts of your blogs. All pages in the `_drafts` folder will not be listed on the actual site.
  If you want to 
  - `posts`: folder to put the blogs.
  - `posts\<any folder>`: you can oraganize your blog into folders. Jekyll will be able to detect the blogs in all sub-diretories in `posts`.
  - `<any folder`>: you can organize your pages into folders. Jekyll will look for pages in all sub-directories.
  - `_site`: the final version or finished product of the web site that will be published.
  - `_config.yaml`: the configuration file.
  - `gemfile`: this file is used with Ruby to store the dependencies (e.g., jekyll, and the "minima" theme) for our website. You can also specify additional plug-ins that you may use in your page.
  - `index.markdown`: this is the main page of your file.

### Markdown

* Markdown offers powerful support for code snippets:
    ```python
    def print_name(name):
      print(f"Hi {name}")

    print_name("HK") # hi
    ```
* You can create hyperlinks easily in markdown. Here are some excellent websites on Jekyll and markdown:
  * [Jekyll tutorial][jekyll-tutorial] by Giraffe Academy - link provided at the end of the script 
  * [Markdown manual](https://www.markdownguide.org/basic-syntax) - inline reference
  * [Jekyll installation](https://jekyllrb.com/docs/step-by-step/01-setup/){:target="_blank"} - opens in a new tab, instead of the same windows.

[jekyll-tutorial]: https://www.youtube.com/playlist?list=PLLAZ4kZ9dFpOPV5C5Ay0pHaa0RJFhcmcB

### FrontMatter
*  The front matter is the section at the beginning of the blog posts that defines the properties of the blog either in `YAML` or `JSON` format.

    ```markdown
    ---
    layout: post
    title:  "Getting Started"
    date:   2023-10-31 09:12:00 +0800
    categories: general
    author: "HK"
    permalink: "
    ---
    ```

* For *blogs*, always use the `post` layout. For *pages*, use the *page* layout.

* Use `permalinks` to create permanent hyperlink to an important blog. Otherwise, the blog url will change depending on the date and category, as follows:
  ```
  ---
  
  permalink: "an-important-blog"
  ---
  ```
  then, the url for the blog is:

  `<your website>/an-important-blog/`

* In the `permalink`, you can also customize the link with variables or properties of the page or blog, as follows:

  ```
  ---
  
  permalink: /:categories/:year/:month/:day/:title.html
  ---
  ```
  then, the url for the blog is:

  `<your website>/<categories>/<year>/<month>/<day>/<title>.html`

### Themes

* The theme is defined in the `_config.yaml` file. By default, jekyll comes with the *minima* theme.

  `theme: minima`

* You can search using the keyword "*jekyll-theme*" at the following site;
  
  [https://rubygems.org](https://rubygems.org){:target="_blank"}

  You can go to the theme's homepage (normally a github repository), and preview the theme. 

* Suppose that we want to use the [jekyll-theme-hacker](https://rubygems.org/gems/jekyll-theme-hacker){:target="_blank"} theme. Here is how you set up your project to use the new theme:

  1. Following the instruction at the theme page, modify the dependency at `Gemfile` to use the *jekyll-theme-hacker* theme instead of *minima*:
      
      `gem 'jekyll-theme-hacker'`

  2. At `_config.yml`, modify the theme:

      `theme: jekyll-theme-hacker`

  3. Stop the jekyll server and then install the new theme. The following command installs all the missing dependencies in `Gemfile`.

      `bundle install`

  4. Restart the jekyll server to reconfigure everything and get the server up and running.
    
      `bundle exec jekyll serve`

* If you use `jekyll-theme-hacker` themes, you may end up with a blank screen for your website. This is because `jekyll-theme-hacker` does not contain the `page` and `home` layout. It only contains a `default` website. You can check the available layouts of a theme in it's homagepage [`_layouts`](https://github.com/pages-themes/hacker/tree/master/_layouts){:target="_blank"} directory. To solve the problem, you will have the change the layout for `index.md` to use `default`.

  ```yaml
  layout: default
  ```

### Layout

The `minima` thema defines four different types of types of layout as defined [here](https://github.com/jekyll/minima/tree/master/_layouts){:target="_blank"}, namely `base`, `home`, `page` and `post`.

You can define and use your own layout by putting the layout files (`.html`) at the folder `_layouts`, e.g., 

```
_layouts \
    post.html
    home.html
    page.html
    post.html
```

You can also defines a hierarchy of layout. 

### Variables

* You can access the set of pre-defined variables as defined at <https://jekyllrb.com/docs/variables/>{:target="_blank"}. You can use these variables to display certain information about your site. You can also use it to improve the layout of your site. For example:
  * \{\{ `content` }} - stores the content of a post or page.
  * \{\{ `site.title` }} - accesses the `title` attribute defined in `_config.yaml`. 

 
* You can also define your own variables in the front matter. For example:

  ```
  ---
  layout: post
  state: "Perak"
  title: "This is my title"
  ---
  ```

  You can access the variable defined above as \{\{ `page.state` }} and \{\{ `page.title` }}.

### Includes

* The manual for includes can be found in this [Jekyll tutorial web site](https://jekyllrb.com/docs/includes/){:target="_blank"}.

* The `include` tag allows you to include the content from another file stored in the `_includes` folder. For example, you can define a header file `_includes/header.html`.

  *_includes/header.html*
  ```
  <h1> { { site.title }} is awesome!</h1> <br> <hr><br>
  ```


  and then reference it in other files, e.g., in one of your layout file. For example: 
    
  *_layout/post.html*
  ```
  { % include header.html %}
  ```
  
  Jekyll will look for the referenced file in the `_includes` directory at root and insert its content. 
 
* You can also pass variables from any page or post back to be used in the include file. There are two ways to do that:
  - Define a variable called `my_header_color` in the `include` statement.

    *_layout/post.html*
    ```
    { % include header.html my_header_color="blue" %}
    ```
  
  - Define a variable called `my_header_color` in the front matter.

    *_layout/page.html*
    ```yaml
    --- 
    title: my page
    my_header_color: blue 
    ---    
    ```
  
 - You can then reference `my_header_color` in your include file. For example:

    *_include/header.html*
    ```
    <h1 style="color: { { include.my_header_color }}"> { { site.title }} is awesome!</h1> <br>
    ```

    Then, post and page will have their own customized color although they are using the same include file. 
    
### Looping through posts

* The following shows how to list all the posts in the main page:

    *_layout/home.html*
    ```
    { % for post in site.posts %}
        <li><a href="{ { post.url }}">{{ post.title }}</a></li>
    { % endfor %} |
    ```

### Condition

* You can also put conditions to control the flow of your program. For example, you can print some message to inform users if this is your first, second or later posts:

    *_layout/header.html*
    ```
    { % if page.title == "My First Post" %}
      This is the first post
    { % elsif page.title == "My Second Post" %}
      This is the second post
    { % else %}
      This is another post
    { % endif %}
    ```
* You can put more complex conditions with `and` and `or` operator. For example:

    *_layout/header.html*
    ```
    { % if page.title == "My First Post" or page.title == "My Second Post"%}
      This is the first two posts
    { % endif %}
    ```

* You can put conditions within an html statement. For example, suppose each post shows a list of all posts in your site. You can assign a different color for the current post by using conditional statements.
  ```
  { % for post site.posts %}
    <li><a style="{ % if page.url == post.url %} color : red; { % endif %}" href="{ { post.url }}">{ { post.title}}</a></li>
  { % endfor %}
  ```

### Data Files

* Store your directory in the `_data`` directory.
  ```
  _data\
    people.yml
    states.yml
  ```

* Suppose that `people.yml` contains two records with *name* and *occupation* attributes. Then you can access the data through the following conventions:
  ```
  { { site.data.people }}
  ```
  This will output the following:

    *{“name”=>”Mike”, “occupation”=>”programmer”}{“name”=>”Jobs”, “occupation”=>”CEO”}* <br> 
    *{“name”=>”Obama”, “occupation”=>”President”}*

* You can loop through the items in `people.yml` and display the content one by one:
  ```
  { % for person in site.data.people %}
    { { person.name }}, { { person.occupation}} <br>
  { % endfor %}
  ```

  This will output the following:
  
   *Mike, programmer* <br> 
   *Steve, CEO* <br> 
   *Obama, President*

### Static Files
* Static files are any files or website that are being rendered or used by Jekyll that do not have any front matter. This include files such as word document, pdf file, java script file or css file. 
* It is common to put static files into the `assets` directory. For example:

  ```text
  assets\
     img1.jpg
  ```

* You may access all static files through the variable `site.static_files` which contains the list of *all* static files in your site regardless of where it is stored. 

  ```
  { % for file in site.static_files %}
  { { file.path }} { { file.name }} { { file.extname}} <br>
  { % endfor %}
  ```

<br>

---
## B. Writing a Blog

### Title and web page

* Jekyll requires blog post files to be named according to the following format:

  `<year>-<month>-<day>-<title>.markup`

  where `<year>` is a four-digit number, `<month>` and `<day>` are both two-digit numbers, and `markup` is the file extension representing the format used in the file. 

* The blog's url as follows:

  `<blog site>/<category>/<year>/<month>/<day>/<title>.html`

  The information is typically extracted from the front matter. However, if it is not found there, it will default to `update` category and extract the date and title from the markdown file name.
  
### Draft

* You can write a draft by writing the blog in the `_draft` folder. The draft will be not be listed in the web page unless you run in draft mode `jekyll serve --draft`.

### Default settings

* To create the default settings for a particular setting, e.g., `layout` , we can set their **default value** in the `_config.yml` file. After this, you do not need to explicitly set `layout` in the front matter of every single blog post you write in the future

    ```yaml
    defaults:
      -
        scope:      # defines the scope in terms of path and type
          path: ""  # specify  folders that uses the default value
          type: "post" # only for posts (not pages)
        values:
          layout: "post"
    ```

    Notes: Each time you change `_config.yaml`, you should restart the jekyll server.

---
## C. Writing a Page

* To create a new page, create a new markdown file at the *root* directory or any of its *sub-directory* (e.g., `./pages/`). Jekyll will search hierarchically for all *pages* in *root*.
* For the front matter, use `page` instead of the `post` layout.

---
## D. Working with Git

<img src="/assets/images/202310/2023-10-31_github-process.png" alt="image" style="width:400px;height:auto;">

* **Installation**
  - Download and install [git](https://git-scm.com/downloads){:target="_blank"}. First, you need to configure your git account based on the instructions provided [here](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup){:target="_blank"}
    - The configuration files are saved in `gitconfig` files, one each for the system, user and project level.
    - To view all settings, the command is:
      ```
      git config --list --show-origin
      ```
    - To setup your user-wide account:
      ```
      $ git config --global user.name "John Doe"
      $ git config --global user.email johndoe@example.com
      ```
    - To setup a project-level account, run the command without `--global` option:
      ```
      $ git config user.name "John Doe"
      $ git config user.email johndoe@example.com
      ```
  - To set up rss connection:
    - Generate new SSH key, type the following command. Reference: [link](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent){:target="_blank"}

      ```
      ssh-keygen -t ed25519 -C "your_email@example.com"
      ```
      
      When asked to *enter a file in which to save the file*,press **Enter** to accept the default file location. 

      If you have created SSH keys previously, ssh-keygen may ask you to rewrite another key
      ```
      C:\Users\user1> ssh-keygen -t ed25519 -C "hungkhoon@gmail.com"
      Generating public/private ed25519 key pair.
      
      Enter file in which to save the key (C:\Users\user1/.ssh/id_ed25519):  <Press Enter>
      Created directory 'C:\\Users\\user1/.ssh'.
      
      Enter passphrase (empty for no passphrase): <Press Enter>
      Enter same passphrase again:
      
      Your identification has been saved in C:\Users\user1/.ssh/id_ed25519
      Your public key has been saved in C:\Users\user1/.ssh/id_ed25519.pub
      
      The key fingerprint is:
        <the generated fingerprint>
      The key's randomart image is:
        +--[ED25519 256]--+
        <the generated randomart image>
      ```
    - Add the SSH key to your github account. Reference: [link](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account){:target="_blank"}
      
      To look at your SSH public key, type the following command to display the public key:
      ```
      cat ~/.ssh/id_ed25519.pub 
      ```

      To copy the SSH public key to your clipboard, save output to the clipboard:

      ```
      cat ~/.ssh/id_ed25519.pub | clip
      ```

      Then, log into your github account at the github web page, go to your profile photo and click Setting:

      <img src="https://docs.github.com/assets/cb-45016/mw-1440/images/help/settings/userbar-account-settings-global-nav-update.webp" alt="image" style="width:300px;height:auto;">

      Then go to `Access` section of the sidebar, click `SSH and GPG keys`. Click on `New SSH key` or `Add SSH key`. In the title, add a descriptive label for the new key. For example, if you're using a personal laptop, you might call this key `"Personal laptop"`. Select the type of key, either authentication or signing. In the `"Key"` field, paste your public key and then click `Add SSH key`.

      <img src="/assets/images/202310/2023-11-21_github_sshkey.png" alt="image" style="width:500px;height:auto;">      
    
    - Test your SSH connection by using the following command. Reference: [link](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/testing-your-ssh-connection){:target="_blank"}

      ```
      ssh -T git@github.com
      ```

      If successful, you will get the following messages:

      ```
      Hi hungkhoon! You've successfully authenticated, 
      but GitHub does not provide shell access.
      ```



  - Setup git for VSCode:
    - At VSCode, install the `Git Expansion Pack`
    - To access `git`, click on `source control` icon in the navigation bar.

* **Initialize repository** by selecting  `Initialize Repository` to create the (local) repository.

* **Stage** all modified files. Files that have changed since the last revision or new files are marked with `U` (*untracked*) symbol. To stage an untracked file, click the `+` button. You can do so either on *all*  or  an *individual* file(s).

* **Commit** the staged files. Files that have been committed are marked with `A` (added) symbol.

* **Push** to the github repository by selecting `Publish Branch`. The first time you do this, it will ask you for the password.

* The status of the files are given as follows:
    
    |---|---|
    | U | Untracked or unstaged files. These are new or modified files that have not been added to the repository. |
    | A | Admitted files. These are files that have not been committed. |

* You can specify the files that you want to **ignore** and not update by adding them into `.gitignore`. 
  * Install the `gitignore` extension
  * Open the `Command Pallete` by pressing Ctrl+Shift+P. Then type the command `Add gitignore`.
  * Add the files that you do not wish to update to the repository. 

* You can check the **history** of the commit by typing `git history` at the `Command Pallete`