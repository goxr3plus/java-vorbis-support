# java-vorbis-support

Combination and continuation of JOrbis, JavaSPI and Tritonus-Share to provide Ogg/Vorbis playback capabilities for Java using the Sound SPI.

# TODO NOT WORKING YET
### Step 1. Add the JitPack repository to your build file
https://jitpack.io/private#goxr3plus/java-vorbis-support
``` XML
<repositories>
	<repository>
	   <id>jitpack.io</id>
	   <url>https://jitpack.io</url>
        </repository>
</repositories>
```

###  Step 2. Add the dependency
``` XML
<dependency>
   <groupId>com.github.goxr3plus</groupId>
   <artifactId>java-vorbis-support</artifactId>
   <version>TAG</version>
</dependency>
```


## Introduction

Ogg/Vorbis is a widely used free audio format featuring high compression ratios and there are libraries who enable support for Ogg in Java. Among them are [JOrbis](http://www.jcraft.com/jorbis/)
by JCraft - a pure Java Ogg/Vorbis decoder and [Ogg Vorbis SPI](http://www.javazoom.net/vorbisspi/vorbisspi.html) by JavaZoom which registers JOrbis as a service for the Java Sound API. The later also relies partly on the [Tritonus share](http://www.tritonus.org/) library. All three projects are inactive for several years now.

The reference implementation for Ogg/Vorbis is [libvorbis](http://xiph.org/vorbis/) written in C.

Vorbis support is the all-in-one combination and possibly continuation of JOrbis, JavaSPI and Tritonus-Share.

Alternatives are [Paul's SoundSystem](http://www.paulscode.com/forum/index.php?topic=4.0) (full support of Java Sound, JOAL, LWJGL gaming libraries, wrappers around JOrbis, J-OGG),
[Vorbis-Java](http://downloads.xiph.org/releases/vorbis-java/) (Java encoder and decoder at xiph.org), [EasyOgg](http://www.cokeandcode.com/index.html?page=libs) (wrapper around JOrbis)
and [J-OGG](http://www.j-ogg.de/) (independent ogg/vorbis decode). These projects are mostly inactive for years now.

## Why this project?

All three libraries, Jorbis, Vorbis SPI and Tritonus Share are almost always bundled together. Together they constitute a complete plattform independent Ogg/Vorbis support for the Java Sound API. Fortunately they share the same open source license (LGPL). Combining them together makes distribution and handling easier, can reduce the size of the download and makes testing and debugging easier. Last but not least, increasing the efficiency by optimizing the code will be a bit easier.

However since these libraries already exist, we do not need to take care of backwards compatibility, since there is always the fallback to the original libraries. Therefore to be able to use newer features of the Java language, the required Java version currently is 7 or later.

## Example

This library used the Services Provider Interface to enable Ogg/Vorbis playback under the hood without changing any of the code on the client's library side. Just put this library in the classpath
and access your sound resources (SourceDataLine or Clip) as you would without Ogg/Vorbis support. See the Java Tutorial on [Playing Back Audio](http://docs.oracle.com/javase/tutorial/sound/playing.html) for more information. And here is also an example that would play an entire ogg file.

    try {
        AudioInputStream in = AudioSystem.getAudioInputStream(new File("xyz.ogg");
        if (in != null) {
            AudioFormat baseFormat = in.getFormat();

            AudioFormat targetFormat = new AudioFormat(AudioFormat.Encoding.PCM_SIGNED, baseFormat.getSampleRate(),
            16, baseFormat.getChannels(), baseFormat.getChannels() * 2, baseFormat.getSampleRate(), false);

            AudioInputStream dataIn = AudioSystem.getAudioInputStream(targetFormat, in);

            byte[] buffer = new byte[4096];

            // get a line from a mixer in the system with the wanted format
            DataLine.Info info = new DataLine.Info(SourceDataLine.class, targetFormat);
            SourceDataLine line = (SourceDataLine) AudioSystem.getLine(info);

            if (line != null) {
                line.open();

                line.start();
                int nBytesRead = 0, nBytesWritten = 0;
                while (nBytesRead != -1) {
                    nBytesRead = dataIn.read(buffer, 0, buffer.length);
                    if (nBytesRead != -1) {
                        nBytesWritten = line.write(buffer, 0, nBytesRead);
                    }
                }

                line.drain();
                line.stop();
                line.close();

                dataIn.close();
            }

            in.close();
            // playback finished
        }
    } catch (UnsupportedAudioFileException | IOException | LineUnavailableException e) {
        // failed
    } 

## Alternatives

[VorbisJava](https://github.com/Gagravarr/VorbisJava) by Gagravarr.
