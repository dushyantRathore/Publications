---
layout:     post
title:      Building a Spotify-Lyrics GUI application for Linux
date:       2017-10-14 11:32:18
summary:    Basic steps to build a GUI application to display the lyrics of the song being played on the spotify app
categories: 
---

## What is the GUI application about ?
This is a simple GUI application which generates the lyrics of the song currently being played on the Spotify app for linux systems. Most of the times we would have to surf the net to get the lyrics of the song we like and sometimes this is quite an irritating procedure. This app is a one step solution to the problem wherein the lyrics of the song get generated side by side.

## Steps to be followed
The basic setup is divided into the following three steps :
* Getting the name of the song currently being played on the spotify app
* Fetching the lyrics of the song
* Displaying the entire information in a GUI application

Let's get started with the development process.

## Getting the name of the song
The following code snippet explains the process of generating the name of the song being played :

```python
import dbus
import re


def get_song_details():

    try:
        # Setup a new bus session
        session_bus = dbus.SessionBus()

        spotify_bus = session_bus.get_object("org.mpris.MediaPlayer2.spotify",
                                             "/org/mpris/MediaPlayer2")
        spotify_properties = dbus.Interface(spotify_bus,
                                            "org.freedesktop.DBus.Properties")
        metadata = spotify_properties.Get("org.mpris.MediaPlayer2.Player", "Metadata")

        # The property Metadata behaves like a python dict
        # for key, value in metadata.items():
        #     print key, value

        # To just print the title

        song_artist = metadata['xesam:artist']
        song_artist = str(song_artist[0]).lower()
        song_artist = song_artist.replace(" ", "")

        song_title = str(metadata['xesam:title']).lower()
        song_title = song_title.replace(" ", "")

        song_art = metadata['mpris:artUrl']
        song_art = str(song_art)

        song_title = re.sub("[\(\[].*?[\)\]]", "", song_title)
        song_title = re.sub('[^A-Za-z0-9]+', '', song_title)

        song_artist = re.sub("[\(\[].*?[\)\]]", "", song_artist)
        song_artist = re.sub('[^A-Za-z0-9]+', '', song_artist)

        print song_title
        print song_artist
        print song_art

        song ={}
        song["song_artist"] = song_artist
        song["song_title"] = song_title
        song["song_art"] = song_art

        return song

    except Exception as e:
        return 0
```

Let us try to analyze the code step by step :
* First we include the necessary python packages to be used in the process.
* The next step uses the dbus session to fetch the metadata from the spotify application interface. To install `dbus`, use the following code :
```python
sudo apt-get install python-dbus
```
* Finally, we use regular expressions to remove unneccessary characters from the name of the artist and the song. A python dictionary with all the important details of the song is formed and returned from the function.

## Fetching the lyrics of the song

The following code snippet uses the name of the song and the artist as parameters to fetch the lyrics from azlyrics.com. Here, we have used BeautifulSoup to scrape the lyrics of the song.

```python
import requests
import urllib2
from bs4 import BeautifulSoup


def get_lyrics(artist, song):

    url = "http://www.azlyrics.com/lyrics/" + artist + "/" + song + ".html"

    # add header
    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.71 Safari/537.36'}

    request = urllib2.Request(url, headers=headers)
    lyrics_file = urllib2.urlopen(request)
    lyrics_html = lyrics_file.read()
    lyrics_file.close()

    soup = BeautifulSoup(lyrics_html, 'html.parser')

    l = []

    for div in soup.find_all('div', attrs={'class': 'col-xs-12 col-lg-8 text-center'}):
        for sub_div in div.find_all('div'):
            l.append(sub_div.text)

    l = map(lambda s: s.strip(), l)

    return l
```

## Building the GUI to show the information

The following code snippet deals with the GUI development :

```python
from Get_song_details import get_song_details
from Get_lyrics import get_lyrics
from Tkinter import *
from PIL import ImageTk, Image
import urllib
import Image
import ImageTk


class GUI():

    def __init__(self):
        song = get_song_details()

        if song == 0 :
            print "Spotify is currently inactive"

        else:

            art_url = song["song_art"]
            lyrics = get_lyrics(song["song_artist"], song["song_title"])

            f = urllib.urlretrieve(art_url, "cover.jpg")

            self.root = Tk()
            self.root.geometry('700x700')
            self.root.title("Spotify Lyrics")
            self.mainframe = Frame(self.root, padx=10, pady=10)


            self.label1 = Label(self.mainframe, text="Song : ")
            self.label1.config(font=("Arial", 18))

            self.label2 =  Label(self.mainframe, text = song["song_title"])
            self.label2.config(font=("Arial", 18))

            self.label3 = Label(self.mainframe, text="Artist : ")
            self.label3.config(font=("Arial", 18))

            self.label4 = Label(self.mainframe, text=song["song_artist"])
            self.label4.config(font=("Arial", 18))

            # Setting the image up
            img = ImageTk.PhotoImage(Image.open("cover.jpg"))

            # Displaying the image

            self.imglabel = Label(self.mainframe, image=img)

            # Textbox for lyrics
            self.T = Text(self.mainframe)
            self.T.insert(END, lyrics[6])

            # Grid Packing

            self.label1.grid(row=0, sticky=E)
            self.label2.grid(row=0, column=1)
            self.label3.grid(row=1, sticky=E)
            self.label4.grid(row=1, column=1)
            self.imglabel.grid(row=2, column = 0, columnspan =2)
            self.T.grid(row=4, column=0, columnspan = 3)

            self.mainframe.pack()

            self.root.mainloop()


if __name__ == "__main__":
    GUI()
```

Let us try to analyse the code step by step :
* In the first step we have included the necessary packages to build the GUI application. Here we are using `Tkinter` to build the GUI. It can be installed using the following code :
```python
sudo apt-get install python-tk
```
* In the next step we fetch the song details using the code discussed in the previous snippet. Using the name of the artist and the song, we fetch the lyrics.
* In the next step we form the basic framework of the GUI using the various features available in the Tkinter package.
* Finally all the elements of the GUI are aligned to their respective positions to give final touches to the GUI.

On running the script, we get the following output :

![Screenshot from 2017-10-14 10-57-22.png](https://cdn.filestackcontent.com/1cdpamNKRSSS5mTkQH1v)

Github repository for this desktop notifier application: [Spotify_lyrics](https://github.com/dushyantRathore/Spotify_lyrics).

Feel free to fork, suggest changes, or point out bugs!

Hope you found this helpful 😄






