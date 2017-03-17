---
layout: post
title:  "Elektronik-Kit und erste Inbetriebnahme mit LED-Schaltung"
date:   2017-03-17 12:00:00
categories: electronics hardware
---

Auf der Suche nach einem Elektronikbaukasten bin ich auf das [Elegoo Electronics Fun Kit](https://www.amazon.de/Elegoo-Electronic-Breadboard-Kondensator-Potentiometer/dp/B01J79YG8G/ref=sr_1_fkmr0_1?ie=UTF8&qid=1489770984&sr=8-1-fkmr0&keywords=eleego+electronics+fun+kit) aufmerksam geworden. Das Kit enthält alle notwendigen Bauteile für einfache, prototypische Schaltungen für die Arbeit mit Mikrocontrollern. Besonders wichtig waren mir ein Steckbrett (Breadboard), damit ich ohne Löten Schaltungen zusammenbauen kann. Zudem war mir eine komfortable Spannungsversorgung wichtig.

![]({{ site.url }}/assets/inhalt_eleegokit.jpg)

Das Kit enthält ein Spannungsversorgungsmodul, das auf das Steckbrett aufsteckbar ist. Über USB kann man das Steckbrett wahlweise mit einer Spannung von 3,3V oder 5V komfortabel versorgen.

Als Inbetriebnahmetest möchte ich einer der im Kit enthaltenen LEDs zum Leuchten bringen. Die LED darf nicht ohne Weiteres an die Spannungsversorgung angeschlossen werden. Ein Vorwiderstand ist notwendig, um den Strom, der durch die LED fließt, zu limitieren. Die Datenblätter zu den im Kit enthaltenen Bauteilen stehen auf der Webseite des Herstellers im [Downloadbereich](http://www.elegoo.com/download/) bereit.

Um den nötigen Vorwiderstand zu bestimmen ist ein Blick in das Datenblatt für die LED notwendig. Der kontinuierlich erlaubte Vorwärtsspannung beträgt:

\[I_F = 50 mA\]

Die Vorwärtsspannung der LED beträgt laut Datenblatt:

\[U_F=  2 V\]

Als Versorgungsspannung habe ich gewählt:

\[U_0=3,3 V\]

Nach der Maschenregel ergibt sich für eine Reihenschaltung von Widerstand und LED:

\[-U_0+I_F⋅R+U_F=0\]

Hiermit lässt sich die Größe des Widerstands bestimmen:

\[R=\frac{U_0-U_F}{I_F}\]

Bei einer Versorgungsspannung von U_0=3,3 V ergibt sich ein notwendiger Vorwiderstand von:

\[R=26 \Omega\]

Wird die Versorgungsspannung auf \\(U_0=5 V\\) erhöht, ergeben sich:

\[R=60 \Omega\]

Der Vorwiderstand kann ruhig größer gewählt werden, damit die LED nicht so hell leuchtet. Häufig ist dies sogar gewollt. Je größer der Vorwiderstand, desto kleiner der Strom, desto dunkler auch die LED.

Die folgende Abbildung zeigt die Inbetriebnahme mit einem Vorwiderstand von \\(R = 100 \Omega\\)

![]({{ site.url }}/assets/LED.png)
