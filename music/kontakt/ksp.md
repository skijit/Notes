

KSP Basics
=================
- KSP Manual is [manual](/resources/docs/music/manuals/KSP%20Reference%20Manual.pdf)
- Excellent tutorial [here](http://www.nilsliberg.se/ksp/scripts/tutorial/)



## General 
- KSP Scripts are like MIDI effects, but different insofar as they let you:
    - change volume/panning/tuning of a specific note
    - fade in/out a specific note
    - specify which groups should be used for playing back a specific note
    - start playback of a note at any sample offset (only in Sampler mode)
    - control a wide range of Kontakt parameters (maybe in the Kontakt 2.1 update)
- **NEVER ENTER IN Ctrl+Z in the Kontakt script editor or you may loose everything!!!**

## Language
- KSP Numerics are limited to integers
- Comments occur between {}
- Primary Block of code is the 'Callback'.  (Basically an event handler)
```(ksp) 
    on init
        { Called when the script is loaded- only once! }
    end on   
             
    on note  
        { called whenever a note is pressed.  }
        { you can alter the note or generate new notes! }
    end on   

    one release
        { called whenever a note is released }
    end on

    on controller
        { called whenever a CC message is received. }
        { you can use this to respond to mod wheel or }
        { sustain pedal, etc.}
    end on

    on ui_control(<variable>)
        { UI callback, executed whenever the user changes }
        { the respective UI element }
    end on
```

- Callbacks can be stopped by using command 'exit'
- All user defined variables must be declared in the on init callback.
               
### Variables
- begin with $
- declare with declare: `declare $x`
- Assignment operation is ':=': `declare $x := 40`
- **division operator is integer based!  Be careful!**
- String variables:
```(ksp)
on init
    declare @text
    @text := "Last received note number played or released: "
end on
```

### Arrays              
- Numeric Arrays
    - prefixed with %
    ```(ksp)      
    declare %note_tunings[12]
    %note_tunings[0] := 10
    %note_tunings[1] := -3
    %note_tunings[2] := -15
    ```
- String arrays:
    - prefixed with '!' 
    ```(ksp)
    declare !note[12]
    ```
                               
### Constant variables
- use the 'const' modifier: `declare const $max_velocity := 100`
 
### User interface controls
- knobs (`ui_knob`)    (can be automated!)
- value edit boxes (`ui_value_edit`)  (can be automated!)
- switch buttons (`ui_switch`) (can be automated!)
- menus
- buttons (`ui_button`) (NOT AUTOMATABLE)
- text labels 
- tables
- examples
    ```(ksp)
    declare ui_button $mybutton
    declare ui_knob $volume(-10, 10, 1)
    declare ui_value_edit $humanize_delay(0, 1000, 1000)
    ```
       
### Functions
- provided by the Kontakt API, see manual
- example:
    ```(ksp)
    on note
        play_note(60, 100, 0, -1)    
        { play C3 at velocity 100, release ...
          when the triggering note is }
    end on
    ```
 
### Built-in Variables
- accessible in most callbacks
- read-only / predeclared
- examples
    ```(ksp)
    {   the MIDI note number (pitch) of the 
        note that triggered the note/release callback. 
        In the range 0-127. }
    $EVENT_NOTE

    {   the velocity of the note that triggered the 
        note/release callback. In the range 0-127. }
    $EVENT_VELOCITY 
    
    {   a number which is unique to the note event 
        that triggered the note/release callback.  }
    $EVENT_ID

    on note
        { stop original note   }
        ignore_event($EVENT_ID) 
        
         { play transposed note }
        play_note($EVENT_NOTE+12, $EVENT_VELOCITY, 0, -1)    
    end on
    ```
 
### Conditionality
- example
    ```(ksp)
    on note
        if ($tuning_active = 1)
        { lines to be executed if the condition is true }
        else
        { lines to be executed if the condition is false }
        end if
    end on
    ```
               
### Operators:
- `=`             : equals
- `#`             : not equals
- `>`, `<`, `>=`, `<=`
- `and`
- `or`
- `not`
- you can use as many parens as necessary in your conditions
 
### Looping
- example
    ```(ksp)
    on note
        { ignore original note }
        ignore_event($EVENT_ID)

        { repeat while the original is held: }
        while ($NOTE_HELD = 1)                

            { play note of length 0.2 seconds  }
            play_note($EVENT_NOTE, $EVENT_VELOCITY, 0, 200000)   
        
            { wait 0.2 seconds }
            wait(200000)                                         
        end while
    end on
    ```
- incrementor function: `inc()` 
- example iterating through an arrays
    ```(ksp)
    on init
        declare %notes[8] := (0, 4, 7, 4, 12, 4, 7, 4)
        declare polyphonic $i                                                   
    end on

    on note
        ignore_event($EVENT_ID)        
        
        { assign the value 0 to $i } 
        $i := 0                        
        
        { repeat while $i is less than 8 }
        while ($i < 8)                  
            play_note($EVENT_NOTE + %notes[$i], $EVENT_VELOCITY, 0, 100000)
            
            wait(100000)  

            { increment $i by one } 
            inc($i)                       
        end while
    end on
    ```
- note this line above:
    - `declare polyphonic $i`
    - `polyphonic` is a modifier that makes sure the value is tied to the $EVENT_ID.  Otherwise separate note events could increment this value.

### Misc
- Group Functions
    - You can use `allow_group()` or `disallow_group()` to block a group from being active
- You can query which callback triggered a function with `$NI_CALLBACK_TYPE` and the corresponding built-in constants.
    ```(ksp)
    function show_callback_type
        if ($NI_CALLBACK_TYPE = $NI_CB_TYPE_NOTE)
            message("Function was called from note callback!")
        end if

        if ($NI_CALLBACK_TYPE = $NI_CB_TYPE_CONTROLLER)
            message("Function was called from controller callback!")
        end if
    end function

    on note
        call show_callback_type
    end on
   
    on controller
        call show_callback_type
    end on
    ```

## UI-Related Functionality
```(ksp)
{ make the current tab visible (outside the edit mode) }
make_perfview 

{ retrieve the numeric id of the specific ui element (integer) }
get_ui_id(<variable>) 
declare ui_switch $<variable-name>  

{  positions an element in the grid use it to move the label to top left corner (0,0) }
move_control(<variable>,<x-position>,<y-position>) 

{ to position with the exact pixels }
move_control_px(<variable>,<x-position>,<y-position>)

{ parm: 1-8  b/c although it allows 16 rows in the grid, a knob is 2 rows}
set_ui_height() 

{ setting in pixels, pass in an int for the pixel height}
set_ui_height_px() 

{ to declare a ui element, you do do this in on init: }
declare ui_knob $myName(myparams)
```
- UI objects
    - ui_knob - A default rotary dial.
    - ui_slider - A default slider / horizontal fader.
    - ui_label - Simple text label
    - ui_button - A simple button
    - ui_menu - A drop down menu
    - ui_switch - Simple on/off switch
    - ui_table - Useful interface for sequencers and arpeggiators
    - ui_file_selector - For selecting and loading files
    - ui_level_meter - A simple audio level meter (Kontakt 5)
    - ui_text_edit - Enter user text
    - ui_value_edit - Enter a user value
    - ui_waveform - Add a waveform display of a zone or slices (Kontakt 5)
- `set_text(uiVar, 'the text you want displayed next to it')`
- every automatable parameter has a corresponding system variable (e.g. `$ENGINE_PAR_BITS`)
- getting/setting engine parameters:
    ```(ksp)
    set_engine_par()
    get_engine_par()
    get_engine_par_display()
    ```
- reacting when $myControl is updated
    ```(ksp)
    on ui_control($myControl)
        { parm1: fixed name, parm2: value to set from, parm3: group index, 
          parm4: slot index, parm5: -1 if group insert, 0 instrument insert, 1 send insert }  
        set_engine_par($ENGINE_PAR_CUTOFF, $myControl, 0, 0, -1)
        
        { sets the value displayed to be synced with the corresponding knob.  
          also copy this command into init callback}
        set_knob_label($myControl, get_engine_par_display($ENGINE_PAR_CUTOFF, 0, 0, -1)) 
    end on
    ```    
- use `make_persistent($myControl)` on all controls in the init callback!
- to set knob units (there are vals for DB, ms, %, Octave, and Semitone ) 
    - `set_knob_unit($myControl, $KNOB_UNIT_HZ)` 
- to get the system name of a modulator, right click on that area with the script open.
- misc links:
    - [blog](http://blog.yummybeats.com/ksp-kontakt-scripting/)
    - [noisepages tutorial](http://kore.noisepages.com/2008/12/21/dive-into-kore-scripting-a-gentle-introduction-plus-script-downloads/)
    - [article in askaudiomag](http://www.askaudiomag.com/articles/introduction-to-scripting-in-kontakt-part-4)
    - [kvr thread](http://www.kvraudio.com/forum/viewtopic.php?t=324805)
    - [youtube video](https://www.youtube.com/watch?v=FvBntBshcM4)






