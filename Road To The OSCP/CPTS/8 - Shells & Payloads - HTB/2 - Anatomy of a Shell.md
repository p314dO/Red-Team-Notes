Todo sistema operativo tiene un shell y para interactuar con él debemos utilizar una aplicación conocida como `terminal emulator`. Estos son algunos de los emuladores de terminal más comunes:

|**Terminal Emulator**|**Operating System**|
|:--|:--|
|[Windows Terminal](https://github.com/microsoft/terminal)|Windows|
|[cmder](https://cmder.app)|Windows|
|[PuTTY](https://www.putty.org)|Windows|
|[kitty](https://sw.kovidgoyal.net/kitty/)|Windows, Linux and MacOS|
|[Alacritty](https://github.com/alacritty/alacritty)|Windows, Linux and MacOS|
|[xterm](https://invisible-island.net/xterm/)|Linux|
|[GNOME Terminal](https://en.wikipedia.org/wiki/GNOME_Terminal)|Linux|
|[MATE Terminal](https://github.com/mate-desktop/mate-terminal)|Linux|
|[Konsole](https://konsole.kde.org)|Linux|
|[Terminal](https://en.wikipedia.org/wiki/Terminal_(macOS))|MacOS|
|[iTerm2](https://iterm2.com)|MacOS|
Forma de identificar al intérprete de idiomas es viendo los procesos que se ejecutan en la máquina. (ZSH)
```js
┌──(pelado㉿kali)-[~]
└─$ ps                         
    PID TTY          TIME CMD
  34391 pts/0    00:00:00 zsh
  34976 pts/0    00:00:00 ps   
```

También podemos averiguar qué lenguaje de shell está en uso viendo las variables de entorno usando el `env` dominio:
```js
┌──(pelado㉿kali)-[~/HTB/Academy/ShellsPayloads]
└─$ env
...
SHELL=/usr/bin/zsh
...
```

