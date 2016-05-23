**Das hier ist erst einmal die Beschreibung einer Idee. Weder vollständig noch korrekt.**

[Offene Fragen](https://github.com/funkenstrahlen/podlive-standard/issues)

# Podlive Standard

Ein Standard für offenes Podcast Livestreaming.

Es gibt drei Parteien: Der Podcaster und sein Feed, das Livestreaming Backend und ein Podcastclient Cloudbackend. Podcastclient deshalb, weil Podcast Livestreams in einem Podcastclient perfekt aufgehoben sind und das Cloudbackend ohnehin schon Feeds verarbeitet.

## Die Grundidee
Informationen zur Livesendung wandern in den Podcastfeed. Geplante Episoden als eigenes `item`, aber **ohne Enclosure**. So werden sie von Podcastclients ignoriert.

Ein `item` enthält dann folgende Infos:

* Wann wird (voraussichtlich) gesendet?
* Wo gibt es den Livestream? (mehrere URLs möglich)
* Sendungstitel
* Sendungsbeschreibung
* Infos zum Chat (optional)
* Collaborators (optional) (wobei das eigentlich ein anderer Standard sein sollte unabhängig vom Livestreaming)
* Wie lange wird die Sendung dauern? (optional)
* Coverart (optional)
* Websocket URL für Callbacks (optional)

Diese Infos kann dann ein Podcast Backend parsen, das ohnehin schon Feeds ausliest.

Die Callbacks sind vor allem für Push-Notifications interessant und um zu wissen, ob eine Sendung wirklich läuft. Die Callback URL zeigt dann zum Beispiel auf ein Streaming Backend, das diese standardisierte Schnittstelle anbietet. Jedes Backend, das sich für Echtzeit Informationen zu einer Sendung interessiert kann sich zu diesem Websocket verbinden und darüber Infos direkt vom Streaming Backend erhalten.

Anfragen, die man über den Wegsocket Callback abwickeln könnte:

* Läuft der Stream?
* Läuft die Sendung?
* Aktuelle Hörerzahlen
* Sende Notification (zum Beispiel *Sendung beginnt in 10min*)

![Grafik](images/Grafik.png)

## RSS Livestream Item

Namespace definieren: `xmlns:podlive="http://podlove.org/podlive"`

```xml
<channel>
	<title>Funkenstrahlen</title>
	<link>http://podcast.funkenstrahlen.de</link>
	<description>Technik, Internet, Gesellschaft.</description>
	<lastBuildDate>Fri, 13 May 2016 11:22:17 +0000</lastBuildDate>
	<image><url>http://podcast.funkenstrahlen.de/wp-content/cache/podlove/b8/384e4a23bd372ef6ef74855365d14e/funkenstrahlen_original.png</url><title>Funkenstrahlen</title><link>http://podcast.funkenstrahlen.de</link></image>
	...
	<podlive:callback>URL</podlive:callback>
	<podlive:audiosources>
	    <podlive:audiosource format="mp3" bitrate="128" type="dash">URL</podlive:audiosource>
	    <podlive:audiosource format="mp3" bitrate="128" type="hls">URL</podlive:audiosource>
	    <podlive:audiosource format="mp3" bitrate="128" type="direct">URL</podlive:audiosource>
	    <podlive:audiosource format="mp3" bitrate="256" type="direct">URL</podlive:audiosource>
	</podlive:audiosources>
	<item>
	    <title>FS002 - Arduino Blinkenlichter</title>
	    <link>http://podcast.funkenstrahlen.de/2013/02/27/fs002-arduino-blinkenlichter/</link>
	    <pubDate>Wed, 27 Feb 2013 08:46:18 +0000</pubDate>
	    <guid isPermaLink="false">podlove-2015-05-17t15:21:00+00:00-9a6539d4d72f6e7</guid>
	    <description>Die zweite Folge und schon eine Sonderfolge. Ich erzähle von meinem Arduino Projekt, an dem ich die letzten Tage gebastelt habe. Dabei versuche ich zu erklären wie man die LED-Leuchtleisten von IKEA an den Arduino anschließen kann, welche Bauteile man dafür braucht und wie ich es geschafft habe, dass man die Farbe der LEDs dann mit dem iPhone steuern kann. Durch die simple API bieten sich nun unendlich viele Möglichkeiten.</description>
	    <atom:link rel="http://podlove.org/deep-link" href="http://podcast.funkenstrahlen.de/2013/02/27/fs002-arduino-blinkenlichter/#" />
	    <podlive:startDate>Wed, 27 Feb 2013 08:46:18 +0000</podlive:startDate>
	    <podlive:endDate>Wed, 27 Feb 2013 08:46:18 +0000</podlive:endDate>
	    <itunes:image href="http://podcast.funkenstrahlen.de/wp-content/cache/podlove/fe/cf0a7a7dfb680f8da110c73274b623/fs002-arduino-blinkenlichter_original.png" />
	</item>
</channel>
```

## Websocket Callback API

