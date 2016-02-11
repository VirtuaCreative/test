---
layout: post
title:  "Testing Jekyll v3 on GitHub Pages"
date:   2016-02-07 15:19:44 -0200
categories: jekyll update
extra_imgs:
---

You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Hello cs

```cs
public interface ITest : ITestKey
{
    Task<string> SayHello(string name);
}
```

Hello Scala

```scala
def test(i: Int): Unit = {
   println(i)
 }
```

Testing Python Linenos

{% highlight python linenos %}
'''
scrape lyrics from vagalume.com.br
(author: thiagomarzagao.com)
'''

import json
import time
import pickle
import requests
from bs4 import BeautifulSoup

# get each genre's URL
basepath = 'http://www.vagalume.com.br'
r = requests.get(basepath + '/browse/style/')
soup = BeautifulSoup(r.text)
genres = [u'Rock']
          u'Ax\u00E9',
          u'Forr\u00F3',
          u'Pagode',
          u'Samba',
          u'Sertanejo',
          u'MPB',
          u'Rap']
genre_urls = {}
for genre in genres:
    genre_urls[genre] = soup.find('a', class_ = 'eA', text = genre).get('href')

# get each artist's URL, per genre
artist_urls = {e: [] for e in genres}
for genre in genres:
    r = requests.get(basepath + genre_urls[genre])
    soup = BeautifulSoup(r.text)
    counter = 0
    for artist in soup.find_all('a', class_ = 'top'):
        counter += 1
        print 'artist {} \r'.format(counter)
        artist_urls[genre].append(basepath + artist.get('href'))
    time.sleep(2) # don't reduce the 2-second wait (here or below) or you get errors

# get each lyrics, per genre
api = 'http://api.vagalume.com.br/search.php?musid='
genre_lyrics = {e: {} for e in genres}
for genre in artist_urls:
    print len(artist_urls[genre])
    counter = 0
    artist1 = None
    for url in artist_urls[genre]:
        success = False
        while not success: # foor loop in case your connection flickers
            try:
                r = requests.get(url)
                success = True
            except:
                time.sleep(2)
        soup = BeautifulSoup(r.text)
        hrefs = soup.find_all('a')
        for href in hrefs:
            if href.has_attr('data-song'):
                song_id = href['data-song']
                print song_id
                time.sleep(2)
                success = False
                while not success:
                    try:
                        song_metadata = requests.get(api + song_id).json()
                        success = True
                    except:
                        time.sleep(2)
                if 'mus' in song_metadata:
                    if 'lang' in song_metadata['mus'][0]: # discard if no language info
                        language = song_metadata['mus'][0]['lang']
                        if language == 1: # discard if language != Portuguese
                            if 'text' in song_metadata['mus'][0]: # discard if no lyrics
                                artist2 = song_metadata['art']['name']
                                if artist2 != artist1:
                                    if counter > 0:
                                        print artist1.encode('utf-8') # change as needed
                                        genre_lyrics[genre][artist1] = artist_lyrics
                                    artist1 = artist2
                                    artist_lyrics = []
                                lyrics = song_metadata['mus'][0]['text']
                                artist_lyrics.append(lyrics)
                                counter += 1
                                print 'lyrics {} \r'.format(counter)

    # serialize
    with open(genre + '.json', mode = 'wb') as fbuffer:
        json.dump(genre_lyrics[genre], fbuffer)
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/
