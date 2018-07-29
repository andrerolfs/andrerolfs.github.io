{% include_relative menu.md %}

## 20180721 Theme And Menu On GitHub Pages

I have found out how to configure a theme and have a menu on my GitHub pages.

Menu means for me to have a file included in every other file which holds some links,
so I don't have to repeat myself with linking.

You get the menu by

1. creating a file like `menufile.md` in your pages repo root directory
2. write into the menu file the markdown code you want to use as menu
3. include `{` `% include_relative menufile.md %` `}` where you want to integrate this menu code

You get the theme by clicking to `Settings` in your repository, click `change theme` and select one.
This changes the file `_config.yml`.

I have chosen the hacker theme, the tool has added this code in my `_config.yml` :

    theme: jekyll-theme-hacker

This works since it is an official theme and the Jekyll framework knows how to get from this configuration
to this offical theme.

I have read you can configure any other user repo providing themes bye using `remote_theme: owner/name` like
explained in [The GitHub Blog](https://blog.github.com/2017-11-29-use-any-theme-with-github-pages/) but I did not test this.
