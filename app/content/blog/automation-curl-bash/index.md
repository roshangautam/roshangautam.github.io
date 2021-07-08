---
title: "Automation using curl and bash"
description: "Have you ever found yourself in a situation where you want to download a list of videos from an online course to watch them offline and there is no option to download all the files at once ?"
coverImage: "/assets/images/posts/covers/automating-video-downloads.png"
date: "2017-10-01"
author:
  name: Roshan Gautam
  picture: "https://avatars.githubusercontent.com/u/978347?v=4"
ogImage:
  url: "/assets/images/posts/covers/automating-video-downloads.png"
---

<p style="text-align: center;">
  <image src="./automating-video-downloads.png"/>
</p>

Have you ever found yourself in a situation where you want to download a list of videos from an online course to watch them offline and there is no option to download all the files at once ? Something similar happened to me today while trying to download all the videos from Adam Wathan's Course Test-Driven Laravel – Build robust applications with TDD Thanks to Adam, he sent me a link to a page where all the videos are listed and has option to download each video either in HD or SD. But the problem was still there. Either I had to click on every link (a total of 142 videos) or use my ability to write code and get this done all at once. And of course I used my programming skills because programmers are lazy and like to create tools which helps them become more lazy. Here are the steps I followed:

---

## Create a list of videos

The first challenge was to create a list of links to all the videos listed in course page. So this is what I did

- Logged in to my account. This is important as all videos are private and need authentication
- Right clicked and opened the dev tools and inspected one of the HD link tags
- I noticed that HD links can be listed using the text HD links and has a class link-brand. So I ran following JS in dev tools console

```javascript
document.querySelectorAll("http://a.link-brand").forEach(function (el) {
  if (el.text == "Download HD") console.log(el.href)
})
```

- This gave me a list of HD links to all episodes listed in the course. I copied them and pasted them in a file called videos.txt. We will use this file in our next step.

## Download Videos

The next step was to download all videos listed in the file we just created with a single command.

- Just to test I executed a curl command on one of the links to check if it gets redirected to some other URL or not ? and Voila !!! it does get redirected to a different URL and the redirected url has a filename in it. And I thought, I must use the filename while saving the files to my machine to make it easy to know what episode I am on while watching offline.

```sbtshell
curl -s -w %{redirect_url} https://link.to.your.episode.net/and/all/id/token/blah/blah
```

> If you run this $redirect_url will contain the location of the video with additional params. I quickly wrote a shell script to leverage all this. Here’s the script

```sbtshell
#!/bin/bash
filename="$1"
curl='/usr/bin/curl'
args="-s -w %{redirect_url}"
while read -r url
do
    raw="$($curl $args $url)"
    encoded="$(echo $raw | awk -F'[=&]' '{print $6}')"
    name="$(python -c "import sys, urllib as ul; print ul.unquote_plus('$encoded')")"
    slug="$(echo $name | iconv -t ascii//TRANSLIT | sed -E s/[^a-zA-Z0-9.]+/-/g |sed -E s/^-+\|-+$//g | tr A-Z a-z)"
    wget -O "$slug" $raw
done < "$filename"
```

> This script is basically reading a list of urls from an external file ( the one we created in our first step) and then executing curl against each of them one by one and grabbing the redirect url, stripping out the filename query parameter from url, converting the filename into a slug and then finally using wget to save the video with this custom slugged name.

- Save the script (videos.sh) along with the file containing the list of videos (videos.txt) and run the following command from your terminal
  > Make sure you change the permission of your .sh file to 755 and possibly put both files inside a directory. In your terminal navigate to the location of .sh and .txt files created in the steps above
