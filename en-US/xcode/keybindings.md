---
name: Keybindings
---

```
{
/* Keybindings for emacs emulation.  Compiled by Jacob Rus.
 *
 * To use: copy this file to ~/Library/KeyBindings/
 * after that any Cocoa applications you launch will inherit these bindings
 *
 * This is a pretty good set, especially considering that many emacs bindings
 * such as C-o, C-a, C-e, C-k, C-y, C-v, C-f, C-b, C-p, C-n, C-t, and
 * perhaps a few more, are already built into the system.
 *
 * BEWARE:
 * This file uses the Option key as a meta key.  This has the side-effect
 * of overriding Mac OS keybindings for the option key, which generally
 * make common symbols and non-english letters.
 */

    /* Ctrl shortcuts */
    "^l"        = "centerSelectionInVisibleArea:";  /* C-l          Recenter */
    "^/"        = "undo:";                          /* C-/          Undo */
    "^_"        = "undo:";                          /* C-_          Undo */
    "^ "        = "setMark:";                       /* C-Spc        Set mark */
    "^\@"       = "setMark:";                       /* C-@          Set mark */
    "^w"        = "deleteToMark:";                  /* C-w          Delete to mark */
    "^y"	= "yankAndSelect:";                 /* C-y          Cycle through kill ring */

    /* Meta shortcuts */
    "~f"        = "moveWordForward:";               /* M-f          Move forward word */
    "~b"        = "moveWordBackward:";              /* M-b          Move backward word */
    "~<"        = "moveToBeginningOfDocument:";     /* M-<          Move to beginning of document */
    "~>"        = "moveToEndOfDocument:";           /* M->          Move to end of document */
    "~v"        = "pageUp:";                        /* M-v          Page Up */
    "~/"        = "complete:";                      /* M-/          Complete */
    "~c"        = ( "capitalizeWord:",              /* M-c          Capitalize */
                    "moveForward:",
                    "moveForward:");                                
    "~u"        = ( "uppercaseWord:",               /* M-u          Uppercase */
                    "moveForward:",
                    "moveForward:");
    "~l"        = ( "lowercaseWord:",               /* M-l          Lowercase */
                    "moveForward:",
                    "moveForward:");
    "~d"        = "deleteWordForward:";             /* M-d          Delete word forward */
    "^~h"       = "deleteWordBackward:";            /* M-C-h        Delete word backward */
    "~\U007F"   = "deleteWordBackward:";            /* M-Bksp       Delete word backward */
    "~t"        = "transposeWords:";                /* M-t          Transpose words */
    "~\@"       = ( "setMark:",                     /* M-@          Mark word */
                    "moveWordForward:",
                    "swapWithMark");
    "~h"        = ( "setMark:",                     /* M-h          Mark paragraph */
                    "moveToEndOfParagraph:",
                    "swapWithMark");

    /* C-x shortcuts */
    "^x" = {
        "u"     = "undo:";                          /* C-x u        Undo */
        "k"     = "performClose:";                  /* C-x k        Close */
        "^f"    = "openDocument:";                  /* C-x C-f      Open (find file) */
        "^x"    = "swapWithMark:";                  /* C-x C-x      Swap with mark */
        "^m"    = "selectToMark:";                  /* C-x C-m      Select to mark*/
        "^s"    = "saveDocument:";                  /* C-x C-s      Save */
        "^w"    = "saveDocumentAs:";                /* C-x C-w      Save as */
    };
	/* Modifier keys: start with C-m - from mgrimes AT stateful.net */
	"^m" = {
		"^ "    = ("insertText:", "\U2423");  /* C-space space */

		"^e"    = ("insertText:", "\U21A9");  /* C-e     return */
		"e"     = ("insertText:", "\U2305");  /* e       enter */

		"^t"    = ("insertText:", "\U21E5");  /* C-t     tab */
		"t"     = ("insertText:", "\U21E4");  /* t       backtab */

		"^d"    = ("insertText:", "\U232B");  /* C-d     delete */
		"d"     = ("insertText:", "\U2326");  /* d       forward delete */

		"^a"    = ("insertText:", "\U2318");  /* C-a     command (apple) */
		"^o"    = ("insertText:", "\U2325");  /* C-o     option */
		"^c"    = ("insertText:", "\U2303");  /* C-c     control */
		"^s"    = ("insertText:", "\U21E7");  /* C-s     shift */
		"s"     = ("insertText:", "\U21EA");  /* s       caps lock */

		"^b"    = ("insertText:", "\U2190");  /* C-b     solid left */
		"^f"    = ("insertText:", "\U2192");  /* C-f     solid right */
		"^p"    = ("insertText:", "\U2191");  /* C-p     solid up */
		"^n"    = ("insertText:", "\U2193");  /* C-n     solid down */
		"b"     = ("insertText:", "\U21E0");  /* f       dotted left */
		"f"     = ("insertText:", "\U21E2");  /* b       dotted right */
		"p"     = ("insertText:", "\U21E1");  /* p       dotted up */
		"n"     = ("insertText:", "\U21E3");  /* n       dotted down */

		"^h"    = ("insertText:", "\U2196");  /* C-h     home */
		"h"     = ("insertText:", "\U2198");  /* h       end */
		"^u"    = ("insertText:", "\U21DE");  /* C-u     page up */
		"u"     = ("insertText:", "\U21DF");  /* u       page down */

		"^x"    = ("insertText:", "\U238B");  /* C-x     escape */
		"x"     = ("insertText:", "\U23CF");  /* x       eject */
	};

}
```
