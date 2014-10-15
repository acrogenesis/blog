---
layout: post
title: "How-To upgrade your Wordpress Blog to an Octopress Blog"
date: 2014-02-03 12:27:44 -0600
comments: true
categories:
- How-To
---

Recently I upgraded this blog from Wordpress to Octopress. I'll walk you
through on the process I followed.

I'll assume you already have Octopress up & running, if you don't you
can follow setup instructions at
[Octopress](http://octopress.org/docs/setup/).

Now let's export your Wordpress data, go to
http://yourblog.com/wp-admin/export.php and select to export all your
content.

{% img full-width /images/upgrade-wordpress-to-octopress/export-all-content.png 'Export All Content' 'Export All Content' %}

This will give you a ```.xml``` file which we will use to transfer your
comments to disqus after transfering your posts.

To transfer your posts we'll use a tool called [exitwp](https://github.com/thomasf/exitwp)

Let's start by cloning the repo
{% codeblock lang:ruby %}
git clone git@github.com:thomasf/exitwp.git exitwp
cd exitwp
{% endcodeblock %}
Install dependencies
{% codeblock lang:ruby %}
pip install pyyaml
pip install beautifulsoup4
{% endcodeblock %}
Copy the ```.xml``` file into ```wordpress-xml``` folder and check if it's
valid
{% codeblock lang:ruby %}
# copy the .xml file into wordpress-xml
cp ../path_to/exported.xml wordpress-xml/exported.xml
# check xml is properly formated with xmllint
xmllint wordpress-xml/exported.xml
# solve any errors you encounter
{% endcodeblock %}
Run the converter
{% codeblock lang:ruby %}
python exitwp.py
{% endcodeblock %}

This will generate your posts inside ```build/jekyll/yourblog/_posts```
, copy all the _posts files into your _posts files
{% codeblock lang:ruby %}
cp -r build/jekyll/yourblog/_posts ../octopress/source/_posts
{% endcodeblock %}

You have now finished exporting your posts to your Octopress blog; now let's
export the comments to Disqus.

First create an account at [Disqus](https://disqus.com/profile/signup/)

After you've created your account let's [Register your website at Disqus](http://disqus.com/admin/create/)
{% img full-width /images/upgrade-wordpress-to-octopress/add-disqus-site.png 'Add Disqus site' 'Add Disqus site' %}

Take note of your 'shortname' we will be using it next.

Now let's add Disqus to your blog, open your ```_config.yml```.

Find the comment ```#Disqus Comments``` and add your shortname on ```disqus_short_name: myblog``` this will automagically setup Disqus on your blog.

Now lets import the comments on Disqus, we will be using the same
```.xml``` file.

Visit the admin panel then click on Discussions and
select your newly created site.

Inside here click on Import and the Wordperss import should be already selected, click Choose File and select your ```.xml``` file

Click Upload and Import

{% img full-width /images/upgrade-wordpress-to-octopress/import-wordpress-comments.png 'Import Disqus Comments' 'Import Disqus Comments' %}

This will upload your current comments to Disqus, if your url's won't
change you can stop here and in a few hours all your comments will be
appearing on your new blog! But if your url's will be chaning
you have to map the old url's with the new url's.

To do this click on Tools (on the same page you clicked Import) and
select the most appropiate method for merging threads, in my case I used
Url Mapper.

{% img full-width /images/upgrade-wordpress-to-octopress/migrate-threads.png 'Migrate threads' 'Migrate threads' %}

Click on the link that says ```you can download a CSV here``` that will
email you a CSV file which will contain all the old url's, you will
have to map this url's with the new one's.

Upload URL maps in CSV format (comma separated value). The correct format is:
{% codeblock %}
http://example.com/old-path/old/posta.html, http://example.com/new-path/new/posta.html
http://example.com/old-path/old/postb.html, http://example.com/new-path/new/postb.html
{% endcodeblock %}

You can also add a [3rd party theme](https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes)
to further customize your blog.

Don't forget to run
{% codeblock lang:ruby %}
rake generate
git add -A
git commit -m "finish upgrading wordpress to octopress"
{% endcodeblock %}
Which will make all the static files for your website.

You have successfully upgraded your Wordpress Blog to an Octopress Blog, now just [Deploy](http://octopress.org/docs/deploying/) your blog.
