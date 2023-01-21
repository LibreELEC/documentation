# LCDProc

Since Kodi v17 LCD/VFD displays are supported through the LCDProc add-on which must be installed from the Kodi add-on repo. See [script.xbmc.lcdproc/wiki](https://github.com/herrnst/script.xbmc.lcdproc/wiki) for documentation and [LCD.xml.defaults](https://github.com/herrnst/script.xbmc.lcdproc/blob/master/resources/LCD.xml.defaults) for a sample file, and the [LCDProc add-on support thread in Kodi forums](https://forum.kodi.tv/showthread.php?tid=143912).

## Configuration

The `/storage/.kodi/userdata/LCD.xml` file can be created to customise output to an LCD or VFD display. It contains "content" sections that describe how the display will look:

* `<Navigation>` is used for navigation in menus
* `<Music>` is used when listening to music
* `<Video>` is used when watching videos
* `<Tvshow>` is used when watching videos classified as TV shows
* `<General>` is used when there is no activity

Each section contains `<line>` items representing one physical line on the display. Lines scroll horizontally if display text is longer than display width. The display cannot scroll vertically so if you configure five lines on a four line display the fifth line will not show. If there is no value for an Infolabel the line on the LCD/VFD display will be empty and the next line is displayed to avoid empty lines on the display. The file also contains display properties, e.g.

* `<disableonplay>video</disableonplay>` disables the display during video playback
* `<disableonplay>music</disableonplay>` disables the display during music playback
* `<disableonplay>video,music</disableonplay>` disables the display for video and music

For example, the following configuration:

```text
<Music>
  <line>$INFO[[LCD.PlayIcon]] $INFO[[Player.Time]]/$INFO[[Player.Duration]]</line>
  <line>$INFO[[MusicPlayer.Title]]</line>
  <line>$INFO[[MusicPlayer.Artist]]</line>
  <line>$INFO[[MusicPlayer.Album]] ($INFO[[MusicPlayer.Year]])</line>
</Music>
```

Will output on the display like this:

```text
> 00:42/3:20
Gold Digger
Kanye West feat. Jamie Foxx
Late Registration (2005)
```

Please see [Infolabels](https://kodi.wiki/view/InfoLabels) and [Line Tags](https://kodi.wiki/view/Label_Parsing) for more info on how Kodi processes the `<line>` items. The `$LOCALIZE[[ID]]` variable does a lookup in the [Language File](https://github.com/xbmc/xbmc/blob/master/language/English/strings.po|strings.po) and displays the text corresponding to the `[[ID]]` on the LCD/VDF display.

## LCD InfoLabels

In addition to normal InfoLabels, LCD InfoLabels display values without additional text. This helps configuration display more information in one line at the same time and to avoid scrolling of the specified line.

| LCD Infolabel | Function |
| :--- | :--- |
| LCD.PlayIcon | Displays the Play / Stop / Pause / FF / REW Icon |
| LCD.ProgressBar | Displays a progress bar of the currently played item |
| LCD.CPUTemperature | Displays the CPU temperature value with no extra text |
| LCD.GPUTemperature | Displays the GPU temperature value with no extra text |
| LCD.HDDTemperature | Displays the HDD temperature value with no extra text |
| LCD.FanSpeed | Displays the Fan Speed value with no extra text |
| LCD.Date | Displays the current Date with no additional text |
| LCD.FreeSpace\(Drive\) | Displays the value for remaining free space on Drive \(C/E/F/G\) with no additional text |
| LCD.Time21, LCD.Time22 | Displays the time in 2×1 sized characters |
| LCD.TimeWide21, LCD.TimeWide22 | Displays the time in 2×2 sized characters |
| LCD.Time41, LCD.Time42, LCD.Time43, LCD.Time44 | Displays the time in 4×3 sized characters |

The `LCD.Time` InfoLabels can be displayed while Kodi is in screensaver mode or if the visualisation is started by the screensaver. In all other cases the play and progress icons are shown, which may give unexpected results.

