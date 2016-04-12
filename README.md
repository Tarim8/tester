NAME
====

  connected ---- connect to programs like nc, telnet, ssh, php, with local Bash line editing


SYNOPSIS
========

  connected [-esc ESCAPE_CHAR] [-hist HISTORY_FILE] [-int] [-debug] CONNECT_CMD


DESCRIPTION
===========

  connected is a program to connect to line based services, computer emulators
  (like multics) or games (like adventure, or mud)


OPTIONS
=======
    -esc
  set escape character for escape commands:  
      ~e  turn off echo  
      ~q  quit  
      ~r  raw mode; exit with ~{ctrl-J}.  
      ~X  send {ctrl-X} (or other control characters)  
      ~~...  strip leading ~  

    -hist
  set history file (default is ~/.connected_history)

    -int
  allow interrupts {ctrl-C} to be passed through

    -overkill
  kill program immediately before exiting

    -debug
  print debug messages


FILES
=====
  ~/.connected_history is the default file for the command history.


CAVEATS
=======
  The exit from raw mode is somewhat ungainly - it sends ~{ctrl-J} but drops
  the next character.


AUTHOR
======
  Written by Tarim.
  Error reports to <connected-cmd@mediaplaygrounds.co.uk>.


SEE ALSO
========
  nc(1), telnet(1), ssh(1)

