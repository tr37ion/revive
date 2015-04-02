# What is 'revive'?

Revive.el  saves current editing  status including  the window
splitting   configuration,   which   can't   be   recovered   by
`desktop.el' nor by `saveconf.el', into a file  and reconstructs
that status correctly.

## Installation

Put revive.el into your elisp  directory included in load-path
and write the next expressions.

    (autoload 'save-current-configuration "revive" "Save status" t)
    (autoload 'resume "revive" "Resume Emacs" t)
    (autoload 'wipe "revive" "Wipe Emacs" t)

And define favorite keys to those functions.  Here is a sample.

    (define-key ctl-x-map "S" 'save-current-configuration)
    (define-key ctl-x-map "F" 'resume)
    (define-key ctl-x-map "K" 'wipe)

## How to use

Call `save-current-configuration' (`C-x S' if you define key as
above) when  you want to   save current editing status  and call
`resume' to restore it.  Numerical prefix  arg to them specifies
the buffer number in which the editing status will be saved.

### Sample Operations

    C-u 2 C-x ;save status into the buffer #2
    C-u 3 C-x F;load status from the buffer #3

### Save Variables

Revive.el can save global or local variables.  The default
variables to be saved are in revive:save-variables-global-default
and revive:save-variables-local-default.  If you want to save other
global/local variables, define them in
revive:save-variables-global-private or
revive:save-variables-local-private in a form of a list.  If you
are using `gmhist', the next expression in ~/.emacs is useful.

    (setq revive:save-variables-global-private
      '(file-history buffer-history minibuffer-history))

### Restoring abnormal buffers

The variable revive:major-mode-command-alist-default holds the
list of  major-mode  name  vs.   command  name.   For  example,
mh-rmail  command sees the directory ~/Mail/inbox/ and sets  the
major-mode  to  'mh-folder-mode.   And  gnus  command  sets  the
major-mode to  'gnus-Group-mode.   If  you want to define  other
relations between major-mode and command,  set the user variable
revive:major-mode-command-alist-private as follows:

    (setq revive:major-mode-command-alist-private
	  '((your-own-mode. your-own)
	   (foo-mode. foo)
	   ("*Hanoi*". hanoi)

it tells revive.el to execute the corresponding command when the
saved configuration has the buffer with that major-mode. To know
the major-mode of a certain buffer, type `M-ESC' and `major-mode
CR'.   And as  you  see above, buffer-name  string  can be  used
instead of major-mode symbol.

For  other  special modes that  cannot be resumed by executing
certain function,  define a function to resume and declare it in
the  variable revive:major-mode-command-alist-private.  In  that
function, the following values are available.

    (revive:prop-file-name x)   ;file name
    (revive:prop-buffer-name x) ;buffer name
    (revive:prop-major-mode x)  ;major-mode
    (revive:prop-point x)       ;(point)
    (revive:prop-mark x)        ;(mark)
    (revive:prop-varlist x)     ;alist of pairs of (var . val)

## For programmers

This program provides two powerful functions:

 * current-window-configuration-printable
 * restore-window-configuration

When you want to save a screen your program manages into a file,
and  restore  it,  save  the  return  value  of  current-window-
configuration-printable, and read and give it to restore-window-
configuration.

### Sample
 * To save:
     (insert (prin1-to-string (current-window-configuration-printable)))
 * To restore:
     (restore-window-configuration (read (current-buffer)))

Since set-window-configuration cannot set the configuration of
other frame, the program as below should be useful.

### Sample

    (defun frame-copy-configuration (nth)

 * Copy confinguration of current frame to NTH next frame.

    (let ((config (current-window-configuration-printable)))
    (other-frame nth)
    (restore-window-configuration config)))

# Copying

This program is distributed as a free  software. The author is
not responsible  for  any  possible   defects   caused  by  this
software.

# Notes
Comments  and bug   reports  are welcome. Don't  hesitated  to
report.  My possible e-mail address is following:

 yuuji@gentei.org
