## Bash with Configurable Mode Strings ##

This is a patched version of bash 4.3 that adds support for custom prompt mode indicators. It also adds support for a `\m` prompt escape to specify where in the prompt the indicator should occur.

![modestr demo](http://i.imgur.com/7P9FqOn.gif?1)

This will (mostly) [be available in bash 4.4/readline 7.0](http://thread.gmane.org/gmane.comp.shells.bash.bugs/22580/focus=22613).  However the 4.4 beta is currently pretty buggy and the `\m` indicator hasn't been accepted (yet). So for now if you really want this functionality you'll probably have to use the patched 4.3 version.


## Install ##

    $ git clone https://github.com/calid/bash.git -b modestrs --recurse-submodules
    $ cd bash
    $ ./configure
    $ make
    $ sudo make install

You can also just run `./bash` after make completes if you want to try the patched version without installing it

## Usage ##

#### In your `inputrc` ####

```
set show-mode-in-prompt on
set vi-ins-mode-string <some string>
set vi-cmd-mode-string <some other string>
```

If you don't set custom strings the defaults are `(ins)` and `(cmd)`.

#### In your `PS1` ####

Set your PS1 as normal, but now you can also include a `\m` escape to specify where in the prompt you want the mode strings to display.  e.g. `PS1='\u@\h \m $ '`

If you turn `show-mode-in-prompt` on without specifying `\m` the default is to display the mode string at the beginning of the last line of the prompt.

#### All the pretty colors ####

You can use color codes in your mode strings. Simply bracket the color escape sequences with `\1` and `\2`. So to produce colored `(ins)` and `(cmd)` mode strings like in the demo above you would do:

```
set vi-ins-mode-string \1\e[34;1m\2(ins)\1\e[0m\2
set vi-cmd-mode-string \1\e[35;1m\2(cmd)\1\e[0m\2
```

You can also use 256 color escape sequences. Throw in some unicode characters and things can start to get pretty fancy:

![multiline unicode color mode strings](http://i.imgur.com/80zXYbd.gif?1)

## Known Issues ##

#### Multiline prompts ####

* Mode strings currently only work on the last line of multiline prompts. Lame, I know. Unfortunately readline has deeply baked logic to only redraw the last line of multiline prompts, and it will take some work to change this.

* For multiline prompts the vi movement seems to get confused if there's no terminal reset sequence after the last newline. I haven't figured out why this is, but the fix is easy: just make sure you put a reset sequence after the last newline. So not:

    ```
    PS1='\n\m$ '
    ```

    but rather:

    ```
    PS1='\n\[\e[0m\]\m$ '
    ```

## Contributing ##

If you'd like to submit pull requests with fixes or improvements for the mode strings functionality I'm happy to accept them. I'll forward any relevant patches upstream to the bash mailing list (with attribution of course).

## See Also ##

If you want a patched version of the standalone readline library:
https://github.com/calid/readline.git

The original mailing list post:
https://lists.gnu.org/archive/html/bug-bash/2014-08/msg00124.html
